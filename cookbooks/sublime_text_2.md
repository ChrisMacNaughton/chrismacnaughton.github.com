---
layout: Cookbook
title: "Cookbook"
---
#Description
This is a cookbook to install Sublime Text 2 and install the license key if available

#Requirements
- Ubuntu
- Debian

#Attributes


#Usage

```node['sublime']['licensekey'] = Base64.decode64('licensekey' || false)```

```node['sublime']['licensekey']``` is the entire contents of the licensekey between:

```----- BEGIN LICENSE -----```

and

```------ END LICENSE ------```