In Chef, **notifications** allow resources to **communicate with each other** — specifically, to **trigger actions** like restarting a service **after a file or package changes**.

Chef supports two types of notifications:

---

## 🔄 1. **`notifies`** — Sending a Notification

Used when **one resource tells another to do something** (e.g., restart a service) after an action (like updating a config file).

**Syntax:**

```ruby
resource 'name' do
  ...
  notifies :action, 'resource[name]', :timing
end
```

* `:action` — the action to trigger (e.g., `:restart`, `:reload`)
* `resource[name]` — the target resource
* `:timing` — when to do it (`:immediately`, `:delayed`)

---

## 🔄 2. **`subscribes`** — Receiving a Notification

The **inverse** of `notifies`. This resource **watches another** and reacts when it changes.

**Syntax:**

```ruby
resource 'name' do
  ...
  subscribes :action, 'resource[name]', :timing
end
```

---

## 📘 Example: Restart Apache When Config Changes

### 🔧 Step 1: Manage config file

```ruby
template '/etc/apache2/sites-available/000-default.conf' do
  source 'webapp.conf.erb'
  notifies :restart, 'service[apache2]', :delayed
end
```

This says:

> “If this file changes, notify `service[apache2]` to restart after the run (`:delayed`).”

---

### 🔧 Step 2: Define the service

```ruby
service 'apache2' do
  action [:enable, :start]
end
```

---

## 🧭 How Timing Works

| Timing         | Meaning                                                          |
| -------------- | ---------------------------------------------------------------- |
| `:immediately` | Trigger the action **as soon as the notifying resource updates** |
| `:delayed`     | Wait until **after all other resources finish**, then trigger    |
| `:before`      | (Rare) Run **before** the notifying resource                     |

---

## 📥 Example: Using `subscribes`

```ruby
service 'apache2' do
  action [:enable, :start]
  subscribes :restart, 'template[/etc/apache2/sites-available/000-default.conf]', :delayed
end
```

Same outcome, but from the **receiving side**.

---

## 🔄 Combine Both

You can mix `notifies` and `subscribes` in complex cookbooks to create dependency chains.

---

## 🧪 Real-World Example: Update + Restart App

```ruby
remote_file '/opt/myapp/myapp.jar' do
  source 'https://example.com/myapp.jar'
  mode '0755'
  notifies :restart, 'service[myapp]', :immediately
end

service 'myapp' do
  action [:enable, :start]
end
```

> App is restarted only if the JAR file is updated.

---

## 📝 Summary

| Keyword        | Role            | Triggers                      |
| -------------- | --------------- | ----------------------------- |
| `notifies`     | **Sender**      | "I changed, notify this one"  |
| `subscribes`   | **Receiver**    | "I act when this one changes" |
| `:immediately` | Runs right away |                               |
| `:delayed`     | Waits until end | Most common, default timing   |

---

## ✅ Best Practices

* Use `:delayed` unless you must restart things immediately.
* Keep notifications **clear and minimal** — too many make the run order complex.
* Chain resources (like: file → config → service) to build reliable deployments.

---


