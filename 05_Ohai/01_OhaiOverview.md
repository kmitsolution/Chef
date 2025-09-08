
## What Is **Ohai**?

* **Ohai** is Chef’s built-in **system discovery tool** that runs automatically at the **start of every `chef-client` run**. Its job is to **collect detailed system data** and provide that information to Chef as **automatic node attributes**.([docs.chef.io][1])

* It gathers a wide range of system information, including:

  * OS name and version
  * Network configuration (IP, hostnames)
  * Memory and disk usage
  * CPU and kernel details
  * Virtualization and cloud metadata (if applicable)([docs.chef.io][1], [docs-acceptance.chef.io][2])

These automatically available attributes let your cookbooks adapt to the environment they’re running on, such as installing different packages depending on whether the node is CentOS, Ubuntu, AWS, etc.

---

## Key Capabilities and Customization

* **Plugin Model**: Ohai operates with built-in detection plugins and allows developers to write **custom plugins** to gather additional data.([docs.chef.io][1])

* **Optional Plugins**: You can enable additional data collection by enabling optional plugins such as `Lspci`, `Sysctl`, `Passwd`, `Sessions`, etc., via your `client.rb` configuration.([docs.chef.io][1])

* **Plugin Control**: You can also disable plugins you don’t need to reduce overhead or sensitive data gathering.([docs.chef.io][1])

* **Attribute Blocklisting**: If you want to omit certain automatic attributes from being saved to the Chef Server, you can use the `blocked_automatic_attributes` setting.([docs.chef.io][1])

* **Accessing Attribute Data**: To inspect the full set of data collected, you can run the `ohai` command directly on a system. It outputs all detected attributes in JSON format.([docs.chef.io][1], [docs-acceptance.chef.io][2])

---

## How It Fits in Chef's Workflow

Here’s a simplified flow of how Ohai integrates with Chef runs:

1. **Chef Client starts**
2. **Ohai runs** and gathers system state and metadata
3. Chef builds the node object (including these automatic attributes)
4. Cookbooks run, using `node[...]` attributes to make decisions (e.g., `node['platform']`)
5. Chef converges the system toward the defined desired state([docs.chef.io][3], [docs-acceptance.chef.io][4])

A Reddit user summed it up succinctly:

> "Ohai ... dumps every bit of software configuration info and operating system info into JSON output. It is used extensively behind the scenes for Chef."([Reddit][5])

---

## Summary: Ohai at a Glance

| **Feature**           | **Description**                                                              |
| --------------------- | ---------------------------------------------------------------------------- |
| Purpose               | System discovery tool for Chef (\`chef-client\`)                             |
| Collected Data        | OS, network, CPU, memory, disk, virtualization, cloud metadata, etc.         |
| Plugins               | Supports built-in, optional, and custom plugins                              |
| Configuration Options | Enable/disable plugins, configure hints, log levels, blocklist attributes    |
| Invocation            | Runs automatically with `chef-client`; can also be run standalone via `ohai` |
| Usage in Cookbooks    | Accessed via `node[...]`, informing resource choices and logic conditions    |

---


