---
title: Inaktivera nätverksprinciper för privata slutpunkter i Azure
description: Lär dig hur du inaktiverar nätverksprinciper för privata slutpunkter.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: b5ab62e7ab57d32a11a45713519633034deb6a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453025"
---
# <a name="disable-network-policies-for-private-endpoints"></a>Inaktivera nätverksprinciper för privata slutpunkter

Nätverksprinciper som NSG (Network Security Groups) stöds inte för privata slutpunkter. För att distribuera en privat slutpunkt i ett visst undernät krävs en explicit inaktiverande inställning i det undernätet. Den här inställningen gäller endast för den privata slutpunkten. För andra resurser i undernätet kontrolleras åtkomsten baserat på NSG-säkerhetsregler (Network Security Groups). 
 
När du använder portalen för att skapa en privat slutpunkt inaktiveras den här inställningen automatiskt som en del av skapa-processen. Distribution med andra klienter kräver ytterligare ett steg för att ändra den här inställningen. Du kan inaktivera inställningen med molngränssnittet från Azure-portalen eller lokala installationer av Azure PowerShell, Azure CLI eller använda Azure Resource Manager-mallar.  
 
I följande exempel beskrivs hur du `PrivateEndpointNetworkPolicies` inaktiverar för ett virtuellt nätverk med namnet *myVirtualNetwork* med ett *standardundernät* i en resursgrupp med namnet *myResourceGroup*.

## <a name="using-azure-powershell"></a>Använda Azure PowerShell
I det här avsnittet beskrivs hur du inaktiverar privata slutpunktsprinciper för undernät med Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Använda Azure CLI
I det här avsnittet beskrivs hur du inaktiverar privata slutpunktsprinciper för undernät med Azure CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
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
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azures privata slutpunkt](private-endpoint-overview.md)
 
