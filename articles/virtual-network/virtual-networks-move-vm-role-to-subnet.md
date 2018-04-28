---
title: Flytta en virtuell dator (klassisk) eller molntjänster roll till ett annat undernät - Azure PowerShell | Microsoft Docs
description: Lär dig mer om att flytta virtuella datorer (klassisk) och molntjänster rollinstanser till ett annat undernät med hjälp av PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: de4135c7-dc5b-4ffa-84cc-1b8364b7b427
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eb1576b7b842537dbdc49e6b9d402ef47ad9fae9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="move-a-vm-classic-or-cloud-services-role-instance-to-a-different-subnet-using-powershell"></a>Flytta en virtuell dator (klassisk) eller molntjänster roll till ett annat undernät med hjälp av PowerShell
Du kan använda PowerShell för att flytta din virtuella dator (klassisk) från ett undernät till en annan i samma virtuella nätverk (VNet). Du kan flytta rollinstanser genom att redigera filen CSCFG i stället för med hjälp av PowerShell.

> [!NOTE]
> Den här artikeln förklarar hur du flyttar virtuella datorer distribueras via den klassiska distributionsmodellen endast.
> 
> 

Varför flytta virtuella datorer till ett annat undernät? Undernät migrering är användbart när det äldre undernätet är för litet och kan inte expanderas på grund av befintliga virtuella datorer som körs i det undernätet. I så fall kan du skapa en ny, större undernät och migrera de virtuella datorerna till det nya undernätet och när migreringen är klar kan du ta bort gamla tomma undernät.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Hur du flyttar en virtuell dator till ett annat undernät
Om du vill flytta en virtuell dator, kör Set-AzureSubnet PowerShell-cmdleten med exemplet nedan som mall. I exemplet nedan flyttar vi TestVM från dess nuvarande undernät till undernät 2. Se till att redigera exempel för att avspegla din miljö. Observera att när du kör cmdlet Update AzureVM som en del av en procedur, startas den virtuella datorn som en del av uppdateringen.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
    | Set-AzureSubnet –SubnetNames Subnet-2 `
    | Update-AzureVM

Om du har angett en statisk interna privata IP-adress för den virtuella datorn har du avmarkera inställningen innan du kan flytta den virtuella datorn till ett nytt undernät. I så fall använder du följande:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Set-AzureSubnet -SubnetNames Subnet-2 `
    | Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Flytta en rollinstans till ett annat undernät
Redigera CSCFG-filen om du vill flytta en rollinstans. I exemplet nedan vi flyttar ”Role0” i det virtuella nätverket *VNETName* från dess nuvarande undernätet *undernät 2*. Eftersom instansen redan har distribuerats, ska du bara ändra undernätnamnet = undernät 2. Se till att redigera exempel för att avspegla din miljö.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
