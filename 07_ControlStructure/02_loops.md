Chef supports **loops** inside recipes using plain Ruby syntax — typically `each`, `for`, or even `times`, because Chef recipes are Ruby DSL.

Loops in Chef are useful for:

* Creating multiple users
* Installing multiple packages
* Creating multiple config files
* Managing repetitive resources

---

## ✅ Common Loop Types in Chef

---

### 🔁 1. `each` Loop

#### 📦 Example: Install multiple packages

```ruby
%w[git vim curl].each do |pkg|
  package pkg do
    action :install
  end
end
```

---

### 👤 Example: Create multiple users

```ruby
users = ['alice', 'bob', 'charlie']

users.each do |u|
  user u do
    manage_home true
    shell '/bin/bash'
  end
end
```

---

### 📝 Example: Create multiple files

```ruby
['file1.txt', 'file2.txt', 'file3.txt'].each do |filename|
  file "/tmp/#{filename}" do
    content "This is #{filename}"
  end
end
```

---

### 🔁 2. `for` Loop

Rarely used, but works:

```ruby
for i in 1..3 do
  file "/tmp/file#{i}.txt" do
    content "This is file #{i}"
  end
end
```

---

### 🔁 3. `.times` Loop

```ruby
3.times do |i|
  log "This is loop iteration #{i}" do
    level :info
  end
end
```

---

## 🔄 Loop with Hashes

```ruby
packages = {
  'nginx' => '1.18.0',
  'curl' => '7.68.0'
}

packages.each do |pkg, version|
  package pkg do
    version version
    action :install
  end
end
```

---

## 💡 Loop Over Data Bags

If you have users in a data bag:

```ruby
search(:users, '*:*').each do |user_data|
  user user_data['id'] do
    uid user_data['uid']
    shell user_data['shell']
  end
end
```

---

## 🔥 Advanced: Loop + Template Files

```ruby
%w[dev staging prod].each do |env|
  template "/etc/myapp/config_#{env}.yml" do
    source "config.yml.erb"
    variables(env: env)
  end
end
```

---

## ✅ Summary Table

| Loop Type   | Example Use Case               |
| ----------- | ------------------------------ |
| `each`      | Install packages, create users |
| `for`       | Simple numbered iteration      |
| `times`     | Fixed number of repetitions    |
| Hash loops  | Install packages with versions |
| Search loop | Loop through data bag or nodes |

---


