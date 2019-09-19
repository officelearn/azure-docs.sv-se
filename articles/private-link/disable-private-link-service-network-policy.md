---
title: 'Inaktivera nätverks principer för Azures tjänst källa IP-adress för privat länk '
description: Lär dig hur du inaktiverar nätverks principer för privat Azure-länk
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: b2003b9c7520cfa3e82576fd3430063c20d452ff
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104568"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>Inaktivera nätverks principer för tjänst Källans IP-adress för privata länkar

För att kunna välja en käll-IP-adress för din privata länk tjänst krävs en explicit `privateLinkServiceNetworkPolicies` inaktivera inställning i under nätet. Den här inställningen gäller bara för den angivna privata IP-adressen som du valde som käll-IP för den privata länk tjänsten. För andra resurser i under nätet styrs åtkomsten baserat på säkerhets regel definitionen för nätverks säkerhets grupper (NSG). 
 
När du använder en Azure-klient (PowerShell, CLI eller mallar) krävs ett ytterligare steg för att ändra den här egenskapen. Du kan inaktivera principen med hjälp av Cloud Shell från Azure Portal eller lokala installationer av Azure PowerShell, Azure CLI eller använda Azure Resource Manager mallar.  
 
Följ stegen nedan om du vill inaktivera nätverks principer för privata Länkar för ett virtuellt nätverk med namnet *myVirtualNetwork* med ett *standard* -undernät som finns i en resurs grupp med namnet *myResourceGroup*. 

## <a name="using-azure-powershell"></a>Använda Azure PowerShell
I det här avsnittet beskrivs hur du inaktiverar privata slut punkts principer för undernät med Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork ` 
  | Select -ExpandProperty subnets ` 
  | Where-Object  {$_.Name -eq 'default'} ).privateLinkServiceNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Använda Azure CLI
I det här avsnittet beskrivs hur du inaktiverar privata slut punkts principer för undernät med Azure CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
```
## <a name="using-a-template"></a>Använda en mall
I det här avsnittet beskrivs hur du inaktiverar privata slut punkts principer för undernät med Azure Resource Manager-mall.
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
- Läs mer om [privat Azure-slutpunkt](private-endpoint-overview.md)
 
