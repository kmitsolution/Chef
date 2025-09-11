### 👑 What Are **Roles** in Chef Server?

In Chef, a **role** is a way to define a **server’s function** within your infrastructure — like `webserver`, `database`, `load_balancer`, etc. Roles group together **recipes**, **attributes**, and even **run lists**, so you can easily assign a complete configuration to a node with just one line.

---

## 🧠 Why Use Roles?

Roles help you:

* DRY (Don't Repeat Yourself) your run\_lists across multiple nodes
* Centralize configurations for a specific purpose
* Easily update multiple nodes by changing one role

---

## 🧱 Role Structure (JSON/YAML/Ruby DSL)

Example `webserver` role (JSON format):

```json
{
  "name": "webserver",
  "description": "Web Server Role",
  "run_list": [
    "recipe[apache]",
    "recipe[security::firewall]"
  ],
  "override_attributes": {
    "apache": {
      "listen_ports": [ "80", "443" ]
    }
  }
}
```

You can also create roles using Ruby DSL:

```ruby
name "webserver"
description "Web Server Role"
run_list "recipe[workstation::apache]", "recipe[test::new]"
override_attributes({
  "apache" => {
    "listen_ports" => [ "80", "443" ]
  }
})

```

---

## 🚀 How to Create and Use Roles

### ✅ Step 1: Generate a Role (from your workstation)

```bash
cd chef-repo/roles
```

Create a role manually (e.g., `webserver.json`) or generate one:

```bash
chef generate role webserver
```

Or write your own JSON or Ruby DSL role file.

---

### ✅ Step 2: Upload the Role to Chef Server

```bash
knife role from file roles/webserver.rb
```

or

```bash
knife role from file roles/webserver.json
```

---

### ✅ Step 3: Assign Role to a Node

```bash
knife node run_list add webserver1 "role[webserver]"
```

This will:

* Replace or append to the node’s run\_list
* Cause the node to run all recipes defined in the role

---

### ✅ Step 4: Run Chef on the Node

```bash
ssh ubuntu@webserver1
sudo chef-client
```

---

## 📌 Pro Tips

| Tip                                                                           | Description                                          |
| ----------------------------------------------------------------------------- | ---------------------------------------------------- |
| 💡 Use roles for high-level structure                                         | e.g., `webserver`, `dbserver`, `monitoring-agent`    |
| 🧪 Keep environment-specific values in **environments**, not roles            | Roles describe *what*, environments describe *where* |
| 🔁 Change a role → all nodes using it pick up changes on next chef-client run | Great for fleet updates!                             |
| 🔐 Avoid hardcoding secrets in roles                                          | Use encrypted data bags instead                      |

---

## ✅ Summary

* **Roles** define the purpose and configuration of a node
* They group **recipes**, **attributes**, and a **run list**
* Created as `.json` or `.rb`, uploaded with `knife`
* Applied to nodes using `knife node run_list add <node> "role[rolename]"`

---

