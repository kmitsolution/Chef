The `include_recipe` method in Chef is used **within a recipe** to **include and execute another recipe** from the same cookbook or a different one.

---

##  What is `include_recipe`?

`include_recipe` is a **Ruby method provided by Chef DSL** that tells Chef to **evaluate another recipe** as part of the current Chef run.

---

## Syntax

```ruby
include_recipe 'cookbook_name::recipe_name'
```

###  Examples:

1. **Include the `default` recipe from the same cookbook:**

```ruby
include_recipe 'my_cookbook::default'
```

Or, if you're in the same cookbook:

```ruby
include_recipe '::default'
```

2. **Include a recipe from another cookbook:**

```ruby
include_recipe 'nginx::install'
```

---

##  What Does It Do?

When you call `include_recipe`:

* Chef **loads and evaluates** the specified recipe immediately.
* The included recipe’s resources become part of the **current run list**, in the current execution context.
* This is different from `chef-client -r`, which sets the **run-list for the node or that Chef run**.

---

##  Use Case Example

Suppose you have a cookbook `webserver` with two recipes:

### `webserver::install`

```ruby
package 'httpd'
```

### `webserver::configure`

```ruby
include_recipe 'webserver::install'

template '/etc/httpd/conf/httpd.conf' do
  source 'httpd.conf.erb'
end
```

In this case, `configure` recipe includes `install`, ensuring that Apache is installed before configuration is applied.

---

##  Best Practices

| Tip                                                       | Why                                               |
| --------------------------------------------------------- | ------------------------------------------------- |
| Avoid deeply nested `include_recipe` calls                | Makes the run list hard to trace/debug            |
| Prefer **roles or Policyfiles** for managing dependencies | Cleaner and more predictable                      |
| Keep recipes **modular**                                  | So they can be reused easily via `include_recipe` |

---

## How It Differs from Run List

| Feature     | `include_recipe`                  | Run list (`-r`, role, node)             |
| ----------- | --------------------------------- | --------------------------------------- |
| Scope       | Only current recipe               | Whole Chef run                          |
| Timing      | Evaluated during recipe execution | Evaluated before run starts             |
| Persistent? | No (not saved to node)            | Yes (if using roles or node's run list) |

---
Sure! Here's a complete **Chef cookbook example** that uses `include_recipe` to keep the logic clean and modular. This example sets up an **Apache web server**, using multiple recipes within a cookbook, and ties them together using `include_recipe`.

---

##  Cookbook Name: `apache_web`

This cookbook will have:

* A recipe to **install Apache**
* A recipe to **configure the homepage**
* A main `default` recipe that includes the others using `include_recipe`

---

## Folder Structure

```
apache_web/
├── metadata.rb
├── recipes/
│   ├── default.rb
│   ├── install.rb
│   └── configure.rb
├── files/
│   └── default/
│       └── index.html
├── README.md
```

---

## 🔧 1. `metadata.rb`

```ruby
name 'apache_web'
maintainer 'Your Name'
version '0.1.0'
description 'Installs and configures Apache web server'
supports 'centos'
supports 'ubuntu'
```

---

##  2. `recipes/install.rb`

```ruby
# Install Apache package
package 'httpd' do
  action :install
end

# Enable and start the Apache service
service 'httpd' do
  action [:enable, :start]
end
```

> On Debian/Ubuntu, replace `'httpd'` with `'apache2'` and the service name as well (or use platform-specific logic).

---

## 3. `recipes/configure.rb`

```ruby
# Deploy a static homepage
cookbook_file '/var/www/html/index.html' do
  source 'index.html'
  mode '0644'
end
```

---

##  4. `recipes/default.rb`

This is the **main entry point** of the cookbook.

```ruby
# Include install and configure recipes
include_recipe 'apache_web::install'
include_recipe 'apache_web::configure'
```

---

## 5. `files/default/index.html`

```html
<!DOCTYPE html>
<html>
<head>
  <title>Welcome</title>
</head>
<body>
  <h1>Hello from Chef-managed Apache!</h1>
</body>
</html>
```

---

## 6. How to Use It

### Upload the cookbook:

```bash
knife cookbook upload apache_web
```

### Add to a node’s run list:

```bash
knife node run_list add NODE_NAME "recipe[apache_web]"
```

### Run the Chef client on the node:

```bash
sudo chef-client
```

You should see:

* Apache installed
* Apache running
* Homepage deployed at `http://NODE_IP/`

---

## Why Use `include_recipe` Here?

| Benefit                | Example                                                                |
| ---------------------- | ---------------------------------------------------------------------- |
| Separation of concerns | `install.rb` handles only installation, `configure.rb` handles content |
| Reusability            | You can call `install` without `configure` in other cookbooks          |
| Clarity                | `default.rb` clearly shows the run flow                                |

---




