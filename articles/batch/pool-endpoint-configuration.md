---
title: Konfigurera nodslutpunkter i Azure Batch-pool | Microsoft-dokument
description: Konfigurera eller inaktivera åtkomst till SSH- eller RDP-portar på beräkningsnoder i en Azure Batch-pool.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: labrenne
ms.openlocfilehash: 098ccf999391412520989c4ec2433fd73bc0a72d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77017232"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Konfigurera eller inaktivera fjärråtkomst för beräkningsnoder i en Azure Batch-pool

Som standard tillåter Batch en [nodanvändare](/rest/api/batchservice/computenode/adduser) med nätverksanslutning att ansluta externt till en beräkningsnod i en batchpool. En användare kan till exempel ansluta via Fjärrskrivbord (RDP) på port 3389 till en beräkningsnod i en Windows-pool. På samma sätt kan en användare som standard ansluta med Secure Shell (SSH) på port 22 till en beräkningsnod i en Linux-pool. 

I din miljö kan du behöva begränsa eller inaktivera dessa standardinställningar för extern åtkomst. Du kan ändra dessa inställningar genom att använda batch-API:erna för att ange egenskapen [PoolEndpointConfiguration.](/rest/api/batchservice/pool/add#poolendpointconfiguration) 

## <a name="about-the-pool-endpoint-configuration"></a>Om konfigurationen av poolenslutpunkt
Slutpunktskonfigurationen består av en eller [flera NAT-pooler (Network Address Translation)](/rest/api/batchservice/pool/add#inboundnatpool) med klientportar. (Blanda inte ihop en NAT-pool med batch-poolen med beräkningsnoder.) Du ställer in varje NAT-pool för att åsidosätta standardanslutningsinställningarna på poolens beräkningsnoder. 

Varje NAT-poolkonfiguration innehåller en eller [flera NSG-regler (Network Security Group).](/rest/api/batchservice/pool/add#networksecuritygrouprule) Varje NSG-regel tillåter eller nekar viss nätverkstrafik till slutpunkten. Du kan välja att tillåta eller neka all trafik, trafik som identifieras av ett [servicetag](../virtual-network/security-overview.md#service-tags) (till exempel "Internet" eller trafik från specifika IP-adresser eller undernät.

### <a name="considerations"></a>Överväganden
* Poolslutpunktskonfigurationen är en del av poolens [nätverkskonfiguration](/rest/api/batchservice/pool/add#networkconfiguration). Nätverkskonfigurationen kan eventuellt innehålla inställningar för att ansluta till poolen till ett [virtuellt Azure-nätverk](batch-virtual-network.md). Om du konfigurerar poolen i ett virtuellt nätverk kan du skapa NSG-regler som använder adressinställningar i det virtuella nätverket.
* Du kan konfigurera flera NSG-regler när du konfigurerar en NAT-pool. Reglerna kontrolleras i prioritetsordning. När villkoren för en regel uppfylls testas inga fler regler.


## <a name="example-deny-all-rdp-traffic"></a>Exempel: Neka all RDP-trafik

Följande C#-kodavsnitt visar hur du konfigurerar RDP-slutpunkten på beräkningsnoder i en Windows-pool för att neka all nätverkstrafik. Slutpunkten använder en frontend pool av portar i intervallet *60000 - 60099*. 

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
      new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 60000, 60099, new NetworkSecurityGroupRule[]
        {
            new NetworkSecurityGroupRule(162, NetworkSecurityGroupRuleAccess.Deny, "*"),
        })
    })    
};
```

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Exempel: Neka all SSH-trafik från internet

Följande Python-kodavsnitt visar hur du konfigurerar SSH-slutpunkten på beräkningsnoder i en Linux-pool för att neka all internettrafik. Ändpunkten använder en frontend pool av portar i intervallet *4000 - 4100*. 

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                    priority=170,
                    access=batchmodels.NetworkSecurityGroupRuleAccess.deny,
                    source_address_prefix='Internet'
                )
            ]
        )
        ]
    )
)
```

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Exempel: Tillåt RDP-trafik från en viss IP-adress

Följande C#-kodavsnitt visar hur du konfigurerar RDP-slutpunkten på beräkningsnoder i en Windows-pool så att RDP endast tillåts från IP-adress *198.51.100.7*. Den andra NSG-regeln nekar trafik som inte matchar IP-adressen.

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
        new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 7500, 8000, new NetworkSecurityGroupRule[]
        {   
            new NetworkSecurityGroupRule(179,NetworkSecurityGroupRuleAccess.Allow, "198.51.100.7"),
            new NetworkSecurityGroupRule(180,NetworkSecurityGroupRuleAccess.Deny, "*")
        })
    })    
};
```

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Exempel: Tillåt SSH-trafik från ett visst undernät

Följande Python-kodavsnitt visar hur du konfigurerar SSH-slutpunkten på beräkningsnoder i en Linux-pool så att åtkomst endast tillåts från undernätet *192.168.1.0/24*. Den andra NSG-regeln nekar trafik som inte matchar undernätet.

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                    priority=170,
                    access='allow',
                    source_address_prefix='192.168.1.0/24'
                ),
                batchmodels.NetworkSecurityGroupRule(
                    priority=175,
                    access='deny',
                    source_address_prefix='*'
                )
            ]
        )
        ]
    )
)
```

## <a name="next-steps"></a>Nästa steg

- Mer information om NSG-regler i Azure finns i [Filtrera nätverkstrafik med nätverkssäkerhetsgrupper](../virtual-network/security-overview.md).

- En djupgående översikt över Batch finns i [Utveckla storskaliga parallella beräkningslösningar med Batch](batch-api-basics.md).

