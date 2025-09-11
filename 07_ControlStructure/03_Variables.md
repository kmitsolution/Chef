In Chef, **variables** help you store values (like strings, arrays, hashes) that you can reuse throughout your **recipe**, **template**, or **resource definitions**. Since Chef recipes are written in Ruby, you define and use variables just like in Ruby.

---

## ✅ 1. **Basic Variable Usage**

```ruby
app_user = 'deploy'
app_dir = "/home/#{app_user}/myapp"

directory app_dir do
  owner app_user
  mode '0755'
  recursive true
end
```

---

## ✅ 2. **Variables with Arrays / Loops**

```ruby
packages = ['nginx', 'curl', 'git']

packages.each do |pkg|
  package pkg do
    action :install
  end
end
```

---

## ✅ 3. **Variables with Hashes**

```ruby
users = {
  'alice' => { 'uid' => 1001 },
  'bob'   => { 'uid' => 1002 }
}

users.each do |name, info|
  user name do
    uid info['uid']
    manage_home true
  end
end
```

---

## ✅ 4. **Node Attributes as Variables**

You can assign node attributes to variables for cleaner code:

```ruby
env = node.chef_environment
platform = node['platform']

log "Running in #{env} on #{platform}" do
  level :info
end
```

---

## ✅ 5. **Variables in Templates**

You can pass variables into a template:

### Recipe:

```ruby
template '/etc/myapp/config.ini' do
  source 'config.ini.erb'
  variables(
    app_name: 'myapp',
    port: 8080
  )
end
```

### Template: `templates/default/config.ini.erb`

```erb
[app]
name = <%= @app_name %>
port = <%= @port %>
```

> Use `@variable_name` inside `.erb` files.

---

## ✅ 6. **Using Conditional Variables**

```ruby
if node['platform'] == 'ubuntu'
  apache_pkg = 'apache2'
else
  apache_pkg = 'httpd'
end

package apache_pkg do
  action :install
end
```

---

## ✅ 7. **Default Attributes as Variables (from `attributes/default.rb`)**

In `attributes/default.rb`:

```ruby
default['myapp']['port'] = 8080
```

In your recipe:

```ruby
port = node['myapp']['port']

template '/etc/myapp/config.ini' do
  variables(port: port)
end
```

---

## 📝 Summary Table

| Variable Type          | Example Use Case                        |
| ---------------------- | --------------------------------------- |
| String / Int / Bool    | Store static values (`'nginx'`, `true`) |
| Arrays                 | Loop over items (packages, users)       |
| Hashes                 | Store grouped data                      |
| Node attributes        | Get system/env values (`node['ip']`)    |
| ERB Template variables | Pass values to config files             |
| Conditional vars       | Set values based on OS/env              |

---

