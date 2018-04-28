---
title: Konfigurera ett Azure Virtual Network (klassisk) - nätverket konfigurationsfilen | Microsoft Docs
description: Lär dig mer om att skapa och ändra virtuella nätverk (klassiskt) genom att exportera, ändra och importera en konfigurationsfil för nätverket.
services: virtual-network
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/23/2017
ms.author: genli
ms.custom: ''
ms.openlocfilehash: ed47a5d1449ba634f90e93a82f15daf6e44a553e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>Konfigurera ett virtuellt nätverk (klassiska) med en konfigurationsfil för nätverk
> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-distributionsmodellen.

Du kan skapa och konfigurera ett virtuellt nätverk (klassiska) med en konfigurationsfil för nätverk med Azure-kommandoradsgränssnittet (CLI) 1.0 eller Azure PowerShell. Du kan inte skapa eller ändra ett virtuellt nätverk med Azure Resource Manager-distributionsmodellen med en konfigurationsfil för nätverket. Du kan inte använda Azure-portalen att skapa eller ändra ett virtuellt nätverk (klassiska) med en konfigurationsfil för nätverket, men du kan använda Azure-portalen för att skapa ett virtuellt nätverk (klassiska) utan att använda en konfigurationsfil för nätverket.

Skapa och konfigurera ett virtuellt nätverk (klassiska) med en konfigurationsfil för nätverket kräver exportera, ändra och importera filen.

## <a name="export"></a>Exportera en konfigurationsfil för nätverk

Du kan använda PowerShell eller Azure CLI för att exportera en konfigurationsfil för nätverket. PowerShell exporterar en XML-fil, medan Azure CLI exporterar en json-fil.

### <a name="powershell"></a>PowerShell
 
1. [Installera Azure PowerShell och logga in på Azure](/powershell/azure/install-azure-ps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Ändra katalogen (och se till att den finns) och ett filnamn i kommandot efter behov, kör kommandot för att exportera konfigurationsfilen nätverk:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>Azure CLI 1.0

1. [Installera Azure CLI 1.0](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Slutför stegen från en kommandotolk i Azure CLI 1.0.
2. Logga in i Azure genom att ange den `azure login` kommando.
3. Kontrollera att du befinner dig i asm-läge genom att ange den `azure config mode asm` kommando.
4. Ändra katalogen (och se till att den finns) och ett filnamn i kommandot efter behov, kör kommandot för att exportera konfigurationsfilen nätverk:
    
    ```azurecli
    azure network export c:\azure\networkconfig.json
    ```

## <a name="create-or-modify-a-network-configuration-file"></a>Skapa eller ändra en konfigurationsfil för nätverk

En konfigurationsfil för nätverk är en XML-fil (när du använder PowerShell) eller en json-fil (när du använder Azure CLI). Du kan redigera filen i en text, eller XML/json-redigerare. Den [nätverk konfigurationsinställningar filen schemat](https://msdn.microsoft.com/library/azure/jj157100.aspx) artikeln innehåller information om alla inställningar. Ytterligare förklaring av inställningarna finns [Visa inställningar för virtuella nätverk och](manage-virtual-network.md#view-virtual-networks-and-settings). De ändringar du gör i filen:

- Måste vara kompatibel med schemat eller importera konfigurationsfilen nätverket misslyckas.
- Skriv över alla befintliga nätverksinställningarna för din prenumeration, så mycket försiktig när du gör ändringar. Till exempel referera till exempel nätverk konfigurationsfilerna som följer. Säg originalfilen finns två **VirtualNetworkSite** instanser och du ändrade den som visas i exemplen. När du importerar filen Azure tar bort det virtuella nätverket för den **VirtualNetworkSite** instans som du har tagit bort i filen. Det här förenklad scenariot förutsätter att inga resurser har i det virtuella nätverket som om det fanns gick inte att ta bort det virtuella nätverket och misslyckas importen.

> [!IMPORTANT]
> Azure tar hänsyn till ett undernät som har något distribueras till den som **används**. När ett undernät kan inte ändras. Flytta allt som du har distribuerat till undernätet för att ett annat undernät som inte ändras innan du ändrar information om undernät i en konfigurationsfil för nätverket. Se [flytta en virtuell dator eller Rollinstans till ett annat undernät](virtual-networks-move-vm-role-to-subnet.md) mer information.

### <a name="example-xml-for-use-with-powershell"></a>XML-exempel för användning med PowerShell

Följande exempel nätverket konfigurationsfil skapar ett virtuellt nätverk med namnet *myVirtualNetwork* med ett adressutrymme för *10.0.0.0/16* i den *östra USA* Azure-region. Det virtuella nätverket innehåller ett undernät med namnet *mySubnet* med en adressprefixet *10.0.0.0/24*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <Dns />
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myVirtualNetwork" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="mySubnet">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

Om nätverket konfigurationsfil som du exporterade innehåller inget innehåll kan du kopiera XML-filen i föregående exempel och klistra in den i en ny fil.

### <a name="example-json-for-use-with-the-azure-cli-10"></a>Exempel JSON för användning med Azure CLI 1.0

Följande exempel nätverket konfigurationsfil skapar ett virtuellt nätverk med namnet *myVirtualNetwork* med ett adressutrymme för *10.0.0.0/16* i den *östra USA* Azure-region. Det virtuella nätverket innehåller ett undernät med namnet *mySubnet* med en adressprefixet *10.0.0.0/24*.

```json
{
   "VirtualNetworkConfiguration" : {
      "Dns" : "",
      "VirtualNetworkSites" : [
         {
            "AddressSpace" : [ "10.0.0.0/16" ],
            "Location" : "East US",
            "Name" : "myVirtualNetwork",
            "Subnets" : [
               {
                  "AddressPrefix" : "10.0.0.0/24",
                  "Name" : "mySubnet"
               }
            ]
         }
      ]
   }
}
```

Om nätverket konfigurationsfil som du exporterade innehåller inget innehåll kan du kopiera json i föregående exempel och klistra in den i en ny fil.

## <a name="import"></a>Importera en konfigurationsfil för nätverk

Du kan använda PowerShell eller Azure CLI för att importera en konfigurationsfil för nätverket. PowerShell importerar en XML-fil, medan Azure CLI importerar en json-fil. Om importen misslyckas, bekräfta att filen uppfyller de [nätverk Konfigurationsschemat](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

### <a name="powershell"></a>PowerShell
 
1. [Installera Azure PowerShell och logga in på Azure](/powershell/azure/install-azure-ps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Ändra katalog och filnamnet i följande kommando vid behov, kör kommandot för att importera konfigurationsfilen nätverk:
 
    ```powershell
    Set-AzureVNetConfig  -ConfigurationPath c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>Azure CLI 1.0

1. [Installera Azure CLI 1.0](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Slutför stegen från en kommandotolk i Azure CLI 1.0.
2. Logga in i Azure genom att ange den `azure login` kommando.
3. Kontrollera att du befinner dig i asm-läge genom att ange den `azure config mode asm` kommando.
4. Ändra katalog och filnamnet i följande kommando vid behov, kör kommandot för att importera konfigurationsfilen nätverk:

    ```azurecli
    azure network import c:\azure\networkconfig.json
    ```
