---
title: Konfigurera noden slutpunkter i Azure Batch-pool | Microsoft Docs
description: Hur du konfigurerar eller inaktivera åtkomst till SSH eller RDP-portar på compute-noder i en Azure Batch-pool.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: danlep
ms.openlocfilehash: 5898206761e5029f94b6d1f1b48223481ae2ca13
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358735"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Konfigurera eller inaktivera compute-noder i en Azure Batch-pool med fjärråtkomst

Som standard Batch tillåter en [nod användaren](/rest/api/batchservice/computenode/adduser) med nätverksanslutning till externt ansluta till en beräkningsnod i poolen Batch. En användare kan till exempel ansluta med RDP (Remote Desktop) på port 3389 till en beräkningsnod i poolen Windows. På samma sätt kan en användare som standard ansluter av SSH (Secure Shell) på port 22 till en beräkningsnod i poolen Linux. 

Du kan behöva begränsa eller inaktivera dessa standardinställningar för extern åtkomst i din miljö. Du kan ändra dessa inställningar med hjälp av Batch-API: er för att ange den [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) egenskapen. 

## <a name="about-the-pool-endpoint-configuration"></a>Om slutpunktskonfiguration pool
Slutpunktskonfigurationen består av en eller flera [network address translation (NAT) pooler](/rest/api/batchservice/pool/add#inboundnatpool) frontend-portar. (Blanda inte ihop NAT-pool med Batch-pool med beräkningsnoder.) Du kan ställa in varje NAT-pool för att åsidosätta standardinställningarna för anslutningar på poolens beräkningsnoder. 

Varje konfiguration av NAT-pool som innehåller ett eller flera [nätverk regler för nätverkssäkerhetsgrupper (NSG)](/rest/api/batchservice/pool/add#networksecuritygrouprule). Varje NSG-regel som tillåter eller nekar viss nätverkstrafik till slutpunkten. Du kan välja att tillåta eller neka all trafik, trafik som identifieras av en [tjänsten taggen](../virtual-network/security-overview.md#service-tags) (till exempel ”Internet”) eller trafik från särskilda IP-adresser eller undernät.

### <a name="considerations"></a>Överväganden
* Slutpunktskonfigurationen poolen är en del av poolens [nätverkskonfigurationen](/rest/api/batchservice/pool/add#NetworkConfiguration). Nätverkskonfigurationen kan du också inkludera inställningar för att ansluta till poolen till en [virtuella Azure-nätverket](batch-virtual-network.md). Om du ställer in pool i ett virtuellt nätverk kan du skapa NSG-regler som använder adressinställningarna i det virtuella nätverket.
* Du kan konfigurera flera NSG-regler när du konfigurerar en NAT-pool. Reglerna kontrolleras i prioritsordning. När villkoren för en regel uppfylls testas inga fler regler.


## <a name="example-deny-all-rdp-traffic"></a>Exempel: Neka alla RDP-trafik

Följande C# utdrag visar hur du konfigurerar RDP-slutpunkt på beräkningsnoder i en Windows-pool för att neka all nätverkstrafik. Slutpunkten använder en frontend-pool med portar i intervallet *60000 60099*. 

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

Python följande utdrag visar hur du konfigurerar SSH-slutpunkten på compute-noder i en Linux-pool för att neka alla internet-trafik. Slutpunkten använder en frontend-pool med portar i intervallet *4000 4100*. 

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

Följande C# utdrag visar hur du konfigurerar RDP-slutpunkt på compute-noder i en Windows-pool för att tillåta RDP-åtkomst från IP-adressen *198.51.100.7*. Den andra regeln NSG nekar trafik som inte matchar IP-adressen.

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

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Exempel: Tillåt SSH-trafik från ett specifikt undernät

Python följande utdrag visar hur du konfigurerar SSH-slutpunkten på compute-noder i en Linux-pool för att tillåta åtkomst från undernätet *192.168.1.0/24*. Den andra regeln NSG nekar trafik som inte matchar undernätet.

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

- Mer information om NSG-regler i Azure finns [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](../virtual-network/security-overview.md).

- En detaljerad översikt över Batch finns [utveckla storskaliga parallell compute lösningar med Batch](batch-api-basics.md).

