# FreePBX Ansible Playbook 
This playbook installs FreePBX v16 on the Ubuntu 22.04LTS or 24.04LTS minimal server.

## Prereqisites 
* Freshly installed Ubuntu 22.04LTS or 24.04LTS minimal
* Ubuntu 26.04 is not supported: FreePBX 16 needs PHP 7.4, which the ondrej/php PPA does not build for 26.04

## Usage
* Install the required collections: `ansible-galaxy collection install -r requirements.yml`
* Run the playbook. Example: `ansible-playbook -i 172.18.223.4, -u ubuntu cloudpbx-freepbx.yaml`
* It asks for:
  * customer name: the hostname becomes `<name>-pbx.cloudpbx.rs`
  * DB root password: press Enter to use `DB_ROOT_PASS` from the environment
  * provider label for Loki logs (default `nc`)
* Without prompts: `ansible-playbook -i 172.18.223.4, -u ubuntu -e customer_name=acme -e monitoring_provider=nc cloudpbx-freepbx.yaml` with `DB_ROOT_PASS` exported and `-e db_root_pass="$DB_ROOT_PASS"`

After playbook completes, you server IP should have FreePBX web service running

## What else gets configured
* FreePBX update settings: no automatic module upgrades and no update mails. FreePBX rebuilds the asterisk crontab from these settings on every reload, so do not edit those cron lines by hand. Change `fpbx_auto_module_updates`, `fpbx_auto_security_updates` or `fpbx_notification_email` in the playbook instead.
* Monitoring: prometheus-node-exporter (port 9100), mysqld_exporter (port 9104) and promtail shipping `/var/log/asterisk/full` to Loki
* Backups are not configured; set them up in the FreePBX Backup module after install
