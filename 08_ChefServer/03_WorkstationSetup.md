
## 🧰 **Step-by-Step: Chef Workstation Setup and Node Bootstrapping**

---

## 🚀 **1. Install Chef Workstation on Your Local Machine**

Chef Workstation is where you:

* Write cookbooks
* Use `knife` to manage nodes
* Interact with the Chef Server

### 🔽 Download & Install (Ubuntu example):

```bash
wget https://packages.chef.io/files/stable/chef-workstation/23.3.1030/ubuntu/22.04/chef-workstation_23.3.1030-1_amd64.deb
sudo dpkg -i chef-workstation_23.3.1030-1_amd64.deb
```

> For Windows or macOS: [https://downloads.chef.io/chef-workstation](https://downloads.chef.io/chef-workstation)

---

## 📁 **2. Set Up a Chef Repo Directory**

```bash
chef generate repo chef-repo
cd chef-repo
```

This will create the directory structure:

```
chef-repo/
├── cookbooks/
├── data_bags/
├── environments/
└── .chef/            # Knife config goes here
```

---

## 🛠️ **3. Configure `knife.rb`**

Inside `.chef/knife.rb`, add:

```ruby
current_dir = File.dirname(__FILE__)
node_name                'myuser'  # your chef username
client_key               "#{current_dir}/myuser.pem"
chef_server_url          'https://chef-server.example.com/organizations/myorg'
ssl_verify_mode          :verify_none   # OR use knife ssl fetch
```

* Place your `myuser.pem` and `myorg-validator.pem` inside `.chef/`

---

## 🔐 **4. Fetch and Trust SSL Certs (if not disabling verify)**

```bash
knife ssl fetch
knife ssl check
```

---

## 🌐 **5. Bootstrap a Node (Remote Server)**

You’ll need:

* The **IP or hostname** of the node
* SSH access to the node (usually via `ubuntu` or `root` user)

### 🔧 Install Chef Client on Node and Connect It:(should have ssh connection with remote server)

```bash
knife cookbook upload workstation
knife bootstrap 172.31.36.70 -x root -i /root/.ssh/id_rsa --sudo --node-name webserver1 --run-list 'recipe[workstation::apache]' --node-ssl-verify-mode none
```

Explanation:

* `172.31.36.70` = IP of the node
* `-x ubuntu` = SSH username
* `-i` = your SSH private key
* `--node-name` = the node name in Chef
* `--run-list` = what to run (e.g., a role or recipe)

📝 This installs Chef Client on the node, registers it with Chef Server, and runs the `apache` recipe.

---

## 🔎 **6. Verify the Node Registered**

```bash
knife node list
knife node show webserver1
```

---

## 📦 **7. Upload a Cookbook (Optional)**

If you have a custom cookbook:

```bash
cd cookbooks/
chef generate cookbook nginx
cd ..
knife cookbook upload nginx
```

Then bootstrap using `--run-list 'recipe[nginx]'`.

---

## ✅ You're Done!

At this point:

* Chef Server is running
* Chef Workstation is connected
* Nodes can be bootstrapped and configured

---


