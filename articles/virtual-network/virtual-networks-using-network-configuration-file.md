---
title: Konfigurera en Azure-Virtual Network (klassisk) – nätverks konfigurations fil | Microsoft Docs
description: Lär dig hur du skapar och ändrar virtuella nätverk (klassisk) genom att exportera, ändra och importera en nätverks konfigurations fil.
services: virtual-network
documentationcenter: ''
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: ab4faa0f727469e27eb30af54f24036292ec3118
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058705"
---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>Konfigurera ett virtuellt nätverk (klassiskt) med en nätverks konfigurations fil
> [!IMPORTANT]
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och klassisk](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder distributions modellen för Resource Manager.

Du kan skapa och konfigurera ett virtuellt nätverk (klassiskt) med en nätverks konfigurations fil med hjälp av det klassiska kommando rads gränssnittet för Azure (CLI) eller Azure PowerShell. Du kan inte skapa eller ändra ett virtuellt nätverk via Azure Resource Manager distributions modellen med hjälp av en nätverks konfigurations fil. Du kan inte använda Azure Portal för att skapa eller ändra ett virtuellt nätverk (klassiskt) med en nätverks konfigurations fil, men du kan använda Azure Portal för att skapa ett virtuellt nätverk (klassiskt) utan att använda en nätverks konfigurations fil.

Att skapa och konfigurera ett virtuellt nätverk (klassiskt) med en nätverks konfigurations fil måste exportera, ändra och importera filen.

## <a name="export"></a>Exportera en nätverks konfigurations fil

Du kan använda PowerShell eller den klassiska Azure CLI-filen för att exportera en nätverks konfigurations fil. PowerShell exporterar en XML-fil medan Azures klassiska CLI exporterar en JSON-fil.

### <a name="powershell"></a>PowerShell
 
1. [Installera Azure PowerShell och logga in på Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).
2. Ändra katalogen (och se till att den finns) och filename i följande kommando efter behov och kör sedan kommandot för att exportera nätverks konfigurations filen:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\networkconfig.xml
    ```

### <a name="azure-classic-cli"></a>Klassisk Azure CLI

1. [Installera den klassiska Azure CLI](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Slutför de återstående stegen från en klassisk CLI-kommandotolk.
2. Logga in på Azure genom att ange `azure login` kommandot.
3. Se till att du är i ASM-läge `azure config mode asm` genom att ange kommandot.
4. Ändra katalogen (och se till att den finns) och filename i följande kommando efter behov och kör sedan kommandot för att exportera nätverks konfigurations filen:
    
    ```azurecli
    azure network export c:\azure\networkconfig.json
    ```

## <a name="create-or-modify-a-network-configuration-file"></a>Skapa eller ändra en nätverks konfigurations fil

En nätverks konfigurations fil är en XML-fil (när du använder PowerShell) eller en JSON-fil (när du använder klassisk CLI). Du kan redigera filen i valfri text eller XML/JSON-redigerare. Artikeln [schema inställningar för nätverks konfiguration](https://msdn.microsoft.com/library/azure/jj157100.aspx) innehåller information om alla inställningar. Ytterligare förklaringar av inställningarna finns i [Visa virtuella nätverk och inställningar](manage-virtual-network.md#view-virtual-networks-and-settings). De ändringar du gör i filen:

- Måste följa schemat, eller så kommer det inte att gå att importera nätverks konfigurations filen.
- Skriv över eventuella befintliga nätverks inställningar för din prenumeration, så var ytterst försiktig när du gör ändringar. Du kan till exempel referera till exempel på nätverks konfigurations filerna som följer. Anta att den ursprungliga filen innehöll två **VirtualNetworkSite** -instanser och att du har ändrat den, som du ser i exemplen. När du importerar filen tar Azure bort det virtuella nätverket för **VirtualNetworkSite** -instansen som du tog bort i filen. Det här förenklade scenariot förutsätter att det inte fanns några resurser i det virtuella nätverket, eftersom det inte gick att ta bort det virtuella nätverket, och importen kunde inte slutföras.

> [!IMPORTANT]
> Azure betraktar ett undernät som har något distribuerat till det som **används**. När ett undernät används kan det inte ändras. Innan du ändrar under näts informationen i en nätverks konfigurations fil, kan du flytta allt som du har distribuerat till under nätet till ett annat undernät som inte ändras. Mer information finns i [flytta en virtuell dator eller roll instans till ett annat undernät](virtual-networks-move-vm-role-to-subnet.md) .

### <a name="example-xml-for-use-with-powershell"></a>Exempel-XML för användning med PowerShell

Följande exempel på en nätverks konfigurations fil skapar ett virtuellt nätverk med namnet *myVirtualNetwork* med adress utrymmet *10.0.0.0/16* i regionen *USA, östra* Azure. Det virtuella nätverket innehåller ett undernät med namnet *mitt undernät* med adressprefixet *10.0.0.0/24*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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

Om den nätverks konfigurations fil som du exporterade inte innehåller något innehåll kan du kopiera XML i föregående exempel och klistra in den i en ny fil.

### <a name="example-json-for-use-with-the-classic-cli"></a>Exempel-JSON för användning med klassisk CLI

Följande exempel på en nätverks konfigurations fil skapar ett virtuellt nätverk med namnet *myVirtualNetwork* med adress utrymmet *10.0.0.0/16* i regionen *USA, östra* Azure. Det virtuella nätverket innehåller ett undernät med namnet *mitt undernät* med adressprefixet *10.0.0.0/24*.

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

Om den nätverks konfigurations fil som du exporterade inte innehåller något innehåll kan du kopiera JSON i föregående exempel och klistra in den i en ny fil.

## <a name="import"></a>Importera en nätverks konfigurations fil

Du kan använda PowerShell eller klassisk CLI för att importera en nätverks konfigurations fil. PowerShell importerar en XML-fil medan den klassiska CLI importerar en JSON-fil. Om importen Miss lyckas bekräftar du att filen överensstämmer med schemat för [nätverks konfigurationen](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

### <a name="powershell"></a>PowerShell
 
1. [Installera Azure PowerShell och logga in på Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).
2. Ändra katalogen och fil namnet i följande kommando vid behov och kör sedan kommandot för att importera nätverks konfigurations filen:
 
    ```powershell
    Set-AzureVNetConfig  -ConfigurationPath c:\azure\networkconfig.xml
    ```

### <a name="azure-classic-cli"></a>Klassisk Azure CLI

1. [Installera den klassiska Azure CLI](/cli/azure/install-classic-cli). Slutför de återstående stegen från en klassisk CLI-kommandotolk.
2. Logga in på Azure genom att ange `azure login` kommandot.
3. Se till att du är i ASM-läge `azure config mode asm` genom att ange kommandot.
4. Ändra katalogen och fil namnet i följande kommando vid behov och kör sedan kommandot för att importera nätverks konfigurations filen:

    ```azurecli
    azure network import c:\azure\networkconfig.json
    ```
