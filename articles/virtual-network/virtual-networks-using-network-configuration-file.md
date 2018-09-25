---
title: Konfigurera ett Azure-nätverk (klassisk) - nätverkskonfigurationsfilen | Microsoft Docs
description: Lär dig mer om att skapa och ändra virtuella nätverk (klassisk) genom att exportera, ändra och importera en nätverkskonfigurationsfil.
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
ms.openlocfilehash: 5267ce5c50e8a57843f0bc54165289b38013ad91
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986123"
---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>Konfigurera ett virtuellt nätverk (klassisk) med en nätverkskonfigurationsfil
> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-distributionsmodellen.

Du kan skapa och konfigurera ett virtuellt nätverk (klassisk) med en nätverkskonfigurationsfil med klassiska kommandoradsgränssnittet (CLI) eller Azure PowerShell. Du kan inte skapa eller ändra ett virtuellt nätverk via Azure Resource Manager-distributionsmodellen med hjälp av en nätverkskonfigurationsfil. Du kan inte använda Azure-portalen att skapa eller ändra ett virtuellt nätverk (klassisk) med en nätverkskonfigurationsfil, men du kan använda Azure-portalen för att skapa ett virtuellt nätverk (klassisk), utan att använda en nätverkskonfigurationsfil.

Skapa och konfigurera ett virtuellt nätverk (klassisk) med en nätverkskonfigurationsfil kräver exportera, ändra och importera filen.

## <a name="export"></a>Exportera en nätverkskonfigurationsfil

Du kan använda PowerShell eller Azure klassiska CLI för att exportera en nätverkskonfigurationsfil. PowerShell exporterar en XML-fil, medan den klassiska Azure CLI exporterar en json-fil.

### <a name="powershell"></a>PowerShell
 
1. [Installera Azure PowerShell och logga in på Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).
2. Ändra katalogen (och se till att den finns) och ett filnamn i följande kommando som du vill, kör sedan kommandot för att exportera nätverkskonfigurationsfilen:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\networkconfig.xml
    ```

### <a name="azure-classic-cli"></a>Klassisk Azure CLI

1. [Installera Azure CLI för klassiska](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Slutför de återstående stegen från Kommandotolken klassiska CLI.
2. Logga in på Azure genom att ange den `azure login` kommando.
3. Kontrollera att du befinner dig i asm-läge genom att ange den `azure config mode asm` kommando.
4. Ändra katalogen (och se till att den finns) och ett filnamn i följande kommando som du vill, kör sedan kommandot för att exportera nätverkskonfigurationsfilen:
    
    ```azurecli
    azure network export c:\azure\networkconfig.json
    ```

## <a name="create-or-modify-a-network-configuration-file"></a>Skapa eller ändra en nätverkskonfigurationsfil

En nätverkskonfigurationsfil är en XML-fil (när du använder PowerShell) eller en json-fil (när du använder klassisk CLI). Du kan redigera filen i valfri text eller XML/json-redigerare. Den [nätverks-inställningar för konfigurationsfiler schemat](https://msdn.microsoft.com/library/azure/jj157100.aspx) artikeln innehåller information om alla inställningar. Ytterligare förklaring av inställningarna finns i [visa virtuella nätverk och inställningar](manage-virtual-network.md#view-virtual-networks-and-settings). De ändringar du gör i filen:

- Måste överensstämma med schemat, eller import av nätverkskonfigurationsfilen misslyckas.
- Skriv över alla befintliga nätverksinställningar för din prenumeration, så mycket försiktig när du gör ändringar. Till exempel referera till exempel nätverk konfigurationsfilerna som följer. Anta att den ursprungliga filen innehåller två **VirtualNetworkSite** instanser och ändrade den som visas i exemplen. När du importerar filen Azure tar bort det virtuella nätverket för den **VirtualNetworkSite** instans som du har tagit bort i filen. Den här förenklad scenariot förutsätter vi att inga resurserna fanns i det virtuella nätverket, som om det var det gick inte att ta bort det virtuella nätverket och importen misslyckades.

> [!IMPORTANT]
> Azure tar hänsyn till ett undernät som har något distribueras till den som **används**. När ett undernät används, kan inte ändras. Innan du ändrar information om undernät i en nätverkskonfigurationsfil att flytta allt som har distribuerats till undernät till ett annat undernät som inte ändras. Se [flytta en virtuell dator eller Rollinstans till ett annat undernät](virtual-networks-move-vm-role-to-subnet.md) mer information.

### <a name="example-xml-for-use-with-powershell"></a>XML-exempel för användning med PowerShell

Följande exempel nätverkskonfigurationsfilen skapar ett virtuellt nätverk med namnet *myVirtualNetwork* med ett adressutrymme för *10.0.0.0/16* i den *USA, östra* Azure region. Det virtuella nätverket innehåller ett undernät som heter *mySubnet* med adressprefixet *10.0.0.0/24*.

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

Om du har exporterat nätverkskonfigurationsfilen innehåller inget innehåll kan du kopiera XML-koden i exemplet ovan och klistra in den i en ny fil.

### <a name="example-json-for-use-with-the-classic-cli"></a>Exempel på JSON för användning med klassisk CLI

Följande exempel nätverkskonfigurationsfilen skapar ett virtuellt nätverk med namnet *myVirtualNetwork* med ett adressutrymme för *10.0.0.0/16* i den *USA, östra* Azure region. Det virtuella nätverket innehåller ett undernät som heter *mySubnet* med adressprefixet *10.0.0.0/24*.

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

Om du har exporterat nätverkskonfigurationsfilen innehåller inget innehåll kan du kopiera json i exemplet ovan och klistra in den i en ny fil.

## <a name="import"></a>Importera en nätverkskonfigurationsfil

Du kan använda PowerShell eller klassiska CLI för att importera en nätverkskonfigurationsfil. PowerShell importerar en XML-fil, medan klassiskt CLI importerar en json-fil. Om importen misslyckas kontrollerar du att filen uppfyller de [nätverk konfigurationsschema](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

### <a name="powershell"></a>PowerShell
 
1. [Installera Azure PowerShell och logga in på Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).
2. Ändra katalog- och filnamn i följande kommando vid behov, kör sedan kommandot för att importera nätverkskonfigurationsfilen:
 
    ```powershell
    Set-AzureVNetConfig  -ConfigurationPath c:\azure\networkconfig.xml
    ```

### <a name="azure-classic-cli"></a>Klassisk Azure CLI

1. [Installera Azure CLI för klassiska](/cli/azure/install-cli-version-1.0.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Slutför de återstående stegen från Kommandotolken klassiska CLI.
2. Logga in på Azure genom att ange den `azure login` kommando.
3. Kontrollera att du befinner dig i asm-läge genom att ange den `azure config mode asm` kommando.
4. Ändra katalog- och filnamn i följande kommando vid behov, kör sedan kommandot för att importera nätverkskonfigurationsfilen:

    ```azurecli
    azure network import c:\azure\networkconfig.json
    ```
