---
title: Skapa en nätverks säkerhets grupp (klassisk) med hjälp av PowerShell | Microsoft Docs
description: Lär dig hur du skapar och distribuerar en nätverks säkerhets grupp (klassisk) med hjälp av PowerShell
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: 86810b0d-0240-46a2-8548-fca22daa56f3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: 5ef80ccd01c9c6979fd95d161d97d0dfaab58b24
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056654"
---
# <a name="create-a-network-security-group-classic-using-powershell"></a>Skapa en nätverks säkerhets grupp (klassisk) med hjälp av PowerShell
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen. Du kan också [skapa NSG: er i distributions modellen för Resource Manager](tutorial-filter-network-traffic.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

PowerShell-kommandona nedan förväntar sig en enkel miljö som redan har skapats baserat på scenariot ovan. Om du vill köra kommandona som de visas i det här dokumentet måste du först skapa en test miljö genom att [skapa ett VNet](virtual-networks-create-vnet-classic-netcfg-ps.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Skapa en NSG för klient dels under nätet

1. Om du aldrig har använt Azure PowerShell, se [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

2. Skapa en nätverks säkerhets grupp med namnet *NSG-FrontEnd*:

    ```powershell   
    New-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" -Location uswest `
      -Label "Front end subnet NSG"
   ```

3. Skapa en säkerhets regel som tillåter åtkomst från Internet till port 3389:

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '3389'
   ```

4. Skapa en säkerhets regel som tillåter åtkomst från Internet till port 80:

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name web-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 200 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '80'
    ```

## <a name="create-an-nsg-for-the-back-end-subnet"></a>Skapa en NSG för Server dels under nätet

1. Skapa en nätverks säkerhets grupp med namnet *NSG-BackEnd*:
   
    ```powershell
    New-AzureNetworkSecurityGroup -Name "NSG-BackEnd" -Location uswest `
      -Label "Back end subnet NSG"
    ```

2. Skapa en säkerhets regel som tillåter åtkomst från klient delens undernät till port 1433 (standard port som används av SQL Server):
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix 192.168.1.0/24  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '1433'
    ```

3. Skapa en säkerhets regel som blockerar åtkomst från under nätet till Internet:
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-BackEnd" `
      | Set-AzureNetworkSecurityRule -Name block-internet `
      -Action Deny -Protocol '*' -Type Outbound -Priority 200 `
      -SourceAddressPrefix '*'  -SourcePortRange '*' `
      -DestinationAddressPrefix Internet -DestinationPortRange '*'
   ```