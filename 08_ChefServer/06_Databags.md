
## 🗃️ What Are Data Bags?

**Data Bags** in Chef are **JSON-based key-value stores** used to store **global, sharable configuration data**. Unlike node attributes, data bags live on the **Chef Server** and are accessible from **any node or recipe**.

Think of them as Chef's version of a mini database — great for things like:

| Use Case                 | Example                      |
| ------------------------ | ---------------------------- |
| User accounts            | Store usernames, SSH keys    |
| App configs              | DB connection info, API keys |
| Environment configs      | Settings shared across roles |
| Secrets (via encryption) | Encrypted passwords, tokens  |

---

## 🛠️ How to Create and Use Data Bags

---

### ✅ Step 1: Create a Data Bag

```bash
knife data bag create <bag_name>
```

Example:

```bash
knife data bag create users
```

This creates a directory under `data_bags/users/` (if using Chef repo structure).

---

### ✅ Step 2: Create a Data Bag Item

Create a file: `data_bags/users/john.json`

```json
{
  "id": "john",
  "uid": 1001,
  "shell": "/bin/bash",
  "ssh_keys": [
    "ssh-rsa AAAAB3Nza... user@host"
  ]
}
```

Upload it:

```bash
knife data bag from file users john.json
```

---

### ✅ Step 3: Access Data Bag from a Recipe

```ruby
user_data = data_bag_item('users', 'john')

user 'john' do
  uid user_data['uid']
  shell user_data['shell']
  action :create
end

```

This recipe:

* Reads the `john` data bag item
* Creates a Linux user with matching `uid` and `shell`


---

---

## 🧪 Bonus: List/Show via Knife

```bash
knife data bag list
knife data bag show users
knife data bag show users john
```

---

## ✅ Summary

| Task                | Command Example                                     |
| ------------------- | --------------------------------------------------- |
| Create a data bag   | `knife data bag create users`                       |
| Create item (JSON)  | `knife data bag from file users john.json`          |
| Access in recipe    | `data_bag_item('users', 'john')`                    |

---

