---
title: ta med fil
description: ta med fil
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: bda289e73b9a782cd56c0c94b8f53e8002b1ccf4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60869990"
---
## <a name="how-to-create-a-virtual-network-using-a-network-config-file-from-powershell"></a>Så här skapar du ett virtuellt nätverk med en konfigurationsfil för nätverk från PowerShell
Azure använder en xml-fil för att definiera alla virtuella nätverk som är tillgängliga för en prenumeration. Du kan hämta den här filen, redigera det för att ändra eller ta bort befintliga virtuella nätverk och skapa nya virtuella nätverk. I de här självstudierna lär du dig hur du laddar ned filen, kallas konfiguration (eller netcfg) nätverksfil, och redigera den för att skapa ett nytt virtuellt nätverk. Läs mer om nätverkskonfigurationsfilen i den [konfigurationsschema för Azure-nätverk](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Utför följande steg för att skapa ett virtuellt nätverk med en netcfg-filen med hjälp av PowerShell:

1. Om du aldrig har använt Azure PowerShell kan du slutföra stegen i den [installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs) artikel, och sedan logga in på Azure och välj din prenumeration.
2. Azure PowerShell-konsolen använda de **Get-AzureVnetConfig** cmdlet för att hämta nätverkskonfigurationsfilen till en katalog på datorn genom att köra följande kommando: 
   
   ```powershell
   Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
   ```
   
   Förväntad utdata:
  
      ```
      XMLConfiguration                                                                                                     
      ----------------                                                                                                     
      <?xml version="1.0" encoding="utf-8"?>...
      ```

3. Öppna filen som du sparade i steg 2 med XML- eller textredigerare och leta efter den  **\<VirtualNetworkSites >** element. Om du har några nätverk som redan har skapats visas varje nätverk som sin egen  **\<VirtualNetworkSite >** element.
4. För att skapa det virtuella nätverket som beskrivs i det här scenariot, lägger du till följande XML strax under den  **\<VirtualNetworkSites >** element:

   ```xml
         <?xml version="1.0" encoding="utf-8"?>
         <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
           <VirtualNetworkConfiguration>
             <VirtualNetworkSites>
                 <VirtualNetworkSite name="TestVNet" Location="East US">
                   <AddressSpace>
                     <AddressPrefix>192.168.0.0/16</AddressPrefix>
                   </AddressSpace>
                   <Subnets>
                     <Subnet name="FrontEnd">
                       <AddressPrefix>192.168.1.0/24</AddressPrefix>
                     </Subnet>
                     <Subnet name="BackEnd">
                       <AddressPrefix>192.168.2.0/24</AddressPrefix>
                     </Subnet>
                   </Subnets>
                 </VirtualNetworkSite>
             </VirtualNetworkSites>
           </VirtualNetworkConfiguration>
         </NetworkConfiguration>
   ```
   
5. Spara nätverkskonfigurationsfilen.
6. Azure PowerShell-konsolen använda de **Set-AzureVnetConfig** cmdlet för att överföra nätverkskonfigurationsfilen genom att köra följande kommando: 
   
   ```powershell
   Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
   ```
   
   Returnerade utdata:
   
      ```
      OperationDescription OperationId                          OperationStatus
      -------------------- -----------                          ---------------
      Set-AzureVNetConfig  <Id>                                 Succeeded 
      ```
   
   Om **OperationStatus** är inte *lyckades* returnerade utdata och kontrollera xml-filen för fel och slutför steg 6 igen.

7. Azure PowerShell-konsolen använda de **Get-AzureVnetSite** cmdlet för att kontrollera att det nya nätverket har lagts till genom att köra följande kommando: 

   ```powershell
   Get-AzureVNetSite -VNetName TestVNet
   ```
   
   Returnerade (förkortas) utdata innehåller följande text:
  
      ```
      AddressSpacePrefixes : {192.168.0.0/16}
      Location             : Central US
      Name                 : TestVNet
      State                : Created
      Subnets              : {FrontEnd, BackEnd}
      OperationDescription : Get-AzureVNetSite
      OperationStatus      : Succeeded
      ```
