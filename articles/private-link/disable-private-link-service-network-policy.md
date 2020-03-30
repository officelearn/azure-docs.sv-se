---
title: 'Inaktivera nätverksprinciper för AZURE Private Link-tjänstkällans IP-adress '
description: Lär dig hur du inaktiverar nätverksprinciper för Azure private Link
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 4c6bd64d141341e0b7fa5641e04320a95d7951bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452997"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>Inaktivera nätverksprinciper för PRIVATE Link-tjänstkällaNS IP

För att välja en käll-IP-adress för din `privateLinkServiceNetworkPolicies` Private Link-tjänst krävs en explicit inaktiverande inställning i undernätet. Den här inställningen gäller endast för den specifika privata IP-adress som du valde som käll-IP för tjänsten Private Link. För andra resurser i undernätet kontrolleras åtkomsten baserat på NSG-säkerhetsregler (Network Security Groups). 
 
När du använder en Azure-klient (PowerShell, CLI eller mallar) krävs ytterligare ett steg för att ändra den här egenskapen. Du kan inaktivera principen med hjälp av molngränssnittet från Azure-portalen eller lokala installationer av Azure PowerShell, Azure CLI eller använda Azure Resource Manager-mallar.  
 
Följ stegen nedan för att inaktivera principer för privata länktjänstnätverk för ett virtuellt nätverk med namnet *myVirtualNetwork* med ett *standardundernät* i en resursgrupp med namnet *myResourceGroup*. 

## <a name="using-azure-powershell"></a>Använda Azure PowerShell
I det här avsnittet beskrivs hur du inaktiverar privata slutpunktsprinciper för undernät med Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Använda Azure CLI
I det här avsnittet beskrivs hur du inaktiverar privata slutpunktsprinciper för undernät med Azure CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
```
## <a name="using-a-template"></a>Använda en mall
I det här avsnittet beskrivs hur du inaktiverar privata slutpunktsprinciper för undernät med Azure Resource Manager-mallen.
```json
{ 
    "name": "myVirtualNetwork", 
    "type": "Microsoft.Network/virtualNetworks", 
    "apiVersion": "2019-04-01", 
    "location": "WestUS", 
    "properties": { 
        "addressSpace": { 
            "addressPrefixes": [ 
                "10.0.0.0/16" 
             ] 
        }, 
        "subnets": [ 
               { 
                 "name": "default", 
                 "properties": { 
                        "addressPrefix": "10.0.0.0/24", 
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure Private Endpoint](private-endpoint-overview.md)
 
