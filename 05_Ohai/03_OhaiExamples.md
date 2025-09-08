##  Use Case:

You want to:

* Install a different package depending on **total memory**
* Tune performance settings depending on the **number of CPU cores**

---

## Cookbook: `system_tuning`

### Folder Structure

```
system_tuning/
├── metadata.rb
└── recipes/
    └── default.rb
```

---

## 🧾 1. `metadata.rb`

```ruby
name 'system_tuning'
maintainer 'Your Name'
version '0.1.0'
description 'Tuning system based on memory and CPU'
supports 'ubuntu'
supports 'centos'
```

---

##  2. `recipes/default.rb`

```ruby
# Get memory in KB and convert to MB
total_memory_kb = node['memory']['total'].to_i
total_memory_mb = total_memory_kb / 1024

# Get total number of CPUs
cpu_cores = node['cpu']['total'].to_i

log "Total memory: #{total_memory_mb} MB"
log "CPU cores: #{cpu_cores}"

# Conditional logic based on memory
if total_memory_mb >= 4096
  log 'System has >= 4GB RAM. Installing full-featured package.'
  
  package 'example-full' do
    action :install
  end
else
  log 'System has less than 4GB RAM. Installing lightweight package.'
  
  package 'example-lite' do
    action :install
  end
end

# Tweak performance settings based on CPU count
template '/etc/sysctl.d/99-performance.conf' do
  source 'performance.conf.erb'
  variables(cpu_cores: cpu_cores)
  notifies :run, 'execute[reload-sysctl]', :immediately
end

execute 'reload-sysctl' do
  command 'sysctl -p /etc/sysctl.d/99-performance.conf'
  action :nothing
end
```

---

## 📝 Template: `templates/default/performance.conf.erb`

```erb
# Performance tuning settings
# Adjusted based on <%= @cpu_cores %> CPU cores

kernel.sched_min_granularity_ns = <%= @cpu_cores * 10000000 %>
kernel.sched_wakeup_granularity_ns = <%= @cpu_cores * 15000000 %>
```

> This dynamically adjusts kernel scheduler parameters based on CPU count.

---

##  What Happens When You Run `chef-client`?

1. Ohai collects memory and CPU info.
2. The recipe reads:

   * `node['memory']['total']`
   * `node['cpu']['total']`
3. It decides:

   * Which package to install (`example-full` or `example-lite`)
   * How to generate the performance tuning config file
4. Applies system tuning using `sysctl`

---

## Summary

| Ohai Attribute            | Usage                                     |
| ------------------------- | ----------------------------------------- |
| `node['memory']['total']` | Conditional logic for installing packages |
| `node['cpu']['total']`    | Generate dynamic config file              |

---


