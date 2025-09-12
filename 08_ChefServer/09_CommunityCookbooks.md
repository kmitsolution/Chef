

* You manage cookbooks and policies locally on your **workstation**
* Upload them to the **Chef Server**
* Nodes pull configurations from the Chef Server via **`chef-client`**

---

## 🎯 Goal: Deploy `apache2` via your custom cookbook using **Chef Server**

We’ll go step-by-step from scratch.

---

# ✅ Step-by-Step: Chef Server Workflow for Your `my_webserver` Cookbook

---

## 🔹 1. Prerequisites

Make sure you have:

* ✅ **Chef Server URL**
* ✅ **Chef Workstation** installed on your local machine
* ✅ `.chef/` directory with:

  * `knife.rb`
  * `user.pem` (API client key)
  * `organization-validator.pem` (optional)

Example `knife.rb`:

```ruby
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                'your_user'
client_key               "#{current_dir}/your_user.pem"
chef_server_url          'https://chef-server.example.com/organizations/your_org'
cookbook_path            ["#{current_dir}/../cookbooks"]
```

---

## 🔹 2. Create a Chef repo

If you don’t have a repo yet:

```bash
chef generate repo chef-repo
cd chef-repo
```

✅ Inside, you should now have:

```
chef-repo/
├── cookbooks/
├── roles/
├── environments/
└── .chef/knife.rb
```

Place your `knife.rb` and keys in `.chef/`.

---

## 🔹 3. Generate Your Custom Cookbook

In `chef-repo/cookbooks/`:

```bash
cd cookbooks
chef generate cookbook my_webserver
```

---

## 🔹 4. Add `apache2` dependency

### In `my_webserver/metadata.rb`:

```ruby
name 'my_webserver'
version '0.1.0'
depends 'apache2'
```

---

## 🔹 5. Add `apache2` to your recipe

### In `my_webserver/recipes/default.rb`:

```ruby
apt_update 'update'

apache2_install 'apache2' do
  action :install
end


#

# Simple homepage
file '/var/www/html/index.html' do
  content '<h1>Hello from Apache2 via Chef Server (custom resource)</h1>'
  mode '0644'
  owner 'root'
  group 'root'
end

```

---

## 🔹 6. Download `apache2` cookbook

Use **Berkshelf** to pull the community cookbook:

### Create or edit `my_webserver/Berksfile`:

```ruby
source 'https://supermarket.chef.io'

metadata
cookbook 'apache2', '~> 9.3.7'
```

Then run:

```bash
cd my_webserver
berks install
berks vendor ../../cookbooks
```

This vendors the `apache2` and its dependencies into your `cookbooks/` folder.

---

## 🔹 7. Upload cookbooks to Chef Server

From the root of your Chef repo:

```bash
knife cookbook upload apache2
knife cookbook upload my_webserver
```

---

## 🔹 8. Bootstrap or register a node (if needed)

To bootstrap a new node over SSH:

```bash
 knife bootstrap 172.31.47.230 -x root -i /root/.ssh/id_rsa --sudo --node-name webserver1 --node-ssl-verify-mode none

```

> This installs `chef-client` on the node and registers it with your Chef Server.

If the node is already set up, make sure it has:

* `client.pem`
* `client.rb` pointing to your Chef Server

---

## 🔹 9. Set node run list

```bash
knife node run_list add webserver1 'recipe[my_webserver]'
```

---

## 🔹 10. Run Chef on the node

SSH into your node:

```bash
ssh ubuntu@<NODE_IP>
sudo chef-client
```

This will:

* Pull the `my_webserver` cookbook
* Run the `apache2::default` recipe
* Serve your custom homepage

---

## ✅ Test

On the node:

```bash
curl http://localhost
```

You should see:

```html
<h1>Hello from Apache via Chef Server!</h1>
```

---

## 📦 Directory Structure Summary

```
chef-repo/
├── .chef/
│   ├── knife.rb
│   ├── your_user.pem
├── cookbooks/
│   ├── apache2/       ← from Berkshelf
│   └── my_webserver/
│       ├── recipes/
│       ├── metadata.rb
│       └── Berksfile
```

---

## 🔁 Run Again

Any time you make changes:

```bash
berks install
berks vendor ../../cookbooks
knife cookbook upload my_webserver
knife ssh 'name:mynode1' 'sudo chef-client' --manual-list --ssh-user ubuntu --identity-file ~/.ssh/id_rsa
```

## SSl error Fix
```
🔧 Add this to ~/.berkshelf/config.json:
{
  "ssl": {
    "verify": false
  }
}
```

## Error to upload the cook book
```
cd ~/chef-repo/cookbooks/my_webserver
berks install
berks upload
```
