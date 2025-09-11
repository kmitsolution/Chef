Certainly! Chef recipes are written in Ruby, so you can use regular Ruby conditional statements like `if`, `case`, `unless`, etc., to control the flow of your configuration.

Below are common ways to use **conditional statements in Chef recipes**, with practical examples.

---

## ✅ 1. `if` Statement Example

### Use case: Install different packages based on OS

```ruby
if node['platform'] == 'ubuntu'
  package 'apache2'
elsif node['platform'] == 'centos'
  package 'httpd'
else
  log 'Unsupported platform'
end
```

---

## ✅ 2. `unless` Statement

### Use case: Only create a file if it doesn't exist

```ruby
unless File.exist?('/etc/myconfig.conf')
  file '/etc/myconfig.conf' do
    content 'default config'
    mode '0644'
  end
end
```

---

## ✅ 3. Inline Conditional in Resources

### Use case: Conditionally run a resource

```ruby
package 'telnet' do
  action :install
  only_if { node['environment'] == 'development' }
end

```

---

## ✅ 4. Based on `node.chef_environment`

### Use case: Different behavior based on Chef environment

```ruby
if node.chef_environment == 'production'
  log 'This is a production node'
else
log "This node is in the '#{node.chef_environment}' environment"
end
```

---

## ✅ 5. Using `case` Statements

```ruby
case node['platform_family']
when 'debian'
  package 'apache2'
when 'rhel'
  package 'httpd'
else
  log 'Unknown platform family'
end
```

---
