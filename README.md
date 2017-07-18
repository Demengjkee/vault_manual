# Vault Installation and usage guide

## Initial Setup

1. Install Vault server (e.g. with docker-compose) and download Vault binary file or use docker container for requests
2. Export `VAULT_ADDR` env variable (e.g. `http://127.0.0.1:8200` )
3. Use `vault init` command and save **unseal keys** and **Root token**
4. Unseal vault with `vault unseal <unseal_key>` command (buy default you'll need to run this command with any 3 keys of 5 generated)
5. Login to vault with `vault auth <root_token>` command

## Configure policies

6. Create policy `.hcl` file
</br> e.g.
```
path "secret/epam/*" {
        policy = "write"
}
```
7. Run `vault policy-write <policy_name> <file_name>.hcl`

## Configure github access

8. Run `vault auth-enable github`
9. Enable access for Org. `vault write auth/github/config organization=<org_name>`
10. Configure access for users/teams:
</br> `vault write auth/github/map/teams/<team_name> value=<policy_name>`
</br> `vault write auth/github/map/users/<username> value=<policy_name>`
11. Auth with github token `vault auth -method=github token=<github_token>`

## Writing, reading, deleting
* Write: `vault write <secret_path> <key>=<value>` (files can be written as `<key>=@<filepath>`)
* Read: `vault read <secret_path>`
* Read specific field (e.g. for file output redirect): `vault read -field <key> <secret_path>`
* Delete: `vault delete <secret_path>`

## Installing Vault with Docker compose:
1. Directories `./vault/file` and `./vault/configg` should exist
2. `./vault/file:/vault/file` stores secrets and should be backed up
3. `./vault/config/config.json` should be following:
```
{
  "backend": {"file": {"path": "/vault/file"}},
  "listener": {"tcp": {"address": "0.0.0.0:8200", "tls_disable": 1}},
  "default_lease_ttl": "168h",
  "max_lease_ttl": "720h"
}
```
