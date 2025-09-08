##  `ohai` Command Overview

The `ohai` command gathers and displays **automatic system attributes** in **JSON** format. You can:

* Run `ohai` alone to dump **all attributes**
* Or use `ohai key`, `ohai key/subkey`, etc., to **target specific data**

---

##  Example Commands You Mentioned

| Command             | Description                                              |
| ------------------- | -------------------------------------------------------- |
| `ohai`              | Dumps **all available system attributes** (very verbose) |
| `ohai ipaddress`    | Shows the node’s primary **IP address**                  |
| `ohai hostname`     | Shows the system’s **hostname**                          |
| `ohai memory`       | Dumps detailed memory info (total, free, used, etc.)     |
| `ohai memory/total` | Shows **total memory** as a string (e.g., `"7923568kB"`) |
| `ohai cpu/0/mhz`    | Shows the **clock speed (MHz)** of CPU core 0            |

---

## 🔍 More Useful Ohai Attributes

Here are some other commonly used Ohai attribute queries:

| Attribute          | Command                   | Description                                         |
| ------------------ | ------------------------- | --------------------------------------------------- |
| OS                 | `ohai platform`           | OS family (e.g., `ubuntu`, `centos`)                |
| OS Version         | `ohai platform_version`   | Exact OS version (e.g., `20.04`)                    |
| CPU Cores          | `ohai cpu/total`          | Number of CPU cores                                 |
| Architecture       | `ohai kernel/machine`     | CPU architecture (e.g., `x86_64`)                   |
| Uptime             | `ohai uptime`             | System uptime                                       |
| Network Interfaces | `ohai network/interfaces` | Shows all network devices and their IPs             |
| Disk Info          | `ohai block_device`       | Disk and partition details                          |
| Cloud Metadata     | `ohai cloud`              | Cloud provider info (e.g., AWS, Azure), if detected |
| FQDN               | `ohai fqdn`               | Fully qualified domain name                         |

---

##  Pro Tips

* You can use these values in your **Chef recipes** via the `node` object:

  ```ruby
  log "This node's IP address is #{node['ipaddress']}"
  ```

* Use `jq` (if installed) to filter Ohai output:

  ```bash
  ohai | jq '.memory.total'
  ```

* To see **everything available** in Ohai:

  ```bash
  ohai | less
  ```

---

## Warning

* Ohai attributes can differ between platforms (e.g., Ubuntu vs. CentOS), so always test your attribute usage on **all target systems**.
* Keys like `ipaddress` may not return expected values on cloud systems with multiple interfaces. In such cases, use `node['network']['interfaces']`.

---


