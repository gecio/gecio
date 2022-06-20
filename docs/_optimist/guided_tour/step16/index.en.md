---
title: "16: Get to know HEAT better"
lang: en
permalink: /optimist/guided_tour/step16/
nav_order: 1160
parent: Guided Tour
---

# Step 16: Get to know HEAT better

## Start

At first, it may look like that creating a VM with a *Heat* template, or
alternatively with the OpenStack client takes the same amount of time. While this is true if you
only want to create the VM once, the advantage of using *Heat* is
that you can reuse the template.

Now that you have a simple template, you can familiarise yourself with *Heat* by adding a variable parameter to your template.

## Parameters

In this example, you will add a parameter for the SSH key. The advantage of this
is that you can use a VM with different keys without changing the template.

Define the parameter and its type. The correct type for
what we want to accomplish here is `string`:

```yaml
heat_template_version: 2014-10-16
 
parameters:
    key_name:
        type: string
```

Now that you have defined the first parameter, you can add the same resource to
your template as follows:

```yaml
heat_template_version: 2014-10-16

parameters:
    key_name:
        type: string


resources:
    Instanz:
    type: OS::Nova::Server
    properties:
        key_name: Beispiel
        image: Ubuntu 16.04 Xenial Xerus - Latest
        flavor: m1.small
```

Now you can actually use your parameter, and can replace *Beispiel* with
your parameter.

You can do this with the get\_param syntax (to get the parameter).

The template is now ready to use and you can define the key\_name from the command line as demonstrated in our previous command:

```yaml
heat_template_version: 2014-10-16

parameters:
    key_name:
        type: string

resources:
    Instanz:
    type: OS::Nova::Server
    properties:
        key_name: { get_param: key_name }
        image: Ubuntu 16.04 Xenial Xerus - Latest
        flavor: m1.small
```

## Conclusion

You have now added a variable parameter to your template. In the next step, you will add the network.
