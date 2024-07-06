ansible-role-hvault-secrets-fetcher
========================

A role to fetch secrets from HashiCorp Vault and store them remotely.
Currently supports only the KV secrets engine (version 1 and version 2).

Requirements
------------

Ansible collections:

- `community.hashi_vault`

Python modules:

- `hvac`
- `requests>=2.29`

Role defaults
-------------

Available defaults are listed below:

```yml
hvault_secrets_fetcher_vault_address: http://127.0.0.1:8200
```

The address of the Vault server.

```yml
hvault_secrets_fetcher_vault_validate_certs: true
```

When using TLS, validate certificates. Leave this enabled in production environments.

```yml
hvault_secrets_fetcher_auth_method: approle
```

Supported auth methods: token, userpass and approle.

See `defaults/main.yml` for other defaults and examples

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```yml
- name: Retrieve static KV certificates from Vault for web app
  hosts: webserver

  roles:
    - role: chrisvanmeer.hvault_secrets_fetcher
      hvault_secrets_fetcher_vault_address: https://vault.domain.org:8200
      hvault_secrets_fetcher_auth_method: approle

      hvault_secrets_fetcher_auth_approle_role_id: 8c035e48-c065-ba00-7e29-73a387f5938b
      hvault_secrets_fetcher_auth_approle_secret_id: !vault |
        $ANSIBLE_VAULT;1.1;AES256
        31333138656334333062623162303539616634376661333530326138613439656362313037316631
        3666343965316236393230313939643963623736656464330a376161626639656137333135656236
        63616430333338613761373733613261346332636334383334613462356136636661366365363162
        6263636334666231350a623238376261613337653766353862363836346164383364356138313765
        37313238333065346263333961636161326639326161383739313431353537626630373535666436
        3938383230356338333836353933326330363833653766316530

      hvault_secrets_fetcher_list:
        - secrets_engine: kv2
          secrets_engine_mount_point: tlscerts
          secret_name: webapp.domain.org
          secret_version: 3
          secret_keys:
            - secret_key: certificate
              dst_path: /etc/pki/tls/certs/webapp.domain.org.pem
              dst_mode: '0644'
              dst_owner: root
              dst_group: root
              dst_replace_file: false
              dst_restore_selinux: true
            - secret_name: webapp.domain.org
              secret_key: private_key
              dst_path: /etc/pki/tls/private/webapp.domain.org.key
              dst_mode: '0600'
              dst_owner: root
              dst_group: root
              dst_replace_file: false
              dst_restore_selinux: true
```

License
-------

BSD

Author Information
------------------

- Chris van Meer <c.v.meer@atcomputing.nl>
