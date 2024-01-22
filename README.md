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

## 3. Download, Configure and Run the Agent

In Azure DevOps, we go to **Project settings > Agent pools**

We choose the **default pool** or we create a new one

![image](https://github.com/luiscoco/AzureDevops_Sample4_Configure_your_laptop_as_self-hosted-agent/assets/32194879/a7319621-1ef8-4d6e-bc8d-1944c9aaaa9c)

We click **New agent** and we select the operating system of your target machine

![image](https://github.com/luiscoco/AzureDevops_Sample4_Configure_your_laptop_as_self-hosted-agent/assets/32194879/f856b1f7-92ed-4cf9-91a9-6800afeb5a3f)

We have to follow the instructions to **download the agent package**, **configure** and **run** it

![image](https://github.com/luiscoco/AzureDevops_Sample4_Configure_your_laptop_as_self-hosted-agent/assets/32194879/d87a5a6e-12e6-4b1d-bf3d-1f9f3e317f78)

![image](https://github.com/luiscoco/AzureDevops_Sample4_Configure_your_laptop_as_self-hosted-agent/assets/32194879/054289e5-9342-4d81-8aaa-f1d11b072953)

We run PowerShell and we execute the following commands:

**Create the agent**

```
PS C:\> mkdir agent ; cd agent
```

We move the downloaded agent ZIP file from the **Downloads** folder to the in the **agent** directory

![image](https://github.com/luiscoco/AzureDevops_Sample4_Configure_your_laptop_as_self-hosted-agent/assets/32194879/48cb735a-acc7-4214-97e2-b6a3f70dce69)

We create the agent from the ZIP file

```
PS C:\agent> Add-Type -AssemblyName System.IO.Compression.FileSystem ; [System.IO.Compression.ZipFile]::ExtractToDirectory("$HOME\Downloads\vsts-agent-win-x64-3.232.3.zip", "$PWD")
```

![image](https://github.com/luiscoco/AzureDevops_Sample4_Configure_your_laptop_as_self-hosted-agent/assets/32194879/890cf46f-6ce0-437c-943e-902f3c7ace37)

**Configure the agentDetailed instructions**

```
PS C:\agent> .\config.cmd
```

![image](https://github.com/luiscoco/AzureDevops_Sample4_Configure_your_laptop_as_self-hosted-agent/assets/32194879/499eb45a-f5cf-49ce-9b59-f8242bc9d7ee)

**Run the agent interactively**

```
PS C:\agent> .\run.cmd
```

![image](https://github.com/luiscoco/AzureDevops_Sample4_Configure_your_laptop_as_self-hosted-agent/assets/32194879/aab84909-a9a3-44cd-894a-196cbb3d9c13)

## 4. Use the Self-hosted Agent in Your Pipelines

When defining a pipeline (YAML or through the UI), specify the pool where your self-hosted agent resides:

```yaml
pool:
  name: MyPool # Replace with your agent pool name
```

For example in our case we created a Default agent:

```
pool:
  name: 'Default'
```

In our case see the Azure DevOps Pipeline configuration yaml file:

```yaml
trigger:
- main

# Modificar aquí para usar un agente autohospedado
pool:
  name: 'Default' # Asegúrate de que este sea el nombre de tu grupo de agentes autohospedados

variables:
  solution: '**/*.sln'
  buildPlatform: 'Any CPU'
  buildConfiguration: 'Release'

steps:
- task: UseDotNet@2
  inputs:
    version: '8.x'
    packageType: 'sdk'

- task: DotNetCoreCLI@2
  inputs:
    command: 'restore'
    projects: '**/*.csproj'
    feedsToUse: 'select'

- task: DotNetCoreCLI@2
  inputs:
    command: 'build'
    projects: '**/*.csproj'
    arguments: '--configuration $(buildConfiguration)'

# Optional: Add steps for running tests here

- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    publishWebProjects: true
    arguments: '--configuration $(buildConfiguration) --output $(Build.ArtifactStagingDirectory)'
    zipAfterPublish: true

- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(Build.ArtifactStagingDirectory)'
    ArtifactName: 'drop'
    publishLocation: 'Container'
```

We create a New Pipeline

![image](https://github.com/luiscoco/AzureDevops_Sample4_Configure_your_laptop_as_self-hosted-agent/assets/32194879/9340902f-7d63-4d8c-84c3-52fb615fbb1c)

We input the above **yml file source code** to configure the pipeline and we **Run the pipeline**

![image](https://github.com/luiscoco/AzureDevops_Sample4_Configure_your_laptop_as_self-hosted-agent/assets/32194879/77d44e54-07bf-4614-bdcd-9434bb2ed2a1)

Our jobs will now run on my laptop, where we intalled and setup the **self-hosted agent**

## 5. Verify the Job successfully run

![image](https://github.com/luiscoco/AzureDevops_Sample4_Configure_your_laptop_as_self-hosted-agent/assets/32194879/a4ec4763-95c9-4781-b021-170c52f3cd30)

![image](https://github.com/luiscoco/AzureDevops_Sample4_Configure_your_laptop_as_self-hosted-agent/assets/32194879/c959f291-9b39-4cbe-815c-bf6ede5c2892)

![image](https://github.com/luiscoco/AzureDevops_Sample4_Configure_your_laptop_as_self-hosted-agent/assets/32194879/09aed653-904c-45d0-b1b1-92b06dc19aa5)
