---
title: Distribuera Azure Datautforskaren till din Virtual Network (förhands granskning)
description: Lär dig hur du distribuerar Azure Datautforskaren till din Virtual Network
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 8dec673408b706a92a29f418af3bef4cc05a8d2d
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668575"
---
# <a name="deploy-azure-data-explorer-into-your-virtual-network-preview"></a>Distribuera Azure Datautforskaren till din Virtual Network (förhands granskning)

I den här artikeln förklaras de resurser som finns när du distribuerar ett Azure Datautforskaren-kluster till en anpassad Azure-Virtual Network. Den här informationen hjälper dig att distribuera ett kluster till ett undernät i din Virtual Network (VNet). Mer information om virtuella Azure-nätverk finns i [Vad är azure Virtual Network?](/azure/virtual-network/virtual-networks-overview)

   ![VNet-diagram](media/vnet-deployment/vnet-diagram.png)

Azure Datautforskaren stöder distribution av ett kluster till ett undernät i Virtual Network (VNet). Med den här funktionen kan du:

* Tillämpa regler för [nätverks säkerhets grupper](/azure/virtual-network/security-overview) (NSG) på Azure datautforskaren Cluster-trafiken.
* Anslut ditt lokala nätverk till Azure Datautforskaren klustrets undernät.
* Skydda dina data anslutnings källor ([händelsehubben](/azure/event-hubs/event-hubs-about) och [Event Grid](/azure/event-grid/overview)) med [tjänst slut punkter](/azure/virtual-network/virtual-network-service-endpoints-overview).

> [!NOTE]
> Virtual Network integrering och distribution är i förhands gransknings läge. Öppna ett [support ärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)om du vill aktivera den här funktionen.

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>Få åtkomst till ditt Azure Datautforskaren-kluster i ditt VNet

Du kan komma åt ditt Azure Datautforskaren-kluster med följande IP-adresser för varje tjänst (motor-och data hanterings tjänster):

* **Privat IP**: används för att komma åt klustret i VNet.
* **Offentlig IP**: används för åtkomst till klustret utanför det virtuella nätverket för hantering och övervakning, och som käll adress för utgående anslutningar som startas från klustret.

Följande DNS-poster skapas för att få åtkomst till tjänsten: 

* `[clustername].[geo-region].kusto.windows.net` (motor) `ingest-[clustername].[geo-region].kusto.windows.net` (data hantering) mappas till den offentliga IP-adressen för varje tjänst. 

* `private-[clustername].[geo-region].kusto.windows.net` (motor) `private-ingest-[clustername].[geo-region].kusto.windows.net` (data hantering) mappas till den privata IP-adressen för varje tjänst.

## <a name="plan-subnet-size-in-your-vnet"></a>Planera under näts storlek i ditt VNet

Storleken på det undernät som används som värd för ett Azure Datautforskaren-kluster kan inte ändras efter att under nätet har distribuerats. I ditt VNet använder Azure Datautforskaren en privat IP-adress för varje virtuell dator och två privata IP-adresser för de interna belastningsutjämnare (motor-och data hantering). Azure-nätverk använder också fem IP-adresser för varje undernät. Azure Datautforskaren etablerar två virtuella datorer för data hanterings tjänsten. Virtuella datorer i motor tjänsten har allokerats per skalnings kapacitet för användar konfiguration.

Totalt antal IP-adresser:

| Användning | Antal adresser |
| --- | --- |
| Motor tjänst | 1 per instans |
| Data hanterings tjänst | 2 |
| Interna belastningsutjämnare | 2 |
| Reserverade Azure-adresser | 5 |
| **Totalt** | **#engine_instances + 9** |

> [!IMPORTANT]
> Under näts storleken måste planeras i förväg eftersom den inte kan ändras efter att Azure Datautforskaren distribuerats. Därför är det lämpligt att reservera en under näts storlek.

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>Tjänst slut punkter för att ansluta till Azure Datautforskaren

Med [tjänst slut punkter i Azure](/azure/virtual-network/virtual-network-service-endpoints-overview) kan du skydda dina Azure-resurser för flera klienter i ditt virtuella nätverk.
Genom att distribuera Azure Datautforskaren-kluster i under nätet kan du konfigurera data anslutningar med [händelsehubben](/azure/event-hubs/event-hubs-about) eller [Event Grid](/azure/event-grid/overview) samtidigt som du begränsar de underliggande resurserna för Azure datautforskaren-undernätet.

> [!NOTE]
> När du använder EventGrid-installation med [Storage](/azure/storage/common/storage-introduction) och [Event Hub], kan det lagrings konto som används i prenumerationen låsas med tjänstens slut punkter till azures datautforskaren under nätet samtidigt som Trusted Azure Platform Services tillåts i [brand Väggs konfigurationen](/azure/storage/common/storage-network-security), men Händelsehubben inte kan aktivera tjänst slut punkten eftersom den inte har stöd för betrodda [Azure Platform Services](/azure/event-hubs/event-hubs-service-endpoints).

## <a name="dependencies-for-vnet-deployment"></a>Beroenden för VNet-distribution

### <a name="network-security-groups-configuration"></a>Konfiguration av nätverks säkerhets grupper

[Nätverks säkerhets grupper (NSG)](/azure/virtual-network/security-overview) ger möjlighet att kontrol lera nätverks åtkomst i ett VNet. Azure Datautforskaren kan nås med två slut punkter: HTTPs (443) och TDS (1433). Följande NSG-regler måste konfigureras för att tillåta åtkomst till dessa slut punkter för hantering, övervakning och lämplig drift av klustret.

#### <a name="inbound-nsg-configuration"></a>Inkommande NSG-konfiguration

| **Använd**   | **Som**   | **Till**   | **Protokoll**   |
| --- | --- | --- | --- |
| Förvaltning  |[ADX-hantering adresser](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement (ServiceTag) | ADX-undernät: 443  | TCP  |
| Hälsoövervakning  | [ADX för hälso övervakning](#health-monitoring-addresses)  | ADX-undernät: 443  | TCP  |
| Intern kommunikation med ADX  | ADX-undernät: alla portar  | ADX-undernät: alla portar  | Alla  |
| Tillåt inkommande Azure Load Balancer (hälso avsökning)  | AzureLoadBalancer  | ADX-undernät: 80443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>Utgående NSG-konfiguration

| **Använd**   | **Som**   | **Till**   | **Protokoll**   |
| --- | --- | --- | --- |
| Beroende av Azure Storage  | ADX-undernät  | Lagring: 443  | TCP  |
| Beroende av Azure Data Lake  | ADX-undernät  | AzureDataLake: 443  | TCP  |
| EventHub-inmatning och tjänst övervakning  | ADX-undernät  | EventHub: 443, 5671  | TCP  |
| Publicera mått  | ADX-undernät  | AzureMonitor: 443 | TCP  |
| Hämtning av Azure Monitor konfiguration  | ADX-undernät  | [Azure Monitor slut punkts adresser för konfiguration](#azure-monitor-configuration-endpoint-addresses): 443 | TCP  |
| Active Directory (om tillämpligt) | ADX-undernät | AzureActiveDirectory: 443 | TCP |
| Certifikat utfärdare | ADX-undernät | Internet: 80 | TCP |
| Intern kommunikation  | ADX-undernät  | ADX-undernät: alla portar  | Alla  |
| Portar som används för `sql\_request`-och `http\_request`-plugin-program  | ADX-undernät  | Internet: anpassad  | TCP  |

### <a name="relevant-ip-addresses"></a>Relevanta IP-adresser

#### <a name="azure-data-explorer-management-ip-addresses"></a>IP-adresser för Azure Datautforskaren Management

| Region | Adresser |
| --- | --- |
| Australien, centrala | 20.37.26.134 |
| Australien, Central2 | 20.39.99.177 |
| Australien, östra | 40.82.217.84 |
| Australien, sydöstra | 20.40.161.39 |
| Centrala | 191.233.25.183 |
| Kanada, centrala | 40.82.188.208 |
| Kanada, östra | 40.80.255.12 |
| Indien, centrala | 40.81.249.251 |
| USA, centrala | 40.67.188.68 |
| Centrala USA-EUAP | 40.89.56.69 |
| Asien, östra | 20.189.74.103 |
| USA, östra | 52.224.146.56 |
| USA, östra 2 | 52.232.230.201 |
| Östra 2; USA-EUAP | 52.253.226.110 |
| Frankrike, centrala | 40.66.57.91 |
| Frankrike, södra | 40.82.236.24 |
| Japan, östra | 20.43.89.90 |
| Japan, västra | 40.81.184.86 |
| Sydkorea, centrala | 40.82.156.149 |
| Sydkorea, södra | 40.80.234.9 |
| USA, norra centrala | 40.81.45.254 |
| Europa, norra | 52.142.91.221 |
| Sydafrika, norra | 102.133.129.138 |
| Sydafrika, västra | 102.133.0.97 |
| USA, södra centrala | 20.45.3.60 |
| Asien, sydöstra | 40.119.203.252 |
| Indien, södra | 40.81.72.110 |
| Storbritannien, södra | 40.81.154.254 |
| Storbritannien, västra | 40.81.122.39 |
| USA, västra centrala | 52.159.55.120 |
| Europa, västra | 51.145.176.215 |
| Indien, västra | 40.81.88.112 |
| USA, västra | 13.64.38.225 |
| USA, västra 2 | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>Hälso övervaknings adresser

| Region | Adresser |
| --- | --- |
| Australien, centrala | 191.239.64.128 |
| Australien, centrala 2 | 191.239.64.128 |
| Australien, östra | 191.239.64.128 |
| Australien, sydöstra | 191.239.160.47 |
| Brasilien, södra | 23.98.145.105 |
| Kanada, centrala | 168.61.212.201 |
| Kanada, östra | 168.61.212.201 |
| Indien, centrala | 23.99.5.162 |
| USA, centrala | 168.61.212.201 |
| Centrala USA-EUAP | 168.61.212.201 |
| Asien, östra | 168.63.212.33 |
| USA, östra | 137.116.81.189 |
| USA, östra 2 | 137.116.81.189 |
| USA, östra 2 EUAP | 137.116.81.189 |
| Frankrike, centrala | 23.97.212.5 |
| Frankrike, södra | 23.97.212.5 |
| Japan, östra | 138.91.19.129 |
| Japan, västra | 138.91.19.129 |
| Sydkorea, centrala | 138.91.19.129 |
| Sydkorea, södra | 138.91.19.129 |
| USA, norra centrala | 23.96.212.108 |
| Europa, norra | 191.235.212.69 
| Sydafrika, norra | 104.211.224.189 |
| Sydafrika, västra | 104.211.224.189 |
| USA, södra centrala | 23.98.145.105 |
| Indien, södra | 23.99.5.162 |
| Asien, sydöstra | 168.63.173.234 |
| Storbritannien, södra | 23.97.212.5 |
| Storbritannien, västra | 23.97.212.5 |
| USA, västra centrala | 168.61.212.201 |
| Europa, västra | 23.97.212.5 |
| Indien, västra | 23.99.5.162 |
| USA, västra | 23.99.5.162 |
| USA, västra 2 | 23.99.5.162 | 

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Azure Monitor slut punkts adresser för konfiguration

| Region | Adresser |
| --- | --- |
| Australien, centrala | 52.148.86.165 |
| Australien, centrala 2 | 52.148.86.165 |
| Östra Australien | 52.148.86.165 |
| Australien, sydöstra | 52.148.86.165 |
| Södra Brasilien | 13.68.89.19 |
| Centrala Kanada | 13.90.43.231 |
| Kanada, öst | 13.90.43.231 |
| Centrala Indien | 13.71.25.187 |
| USA, centrala | 52.173.95.68 |
| Centrala USA-EUAP | 13.90.43.231 |
| Asien, östra | 13.75.117.221 |
| USA, östra | 13.90.43.231 |
| USA, östra 2 | 13.68.89.19 | 
| USA, östra 2 EUAP | 13.68.89.19 |
| Centrala Frankrike | 52.174.4.112 |
| Frankrike, södra | 52.174.4.112 |
| Östra Japan | 13.75.117.221 |
| Västra Japan | 13.75.117.221 |
| Korea, centrala | 13.75.117.221 |
| Republiken Korea | 13.75.117.221 |
| Norra centrala USA | 52.162.240.236 |
| Nord Europa | 52.169.237.246 |
| Sydafrika, norra | 13.71.25.187 |
| Sydafrika, västra | 13.71.25.187 |
| Södra centrala USA | 13.84.173.99 |
| Södra Indien | 13.71.25.187 |
| Sydostasien | 52.148.86.165 |
| Storbritannien, södra | 52.174.4.112 |
| Storbritannien, västra | 52.169.237.246 |
| USA, västra centrala | 52.161.31.69 |
| Västeuropa | 52.174.4.112 |
| Indien, västra | 13.71.25.187 |
| Västra USA | 40.78.70.148 |
| USA, västra 2 | 52.151.20.103 |

## <a name="expressroute-setup"></a>ExpressRoute-installation

Använd ExpressRoute för att ansluta till ett lokalt nätverk till Azure-Virtual Network. En vanlig installation är att annonsera standard vägen (0.0.0.0/0) via Border Gateway Protocol-sessionen (BGP). Detta tvingar trafik som kommer från Virtual Network att vidarebefordras till kundens lokala nätverk som kan släppa trafiken, vilket leder till att utgående flöden bryts. För att lösa det här standardvärdet kan en [användardefinierad väg (UDR)](/azure/virtual-network/virtual-networks-udr-overview#user-defined) (0.0.0.0/0) konfigureras och nästa hopp är *Internet*. Eftersom UDR har företräde framför BGP kommer trafiken att vara avsedd för Internet.

## <a name="securing-outbound-traffic-with-firewall"></a>Skydda utgående trafik med brand vägg

Om du vill skydda utgående trafik med hjälp av [Azure-brandväggen](/azure/firewall/overview) eller en virtuell installation för att begränsa domän namn, måste följande fullständigt kvalificerade domän namn (FQDN) tillåtas i brand väggen.

```
prod.warmpath.msftcloudes.com:443
production.diagnostics.monitoring.core.windows.net:443
graph.windows.net:443
*.update.microsoft.com:443
shavamanifestcdnprod1.azureedge.net:443
login.live.com:443
wdcp.microsoft.com:443
login.microsoftonline.com:443
azureprofilerfrontdoor.cloudapp.net:443
*.core.windows.net:443
*.servicebus.windows.net:443
shoebox2.metrics.nsatc.net:443
production.diagnostics.monitoring.core.windows.net:443
prod-dsts.dsts.core.windows.net:443
ocsp.msocsp.com:80
*.windowsupdate.com:80
ocsp.digicert.com:80
go.microsoft.com:80
dmd.metaservices.microsoft.com:80
www.msftconnecttest.com:80
crl.microsoft.com:80
www.microsoft.com:80
adl.windows.com:80
crl3.digicert.com:80
```

Du måste också definiera [routningstabellen](/azure/virtual-network/virtual-networks-udr-overview) i under nätet med [hanterings adresser](#azure-data-explorer-management-ip-addresses) och [hälso övervaknings adresser](#health-monitoring-addresses) med nästa hopp- *Internet* för att förhindra problem med asymmetriska vägar.

Till exempel måste följande UDR definieras för regionen **västra USA** :

| Namn | Adressprefix | Nästa hopp |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | Internet |
| ADX_Monitoring | 23.99.5.162/32 | Internet |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>Distribuera Azure Datautforskaren-kluster i VNet med en Azure Resource Manager-mall

Om du vill distribuera Azure Datautforskaren-kluster i det virtuella nätverket använder du [azure datautforskaren-klustret i din VNet](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) -Azure Resource Manager mall.

Den här mallen skapar klustret, det virtuella nätverket, under nätet, nätverks säkerhets gruppen och de offentliga IP-adresserna.
