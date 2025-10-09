+++
date = '2025-10-05T11:34:57+02:00'
title = 'DHM 2025 - Unauthentik'
keywords = ['ctf']
+++

In the second iteration of the [German Hacking Championship,](https://hacking-meisterschaft.de/) one of the challenges consisted of a misconfigured Authentik instance.
Authentik is a self-hosted, open-source identity provider.
The challenge provided us with a `blueprint.yaml` file, a template that can automate Authentik configurations and manage user-interaction flows.
<!--more-->

```yaml
# yaml-language-server: $schema=https://goauthentik.io/blueprints/schema.json
version: 1
metadata:
  name: Challenge Blueprint
entries:
  - model: authentik_core.user
    id: admin
    identifiers:
      username: admin
    attrs:
      name: admin
      is_active: true
      type: internal
      groups:
        - !Find [authentik_core.group, [name, authentik Admins]]
  - model: authentik_core.user
    id: user
    identifiers:
      username: user
    attrs:
      name: user
      is_active: true
      type: internal
      groups: []
      password: user
  - model: authentik_flows.flow
    id: flag_flow
    identifiers:
      slug: flag
    attrs:
      designation: stage_configuration
      authentication: require_superuser
      title: Flag
      name: Flag
  - model: authentik_stages_prompt.prompt
    id: flag_prompt
    identifiers:
      name: flag_prompt
    attrs:
      field_key: flag
      label: Flag
      type: static
      initial_value: !Env FLAG
  - model: authentik_stages_prompt.promptstage
    id: flag_stage
    identifiers:
      name: flag_stage
    attrs:
      fields:
        - !KeyOf flag_prompt
  - model: authentik_flows.flowstagebinding
    identifiers:
      order: 1
      stage: !KeyOf flag_stage
      target: !KeyOf flag_flow
```

The `flag_flow` looks interesting.
The `flag_stage` specifies the screen shown to the user and includes a `flag_prompt`, which defines the field that displays the flag (read from the environment).
The config creates two accounts:
`user` (password `user`) and an `admin` who belongs to the `authentik Admins` group.
From our research, Authentik exposes its initial setup flow whenever the admin password isn’t set.
That lets us visit `/if/flow/initial-setup` and set any admin password.
After logging in with that password, we can go to `/if/flow/flag` and retrieve the flag.

{{< centerimage src="/posts/unauthentik/unauthentik.png" alt="Dodge car" maxWidth="600px" >}}
