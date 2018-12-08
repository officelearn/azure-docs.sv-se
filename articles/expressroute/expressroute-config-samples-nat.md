---
title: Routerkonfiguration exempel - NAT - Azure ExpressRoute | Microsoft Docs
description: Den här sidan innehåller routerkonfigurationer för Cisco och Juniper-routrar.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 9764a03b0f3a3f70e59097359d5a714da821d3b1
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53105996"
---
# <a name="router-configuration-samples-to-set-up-and-manage-nat"></a>Routerkonfigurationer att konfigurera och hantera NAT

Den här sidan innehåller NAT Konfigurationsexempel för Cisco ASA och Juniper SRX serie routrar när du arbetar med ExpressRoute. Dessa är avsedda att vara exempel endast vägledning och får inte användas eftersom. Du kan arbeta med leverantören om att få fram rätt konfigurationer för ditt nätverk.

> [!IMPORTANT]
> Exempel på den här sidan är avsedda att vara helt och hållet anvisningar. Du måste arbeta med leverantörens försäljning / tekniska team och ditt nätverksteam och få fram rätt konfigurationer för att uppfylla dina behov. Microsoft stöder inte problem som rör konfigurationer som anges i den här sidan. För supportärenden måste du kontakta leverantören av enheten.
> 
> 

* Routerkonfigurationer nedan gäller för Azure offentlig och Microsoft-peerings. Inte måste du konfigurera NAT för Azures privata peering. Granska [ExpressRoute-peerkopplingar](expressroute-circuit-peerings.md) och [Expressroutes NAT-krav](expressroute-nat.md) för mer information.

* Du måste använda separata NAT IP-adresspooler för anslutning till internet och ExpressRoute. Använda samma NAT IP-pool i alla internet och ExpressRoute leder till asymmetrisk Routning och förlust av anslutning.


## <a name="cisco-asa-firewalls"></a>Cisco ASA-brandväggar
### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>PATRIK konfiguration för trafik från kundens nätverk till Microsoft
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

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>PATRIK konfiguration för trafik från Microsoft till kundens nätverk

**Gränssnitt och riktning:**

    Source Interface (where the traffic enters the ASA): inside
    Destination Interface (where the traffic exits the ASA): outside

**Konfiguration:**

NAT-Pool:

    object network outbound-PAT
        host <NAT-IP>

Målserver:

    object network Customer-Network
        network-object <IP> <Subnet-Mask>

Objekt-grupp för kundens IP-adresser

    object-group network MSFT-Network-1
        network-object <MSFT-IP> <Subnet-Mask>

    object-group network MSFT-PAT-Networks
        network-object object MSFT-Network-1

NAT-kommandon:

    nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network


## <a name="juniper-srx-series-routers"></a>Juniper SRX serie routrar
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
* Förlitar sig på zon för interna nätverket och Untrust zon för externt nätverk mot Edge-routrar
* Tilldela lämpliga gränssnitt till zoner
* Tillåt tjänster på gränssnitt

    Security {zoner {säkerhetszon förtroende {-inkommande-värdtrafik {-systemtjänster {ping;                   } protokoll {bgp;                   gränssnitt för}} {reth0.100;               }} säkerhetszon Untrust {-inkommande-värdtrafik {-systemtjänster {ping;                   } protokoll {bgp;                   gränssnitt för}} {reth1.100;               }           }       }   }


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
* Skapa två NAT-pooler. En används för att utgående trafik till Microsoft och andra NAT-trafik från Microsoft till kunden.
* Skapa regler för att NAT respektive trafiken
  
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

### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. Konfigurera BGP för att annonsera selektiv prefix i varje riktning
Referera till exempel i [routning Konfigurationsexempel ](expressroute-config-samples-routing.md) sidan.

### <a name="6-create-policies"></a>6. Skapa principer
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

