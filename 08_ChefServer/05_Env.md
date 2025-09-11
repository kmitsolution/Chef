## What Is an Environment in Chef?

A **Chef environment** represents a stage in your infrastructure lifecycle. For example:

* `development`
* `testing`
* `staging`
* `production`

### 🧠 Key Functions of Environments:

* Control **cookbook version constraints**
* Apply **environment-specific attributes**
* Group and manage **nodes** by their purpose or stage

---

## 🔗 **How Environments Are Related to Nodes**

Every **node belongs to one environment**. By default, it's `"_default"` unless changed.

When a node runs `chef-client`, it:

* Informs Chef Server of its current environment
* Chef Server ensures only the **permitted cookbook versions** for that environment are used
* **Attributes defined in the environment** override cookbook or role defaults

---

## 🔧 **How to Set Up an Environment**

### ✅ Step 1: Create an Environment File

Create a new environment file in your `environments/` folder.

Example: `environments/production.rb`

```ruby
name "production"
description "Production Environment"
cookbook_versions({
  "apache" => "= 3.1.0",
  "nginx" => ">= 2.7.0"
})
default_attributes({
  "apache" => {
    "listen_ports" => [ "80", "443" ]
  }
})
override_attributes({
  "nginx" => {
    "worker_processes" => 4
  }
})
```

### 📌 What's Happening:

* **Cookbook version constraints**: Ensures stability in prod
* **Attributes**: Custom environment-wide configs

---

### ✅ Step 2: Upload the Environment to Chef Server

```bash
knife environment from file environments/production.rb
```

You’ll see:

```
Updated Environment production!
```

---

### ✅ Step 3: Assign a Node to an Environment

```bash
knife node environment set webserver1 production
```

You can confirm:

```bash
knife node show webserver1
```

Look for:

```
Environment: production
```

---

### ✅ Step 4: Run Chef on the Node

```bash
ssh ubuntu@webserver1
sudo chef-client
```

Chef will:

* Apply cookbook version constraints for `production`
* Merge in the `default_attributes` and `override_attributes` from the environment

---

## 🔁 Example Use Case

### 🎯 Goal:

* You want version `1.0.0` of `mysql` cookbook in `dev`
* But only use stable version `0.8.0` in `production`

You'd create two environments:

```ruby
include_recipe 'monitoring::agent' if node.chef_environment == 'production'
```
#### `development.rb`

```ruby
cookbook_versions({ "mysql" => ">= 1.0.0" })
```

#### `production.rb`

```ruby
cookbook_versions({ "mysql" => "= 0.8.0" })
```

And assign nodes accordingly.

---

## 🧪 Bonus: View All Environments

```bash
knife environment list
```

Show details of one:

```bash
knife environment show production
```

---

## ✅ Summary

| Feature                  | Description                                 |
| ------------------------ | ------------------------------------------- |
| **Environment**          | Logical grouping of nodes (e.g. dev, prod)  |
| **Cookbook Constraints** | Control cookbook versions per environment   |
| **Custom Attributes**    | Define environment-specific config          |
| **Node Assignment**      | One node can belong to one environment only |
| **Command to Set**       | `knife node environment set NODE ENV_NAME`  |

---

Would you like a working example of multiple environments and how they change behavior on the same node?
