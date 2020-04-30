---
title: 'Azure-ExpressRoute: exempel på router-konfiguration'
description: Den här sidan innehåller konfigurations exempel för routern för Cisco-och Juniper-routrar.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: osamaz
ms.openlocfilehash: 3603bc45b920dc62eb8bf6f2eb8557f98e21638e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024820"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Konfigurations exempel för routern för att konfigurera och hantera routning
Den här sidan innehåller konfigurations exempel för gränssnitt och routning för routrar med Cisco IOS-XE och Juniper MX-serien när du arbetar med Azure ExpressRoute.

> [!IMPORTANT]
> Exempel på den här sidan är helt för vägledning. Du måste arbeta med leverantörens försäljnings-/teknik team och nätverks teamet för att hitta lämpliga konfigurationer som passar dina behov. Microsoft stöder inte problem som rör konfigurationer som anges på den här sidan. Kontakta enhets leverantören för support ärenden.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Inställningar för MTU-och TCP-MSS på routergränssnitt
Den högsta överförings enheten (MTU) för ExpressRoute-gränssnittet är 1500, vilket är den typiska standard-MTUen för ett Ethernet-gränssnitt på en router. Om inte din router har en annan MTU som standard, behöver du inte ange något värde i routergränssnitten.

Till skillnad från en Azure VPN-gateway behöver TCP maximal segment storlek (MSS) för en ExpressRoute-krets inte anges.

Konfigurations exemplen för routern i den här artikeln gäller alla peer-kopplingar. Granska [ExpressRoute-peering](expressroute-circuit-peerings.md) och [krav för ExpressRoute-routning](expressroute-routing.md) för mer information om routning.


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS – XE-baserade routrar
Exemplen i det här avsnittet gäller för alla routrar som kör IOS-XE OS-familjen.

### <a name="configure-interfaces-and-subinterfaces"></a>Konfigurera gränssnitt och under gränssnitt
Du behöver ett under gränssnitt per peering i varje router som du ansluter till Microsoft. Ett under gränssnitt kan identifieras med ett VLAN-ID eller ett stackat par med VLAN-ID: n och en IP-adress.

**Definition av Dot1Q-gränssnitt**

Det här exemplet tillhandahåller del gränssnitts definitionen för ett under gränssnitt med ett enda VLAN-ID. VLAN-ID: t är unikt per peering. Den sista oktetten i IPv4-adressen är alltid ett udda nummer.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**Definition av QinQ-gränssnitt**

Det här exemplet tillhandahåller del gränssnitts definitionen för ett under gränssnitt med två VLAN-ID: n. Det yttre VLAN-ID: t (s-tag), om det används, är detsamma för alla peer-kopplingar. Det interna VLAN-ID: t (c-tag) är unikt per peering. Den sista oktetten i IPv4-adressen är alltid ett udda nummer.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="set-up-ebgp-sessions"></a>Konfigurera eBGP-sessioner
Du måste konfigurera en BGP-session med Microsoft för varje peering. Konfigurera en BGP-session med hjälp av följande exempel. Om IPv4-adressen som du använde för under gränssnittet var a. b. c. d, kommer IP-adressen för BGP-grannen (Microsoft) att vara a. b. c. d + 1. Den sista oktetten i BGP-grannens IPv4-adress är alltid ett jämnt tal.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Konfigurera prefix som ska annonseras via BGP-sessionen
Konfigurera routern så att den annonserar valda prefix till Microsoft med hjälp av följande exempel.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="route-maps"></a>Väg kartor
Använd listorna Route Maps och prefix för att filtrera prefix som sprids till nätverket. Se följande exempel och se till att du har rätt prefix listor konfigurerade.

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

Du konfigurerar BFD på två platser: en på gränssnitts nivå och en annan på BGP-nivå. Exemplet här är för QinQ-gränssnittet. 

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


## <a name="juniper-mx-series-routers"></a>Juniper MX-serie routrar
Exemplen i det här avsnittet gäller för alla Juniper MX Series-routrar.

### <a name="configure-interfaces-and-subinterfaces"></a>Konfigurera gränssnitt och under gränssnitt

**Definition av Dot1Q-gränssnitt**

Det här exemplet tillhandahåller del gränssnitts definitionen för ett under gränssnitt med ett enda VLAN-ID. VLAN-ID: t är unikt per peering. Den sista oktetten i IPv4-adressen är alltid ett udda nummer.

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


**Definition av QinQ-gränssnitt**

Det här exemplet tillhandahåller del gränssnitts definitionen för ett under gränssnitt med två VLAN-ID: n. Det yttre VLAN-ID: t (s-tag), om det används, är detsamma för alla peer-kopplingar. Det interna VLAN-ID: t (c-tag) är unikt per peering. Den sista oktetten i IPv4-adressen är alltid ett udda nummer.

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
Du måste konfigurera en BGP-session med Microsoft för varje peering. Konfigurera en BGP-session med hjälp av följande exempel. Om IPv4-adressen som du använde för under gränssnittet var a. b. c. d, kommer IP-adressen för BGP-grannen (Microsoft) att vara a. b. c. d + 1. Den sista oktetten i BGP-grannens IPv4-adress är alltid ett jämnt tal.

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

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Konfigurera prefix som ska annonseras via BGP-sessionen
Konfigurera routern så att den annonserar valda prefix till Microsoft med hjälp av följande exempel.

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


### <a name="route-policies"></a>Flödes principer
Du kan använda listorna Route Maps och prefix för att filtrera prefix som sprids till nätverket. Se följande exempel och se till att du har rätt prefix listor konfigurerade.

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
Konfigurera BFD under protokollet BGP-avsnitt.

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



