---
title: 'Azure ExpressRoute: Exempel på routerkonfiguration'
description: Den här sidan innehåller router config prover för Cisco och Juniper routrar.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: osamaz
ms.openlocfilehash: 5304aefaf3ad70bb552b4b0d1b26fcce9867c9c0
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397732"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Exempel på routerkonfiguration för att konfigurera och hantera routning
Den här sidan innehåller gränssnitts- och routningskonfigurationsexempel för Cisco IOS-XE- och Juniper MX-serieroutrar när du arbetar med ExpressRoute. Dessa är endast avsedda att vara prover för vägledning och får inte användas som de är. Du kan arbeta med leverantören för att komma med lämpliga konfigurationer för nätverket. 

> [!IMPORTANT]
> Prover på denna sida är avsedda att vara enbart för vägledning. Du måste arbeta med leverantörens försäljning / tekniska team och ditt nätverk team för att komma med lämpliga konfigurationer för att möta dina behov. Microsoft stöder inte problem relaterade till konfigurationer som anges på den här sidan. Du måste kontakta enhetsleverantören för supportproblem.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>MTU- och TCP MSS-inställningar på routergränssnitt
* MTU för ExpressRoute-gränssnittet är 1500, vilket är den typiska standardvärdet MTU för ett Ethernet-gränssnitt på en router. Om inte routern har en annan MTU som standard behöver du inte ange ett värde i routergränssnittet.
* Till skillnad från en Azure VPN Gateway behöver TCP MSS för en ExpressRoute-krets inte anges.

Router konfigurationsexempel nedan gäller för alla peerings. Granska [ExpressRoute-peerings](expressroute-circuit-peerings.md) och [ExpressRoute-routningskrav](expressroute-routing.md) för mer information om routning.


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE-baserade routrar
Exemplen i det här avsnittet gäller för alla routrar som kör IOS-XE OS-familjen.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Konfigurera gränssnitt och delgränssnitt
Du behöver ett undergränssnitt per peering i varje router som du ansluter till Microsoft. Ett undergränssnitt kan identifieras med ett VLAN-ID eller ett staplat par VLAN-ID:n och en IP-adress.

**Definition av Dot1Q-gränssnitt**

Det här exemplet innehåller definitionen av undergränssnitt för ett undergränssnitt med ett enda VLAN-ID. VLAN-ID:et är unikt per peering. Den sista oktetten på din IPv4-adress kommer alltid att vara ett udda nummer.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**QinQ-gränssnittsdefinition**

Det här exemplet innehåller definitionen av undergränssnitt för ett undergränssnitt med två VLAN-ID:n. Det yttre VLAN-ID:t (s-taggen), om det används, förblir detsamma för alla peerings. Det inre VLAN-ID:t (c-taggen) är unikt per peering. Den sista oktetten på din IPv4-adress kommer alltid att vara ett udda nummer.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2. Ställa in eBGP-sessioner
Du måste konfigurera en BGP-session med Microsoft för varje peering. Med exemplet nedan kan du konfigurera en BGP-session med Microsoft. Om IPv4-adressen som du använde för undergränssnittet var a.b.c.d, kommer IP-adressen för BGP-grannen (Microsoft) att vara a.b.c.d+1. Den sista oktetten för BGP-grannens IPv4-adress kommer alltid att vara ett jämnt nummer.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Ställa in prefix som ska annonseras under BGP-sessionen
Du kan konfigurera routern så att den annonserar om utvalda prefix till Microsoft. Du kan göra det med hjälp av exemplet nedan.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. Ruttkartor
Du kan använda ruttkartor och prefixlistor för att filtrera prefix som sprids i nätverket. Du kan använda exemplet nedan för att utföra uppgiften. Kontrollera att du har lämplig konfiguration av prefixlistor.

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

### <a name="5-configuring-bfd"></a>5. Konfigurera BFD

Du kommer att konfigurera BFD på två ställen. En på gränssnittsnivå och annan på BGP-nivå. Exemplet nedan är för QinQ-gränssnittet. 

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
Exemplen i det här avsnittet gäller för alla routrar i Juniper MX-serien.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Konfigurera gränssnitt och delgränssnitt

**Definition av Dot1Q-gränssnitt**

Det här exemplet innehåller definitionen av undergränssnitt för ett undergränssnitt med ett enda VLAN-ID. VLAN-ID:et är unikt per peering. Den sista oktetten på din IPv4-adress kommer alltid att vara ett udda nummer.

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

Det här exemplet innehåller definitionen av undergränssnitt för ett undergränssnitt med två VLAN-ID:n. Det yttre VLAN-ID:t (s-taggen), om det används, förblir detsamma för alla peerings. Det inre VLAN-ID:t (c-taggen) är unikt per peering. Den sista oktetten på din IPv4-adress kommer alltid att vara ett udda nummer.

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
Du måste konfigurera en BGP-session med Microsoft för varje peering. Med exemplet nedan kan du konfigurera en BGP-session med Microsoft. Om IPv4-adressen som du använde för undergränssnittet var a.b.c.d, kommer IP-adressen för BGP-grannen (Microsoft) att vara a.b.c.d+1. Den sista oktetten för BGP-grannens IPv4-adress kommer alltid att vara ett jämnt nummer.

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

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Ställa in prefix som ska annonseras under BGP-sessionen
Du kan konfigurera routern så att den annonserar om utvalda prefix till Microsoft. Du kan göra det med hjälp av exemplet nedan.

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


### <a name="4-route-policies"></a>4. Rutt politik
Du kan använda ruttkartor och prefixlistor för att filtrera prefix som sprids i nätverket. Du kan använda exemplet nedan för att utföra uppgiften. Kontrollera att du har lämplig konfiguration av prefixlistor.

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

### <a name="4-configuring-bfd"></a>4. Konfigurera BFD
Du kommer endast att konfigurera BFD under protokollets BGP-avsnitt.

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

## <a name="next-steps"></a>Efterföljande moment
Se [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md) för mer information.



