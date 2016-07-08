node-deploy
===========

Ansible role to deploy a Node.js app to Plesk

Use [ansible-nvm](https://github.com/Wtower/ansible-nvm) role to ensure node is installed. 

Required variables 
------------------

(passed as extra variables):

- `project`: **Node project name**

Optional variables
------------------

(passed as extra variables):


Required Configuration variables 
--------------------------------

(better to keep in configuration file as shown in examples):

- `conf`: A dictionary to help organize variables, containing:
- `conf.domain_name`: Domain name to be used in Apache conf. Omit to skip configure Apache in Plesk.
- `conf.dev_path`: The local path of the project, eg ~/workspace/python/myproject.
- `conf.host_path`: The remote path, where the wsgi index script lies, eg /var/www/vhosts/subscription/domain.

Optional Configuration variables
--------------------------------

- `conf.node_version`: The node.js version of the app, default: `4.4.7`.
- `conf.node_port`: The node.js server port, default: `3000`.
- `conf.start_script`: The node.js start script, default: `npm start`.

Other default variables
-----------------------

- `ansible_user_id`: The user id with access to mysql and mysql database.

Playbook examples
-----------------

See the folder `examples/`:

- `node_deploy.yml`: playbook example
- `node_apps.yml`: configuration example

Task description
----------------

1. Check that local project path in configuration is valid and has package.json

   Checks that there is a valid local path specified in `node_apps.yml` and a `package.json` exists.
   Avoids deploying wrong files.

2. Check if gulp file exists

   So that to execute next step.

3. Execute gulp

   Execute `gulp --production` locally.

4. Create directory

   Creates a `project` directory in target to deploy files.

5. Enable Apache proxy

   Makes sure that `mod_proxy` is enabled.
   If the configuration changes, causes Apache to restart in the end (notifies handler).

6. Configure Apache

   Configure Apache to proxy to server using the provided template file.
   If the configuration changes, causes Plesk to reconfigure the domain in the end (notifies handler).
   If the configuration changes, causes Apache to restart in the end (notifies handler).

7. Sync files

   Syncs files to target directory.

   :warning: Make sure that you have synced back any user uploaded files with manual `rsync`. 

8. Set node path

    Auxiliary task which sets a variable with the nvm node path executables.

9. Upgrade npm

   Upgrades global npm.

10. Install npm requirements

    Run `npm install`.

11. Set node start script suffix

    Auxiliary task which sets a variable with the proper path if `npm start` is the startup script.
    If a `node script.js` is provided then it is omitted.

12. Configure service

   Configure an init service to execute node.js using the provided template file.
   If the configuration changes, causes service to restart (notifies handler).
