# Gravt Ansible

## Introduction

This repo is used to configure Virtual Machine for different roles.

## What is Ansible

[Introduction to Ansible](https://www.youtube.com/watch?v=1id6ERvfozo)

**Tool to automate IT Task:**

- Automate manual task you would do on server
- Execute task from your own machine (agentless, only need to connect ssh first)
- Configure/Install/Deploy steps in a single YAML file

**How it works?**

- Run an Ansible Playbook
- Assign a Ansible Role to a host

**Glossary:**

- Ansible Module = one small specific task (i.e: Install nginx, copy file,...)
- Ansible Playbooks: sequences of play/configuration
- Configuration: List of tasks for a specific host
- Task: description + module with arguments
- Host attritube defined where it should run + remote_user attribute define who is running it
- Ansible Inventory: all the machines involved in task execution (grouping of host)
- Ansible Vault: keep sensitive data in encrypted files
- Ansible Role: Reusable subset of plays that accomplish a certain goal
- Ansible Handlers: task that only triggers when an other task request it using the `notify` attribute. Used to run task only when something change on the machine.

**What is AWX?**

Is a UI dashboard that centralize all stored automation task and anybody in the team can run.

## Repo Overview

### 1. Scavenge Tools

This tool should already upload on the node via the EventStore playbook.

- [Offline scavenge instruction](./docs/offline-scavenge-instructions.md)
- Scavenger Executable can be found here `./files/offline-scavenger.linux-x64.tar.gz`

### 2. EventStore Playbooks

- [EventStore Playbook file](./eventstore_nodes.yml)
- Variabled + vault files for staging and production can be found at: `./group_vars`

This playbook setup EventStore on Linux machine:

- Install dependencies (curl, python,...)
- Create linux group and user
- Upload + Trust CA certificate file from the Ansible Vault
- Upload Scavenge Tool
- Config DataDog integration
- Assign the `EventStore` and `Datadog` role

Most of the heavy lifting is done in the role assignment. The [eventstore role created by Yohan](https://github.com/gsoft-inc/ansible-role-eventstore) is what is using is using most of the variables:

- Install EventStore
- Create and install node certificate
- **Upload the eventstore config file: `./templates/eventstore.conf.j2`**
- Start EventStore
- Change AdminPassword

**Where is the inventory?**

 I think the inventory was in AWX.

### 3. Build Agents Playbooks

**It is likely not used anymore.**

- [Build Agent Playbook file](./build_agents.yml)
- Variabled + vault files can be found at: `./group_vars`

This playbook setup Azure DevOps build or deployment agent on Linux machine:

- Install dependencies (mongo, apt,...)
- Install dependencies via role (kubernetes-helm, dotnet-core, nodejs,...)
- Install Azure DevOps agent via the role gsoft.azure_devops_agent

**Where is the inventory?**

 I think the inventory was in AWX.

### 4. Requirement

List all roles dependency used in the playbook: gsoft.eventstore, datadog.datadog, geerlingguy.heml,...

## How to run scripts

We once had AWX pods in the shared aks cluster but not anymore. Now not sure how to run those scripts.

## Questions + Potential cleanup

- Do we still need the build_agents or was this replace with the ci cluser?
- Is it idempotent to run?
- Mostly seems targeted for the initial setup
- No playbook available for certificate rotation (which would be useful)
- If we remove the build playbook we could probably delete a lots of requirements
- Does Ansibles Vaults contains expired secrets?
- Where is the Host Inventory? Was it really in AWX?
- Idea: We could merge the code here in a common ShareGate.Gravt.Infra project
- We should close this hbranch that remove build agent: <https://dev.azure.com/sharegate/Sharegate.Gravt/_git/ansible?version=GBfeature/SRE-38_Gravt_VM_Cleanup>
