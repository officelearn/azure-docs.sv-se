---
title: Distribuera Azure Data Explorer till ditt virtuella nätverk
description: Lär dig hur du distribuerar Azure Data Explorer till ditt virtuella nätverk
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: dbc17620cda836ec0ac5c4ebc5a709fb0605c958
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240047"
---
# <a name="deploy-azure-data-explorer-cluster-into-your-virtual-network"></a>Distribuera Azure Data Explorer-kluster till ditt virtuella nätverk

I den här artikeln beskrivs de resurser som finns när du distribuerar ett Azure Data Explorer-kluster till ett anpassat Azure Virtual Network. Den här informationen hjälper dig att distribuera ett kluster till ett undernät i det virtuella nätverket (VNet). Mer information om Virtuella Azure-nätverk finns i [Vad är Virtuellt Azure-nätverk?](/azure/virtual-network/virtual-networks-overview)

   ![vnet-diagram](media/vnet-deployment/vnet-diagram.png)

Azure Data Explorer stöder distribution av ett kluster till ett undernät i ditt virtuella nätverk (VNet). Med den här funktionen kan du:

* Framtvinga NSG-regler [(Network Security Group)](/azure/virtual-network/security-overview) för klustertrafiken i Azure Data Explorer.
* Anslut det lokala nätverket till Azure Data Explorer-klustrets undernät.
* Skydda dina dataanslutningskällor ([Event Hub](/azure/event-hubs/event-hubs-about) och [Event Grid](/azure/event-grid/overview)) med [tjänstslutpunkter](/azure/virtual-network/virtual-network-service-endpoints-overview).

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>Få tillgång till ditt Azure Data Explorer-kluster i ditt virtuella nätverk

Du kan komma åt ditt Azure Data Explorer-kluster med hjälp av följande IP-adresser för varje tjänst (motor- och datahanteringstjänster):

* **Privat IP:** Används för åtkomst till klustret i det virtuella nätverket.
* **Offentlig IP:** Används för åtkomst till klustret utanför det virtuella nätverket för hantering och övervakning och som en källadress för utgående anslutningar startade från klustret.

Följande DNS-poster skapas för att komma åt tjänsten: 

* `[clustername].[geo-region].kusto.windows.net`(motor) `ingest-[clustername].[geo-region].kusto.windows.net` (datahantering) mappas till den offentliga ip-adressen för varje tjänst. 

* `private-[clustername].[geo-region].kusto.windows.net`(motor) `private-ingest-[clustername].[geo-region].kusto.windows.net` (datahantering) mappas till den privata IP-adressen för varje tjänst.

## <a name="plan-subnet-size-in-your-vnet"></a>Planera undernätsstorlek i ditt virtuella nätverk

Storleken på undernätet som används för att vara värd för ett Azure Data Explorer-kluster kan inte ändras när undernätet har distribuerats. I ditt virtuella nätverk använder Azure Data Explorer en privat IP-adress för varje virtuell dator och två privata IP-adresser för interna belastningsutjämnare (motor- och datahantering). Azure-nätverk använder också fem IP-adresser för varje undernät. Azure Data Explorer avserar två virtuella datorer för datahanteringstjänsten. Virtuella datorer för motortjänst etableras per användarkonfigurationsskalakapacitet.

Det totala antalet IP-adresser:

| Användning | Antal adresser |
| --- | --- |
| Motorservice | 1 per instans |
| Tjänsten för datahantering | 2 |
| Interna lastbalanserare | 2 |
| Reserverade Azure-adresser | 5 |
| **Totalt** | **#engine_instances + 9** |

> [!IMPORTANT]
> Undernätsstorlek måste planeras i förväg eftersom den inte kan ändras när Azure Data Explorer har distribuerats. Därför reserv behövs undernät storlek därefter.

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>Tjänstslutpunkter för anslutning till Azure Data Explorer

[Azure Service Slutpunkter](/azure/virtual-network/virtual-network-service-endpoints-overview) kan du skydda dina Azure-resurser för flera innehavare till ditt virtuella nätverk.
Om du distribuerar Azure Data Explorer-kluster i undernätet kan du ställa in dataanslutningar med [Event Hub](/azure/event-hubs/event-hubs-about) eller [Event Grid](/azure/event-grid/overview) samtidigt som du begränsar de underliggande resurserna för Azure Data Explorer-undernätet.

> [!NOTE]
> När du använder EventGrid-konfiguration med [Storage](/azure/storage/common/storage-introduction) och [Event Hub] kan lagringskontot som används i prenumerationen låsas med tjänstslutpunkter till Azure Data Explorers undernät samtidigt som betrodda Azure-plattformstjänster tillåts i [brandväggskonfigurationen](/azure/storage/common/storage-network-security), men händelsehubben kan inte aktivera Service Endpoint eftersom den inte stöder betrodda [Azure-plattformstjänster](/azure/event-hubs/event-hubs-service-endpoints).

## <a name="dependencies-for-vnet-deployment"></a>Beroenden för VNet-distribution

### <a name="network-security-groups-configuration"></a>Konfiguration av nätverkssäkerhetsgrupper

[NSG (Network Security Groups)](/azure/virtual-network/security-overview) ger möjlighet att styra nätverksåtkomsten inom ett virtuella nätverk. Azure Data Explorer kan nås med två slutpunkter: HTTPs (443) och TDS (1433). Följande NSG-regler måste konfigureras för att tillåta åtkomst till dessa slutpunkter för hantering, övervakning och korrekt drift av klustret.

#### <a name="inbound-nsg-configuration"></a>Inkommande NSG-konfiguration

| **Användning**   | **Från**   | **Att**   | **Protokollet**   |
| --- | --- | --- | --- |
| Hantering  |[ADX-hanteringsadresser](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement(ServiceTag) | ADX-undernät:443  | TCP  |
| Hälsoövervakning  | [ADX-adresser för hälsoövervakning](#health-monitoring-addresses)  | ADX-undernät:443  | TCP  |
| ADX intern kommunikation  | ADX-undernät: Alla portar  | ADX-undernät:Alla portar  | Alla  |
| Tillåt ingående Azure-belastningsutjämnare (hälsoavsökning)  | AzureLoadBalancer  | ADX-undernät:80 443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>Utgående NSG-konfiguration

| **Användning**   | **Från**   | **Att**   | **Protokollet**   |
| --- | --- | --- | --- |
| Beroende av Azure Storage  | ADX-undernät  | Lagring:443  | TCP  |
| Beroende av Azure Data Lake  | ADX-undernät  | AzureDataLake:443  | TCP  |
| EventHub-inmatning och serviceövervakning  | ADX-undernät  | EventHub:443 5671  | TCP  |
| Publicera mått  | ADX-undernät  | AzureMonitor:443 | TCP  |
| Hämtning av Azure Monitor-konfiguration  | ADX-undernät  | [Slutpunktadresser för Azure Monitor-konfiguration](#azure-monitor-configuration-endpoint-addresses):443 | TCP  |
| Active Directory (om tillämpligt) | ADX-undernät | AzureActiveDirectory:443 | TCP |
| Certifikatutfärdare | ADX-undernät | Internet:80 | TCP |
| Intern kommunikation  | ADX-undernät  | ADX-undernät:Alla portar  | Alla  |
| Portar som `sql\_request` används `http\_request` för och plugin-program  | ADX-undernät  | Internet:Anpassad  | TCP  |

### <a name="relevant-ip-addresses"></a>Relevanta IP-adresser

#### <a name="azure-data-explorer-management-ip-addresses"></a>Azure Data Explorer management IP-adresser

| Region | Adresser |
| --- | --- |
| Australien, centrala | 20.37.26.134 |
| Australien Central2 | 20.39.99.177 |
| Australien, östra | 40.82.217.84 |
| Australien, sydöstra | 20.40.161.39 |
| BrasilienSouth | 191.233.25.183 |
| Kanada, centrala | 40.82.188.208 |
| Kanada, östra | 40.80.255.12 |
| Indien, centrala | 40.81.249.251 |
| USA, centrala | 40.67.188.68 |
| Centrala USA EUAP | 40.89.56.69 |
| Asien, östra | 20.189.74.103 |
| USA, östra | 52.224.146.56 |
| USA, östra 2 | 52.232.230.201 |
| Östra USA2 EUAP | 52.253.226.110 |
| Frankrike, centrala | 40.66.57.91 |
| Frankrike, södra | 40.82.236.24 |
| Japan, östra | 20.43.89.90 |
| Japan, västra | 40.81.184.86 |
| Sydkorea, centrala | 40.82.156.149 |
| Sydkorea, södra | 40.80.234.9 |
| USA, norra centrala | 40.81.45.254 |
| Europa, norra | 52.142.91.221 |
| Sydafrika North | 102.133.129.138 |
| Sydafrika Väst | 102.133.0.97 |
| USA, södra centrala | 20.45.3.60 |
| Sydostasien | 40.119.203.252 |
| Indien, södra | 40.81.72.110 |
| Storbritannien, södra | 40.81.154.254 |
| Storbritannien, västra | 40.81.122.39 |
| USA, västra centrala | 52.159.55.120 |
| Europa, västra | 51.145.176.215 |
| Indien, västra | 40.81.88.112 |
| USA, västra | 13.64.38.225 |
| USA, västra 2 | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>Hälsoövervakningsadresser

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
| Centrala USA EUAP | 168.61.212.201 |
| Asien, östra | 168.63.212.33 |
| USA, östra | 137.116.81.189 |
| USA, östra 2 | 137.116.81.189 |
| Östra USA 2 EUAP | 137.116.81.189 |
| Frankrike, centrala | 23.97.212.5 |
| Frankrike, södra | 23.97.212.5 |
| Japan, östra | 138.91.19.129 |
| Japan, västra | 138.91.19.129 |
| Sydkorea, centrala | 138.91.19.129 |
| Sydkorea, södra | 138.91.19.129 |
| USA, norra centrala | 23.96.212.108 |
| Europa, norra | 191.235.212.69 
| Sydafrika North | 104.211.224.189 |
| Sydafrika Väst | 104.211.224.189 |
| USA, södra centrala | 23.98.145.105 |
| Indien, södra | 23.99.5.162 |
| Sydostasien | 168.63.173.234 |
| Storbritannien, södra | 23.97.212.5 |
| Storbritannien, västra | 23.97.212.5 |
| USA, västra centrala | 168.61.212.201 |
| Europa, västra | 23.97.212.5 |
| Indien, västra | 23.99.5.162 |
| USA, västra | 23.99.5.162 |
| USA, västra 2 | 23.99.5.162 |    

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Slutpunktsadresser för Azure Monitor-konfiguration

| Region | Adresser |
| --- | --- |
| Australien, centrala | 52.148.86.165 |
| Australien, centrala 2 | 52.148.86.165 |
| Australien, östra | 52.148.86.165 |
| Australien Sydost | 52.148.86.165 |
| Brasilien, södra | 13.68.89.19 |
| Kanada, centrala | 13.90.43.231 |
| Kanada, östra | 13.90.43.231 |
| Indien, centrala | 13.71.25.187 |
| USA, centrala | 52.173.95.68 |
| Centrala USA EUAP | 13.90.43.231 |
| Asien, östra | 13.75.117.221 |
| USA, östra | 13.90.43.231 |
| USA, östra 2 | 13.68.89.19 |    
| Östra USA 2 EUAP | 13.68.89.19 |
| Frankrike, centrala | 52.174.4.112 |
| Frankrike, södra | 52.174.4.112 |
| Japan, östra | 13.75.117.221 |
| Japan, västra | 13.75.117.221 |
| Sydkorea, centrala | 13.75.117.221 |
| Sydkorea, södra | 13.75.117.221 |
| USA, norra centrala | 52.162.240.236 |
| Europa, norra | 52.169.237.246 |
| Sydafrika North | 13.71.25.187 |
| Sydafrika Väst | 13.71.25.187 |
| USA, södra centrala | 13.84.173.99 |
| Indien, södra | 13.71.25.187 |
| Sydostasien | 52.148.86.165 |
| Storbritannien, södra | 52.174.4.112 |
| Storbritannien, västra | 52.169.237.246 |
| USA, västra centrala | 52.161.31.69 |
| Europa, västra | 52.174.4.112 |
| Indien, västra | 13.71.25.187 |
| Västra USA | 40.78.70.148 |
| USA, västra 2 | 52.151.20.103 |

## <a name="expressroute-setup"></a>Inställningar för ExpressRoute

Använd ExpressRoute för att ansluta lokalt nätverk till Azure Virtual Network. En vanlig inställning är att annonsera standardvägen (0.0.0.0/0) via BGP-sessionen (Border Gateway Protocol). Detta tvingar trafik som kommer ut från det virtuella nätverket att vidarebefordras till kundens premissnätverk som kan släppa trafiken, vilket gör att utgående flöden bryts. För att övervinna denna standard kan UDR (0.0.0.0/0) konfigureras och nästa hopp blir *Internet*. [User Defined Route (UDR)](/azure/virtual-network/virtual-networks-udr-overview#user-defined) Eftersom UDR har företräde framför BGP kommer trafiken att vara avsedd för Internet.

## <a name="securing-outbound-traffic-with-firewall"></a>Skydda utgående trafik med brandvägg

Om du vill skydda utgående trafik med [Azure-brandväggen](/azure/firewall/overview) eller någon virtuell installation för att begränsa domännamn, måste följande fullständigt kvalificerade domännamn (FQDN) tillåtas i brandväggen.

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

Du måste också definiera [flödestabellen](/azure/virtual-network/virtual-networks-udr-overview) i undernätet med [hanteringsadresser](#azure-data-explorer-management-ip-addresses) och [hälsoövervakningsadresser](#health-monitoring-addresses) med nästa hop *Internet* för att förhindra asymmetriska vägproblem.

För region **västra USA** måste till exempel följande UDR:er definieras:

| Namn | Adressprefix | Nästa hopp |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | Internet |
| ADX_Monitoring | 23.99.5.162/32 | Internet |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>Distribuera Azure Data Explorer-kluster till ditt virtuella nätverk med hjälp av en Azure Resource Manager-mall

Om du vill distribuera Azure Data Explorer-kluster till ditt virtuella nätverk använder du [azure data explorer-klustret distribueras till din VNet](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) Azure Resource Manager-mall.

Den här mallen skapar kluster-, virtuella nätverk, undernät, nätverkssäkerhetsgrupp och offentliga IP-adresser.