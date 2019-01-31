---
title: Konfigurera nod slutpunkter i Azure Batch-pool | Microsoft Docs
description: Hur du konfigurerar eller inaktivera åtkomst till SSH eller RDP portar på beräkningsnoder i en Azure Batch-pool.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: lahugh
ms.openlocfilehash: a6c2c343b13b77048c772cb1e5c2ba06cf8add50
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457623"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Konfigurera och inaktivera fjärråtkomst till beräkningsnoder i en Azure Batch-pool

Som standard tillåter Batch en [noden användare](/rest/api/batchservice/computenode/adduser) med nätverksanslutning till externt ansluta till en beräkningsnod i en Batch-pool. En användare kan till exempel ansluta med RDP (Remote Desktop) på port 3389 till en beräkningsnod i en Windows-pool. På samma sätt kan en användare som standard ansluta med SSH (Secure Shell) på port 22 till en beräkningsnod i en Linux-pool. 

Du kan behöva begränsa eller inaktivera dessa standardinställningar för extern åtkomst i din miljö. Du kan ändra de här inställningarna med hjälp av Batch-API: er att ställa in den [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) egenskapen. 

## <a name="about-the-pool-endpoint-configuration"></a>Om slutpunktskonfiguration i pool
Konfigurationen av slutpunkten består av en eller flera [network adress translation (NAT) pooler](/rest/api/batchservice/pool/add#inboundnatpool) av frontend-portar. (Blanda inte ihop NAT-pool med Batch-pool med beräkningsnoder.) Du kan ställa in varje NAT-pool för att åsidosätta standardinställningarna för anslutning på poolens beräkningsnoder. 

Varje konfiguration för NAT-pool som innehåller ett eller flera [network regler för nätverkssäkerhetsgrupper (NSG)](/rest/api/batchservice/pool/add#networksecuritygrouprule). Varje NSG-regel som tillåter eller nekar vissa nätverkstrafik till slutpunkten. Du kan välja att tillåta eller neka all trafik, trafik som identifierats av en [servicetagg](../virtual-network/security-overview.md#service-tags) (till exempel ”Internet”) eller trafik från specifika IP-adresser eller undernät.

### <a name="considerations"></a>Överväganden
* Slutpunktskonfiguration i pool är en del av poolens [nätverkskonfiguration](/rest/api/batchservice/pool/add#NetworkConfiguration). Nätverkskonfigurationen kan även inkludera inställningar för att ansluta till poolen till en [Azure-nätverk](batch-virtual-network.md). Om du ställer in pool i ett virtuellt nätverk, kan du skapa NSG-regler som använder inställningar för adresser i det virtuella nätverket.
* Du kan konfigurera flera NSG-regler när du konfigurerar en NAT-pool. Regler kontrolleras enligt prioritetsordning. När villkoren för en regel uppfylls testas inga fler regler.


## <a name="example-deny-all-rdp-traffic"></a>Exempel: Neka alla RDP-trafik

Följande C# kodfragment visar hur du konfigurerar RDP-slutpunkt på beräkningsnoder i en Windows-pool som nekar all nätverkstrafik. Slutpunkten som använder en adresspool på klientdelen av portar i intervallet *60000 60099*. 

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

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Exempel: Neka alla SSH-trafik från internet

Python följande kodfragment visar hur du konfigurerar SSH-slutpunkten på beräkningsnoder i en Linux-pool som nekar all Internettrafik. Slutpunkten som använder en adresspool på klientdelen av portar i intervallet *4000 4100*. 

```python
pool.network_configuration=batchmodels.NetworkConfiguration(
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

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Exempel: Tillåt RDP-trafik från en specifik IP-adress

Följande C# kodfragment visar hur du konfigurerar RDP-slutpunkt på beräkningsnoder i en Windows-pool att tillåta RDP-åtkomst från IP-adress *198.51.100.7*. Den andra NSG-regeln nekar trafik som inte matchar IP-adressen.

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

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Exempel: Tillåta SSH-trafik från ett specifikt undernät

Python följande kodfragment visar hur du konfigurerar SSH-slutpunkten på beräkningsnoder i en Linux-pool att tillåta åtkomst från undernätet *192.168.1.0/24*. Den andra NSG-regeln nekar trafik som inte matchar undernätet.

```python
pool.network_configuration=batchmodels.NetworkConfiguration(
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

- Mer information om regler för Nätverkssäkerhetsgrupper i Azure finns i [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](../virtual-network/security-overview.md).

- En detaljerad översikt över Batch finns [utveckla storskaliga parallella beräkningslösningar med Batch](batch-api-basics.md).

