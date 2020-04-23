---
title: 'Azure ExpressRoute: Exempel på routerkonfiguration'
description: Den här sidan innehåller router config prover för Cisco och Juniper routrar.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: osamaz
ms.openlocfilehash: 3603bc45b920dc62eb8bf6f2eb8557f98e21638e
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024820"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Exempel på routerkonfiguration för att konfigurera och hantera routning
Den här sidan innehåller gränssnitts- och routningskonfigurationsexempel för Routrar i Cisco IOS-XE- och Juniper MX-serien när du arbetar med Azure ExpressRoute.

> [!IMPORTANT]
> Exempel på denna sida är enbart för vägledning. Du måste arbeta med leverantörens försäljnings-/tekniska team och ditt nätverksteam för att hitta lämpliga konfigurationer som uppfyller dina behov. Microsoft stöder inte problem relaterade till konfigurationer som anges på den här sidan. Kontakta enhetsleverantören för supportproblem.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>MTU- och TCP MSS-inställningar på routergränssnitt
Den maximala överföringsenheten (MTU) för ExpressRoute-gränssnittet är 1500, vilket är den typiska standardvärdet MTU för ett Ethernet-gränssnitt på en router. Om inte routern har en annan MTU som standard behöver du inte ange ett värde i routergränssnittet.

Till skillnad från en Azure VPN-gateway behöver inte TCP:s maximala segmentstorlek (MSS) för en ExpressRoute-krets anges.

Exempel på routerkonfiguration i den här artikeln gäller för alla peerings. Granska [ExpressRoute-peerings](expressroute-circuit-peerings.md) och [ExpressRoute-routningskrav](expressroute-routing.md) för mer information om routning.


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE-baserade routrar
Exemplen i det här avsnittet gäller alla routrar som kör IOS-XE OS-familjen.

### <a name="configure-interfaces-and-subinterfaces"></a>Konfigurera gränssnitt och undergränssnitt
Du behöver ett undergränssnitt per peering i varje router som du ansluter till Microsoft. Ett undergränssnitt kan identifieras med ett VLAN-ID eller ett staplat par VLAN-ID:er och en IP-adress.

**Definition av Dot1Q-gränssnitt**

Det här exemplet innehåller undergränssnittsdefinitionen för ett undergränssnitt med ett enda VLAN-ID. VLAN-ID:et är unikt per peering. Den sista oktetten på din IPv4-adress kommer alltid att vara ett udda nummer.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**QinQ-gränssnittsdefinition**

Det här exemplet innehåller undergränssnittsdefinitionen för ett undergränssnitt med två VLAN-ID:er. Det yttre VLAN-ID:t (s-taggen), om det används, förblir detsamma för alla peerings. Det inre VLAN-ID:t (c-taggen) är unikt per peering. Den sista oktetten på din IPv4-adress kommer alltid att vara ett udda nummer.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="set-up-ebgp-sessions"></a>Konfigurera eBGP-sessioner
Du måste konfigurera en BGP-session med Microsoft för varje peering. Konfigurera en BGP-session med hjälp av följande exempel. Om IPv4-adressen som du använde för ditt undergränssnitt var a.b.c.d blir IP-adressen för BGP-grannen (Microsoft) a.b.c.d+1. Den sista oktetten för BGP-grannens IPv4-adress kommer alltid att vara ett jämnt nummer.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Konfigurera prefix som ska annonseras över BGP-sessionen
Konfigurera routern så att den annonserar välja prefix till Microsoft med hjälp av följande exempel.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="route-maps"></a>Kartor över rutt
Använd ruttkartor och prefixlistor för att filtrera prefix som sprids i nätverket. Se följande exempel och se till att du har konfigurerat lämpliga prefixlistor.

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

### <a name="configure-bfd"></a>Konfigurera BFD

Du konfigurerar BFD på två ställen: en på gränssnittsnivå och en annan på BGP-nivå. Exemplet här är för QinQ-gränssnittet. 

    interface GigabitEthernet<Interface_Number>.<Number>
     bfd interval 300 min_rx 300 multiplier 3
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>
    
    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> fall-over bfd
     exit-address-family
    !


## <a name="juniper-mx-series-routers"></a>Routrar i Juniper MX-serien
Exemplen i det här avsnittet gäller för alla Juniper MX-seriens router.

### <a name="configure-interfaces-and-subinterfaces"></a>Konfigurera gränssnitt och undergränssnitt

**Definition av Dot1Q-gränssnitt**

Det här exemplet innehåller undergränssnittsdefinitionen för ett undergränssnitt med ett enda VLAN-ID. VLAN-ID:et är unikt per peering. Den sista oktetten på din IPv4-adress kommer alltid att vara ett udda nummer.

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


**QinQ-gränssnittsdefinition**

Det här exemplet innehåller undergränssnittsdefinitionen för ett undergränssnitt med två VLAN-ID:er. Det yttre VLAN-ID:t (s-taggen), om det används, förblir detsamma för alla peerings. Det inre VLAN-ID:t (c-taggen) är unikt per peering. Den sista oktetten på din IPv4-adress kommer alltid att vara ett udda nummer.

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

### <a name="set-up-ebgp-sessions"></a>Konfigurera eBGP-sessioner
Du måste konfigurera en BGP-session med Microsoft för varje peering. Konfigurera en BGP-session med hjälp av följande exempel. Om IPv4-adressen som du använde för ditt undergränssnitt var a.b.c.d blir IP-adressen för BGP-grannen (Microsoft) a.b.c.d+1. Den sista oktetten för BGP-grannens IPv4-adress kommer alltid att vara ett jämnt nummer.

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

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Konfigurera prefix som ska annonseras över BGP-sessionen
Konfigurera routern så att den annonserar välja prefix till Microsoft med hjälp av följande exempel.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter 
    <Prefix_to_be_advertised/Subnet_Mask> exact;
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


### <a name="route-policies"></a>Ruttprinciper
Du kan använda ruttkartor och prefixlistor för att filtrera prefix som sprids i nätverket. Se följande exempel och se till att du har rätt prefixlistor inställda.

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

### <a name="configure-bfd"></a>Konfigurera BFD
Konfigurera endast BFD under protokollets BGP-avsnitt.

    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
                bfd-liveness-detection {
                       minimum-interval 3000;
                       multiplier 3;
                }
            }                               
        }                                   
    }


## <a name="next-steps"></a>Nästa steg
Se [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md) för mer information.



