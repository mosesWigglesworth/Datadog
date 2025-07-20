---
# 📘 Chapter 2: Deploying the Datadog Agent

## 🚀 What Is It About?
This chapter teaches how to install and configure the Datadog Agent — a lightweight software that collects metrics, logs, traces, and more from your systems, and sends them to Datadog’s cloud platform for monitoring and visualization.

## 🧠 Key Concept:
A monitoring tool is only as effective as the data it collects. The Datadog Agent is how that data gets from your infrastructure into Datadog.

## 📦 What Is the Agent?
The Datadog Agent is a small process that:
  
      🔍 Collects system-level metrics (CPU, RAM, disk, network, etc.)
  
      📈 Monitors applications and services

      📤 Sends collected data securely to Datadog in the cloud

---
## 🗂️ Chapter Topics

The chapter is organized into 5 important parts:

1️⃣ Installing the Datadog Agent
2️⃣ Understanding Agent Components
3️⃣ Running the Agent as a Docker Container
4️⃣ Deployment Use Cases
5️⃣ Advanced Configuration Techniques

---
## #1 ⚙️ Technical Requirements
---

To follow hands-on examples, make sure you have:

🖥️ OS Requirements:

* ✅ Ubuntu 22.04 LTS is preferred
* ✅ Windows Server, macOS, Amazon Linux are also supported

🐧 Shell:

* Bash shell or compatible terminal

🔐 Access:

* 🧑‍💼 A Datadog account (with API & App keys)
* 🛠️ Admin/sudo access to the machine or environment

🐳 If Using Containers:

* Docker installed 🐋
* Or a Kubernetes cluster with kubectl configured


## #2 📥 Installing the Datadog Agent

The Agent can be deployed in two major ways based on your environment:

### 1️⃣ 🏠 Host-Level Installation
Used for physical servers, virtual machines, or cloud instances (EC2, GCP, Azure VMs).

✅ Best for:

* Monitoring infrastructure
* Collecting system metrics, logs, APM
* Easy installation with a single shell script

### 2️⃣ 🐳 Containerized Installation
Used when your apps run in Docker or Kubernetes.

✅ Best for:

* Microservices environments
* Auto-scaling containers
* Dynamic infrastructure

🧠 Tip: Use Autodiscovery so the Agent can auto-detect services based on labels/annotations.

#### 🚢 For Docker:
Run Agent container with environment variables like DD\_API\_KEY.

#### 🧱 For Kubernetes:
Deploy the Agent using Helm.

---

## #2 🧩 Runtime Configurations

📌 What is it?
Runtime configuration refers to how the Datadog Agent is deployed in different environments depending on where and how your applications are running.

🧠 Key Idea:
The Agent can run:

* Directly on the host machine (e.g., VM, EC2)
* Inside a container
* Sidecar-style alongside your app in a containerized setup

Each setup collects:
✅ Infrastructure metrics (CPU, RAM, disk, etc.)
✅ Application metrics (APM, logs, custom metrics)

---
### 1️⃣ Mode 1: 🏠 As a Service on the Host (Monitoring Host-Based Apps)
---

#### 📘 Description:
This is the most traditional method. You install the Datadog Agent as a service directly on the same machine (bare metal or VM) where your applications are running.

#### 🖼️ Visual Representation (as per the image):

* Both the Monitoring Agent and Application Processes are running on the same host.
* The agent observes the system and the apps via local APIs, logs, sockets, or process data.

🧰 How It Works:
🟩 Host Machine
├── 🧠 Monitoring Agent (Datadog Agent service)
└── ⚙️ Application Process (e.g., NGINX, Node.js app, PostgreSQL)

#### 🔄 The Agent collects:

* System metrics via native integrations (e.g., psutil, sysfs)
* Application-specific metrics (via integrations like nginx.d/conf.yaml)
* APM traces via language-specific tracers (e.g., ddtrace for Python)

#### 📦 Example Use Cases:
✅ Legacy VMs or cloud instances without containers
✅ Small services not requiring orchestration
✅ Developers running apps locally on laptops or test servers

#### 🔐 Security Note:
Make sure the Agent is not exposed externally. It's internal to the host and communicates securely with Datadog backend.

---
### 2️⃣ 🐳 As a Service on the Docker Host Monitoring Application Containers
---

#### 📘 Description:
In this model, your applications run as Docker containers 🧱, but the Datadog Agent runs directly on the host machine (outside of Docker).

#### 📦 Architecture:

Docker Host
├── 🧠 Monitoring Agent (runs directly on host)
└── 🐳 App Containers (isolated services managed by Docker)

#### 🖼️ Visual Summary (Figure 2.2):

* The Agent has access to Docker Engine APIs and container stats
* Monitors health, resource usage, logs, and custom metrics of each container

#### 🔧 Requirements:

* Docker installed on the host
* Datadog Agent installed on host with Docker socket access (usually /var/run/docker.sock)

#### 🧠 Example Use Case:
✅ You want to keep monitoring outside your containers
✅ You need full host observability + container insights
✅ Lower container resource overhead

#### 🔐 Security Tip:
Grant the Agent read-only access to Docker socket only.

---
### 3️⃣ 📦 As a Container on the Docker Host Monitoring Application Containers
---

#### 📘 Description:
In this model, both the Datadog Agent and the application run as containers. This is often used in fully containerized setups or CI/CD pipelines.

#### 📦 Architecture:

Docker Host
├── 🧱 App Container(s)
└── 📦 Monitoring Agent Container

#### 🖼️ Visual Summary (Figure 2.3):

* The Agent runs as a container side-by-side with the app containers
* It accesses container metadata, logs, APM, and metrics via shared Docker resources

#### 🧠 Example Use Case:
✅ Cloud-native microservice deployments
✅ CI/CD setups where infrastructure is containerized
✅ Portability across environments

#### 🔐 Security Consideration:
Isolate sensitive config files and restrict network exposure with container firewalls or Docker network policies.

---
### 📊 Summary Table
---

| Mode 🧩          | Agent Location | App Location      | Best For 🏆                |
| ---------------- | -------------- | ----------------- | -------------------------- |
| 🏠 Host Service  | Host machine   | VM/Host           | Traditional apps           |
| 🐳 Host + Docker | Host machine   | Docker containers | Lightweight hybrid         |
| 📦 Containerized | Container      | Containers        | Fully containerized setups |

---
### 📝 Real-World Tip
---
In Kubernetes, the most common pattern is to run the Agent as a DaemonSet on each node. This is similar to "Agent as a container" but orchestrated at scale. More on that soon!

---

### 🌐 Network Connectivity for the Datadog Agent

#### 🧠 What’s Happening Here?

Once the Datadog Agent is installed (in any of the 3 modes: host, container on host, or containerized), it needs to send all collected data to the Datadog SaaS backend over the internet.

The agent → uses HTTPS → to send metrics, traces, and logs → to Datadog Cloud 🔒☁️

#### 🖼️ Illustrated Flow (Figure 2.4):

Company Network
└── 🧠 Datadog Agent
   ⬇
🌐 Internet
   ⬇
☁️ Datadog SaaS Backend


#### 📶 Network Requirements

🧱 Inside a corporate network, ensure:

✅ Outbound internet traffic is allowed
✅ The following ports are open:

* 443 (HTTPS) → used to send data to Datadog backend
* 8125/UDP → if using DogStatsD for custom metrics (internally)

#### 🚫 If your company uses strict firewalls:

Ask your IT/security team to whitelist these Datadog endpoints:

* [https://api.datadoghq.com](https://api.datadoghq.com)
* [https://agent-intake.datadoghq.com](https://agent-intake.datadoghq.com)
* [https://logs.datadoghq.com](https://logs.datadoghq.com)
* And other subdomains based on your region (e.g., datadoghq.eu, datadoghq.com.au)

#### 📎 Optional: Use a proxy
If direct internet access is not permitted, you can configure the Agent to use an outbound proxy:

/etc/datadog-agent/datadog.yaml

proxy:
http: [http://proxy.company.com:8080](http://proxy.company.com:8080)
https: [https://proxy.company.com:8080](https://proxy.company.com:8080)

---
#### 🧩 Choosing Agent Deployment Type
---

Depending on how your application is architected, your Agent deployment style should follow:

##### 🔹 Microservices App → 🧱 Deploy Agent as a container

* Works well with Kubernetes, Docker Swarm
* Easier upgrades and scaling
* Agent can run as a DaemonSet or sidecar

##### 🔹 Monolith/VM-based App → 🏠 Deploy Agent directly on host

* Stable for legacy or on-prem workloads
* Full access to host-level telemetry
* Fewer moving parts

##### 📘 Maintenance Tips:

* ✅ Deploying Agent as a microservice simplifies version upgrades
* ✅ Agents auto-update if installed via Helm or container orchestrators
* ❌ Avoid running multiple agents on the same host — one is enough for all containers

---
#### 📦 Summary: Runtime + Network Model
---

🔄 Regardless of deployment style, the Agent must reach the internet to send data ☁️
🔒 Secure network setup is critical for real-time observability
🧱 Container-based agent deployment = more flexible and cloud-native
🏠 Host-based agent = simple and stable for legacy systems

---

## #4 🪜 Steps for Installing the Datadog Agent

### 🧠 What’s This Section About?
This section introduces how to install the Datadog Agent on various platforms, using Ubuntu as the example. It focuses specifically on how to access the installation command from the Datadog web dashboard.

### 📌 Supported Platforms

* Linux (Ubuntu, CentOS, RHEL)
* Windows
* Kubernetes
* Docker

🧠 The actual steps are similar across all these platforms, with minor differences handled by the platform-specific script.

### 📌Requirements Before Installation

* ✅ A Datadog account
* 🔐 An API key (used to associate data with your account)

### 📌 Installation Version

* Uses Datadog Agent v7 for the example
* Older versions (like Agent 6) are still supported

### 📌 How to Access the Installation Command
To install the Agent on Linux, you’ll need a script provided in the Datadog UI.

### 🖱️ Step 1 : Navigate to Integration Menu

1️⃣ Open the Datadog Dashboard
2️⃣ Click the Integrations menu (🔗 icon in top nav)
3️⃣ From the dropdown, select Agent

### 🖱️ Step 2 : Select OS & Copy Install Command

🖼️ What the Image Shows:

* A left sidebar lists supported OS platforms (e.g. macOS, Debian, Ubuntu, Fedora, etc.)
* The "Ubuntu" option is selected
* A generated one-liner install command is visible
* A section header: “Agent 7 Installation Instructions” is displayed

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
💻 Example Command for Ubuntu (Updated for 2025):

DD\_AGENT\_MAJOR\_VERSION=7 DD\_API\_KEY=\<YOUR\_API\_KEY> bash -c "\$(curl -L [https://s3.amazonaws.com/dd-agent/scripts/install\_script.sh](https://s3.amazonaws.com/dd-agent/scripts/install_script.sh))"

🔍 Breakdown of the Command:

* DD\_AGENT\_MAJOR\_VERSION=7 → Ensures you install Agent version 7
* DD\_API\_KEY → Authenticates your Agent with your Datadog org
* curl … install\_script.sh → Downloads and runs the installation script

### 🖱️ Step 3 : Execute the Command in the Terminal

1️⃣ The script installs APT packages required for the Datadog Agent
2️⃣ Prompts for sudo password (if needed)
3️⃣ Automatically starts the Agent (unless you use DD\_INSTALL\_ONLY=true)

### 🖱️ Step 4 : Verify Agent Installation

Once installed, the Agent tries to connect to:

☁️ Datadog SaaS backend → via HTTPS
📍 If successful, the host appears in:

* Infrastructure → Host Map
* Infrastructure → Infrastructure List

🎯 This is your quick “heartbeat” check — if the host appears, the Agent is active and reporting.

### 🖱️ Step 5 : Troubleshoot if any issue occur

If anything goes wrong during install, check this:

📄 Log file name: dd\_agent.log
📂 Location: The current directory where install\_script.sh was run
📁 Full logs also available in: /var/log/datadog/

🧠 Tip: These logs help diagnose misconfigurations or missing dependencies.

---

## #5 🧩 Agent Components — Overview

The Datadog Agent is a background service made up of modular components that each handle a specific task related to collecting, processing, and forwarding metrics to Datadog.

---

### 🧠 Where It Runs

🖥️ On Ubuntu systems, the Agent runs as a service called:

```bash
datadog-agent
```

You can manage this service using typical Linux service commands (e.g., systemctl status/start/stop).

---

### 📂 Key Configuration Locations

1. 📁 `/etc/datadog-agent/`
   → Root directory for all configuration files.

2. 🛠️ `/etc/datadog-agent/datadog.yaml`
   → Main config file.
   Any changes here require restarting the datadog-agent service.

3. 📦 `/etc/datadog-agent/conf.d/`
   → Contains integration-specific configs (used for monitoring things like Redis, NGINX, etc.)

---

### 🔧 Core Components of the Agent

These are always active by default:

#### 1. 📊 Collector

🔁 Collects system metrics every 15 seconds.
📝 Frequency may vary based on metric type.

#### 2. 📡 Forwarder

🚚 Sends metrics (collected by the Collector) to the Datadog backend over HTTPS.
🧠 Buffers the data in memory for efficiency.

#### 3. 🐶 DogStatsD

📍 UDP server listening on port 8125.
⚙️ Collects and aggregates custom metrics using the StatsD protocol.
✅ Popular for integrating app-level metrics.

---

### ⚙️ Optional Agent Components (enabled via datadog.yaml)

#### 🧬 APM Agent

💡 Enables Distributed Tracing (APM).
💼 Must be turned on if you want to collect traces.

#### 🧠 Process Agent

🛠️ Tracks running processes on the host.
🔍 Useful for infrastructure monitoring and debugging.

#### 🖥️ Agent UI

👁️ Lightweight web UI running on the host.
🧭 Allows local inspection of configuration and logs.
📎 Often disabled on cloud hosts, more useful for local machines (macOS, Windows).

---

### 📌 Summary Table:

| Component     | Default? | Purpose                              | Port / Protocol  |
| ------------- | -------- | ------------------------------------ | ---------------- |
| Collector     | ✅ Yes    | Gathers system metrics               | —                |
| Forwarder     | ✅ Yes    | Sends data to Datadog                | HTTPS            |
| DogStatsD     | ✅ Yes    | Collects custom app metrics          | UDP 8125         |
| APM Agent     | ❌ No     | Enables tracing features             | HTTP/Unix socket |
| Process Agent | ❌ No     | Tracks live system processes         | —                |
| Agent UI      | ❌ No     | Visual web interface for diagnostics | Localhost only   |

---


## #6 🐳 Agent as a Container

Instead of running directly on the host, the Datadog Agent can be installed as a Docker container.

---

### 🔧 Sample Docker Command:

```bash
DOCKER_CONTENT_TRUST=1 docker run -d --name dd-agent \
  -v /var/run/docker.sock:/var/run/docker.sock:ro \
  -v /proc/:/host/proc/:ro \
  -v /sys/fs/cgroup/:/host/sys/fs/cgroup:ro \
  -e DD_API_KEY=<DATADOG_API_KEY> \
  datadog/agent:7
```

### 📌 Explanation of Flags:

* `--name dd-agent`: Names the container `dd-agent`.
* `-v /var/run/docker.sock`: Grants the container access to Docker runtime.
* `-v /proc` and `-v /sys/fs/cgroup`: Grants access to host system-level metrics.
* `-e DD_API_KEY`: Passes the Datadog API key as an environment variable.
* `datadog/agent:7`: Uses the Datadog Agent version 7 Docker image.

✅ Docker image is pulled from Docker Hub.

---

### ⚙️ Configuration in Docker Mode

* Unlike host-mode, no access to `datadog.yaml`.
* Configuration is done via environment variables instead.

  * Example: `DD_API_KEY=<your-key-here>`

🧬 In Kubernetes, the Agent is deployed as a **DaemonSet**, ensuring every node runs an agent.

📁 The API key (`DD_API_KEY`) is usually passed securely as a Kubernetes Secret.

🔧 `kubectl` is used to deploy this configuration.

---

## #7 🚀 Deploying the Agent – Use Cases

## #8 ✅ Use Case 1: All on the Hosts

Both the **Datadog Agent** and **application software** are installed directly on the same host.

🖥️ Host types:

* Bare-metal machines
* Virtual machines

### 🔁 Agent Behavior

* Runs on each host
* Reports metrics/events to the Datadog backend

---

#### ⚙️ Real-World Deployment Strategies

#### 🧱 Prebaked Image (e.g., AWS AMI)

* Agent is included in a base machine image.
* Useful for auto-scaling or bootstrapping instances.

#### ⚙️ Configuration Tools (e.g., Ansible)

* Automates installation on many machines in parallel.
* Improves scalability and reduces manual setup effort.

---

### ☁️ In Public Cloud Environments

* Prebaked images are preferred (due to autoscaling).
* Ansible is viable where dynamic provisioning isn't used.
* Ansible can also generate images or automate config.

---

## #9 ✅ Use Case 2: 🐳 Agent on the Host Monitoring Containers

Deploying Datadog Agent on the host to monitor running containers involves enabling the Docker integration and using the Agent’s Autodiscovery feature to detect containers dynamically.

---

## 📌 Why This Method?

* The **Datadog Agent** runs directly on the host.
* Containers are ephemeral, requiring Autodiscovery to track and monitor them.
* Ideal for setups where your applications are containerized, but you don't want to run the Datadog Agent inside each container.

---

### 🛠️ Step-by-Step: Setting up Docker Monitoring (Updated for 2025)

---

#### 🎯 1️⃣ Enable Docker Integration in Datadog UI

📍 Navigation Path:
`Datadog → Integrations → Marketplace`

➡️ In the search bar, type:

```plaintext
Docker
```

🔍 Once located:

* ✅ If not installed, click **Install** (now done automatically in many SaaS environments).
* ⚙️ Click **Configure** to view setup instructions.

---

#### 🛠️ 2️⃣ Docker Integration Configuration (On Host)

Inside the **Configuration** tab of the Docker integration:

##### a. ✅ Ensure Agent is Installed

Your host should already have the Datadog Agent installed and running.

```bash
sudo systemctl status datadog-agent
```

---

##### b. 👤 Add User to Docker Group

Let the Agent access Docker's socket:

```bash
sudo usermod -aG docker dd-agent
```

* `dd-agent` is the user the Agent runs as.
* Ensures it can monitor Docker without root-level access.

---

##### c. ⚙️ Configure Docker Autodiscovery

Edit/create the following file:

```bash
/etc/datadog-agent/conf.d/docker.d/docker_daemon.yaml
```

Example content:

```yaml
init_config:

instances:
  - url: "unix:///var/run/docker.sock"
    new_tag_names: true
```

**Key Notes:**

* url: Connects via Docker’s Unix socket.
* new\_tag\_names: Ensures modern tagging format.

---

##### d. 🔄 Restart Datadog Agent

Apply changes:

```bash
sudo systemctl restart datadog-agent
```

---

##### e. 📋 Verify via Agent Status

Check if the Docker integration is running:

```bash
datadog-agent status
```

Look under the **Docker** section in the status output.

---

#### 📊 3️⃣ Review Docker Metrics

Navigate in Datadog UI:

`Infrastructure → Containers`

You’ll see:

* Container CPU & Memory Usage
* Container Lifecycle Events
* Network I/O Metrics
* Docker-specific metrics (via Docker API)

---

### 🚨 Billing Reminder:

Monitoring containers via the Docker Integration counts as infrastructure hosts in Datadog billing.

---

### 💡 Advanced (Optional):

* Use Autodiscovery Templates for dynamic monitoring.
* Enable Docker logs collection via:

```bash
logs_enabled: true
```

In `datadog.yaml` or Docker daemon-specific config.

---



## 🛠️ Step-by-Step Setup

### 1️⃣ **Install Docker Integration in Datadog UI**

#### 📌 Navigation:

**Datadog UI → Integrations → Integrations**

#### 🔍 Action:

* Search for:
  **`docker`**

#### 📥 Installation:

* Click **Install** under Docker.

> ✅ After installation, Docker appears under **Installed**.

!\[Docker Integration Search]\(attachment from your shared images)

---

### 2️⃣ **Configure Docker Integration**

#### 📋 In Datadog UI:

* After installation, click **Configure** on Docker integration.
* You’ll see setup instructions & configurations like this:

```yaml
init_config:
instances:
  - url: "unix://var/run/docker.sock"
    new_tag_names: true
```

---

### 3️⃣ **On the Docker Host:**

#### 👤 Add Agent User to Docker Group

Allow Datadog Agent permissions to monitor Docker.

```bash
usermod -a -G docker dd-agent
```

---

#### 📄 Configure Agent to Connect to Docker

* File Path:
  `/etc/datadog-agent/conf.d/docker.d/conf.yaml.example`

* Steps:

  1. Copy and rename:

     ```bash
     cp /etc/datadog-agent/conf.d/docker.d/conf.yaml.example /etc/datadog-agent/conf.d/docker.d/conf.yaml
     ```
  2. Edit the `conf.yaml`:

     ```yaml
     init_config:
     instances:
       - url: "unix://var/run/docker.sock"
         new_tag_names: true
     ```

---

#### 🔄 Restart the Agent

```bash
service datadog-agent restart
```

---

### 4️⃣ **Validate in Datadog UI**

#### 📊 Containers Dashboard:

* Navigate to:
  **Datadog UI → Infrastructure → Containers**

* Search using:
  **Filter by → host:<hostname>**

> 📈 You’ll see Docker containers automatically detected.

---

#### 📈 Metrics Explorer:

* Go to:
  **Metrics → Metrics Explorer**

* Search:
  **`docker.containers.running`**

* Apply filters (by hostname) to check container-specific metrics.

> All Docker metrics (starting with **docker.\*** ) are now available.

---

### 📋 Example Docker Metrics (Available in Metrics Tab):

| Metric                            | Description                           |
| --------------------------------- | ------------------------------------- |
| docker.container.open\_fds        | Open file descriptors in container    |
| docker.container.size\_rootfs     | Size of container’s root filesystem   |
| docker.containers.running         | Number of running containers          |
| docker.container.size\_rw         | Size of read/write layer of container |
| docker.container.size\_rootfs.max | Max size sampled of root filesystem   |

---

## 🛑 Optional: Enable Monitoring for Other Services (like Redis)

For apps like Redis running inside containers:

* Copy:
  `/etc/datadog-agent/conf.d/redisdb.d/conf.yaml.example`
* Rename to:
  `/etc/datadog-agent/conf.d/redisdb.d/conf.yaml`
* Restart Agent.

Now, you can monitor **redis.\*** metrics similarly.

---

## ✅ Verification via CLI (Optional)

```bash
sudo datadog-agent status
```

* Look for:

  * **docker \[OK]**
  * **Instance ID**
  * **Configuration Source**
  * **Metrics Samples**

---

## 📊 Visual Confirmation Example:

* **Containers dashboard:** View running containers, resource usage.
* **Metrics Explorer:** View Docker metrics (prefixed with `docker.*`).
* **Docker Integration page:** List of metrics being collected.

---

# 🚀 Summary Workflow:

> **Install Docker Integration ➔ Configure Agent ➔ Restart Agent ➔ Verify in UI / CLI**

🎯 **Docker containers monitored by Datadog Agent on host.**

---

# 🐳 Agent Running as a Container

### 💡 Why Run Agent as a Container?

* In **containerized environments** (e.g., Docker, Kubernetes), running the **Datadog Agent itself as a container** is recommended.
* **Benefit:**
  You don’t need to install the Agent directly on the host OS — saving time and effort for machine-level setup.
* This method gives **operational flexibility**:

  * No changes to the host machine image.
  * Easily deploy or upgrade Agents via container orchestration.

> 📍 Example:
> In Kubernetes clusters, the Datadog Agent is typically deployed as a **DaemonSet**.

---

## #8 ⚙️ Advanced Agent Configuration

Configuration is handled in the **datadog.yaml** file.

### 🛠️ Common Advanced Config Options:

| Option                                                   | Purpose                                                                                              |
| -------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| **api\_key**                                             | 🔑 Mandatory key to push data to Datadog account.                                                    |
| **proxy**                                                | 🌐 If traffic must go through a proxy, configure here (`http`, `https`, or `socks5`).                |
| **hostname**                                             | 🏷️ Used for reporting under a specific hostname. Defaults to auto-detected value.                   |
| **tags**                                                 | 🏷️ Key tool for organizing/segmenting metrics. Supports multiple key-value pairs.                   |
| **collect\_ec2\_tags**                                   | 🏷️ Collect AWS EC2 tags (useful if Agent runs on EC2 instances).                                    |
| **config\_providers**                                    | 🛠️ Enables Autodiscovery based on Docker image labels. Required for monitoring specific containers. |
| **docker\_labels\_as\_tags** & **docker\_env\_as\_tags** | 🏷️ Extract Docker labels and environment variables as monitoring tags.                              |

> 📍 In Kubernetes, similar configurations use:

* `kubernetes_pod_labels_as_tags`
* `kubernetes_pod_annotations_as_tags`

---

## #8 🏆 Best Practices

1. **Embed Agent in Machine Image (if host install):**
   When installing the Agent on hosts directly, include it in your base machine image (e.g., AMI in AWS) to simplify deployments.

2. **Use Automation:**
   Automate configuration updates using **Ansible Playbooks**, CI/CD pipelines, or other automation tools to avoid manual errors.

3. **Choose Correct Deployment Style:**

   * If monitoring **containers**, prefer deploying the Agent as a **container**.
   * For static workloads, Agent on host may still be valid.

4. **Proper Tagging Strategy:**
   Ensure proper tagging across Docker and Kubernetes environments to enable clear, searchable, and filtered monitoring.

---

## #9 📊 Summary

* Datadog Agent can run:

  * Directly on host
  * Inside Docker container
  * As DaemonSet in Kubernetes
* The **UI dashboards** provide immediate insights, but deeper configurations and fine-tuned tagging improve long-term observability.
* APIs are useful for automation, but configuring through UI or config files remains essential for core setup.

---

### ✅ Key Takeaway:

> **Run the Agent as a container when monitoring containerized workloads. Use tags strategically. Automate and standardize deployments for scale.**

---
