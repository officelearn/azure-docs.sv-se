---
title: Routerkonfigurationer – Azure ExpressRoute | Microsoft Docs
description: Den här sidan innehåller router config-exempel för Cisco och Juniper-routrar.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 2d7fb060896de8df266489451a11ba343760c747
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53079967"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Routerkonfigurationer att konfigurera och hantera routning
Den här sidan innehåller gränssnittet och routning Konfigurationsexempel för Cisco IOS-XE- och Juniper MX serie routrar när du arbetar med ExpressRoute. Dessa är avsedda att vara exempel endast vägledning och får inte användas eftersom. Du kan arbeta med leverantören om att få fram rätt konfigurationer för ditt nätverk. 

> [!IMPORTANT]
> Exempel på den här sidan är avsedda att vara helt och hållet anvisningar. Du måste arbeta med leverantörens försäljning / tekniska team och ditt nätverksteam och få fram rätt konfigurationer för att uppfylla dina behov. Microsoft stöder inte problem som rör konfigurationer som anges i den här sidan. För supportärenden måste du kontakta leverantören av enheten.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Inställningar för MTU och TCP-MSS på router-gränssnitt
* MTU för ExpressRoute-gränssnittet är 1500, som är typiska standard-MTU för ett Ethernet-gränssnitt på en router. Om routern inte har en annan MTU som standard, är det du behöver inte ange ett värde på router-gränssnitt.
* Till skillnad från Azure VPN Gateway behöver TCP-MSS för en ExpressRoute-krets inte anges.

Routerkonfigurationer nedan gäller för alla peerkopplingar. Granska [ExpressRoute-peerkopplingar](expressroute-circuit-peerings.md) och [ExpressRoute-routningskrav](expressroute-routing.md) för mer information om routning.


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE baserat routrar
Exemplen i det här avsnittet gäller för en router som kör IOS-XE-OS-familj.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Konfigurera och underordnade gränssnitt
Du kommer att behöva ett gränssnitt för sub per peering i varje dator som du ansluter till Microsoft. Ett sub-gränssnitt kan identifieras med ett VLAN-ID eller ett stående par med VLAN-ID och en IP-adress.

**Dot1Q gränssnittsdefinitionen**

Det här exemplet innehåller underordnade gränssnittsdefinitionen för en underordnad gränssnittet med ett enda VLAN-ID. VLAN-ID är unikt för varje peering. Den sista oktetten av IPv4-adress kommer alltid att ett udda tal.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**QinQ gränssnittsdefinitionen**

Det här exemplet innehåller underordnade gränssnittsdefinitionen för ett underordnade gränssnitt med en två VLAN-ID: N. Yttre VLAN-ID (s-tagg), om använt detsamma mellan alla peer-kopplingar. Inre VLAN-ID (c-tagg) är unikt för varje peering. Den sista oktetten av IPv4-adress kommer alltid att ett udda tal.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2. Konfigurera eBGP-sessioner
Du måste konfigurera en BGP-session med Microsoft för varje peering. Exemplet nedan kan du konfigurera en BGP-session med Microsoft. Om IPv4-adress som du använde för sub-gränssnittet var a.b.c.d, blir a.b.c.d+1 med IP-adressen för BGP-Grannens (Microsoft). Den sista oktetten av IPv4-adress för BGP-Grannens kommer alltid att ett jämnt tal.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Konfigurera prefix som ska annonseras via BGP-sessionen
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
Du kan använda route-kartor och prefix som visas på filter-prefix som sprids till nätverket. Du kan använda exemplet nedan för att utföra uppgiften. Kontrollera att du har rätt prefix listor installationen.

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


## <a name="juniper-mx-series-routers"></a>Juniper MX serie routrar
Exemplen i det här avsnittet gäller för alla Juniper MX serie routrar.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Konfigurera och underordnade gränssnitt

**Dot1Q gränssnittsdefinitionen**

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


**QinQ gränssnittsdefinitionen**

Det här exemplet innehåller underordnade gränssnittsdefinitionen för ett underordnade gränssnitt med en två VLAN-ID: N. Yttre VLAN-ID (s-tagg), om använt detsamma mellan alla peer-kopplingar. Inre VLAN-ID (c-tagg) är unikt för varje peering. Den sista oktetten av IPv4-adress kommer alltid att ett udda tal.

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

### <a name="2-setting-up-ebgp-sessions"></a>2. Konfigurera eBGP-sessioner
Du måste konfigurera en BGP-session med Microsoft för varje peering. Exemplet nedan kan du konfigurera en BGP-session med Microsoft. Om IPv4-adress som du använde för sub-gränssnittet var a.b.c.d, blir a.b.c.d+1 med IP-adressen för BGP-Grannens (Microsoft). Den sista oktetten av IPv4-adress för BGP-Grannens kommer alltid att ett jämnt tal.

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

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Konfigurera prefix som ska annonseras via BGP-sessionen
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
Du kan använda route-kartor och prefix som visas på filter-prefix som sprids till nätverket. Du kan använda exemplet nedan för att utföra uppgiften. Kontrollera att du har rätt prefix listor installationen.

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

