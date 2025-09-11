---

## 🧾 What is `cookbook_file`?

The `cookbook_file` resource is used to:

> 🔁 Copy a **file from your cookbook's `files/` directory** to a **destination path** on the node.

---

## 🧱 Structure

```ruby
cookbook_file '/destination/path/on/node' do
  source 'source_file_name_in_cookbook'
  owner 'user'
  group 'group'
  mode '0644'
  action :create
end
```

---

## 📁 Folder Layout

Your cookbook should look like:

```
mycookbook/
├── recipes/
│   └── default.rb
├── files/
│   └── default/
│       └── myscript.sh
```

---

## 📦 Example: Copy a Shell Script to `/usr/local/bin`

### 1. Add a file to your cookbook:

Create this file:
`files/default/myscript.sh`

```bash
#!/bin/bash
echo "Hello from Chef script!"
```

Make it executable:

```bash
chmod +x files/default/myscript.sh
```

---

### 2. Add to your recipe:

**`recipes/default.rb`**

```ruby
cookbook_file '/usr/local/bin/myscript.sh' do
  source 'myscript.sh'
  owner 'root'
  group 'root'
  mode '0755'
  action :create
end
```

---

### 3. Run the recipe:

```bash
chef-client -z -o 'mycookbook::default'
```

✅ This will copy `myscript.sh` from the cookbook to `/usr/local/bin/` on the node and make it executable.

---

## 🔍 Attributes of `cookbook_file`

| Attribute  | Description                                                   |
| ---------- | ------------------------------------------------------------- |
| `path`     | Destination path on the node (required)                       |
| `source`   | Filename in `files/default/` (defaults to basename of `path`) |
| `owner`    | File owner on the node                                        |
| `group`    | File group on the node                                        |
| `mode`     | Permissions (e.g., `'0644'`, `'0755'`)                        |
| `cookbook` | If the file is from a **different** cookbook                  |
| `action`   | Usually `:create`, also `:create_if_missing`, `:delete`       |

---

## 🔁 Other Use Cases

### 🔹 Only create if missing

```ruby
cookbook_file '/etc/my.conf' do
  source 'my.conf'
  action :create_if_missing
end
```

### 🔹 Delete a file

```ruby
cookbook_file '/tmp/old.conf' do
  action :delete
end
```

### 🔹 Get file from another cookbook

```ruby
cookbook_file '/usr/bin/tool.sh' do
  source 'tool.sh'
  cookbook 'shared_utils'
end
```

---

## ✅ Summary

| Feature                  | Example                                  |
| ------------------------ | ---------------------------------------- |
| Copy static files        | `cookbook_file '/dest' do ... end`       |
| Source path              | From `files/default/` in your cookbook   |
| Supports `owner`, `mode` | Just like `file` or `template` resources |
| Best for                 | Scripts, config files, binaries          |

---

## 📌 When to Use `cookbook_file` vs Others

| Resource        | Use When You Need...                 |
| --------------- | ------------------------------------ |
| `cookbook_file` | Static file copied as-is             |
| `template`      | Dynamic file using ERB and variables |
| `remote_file`   | Download file from a remote URL      |
| `file`          | Create file with inline content      |

---

