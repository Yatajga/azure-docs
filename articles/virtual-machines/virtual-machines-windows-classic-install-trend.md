---
title: Install Trend Micro Deep Security on a VM | Microsoft Docs
description: This article describes how to install and configure Trend Micro security on a VM created with the Classic deployment model in Azure.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: ''
tags: azure-service-management

ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou

---
# How to install and configure Trend Micro Deep Security as a Service on a Windows VM
> [!IMPORTANT] 
> Azure has two different deployment models for creating and working with resources: [Resource Manager and Classic](../azure-resource-manager/resource-manager-deployment-model.md). This article covers using the Classic deployment model. Microsoft recommends that most new deployments use the Resource Manager model.

This article shows you how to install and configure Trend Micro Deep Security as a Service on a new or existing virtual machine (VM) running Windows Server. Deep Security as a Service includes anti-malware protection, a firewall, an intrusion prevention system, and integrity monitoring.

The client is installed as a security extension via the VM Agent. On a new virtual machine, you install the VM Agent along with the Deep Security Agent. On an existing virtual machine that doesn't have the VM Agent, you need to download and install it first. This article covers both situations.

If you have existing subscription from Trend Micro for an on-premises solution, you can use it to help protect your Azure virtual machines. If you're not a customer yet, you can sign up for a trial subscription. For more information about this solution, see the Trend Micro blog post [Microsoft Azure VM Agent Extension For Deep Security](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## Install the Deep Security Agent on a new VM
The [Azure classic portal](http://manage.windowsazure.com) lets you install the VM Agent and the Trend Micro security extension when you use the **From Gallery** option to create the virtual machine. If you're creating a single virtual machine, using the portal is an easy way to add protection from Trend Micro.

This **From Gallery** option opens a wizard that helps you set up the virtual machine. You use the last page of the wizard to install the VM Agent and Trend Micro security extension. For general instructions, see [Create a virtual machine running Windows in the Azure classic portal](virtual-machines-windows-classic-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). When you get to the last page of the wizard, do the following steps:

1. Under **VM Agent**, check **Install VM Agent**.
2. Under **Security Extensions**, check **Trend Micro Deep Security Agent**.
   
   ![Install the VM Agent and the Deep Security Agent](./media/virtual-machines-windows-classic-install-trend/InstallVMAgentandTrend.png)
3. Click the check mark to create the virtual machine.

## Install the Deep Security Agent on an existing VM
To install the agent on an existing VM, you need the following:

* The Azure PowerShell module, version 0.8.2 or newer, installed on your local computer. You can check the version of Azure PowerShell that you have installed by using the **Get-Module azure | format-table version** command. For instructions and a link to the latest version, see [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs). Log in to your Azure subscription using `Add-AzureAccount`.
* The VM Agent installed on the target virtual machine.

First, verify that the VM Agent is already installed. Fill in the cloud service name and virtual machine name, and then run the following commands at an administrator-level Azure PowerShell command prompt. Replace everything within the quotes, including the < and > characters.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

If you don't know the cloud service and virtual machine name, run **Get-AzureVM** to display that information for all the virtual machines in your current subscription.

If the **write-host** command returns **True**, the VM Agent is installed. If it returns **False**, see the instructions and a link to the download in the Azure blog post [VM Agent and Extensions - Part 2](http://go.microsoft.com/fwlink/p/?LinkId=403947).

If the VM Agent is installed, run these commands.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## Next steps
It takes a few minutes for the agent to start running when it is installed. After that, you need to activate Deep Security on the virtual machine so it can be managed by a Deep Security Manager. See the following articles for additional instructions:

* Trend's article about this solution, [Instant-On Cloud Security for Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101)
* A [sample Windows PowerShell script](http://go.microsoft.com/fwlink/?LinkId=404100) to configure the virtual machine
* [Instructions](http://go.microsoft.com/fwlink/?LinkId=404099) for the sample

## Additional resources
[How to log on to a virtual machine running Windows Server]

[Azure VM Extensions and features]

<!--Link references-->
[How to log on to a virtual machine running Windows Server]: virtual-machines-windows-classic-connect-logon.md
[Azure VM Extensions and features]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
