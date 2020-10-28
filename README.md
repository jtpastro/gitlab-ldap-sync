
# gitlab-ldap-sync

Python project to sync LDAP/Active Directory Groups into GitLab.

The script will create the missing LDAP groups into gitlab and sync membership of all LDAP groups matching a given preffix and/or from a parent group. 

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See deployment for notes on how to deploy the project on a live system.

### Prerequisites

```
Python        : 3.4.9
pip3          : 8.1.2
python-gitlab : 1.6.0
python-ldap   : 3.1.0
```

### Installing

You could either install requirements system wide or use virtual environment / conda, choose your poison.

To get this up and running you just need to do the following :

* Clone the repo
```bash
git clone https://github.com/jtpastro/gitlab-ldap-sync.git
```
* Install requirements
```bash
pip3 install -r ./gitlab-ldap-sync/requirements.txt
```
* Edit config.json with you values
```bash
EDITOR ./gitlab-ldap-sync/config.json
```
* Start the script and enjoy your sync users and groups being synced
```bash
cd ./gitlab-ldap-sync && ./gitlab-ldap-sync.py
```

You should get something like this :
```bash
Initializing gitlab-ldap-sync.
Done.
Updating logger configuration
Done.
Connecting to GitLab
Done.
Connecting to LDAP
Done.
Getting all groups from GitLab.
Done.
Getting all groups from LDAP.
Done.
Groups currently in GitLab : < G1 >, < G2 >, < G3 >, < G4 >, < G5 >, < P1 >, < P2 >, < P3 >
Groups currently in LDAP : < G1 >, < G2 >, < G3 >, < G4 >, < G5 >, < G6 >, < G7 > 
Syncing Groups from LDAP.
Working on group <Group Display Name> ...
|- Group already exist in GitLab, skiping creation.
|- Working on group's members.
|  |- User <User Display Name> already in gitlab group, skipping.
|  |- User <User Display Name> already in gitlab group, skipping.
[...]
|- Done.
[...]
Done
```

You could add the script in a cron to run it periodically.
## Deployment

How to configure config.json
```json5
{
  "log": "/tmp/gitlab-ldap-sync.log",                 // Where to store the log file. If not set, will log to stdout
  "log_level": "INFO",                                // The log level
  "gitlab": {
    "api": "https://gitlab.example.com",              // Url of your GitLab 
    "ssl_verify": true,                               // Verify SSL certificate when using HTTPs (true, false, path to own CA bundle)
    "private_token": "xxxxxxxxxxxxxxxxxxxx",          // Token generated in GitLab for an user with admin access
    "oauth_token": "",
    "ldap_provider":"",                               // Name of your LDAP provider in gitlab.yml
    "create_user": true,                              // Should the script create the user in GitLab
    "group_visibility": "private",                    // Set visibility level of new group (private, internal, public)
    "add_description": true                           // Add description from your LDAP as group description
  },
  "ldap": {
    "url": "ldaps://ldap.loc",                        // URL to your ldap / active directory
    "users_base_dn": "ou=users,dc=example,dc=com",    // Where we should look for users
    "groups_base_dn": "ou=groups,dc=example,dc=com",  // Where we should look for groups
    "user_filter": "(memberOf=CN=GitUsers)",          // What filter we should use on user selection
    "bind_dn": "login",                               // User to log with
    "password": "password",                           // Password of the user
    "parent_group": "",                               // Synced groups must be a member of parent_group
    "group_prefix": ""                                // The prefix of the groups that should be synced
  }
}
```
You should use ```private_token``` or ```oauth_token``` but not both. Check [the gitlab documentation](https://docs.gitlab.com/ce/user/profile/personal_access_tokens.html#creating-a-personal-access-token) for how to generate the personal access token.

```create_user``` If set to true, the script will create the users in gitlab and add them in the corresponding groups. Be aware that gitlab will send a mail to every new users created.

## Built With

* [Python](https://www.python.org/)
* [python-ldap](https://www.python-ldap.org/en/latest/)
* [python-gitlab](https://python-gitlab.readthedocs.io/en/stable/)

## Initial work

* **Jean-François GUILLAUME (Jeff MrBear)** -  [MrBE4R](https://github.com/MrBE4R)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details
