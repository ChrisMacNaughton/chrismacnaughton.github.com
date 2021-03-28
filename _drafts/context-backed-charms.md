---
layout: single
title: "Context Backed Charms"
description: "Exploring a new charming paradigm."
tags: [juju, juju charms]
category: charming
blog: true
comments: true
---

The OpenStack Charms designed an interesting approach to identifying when all necessary data is present to render a configuration file. A simple example of what can be done is to prepare a context that will provide hostname information:

```python
class HostInfoContext(OSContextGenerator):
    """Context to provide host information."""

    def __call__(self):
        name = socket.gethostname()
        ctxt = {
            'host_fqdn': socket.gethostname(),
            'host': name,
        }
        return ctxt
```

<p class="notice">The OSContextGenerator above can be found at <a href="https://github.com/juju/charm-helpers/blob/aeba5d991f72f5526472700554bb1c744394a622/charmhelpers/contrib/openstack/context.py#L163">Github</a></p>

The best way to consume this `Context` is to configure a resource map and add it:

```python
resource_map = {
    '/path/to/config/file': {
        'services': ['my-service-name'],
        'contexts': [HostInfoContext()],
    }
}

TEMPLATES = 'templates/'

def register_configs():
    '''
    Returns an OSTemplateRenderer object with all required configs registered.
    '''
    configs = templating.OSConfigRenderer(templates_dir=TEMPLATES)
    for cfg, d in resource_map.items():
        configs.register(cfg, d['contexts'])
    return configs

def restart_map():
    '''
    Constructs a restart map based on charm config settings and relation
    state.
    '''
    return {k: v['services'] for k, v in resource_map.items()}

@hooks.hook('config-changed')
@pausable_restart_on_change(restart_map())
def config_changed():
    configs = register_configs()
    configs.write_all()
```
<p class="notice">The OSConfigRenderer above can be found at <a href="https://github.com/juju/charm-helpers/blob/36d0955b8d22b2541bea318ad892c33373a80da6/charmhelpers/contrib/openstack/templating.py#L136">Github</a></p>

With the above snippet of code, whenever the `host` and `host_fqdn` is available, the config file at `/path/to/config/file` will be rendered as a Jinja template and will have the specified variables available. A result of the [`pausible_restart_on_change` decorator](https://github.com/juju/charm-helpers/blob/master/charmhelpers/contrib/openstack/utils.py#L1672), whenever the configuration files are updated, services will be restarted. While this example is fairly minimal, it can be expanded nicely to include relation data:

```python
class DBContext(OSContextGenerator):
    interfaces = ['db']

    def __init__(self, database=None, user=None, relation_prefix=None,
                 ssl_dir=None, relation_id=None):
        self.relation_prefix = relation_prefix
        self.database = database
        self.user = user
        self.ssl_dir = ssl_dir
        self.rel_name = self.interfaces[0]
        self.relation_id = relation_id

    def __call__(self):
        if self.relation_id:
            rids = [self.relation_id]
        else:
            rids = relation_ids(self.interfaces[0])

        for rid in rids:
            self.related = True
            for unit in related_units(rid):
                rdata = relation_get(rid=rid, unit=unit)
                host = rdata.get('db_host')
                host = format_ipv6_addr(host) or host
                ctxt = {
                    'database_host': host,
                    'database': self.database,
                    'database_user': self.user,
                    'database_password': rdata.get(password_setting)
                }
                if self.context_complete(ctxt):
                    return ctxt
        return {}
```

Looking at the above snippet, the `__init__` function sets up some variables but is otherwise uninteresting. The `__call__` function, however, handles the Context. When the [self.context_complete](https://github.com/juju/charm-helpers/blob/aeba5d991f72f5526472700554bb1c744394a622/charmhelpers/contrib/openstack/context.py#L173) function is called, the default check is that all entries in the passed in dictionary must have calues, but is overridable as necessary.

When the previous example calls `OSConfigRenderer().write_all()`, the `OSConfigRenderer` checks for `missing_data` in addition to missing or incomplete relations.

If we expand on this concept a bit, it's not unreasonable to assert that a Context could ensure that necessary packages are installed, or could set their own relation data!

To explore this idea, I'll be writing a [Nodepool charm](https://zuul-ci.org/docs/zuul/howtos/nodepool_install.html) in this style!

To start, I'll create a directory for the charm: `mkdir nodepool && cd nodepool`.

In that directory, we'll create a super basic structure for the charm:

```
.
├── README.md
├── config.yaml
├── dispatch
├── lib
│   ├── __init__.py
│   ├── charmhelpers (*)
│   ├── context_charm.py
│   └── contexts.py
├── metadata.yaml
├── templates
│   └── nodepool.yaml
└── tox.ini
```

A couple of things to call out in the above directory structure:

* We've initialized a git submodule in `lib` with [charmhelpers](https://github.com/juju/charm-helpers).
* The `dispatch` file listed above is a new Juju feature recently added that will be called for every hook execution, rather than depending on executable hooks in a `hooks` directory.

