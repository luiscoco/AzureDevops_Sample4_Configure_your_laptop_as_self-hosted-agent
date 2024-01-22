# AzureDevops: How to configure your laptop as self-hosted-agent to run Azure DevOps jobs

We are going to explain how to set up **Jobs** that **run on machines** that you manage (your **latptop** or a **Cloud Virtual Machine**), where you **install agent software supplied by Microsoft**

For this purpose **Azure DevOps** provides a feature called "**self-hosted agents**" for exactly this purpose 

**Self-hosted agents** allow you to **run build and deployment jobs** directly **on machines you manage**, rather than on **Microsoft-hosted agents** 

This approach gives you **more control over the environment** in which your jobs run, including the ability to **customize** the **operating system**, **installed software**, and **hardware specifications**

Here’s a step-by-step guide to setting up **self-hosted agents** in **Azure DevOps**:

## 1. Prepare the Machine

Ensure your target machine meets the requirements:

Supported operating system (Windows, macOS, Linux)

Sufficient hardware resources (CPU, memory, disk space)

Network connectivity to Azure DevOps services

Required software installed (e.g., development tools, SDKs)

## 2. Create a Personal Access Token (PAT) in Azure DevOps

You’ll need a **PAT** to authenticate the agent with Azure DevOps:

We **Sign in** to your Azure DevOps organization

We go to **User settings -> Personal access tokens**

![image](https://github.com/luiscoco/AzureDevops_Sample4_Configure_your_laptop_as_self-hosted-agent/assets/32194879/b39a75f7-6e53-4cfa-9456-7abea6d19df6)

We click **New Token**

![image](https://github.com/luiscoco/AzureDevops_Sample4_Configure_your_laptop_as_self-hosted-agent/assets/32194879/2b9e192e-758a-4c32-9c38-b2ce7e95bfd5)

We give it a descriptive **name**, choose an **expiration**, and select the **Agent Pools (read, manage)** scope under Agent Pools

We press on **Show all scopes** link and we select **Agent Pools (read, manage)**

![image](https://github.com/luiscoco/AzureDevops_Sample4_Configure_your_laptop_as_self-hosted-agent/assets/32194879/afe2560a-fc00-4006-8012-3c47232861e0)

Then we click **Create** and copy the token

![image](https://github.com/luiscoco/AzureDevops_Sample4_Configure_your_laptop_as_self-hosted-agent/assets/32194879/5b1e8ff8-d64e-4e02-bbed-1e5af547c3b9)

Store it securely; you won’t be able to see it again

![image](https://github.com/luiscoco/AzureDevops_Sample4_Configure_your_laptop_as_self-hosted-agent/assets/32194879/50e81727-96f9-41e6-9e24-2eb8cc34be84)

## 3. Download and Configure the Agent

In Azure DevOps, we go to **Project settings > Agent pools**

We choose the **default pool** or we create a new one

![image](https://github.com/luiscoco/AzureDevops_Sample4_Configure_your_laptop_as_self-hosted-agent/assets/32194879/a7319621-1ef8-4d6e-bc8d-1944c9aaaa9c)

We click **New agent** and we select the operating system of your target machine

![image](https://github.com/luiscoco/AzureDevops_Sample4_Configure_your_laptop_as_self-hosted-agent/assets/32194879/f856b1f7-92ed-4cf9-91a9-6800afeb5a3f)

We have to follow the instructions to **download the agent package**



On your target machine, extract the agent package and open a command line in the extracted directory.

Run the configuration script (e.g., ./config.sh on Linux/macOS, .\config.cmd on Windows), and when prompted, enter the URL of your Azure DevOps organization and the PAT you created earlier.

## 4. Run the Agent

After configuring, start the agent. On Linux/macOS, use ./svc.sh install and ./svc.sh start. On Windows, run .\svc.cmd install followed by .\svc.cmd start.

The agent should now be online and appear in the Azure DevOps agent pool.

## 5. Use the Self-hosted Agent in Your Pipelines

When defining a pipeline (YAML or through the UI), specify the pool where your self-hosted agent resides:

```yaml
pool:
  name: MyPool # Replace with your agent pool name
```

Your jobs will now run on your self-hosted agent.

## 6. Verify the Job successfully run

![image](https://github.com/luiscoco/AzureDevops_Sample4_Configure_your_laptop_as_self-hosted-agent/assets/32194879/a4ec4763-95c9-4781-b021-170c52f3cd30)

![image](https://github.com/luiscoco/AzureDevops_Sample4_Configure_your_laptop_as_self-hosted-agent/assets/32194879/c959f291-9b39-4cbe-815c-bf6ede5c2892)

![image](https://github.com/luiscoco/AzureDevops_Sample4_Configure_your_laptop_as_self-hosted-agent/assets/32194879/09aed653-904c-45d0-b1b1-92b06dc19aa5)
