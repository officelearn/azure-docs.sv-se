---
title: Flytta en virtuell dator (klassisk) eller Cloud Services roll instans till ett annat undernät – Azure PowerShell | Microsoft Docs
description: Lär dig hur du flyttar virtuella datorer (klassisk) och Cloud Services roll instanser till ett annat undernät med hjälp av PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: 275d59a7bddd8b2b609169218afcd15e9a0ce913
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058379"
---
# <a name="move-a-vm-classic-or-cloud-services-role-instance-to-a-different-subnet-using-powershell"></a>Flytta en virtuell dator (klassisk) eller Cloud Services roll instans till ett annat undernät med PowerShell
Du kan använda PowerShell för att flytta dina virtuella datorer (klassisk) från ett undernät till ett annat i samma virtuella nätverk (VNet). Roll instanser kan flyttas genom att du redigerar CSCFG-filen i stället för att använda PowerShell.

> [!NOTE]
> Den här artikeln förklarar hur du flyttar virtuella datorer som distribueras via den klassiska distributions modellen.
> 
> 

Varför ska du flytta virtuella datorer till ett annat undernät? Migrering av undernät är användbart när det äldre under nätet är för litet och inte kan utökas på grund av befintliga virtuella datorer som körs i det under nätet. I så fall kan du skapa ett nytt, större undernät och migrera de virtuella datorerna till det nya under nätet. När migreringen är klar kan du ta bort det gamla tomma under nätet.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Så här flyttar du en virtuell dator till ett annat undernät
Om du vill flytta en virtuell dator kör du PowerShell-cmdleten Set-AzureSubnet med hjälp av exemplet nedan som en mall. I exemplet nedan flyttar vi TestVM från dess nuvarande undernät till undernät-2. Se till att redigera exemplet för att avspegla din miljö. Observera att när du kör cmdleten Update-AzureVM som en del av en procedur startar den om den virtuella datorn som en del av uppdaterings processen.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
    | Set-AzureSubnet –SubnetNames Subnet-2 `
    | Update-AzureVM

Om du har angett en statisk intern IP-adress för den virtuella datorn måste du avmarkera inställningen innan du kan flytta den virtuella datorn till ett nytt undernät. I så fall använder du följande:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Set-AzureSubnet -SubnetNames Subnet-2 `
    | Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Så här flyttar du en roll instans till ett annat undernät
Om du vill flytta en roll instans redigerar du CSCFG-filen. I exemplet nedan flyttar vi "Role0" i Virtual Network- *VNETName* från det befintliga under nätet till *undernät-2*. Eftersom roll instansen redan har distribuerats ändrar du bara under nätets namn = undernät-2. Se till att redigera exemplet för att avspegla din miljö.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
