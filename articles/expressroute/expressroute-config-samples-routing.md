---
title: ExpressRoute kunden router configuration prover | Microsoft Docs
description: "Den här sidan innehåller router config-exempel för Cisco och Juniper routrar."
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: 564826bc-017a-4683-a385-37c9fa814948
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
ms.openlocfilehash: 032e584dc5abf59e9e3e8d80673b402f1fbf721b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Router configuration exempel för att konfigurera och hantera routning
Den här sidan innehåller gränssnitt och routning configuration-exempel för Cisco IOS-XE och Juniper MX serien routrar. Dessa är avsedda att prover för endast vägledning och får inte användas eftersom. Du kan arbeta med leverantören för att få fram lämpliga konfigurationerna för nätverket. 

> [!IMPORTANT]
> Exempel på den här sidan är avsedda att vara rent anvisningar. Du måste arbeta med leverantörens försäljning / tekniska teamet och teamet nätverk för att få fram lämpliga konfigurationer för att uppfylla dina behov. Microsoft stöder inte problem relaterade till konfigurationer som anges i den här sidan. För att lösa problem måste du kontakta enhetsleverantören av.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Inställningarna för MTU och TCP-MSS på router-gränssnitt
* MTU för ExpressRoute-gränssnittet är 1500, som är typiska standard-MTU för ett Ethernet-gränssnitt på en router. Om routern har en annan MTU som standard, finns det inget behov av att ange ett värde på router-gränssnitt.
* Till skillnad från en Azure VPN-Gateway behöver inte TCP-MSS för en ExpressRoute-krets anges.

Router configuration exemplen nedan gäller för alla peerkopplingar. Granska [ExpressRoute peerkopplingar](expressroute-circuit-peerings.md) och [ExpressRoute routningskrav](expressroute-routing.md) för mer information om routning.


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE baserat routrar
Exemplen i det här avsnittet gäller för en router som kör IOS XE OS-familjen.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Konfigurera och underordnade gränssnitt
Du behöver en sub-gränssnittet per peering i varje router som du ansluter till Microsoft. En sub-gränssnittet kan identifieras med ett VLAN-ID eller ett staplat par med VLAN-ID och en IP-adress.

**Dot1Q gränssnittsdefinition**

Det här exemplet innehåller underordnade gränssnittsdefinitionen för en underordnad gränssnittet med ett enda VLAN-ID. VLAN-ID är unikt för varje peering. Den sista oktetten av IPv4-adress kommer alltid att ett udda tal.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**QinQ gränssnittsdefinition**

Det här exemplet innehåller underordnade gränssnittsdefinition för en underordnad gränssnitt med två VLAN-ID. Yttre VLAN-ID (s-tagg), om inte över alla peerkopplingar. Inre VLAN-ID (c-tagg) är unik för varje peering. Den sista oktetten av IPv4-adress kommer alltid att ett udda tal.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2. Ställa in eBGP-sessioner
Du måste konfigurera en BGP-session med Microsoft för varje peering. Exemplet nedan kan du konfigurera en BGP-session med Microsoft. Om IPv4-adress som du använde för sub-gränssnittet har a.b.c.d, blir a.b.c.d+1 med IP-adressen för BGP-Grannens (Microsoft). Den sista oktetten av IPv4-adress för BGP-Grannens kommer alltid att ett jämnt tal.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Konfigurera prefix för annonseras över BGP-sessionen
Du kan konfigurera routern att annonsera väljer prefix till Microsoft. Du kan göra det med hjälp av exemplet nedan.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. Väg maps
Du kan använda vägen maps och prefix som visar filter prefix sprids till nätverket. Du kan använda exemplet nedan för att utföra aktiviteten. Se till att du har rätt prefix listor installationen.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !


## <a name="juniper-mx-series-routers"></a>Juniper MX serien routrar
Exemplen i det här avsnittet gäller för alla serier Juniper MX-routrar.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Konfigurera och underordnade gränssnitt

**Dot1Q gränssnittsdefinition**

Det här exemplet innehåller underordnade gränssnittsdefinitionen för en underordnad gränssnittet med ett enda VLAN-ID. VLAN-ID är unikt för varje peering. Den sista oktetten av IPv4-adress kommer alltid att ett udda tal.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


**QinQ gränssnittsdefinition**

Det här exemplet innehåller underordnade gränssnittsdefinition för en underordnad gränssnitt med två VLAN-ID. Yttre VLAN-ID (s-tagg), om inte över alla peerkopplingar. Inre VLAN-ID (c-tagg) är unik för varje peering. Den sista oktetten av IPv4-adress kommer alltid att ett udda tal.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="2-setting-up-ebgp-sessions"></a>2. Ställa in eBGP-sessioner
Du måste konfigurera en BGP-session med Microsoft för varje peering. Exemplet nedan kan du konfigurera en BGP-session med Microsoft. Om IPv4-adress som du använde för sub-gränssnittet har a.b.c.d, blir a.b.c.d+1 med IP-adressen för BGP-Grannens (Microsoft). Den sista oktetten av IPv4-adress för BGP-Grannens kommer alltid att ett jämnt tal.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Konfigurera prefix för annonseras över BGP-sessionen
Du kan konfigurera routern att annonsera väljer prefix till Microsoft. Du kan göra det med hjälp av exemplet nedan.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="4-route-maps"></a>4. Väg maps
Du kan använda vägen maps och prefix som visar filter prefix sprids till nätverket. Du kan använda exemplet nedan för att utföra aktiviteten. Se till att du har rätt prefix listor installationen.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
        prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

## <a name="next-steps"></a>Nästa steg
Se [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md) för mer information.

