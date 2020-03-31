---
title: 'Azure ExpressRoute: Exempel på routerkonfiguration – NAT'
description: Den här sidan innehåller exempel på routerkonfiguration för Cisco- och Juniper-routrar.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.openlocfilehash: ef2fd40db422c459ca966e802344ef45f7ec01de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74072114"
---
# <a name="router-configuration-samples-to-set-up-and-manage-nat"></a>Exempel på routerkonfiguration för att konfigurera och hantera NAT

Den här sidan innehåller NAT-konfigurationsexempel för routrar i Cisco ASA- och Juniper SRX-serien när du arbetar med ExpressRoute. Dessa är endast avsedda att vara prover för vägledning och får inte användas som de är. Du kan arbeta med leverantören för att komma med lämpliga konfigurationer för nätverket.

> [!IMPORTANT]
> Prover på denna sida är avsedda att vara enbart för vägledning. Du måste arbeta med leverantörens försäljning / tekniska team och ditt nätverk team för att komma med lämpliga konfigurationer för att möta dina behov. Microsoft stöder inte problem relaterade till konfigurationer som anges på den här sidan. Du måste kontakta enhetsleverantören för supportproblem.
> 
> 

* Router konfigurationsexempel nedan gäller för Azure Public och Microsoft peerings. Du får inte konfigurera NAT för privat Azure-peering. Granska [ExpressRoute-peerings](expressroute-circuit-peerings.md) och [ExpressRoute NAT-krav](expressroute-nat.md) för mer information.

* Du måste använda separata NAT IP-pooler för anslutning till Internet och ExpressRoute. Om du använder samma NAT IP-pool över internet och ExpressRoute resulterar det i asymmetrisk routning och förlust av anslutning.


## <a name="cisco-asa-firewalls"></a>Cisco ASA-brandväggar
### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>PAT-konfiguration för trafik från kundnätverk till Microsoft
    object network MSFT-PAT
      range <SNAT-START-IP> <SNAT-END-IP>


    object-group network MSFT-Range
      network-object <IP> <Subnet_Mask>

    object-group network on-prem-range-1
      network-object <IP> <Subnet-Mask>

    object-group network on-prem-range-2
      network-object <IP> <Subnet-Mask>

    object-group network on-prem
      network-object object on-prem-range-1
      network-object object on-prem-range-2

    nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>PAT-konfiguration för trafik från Microsoft till kundnätverk

**Gränssnitt och riktning:**

    Source Interface (where the traffic enters the ASA): inside
    Destination Interface (where the traffic exits the ASA): outside

**Konfiguration:**

NAT-pool:

    object network outbound-PAT
        host <NAT-IP>

Målserver:

    object network Customer-Network
        network-object <IP> <Subnet-Mask>

Objektgrupp för kundens IP-adresser

    object-group network MSFT-Network-1
        network-object <MSFT-IP> <Subnet-Mask>

    object-group network MSFT-PAT-Networks
        network-object object MSFT-Network-1

NAT-kommandon:

    nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network


## <a name="juniper-srx-series-routers"></a>Routrar i Juniper SRX-serien
### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1. Skapa redundanta Ethernet-gränssnitt för klustret
    interfaces {
        reth0 {
            description "To Internal Network";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 1;
            }
            unit 100 {
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
        reth1 {
            description "To Microsoft via Edge Router";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 2;
            }
            unit 100 {
                description "To Microsoft via Edge Router";
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
    }


### <a name="2-create-two-security-zones"></a>2. Skapa två säkerhetszoner
* Förtroendezon för internt nätverk och untrust-zon för externa nätverksvändiga edgerouter
* Tilldela lämpliga gränssnitt till zonerna
* Tillåt tjänster i gränssnitten

    säkerhet { zoner { säkerhet-zon Förtroende { värd-inkommande trafik { system-tjänster { ping;                   } protokoll { bgp;                   } } gränssnitt { reth0.100;               } } } säkerhetszon Untrust { host-inbound-traffic { system-services { ping; } }                   } protokoll { bgp;                   } } gränssnitt { reth1.100;               }           }       }   }


### <a name="3-create-security-policies-between-zones"></a>3. Skapa säkerhetsprinciper mellan zoner
    security {
        policies {
            from-zone Trust to-zone Untrust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
            from-zone Untrust to-zone Trust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
        }
    }


### <a name="4-configure-nat-policies"></a>4. Konfigurera NAT-principer
* Skapa två NAT-pooler. Den ena används för att NAT-trafik ska skickas ut till Microsoft och andra från Microsoft till kunden.
* Skapa regler för NAT respektive trafik
  
       security {
           nat {
               source {
                   pool SNAT-To-ExpressRoute {
                       routing-instance {
                           External-ExpressRoute;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   pool SNAT-From-ExpressRoute {
                       routing-instance {
                           Internal;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   rule-set Outbound_NAT {
                       from routing-instance Internal;
                       to routing-instance External-ExpressRoute;
                       rule SNAT-Out {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-To-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
                   rule-set Inbound-NAT {
                       from routing-instance External-ExpressRoute;
                       to routing-instance Internal;
                       rule SNAT-In {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-From-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
               }
           }
       }

### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. Konfigurera BGP för att annonsera selektiva prefix i varje riktning
Se exempel i exempel på [routningskonfiguration.](expressroute-config-samples-routing.md)

### <a name="6-create-policies"></a>6. Skapa policyer
    routing-options {
                  autonomous-system <Customer-ASN>;
    }
    policy-options {
        prefix-list Microsoft-Prefixes {
            <IP-Address/Subnet-Mask;
            <IP-Address/Subnet-Mask;
        }
        prefix-list private-ranges {
            10.0.0.0/8;
            172.16.0.0/12;
            192.168.0.0/16;
            100.64.0.0/10;
        }
        policy-statement Advertise-NAT-Pools {
            from {
                protocol static;
                route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
            }
            then accept;
        }
        policy-statement Accept-from-Microsoft {
            term 1 {
                from {
                    instance External-ExpressRoute;
                    prefix-list-filter Microsoft-Prefixes orlonger;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
        policy-statement Accept-from-Internal {
            term no-private {
                from {
                    instance Internal;
                    prefix-list-filter private-ranges orlonger;
                }
                then reject;
            }
            term bgp {
                from {
                    instance Internal;
                    protocol bgp;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
    }
    routing-instances {
        Internal {
            instance-type virtual-router;
            interface reth0.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Microsoft;
            }
            protocols {
                bgp {
                    group customer {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-ASN-1>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
        External-ExpressRoute {
            instance-type virtual-router;
            interface reth1.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Internal;
            }
            protocols {
                bgp {
                    group edge-router {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-Public-ASN>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
    }

## <a name="next-steps"></a>Nästa steg
Se [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md) för mer information.

