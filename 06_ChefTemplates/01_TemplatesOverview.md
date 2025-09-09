### 🔧 What Are Templates in Chef?

Templates in Chef are **files written in Embedded Ruby (ERB)** that allow you to create dynamic content. They are used in **cookbooks** to generate configuration files (like `nginx.conf`, `httpd.conf`, etc.) with values populated at runtime based on node attributes, data bags, or other dynamic data.

---

###  How Templates Work

1. **Stored in a Cookbook**: Templates are kept in the `templates/` directory of a Chef cookbook.

   * Example: `cookbooks/my_cookbook/templates/default/my_template.conf.erb`

2. **Rendered via the `template` Resource**: The `template` resource in a recipe tells Chef to process the ERB file and write it to a destination on the node.

   ```ruby
   template '/etc/myapp/config.conf' do
     source 'my_template.conf.erb'
     variables({
       setting1: 'value1',
       setting2: node['myapp']['setting2']
     })
     owner 'root'
     group 'root'
     mode '0644'
   end
   ```

3. **Dynamic Variables**: You can pass variables using the `variables` hash and access them in the ERB file like so:

   ```erb
   setting1 = <%= @setting1 %>
   setting2 = <%= @setting2 %>
   ```

---

###  Template File Naming

* Files are named with the `.erb` extension.
* You can have platform-specific templates by using file specificity:

  ```
  templates/
  ├── default/
  │   └── config.conf.erb
  ├── ubuntu-20.04/
  │   └── config.conf.erb
  ```

Chef will pick the most specific file based on the node's platform and version.

---

###  Use Cases

* Generating configuration files (`nginx.conf`, `httpd.conf`, `my.cnf`)
* Creating systemd unit files
* Writing environment-specific configuration
* Managing complex files that change depending on node attributes

---

###  Security Tip

Avoid hardcoding sensitive data in templates. Use **Chef Vault** or **encrypted data bags** and access secrets securely.

---

### Example ERB Template

**`my_template.conf.erb`:**

```erb
# Configuration file for MyApp
port = <%= @port %>
environment = <%= node.chef_environment %>
log_level = <%= @log_level %>
```

**Corresponding recipe:**

```ruby
template '/etc/myapp/myapp.conf' do
  source 'my_template.conf.erb'
  variables({
    port: node['myapp']['port'],
    log_level: node['myapp']['log_level']
  })
end
```

---

###  Testing Templates

You can test your ERB templates outside of Chef using Ruby:

```bash
erb my_template.conf.erb
```

Or use tools like **ChefSpec** to test template rendering.

---


