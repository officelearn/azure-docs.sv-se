---
title: Distribuera skalningsuppsättningar för virtuella datorer med IPv6 i Azure
titlesuffix: Azure Virtual Network
description: Den här artikeln visar hur du distribuerar skaluppsättningar för virtuella datorer med IPv6 i ett virtuellt Azure-nätverk.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 6a751fa193c8dd530707f790af0292d536a6f47d
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420465"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure"></a>Distribuera skalningsuppsättningar för virtuella datorer med IPv6 i Azure

Den här artikeln visar hur du distribuerar en virtuell stack (IPv4 + IPv6) virtual machine scale set med en extern belastningsutjämnad med dubbla staplar i ett virtuellt Azure-nätverk. Processen för att skapa en IPv6-kompatibel virtuell datorskalauppsättning är nästan identisk med processen för att skapa enskilda virtuella datorer som beskrivs [här](ipv6-configure-standard-load-balancer-template-json.md). Du börjar med de steg som liknar de som beskrivs för enskilda virtuella datorer:
1.    Skapa offentliga IPv4- och IPv6-adresser.
2.    Skapa en belastningsutjämnare med dubbla staplar.  
3.    Skapa NSG-regler (Network Security Group).  

Det enda steget som skiljer sig från enskilda virtuella datorer är att skapa nätverksgränssnittskonfigurationen (NIC) som använder skalningsuppsättningsresursen för den virtuella datorn: networkProfile/networkInterfaceConfigurations. JSON-strukturen liknar den för Microsoft.Network/networkInterfaces-objektet som används för enskilda virtuella datorer med tillägget att ange nätverkskortet och IPv4 IpConfiguration som det primära gränssnittet med det **"primära":** true-attributet som visas i följande exempel:

```json
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[variables('nicName')]",
                "properties": {
                  "primary": true,
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups','VmssNsg')]"
          },                  
                  "ipConfigurations": [
                    {
                      "name": "[variables('ipConfigName')]",
                      "properties": {
                        "primary": true,
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv4",                       
                        "publicipaddressconfiguration": {
                          "name": "pub1",
                          "properties": {
                            "idleTimeoutInMinutes": 15
                          }
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer', 'bePool'))]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatPools', 'loadBalancer', 'natPool')]"
                          }
                        ]
                      }
                    },
                    {
                      "name": "[variables('ipConfigNameV6')]",
                      "properties": {
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets','MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv6",
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer','bePoolv6')]"
                          }
                        ],                        
                      }
                    }
                  ]
                }
              }
            ]
          }

```


## <a name="sample-virtual-machine-scale-set-template-json"></a>Exempel på skaluppsättningsmall för virtuell dator JSON

Om du vill distribuera en virtuell stack (IPv4 + IPv6) virtual machine-skaluppsättning med extern belastningsutjämnad och exempelmall för virtuell nätverksvy med dubbla staplar och exempelmall för virtuell nätverksvy [här](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/).
## <a name="next-steps"></a>Nästa steg

Mer information om IPv6-stöd i virtuella Azure-nätverk finns i [Vad är IPv6 för Azure Virtual Network?](ipv6-overview.md).
