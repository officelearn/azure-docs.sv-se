---
title: Azure-trafikanalysschema | Microsoft-dokument
description: Förstå schemat för Traffic Analytics för att analysera flödesloggar för Azure-nätverkssäkerhetsgrupper.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: vinigam
ms.openlocfilehash: ccfbb92c27e4508595f19c2ea6900730cde609b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74666383"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Schema och dataaggregering i Traffic Analytics

Traffic Analytics är en molnbaserad lösning som ger insyn i användar- och programaktivitet i molnnätverk. Traffic Analytics analyserar NSG-flödesloggar (Network Watcher Network Security Group) för att ge insikter om trafikflödet i ditt Azure-moln. Med trafikanalys kan du:

- Visualisera nätverksaktivitet över dina Azure-prenumerationer och identifiera aktiva punkter.
- Identifiera säkerhetshot mot och skydda nätverket, med information som öppna portar, program som försöker komma åt internet och virtuella datorer (VM) som ansluter till oseriösa nätverk.
- Förstå trafikflödesmönster i Azure-regioner och internet för att optimera nätverksdistributionen för prestanda och kapacitet.
- Identifiera nätverksdefigureringar som leder till misslyckade anslutningar i nätverket.
- Känna till nätverksanvändningen i byte, paket eller flöden.

### <a name="data-aggregation"></a>Dataaggregering

1. Alla flödesloggar på en NSG mellan "FlowIntervalStartTime_t" och "FlowIntervalEndTime_t" fångas in med en minuts intervall i lagringskontot som blobbar innan de bearbetas av Traffic Analytics.
2. Standardbearbetningsintervallet för Traffic Analytics är 60 minuter. Det innebär att var 60:e minut Traffic Analytics plockar blobbar från lagring för aggregering. Om bearbetningsintervallet är 10 minuter väljer Traffic Analytics blobbar från lagringskontot efter var 10:e minut.
3. Flöden som har samma käll-IP, mål-IP, Målport, NSG-namn, NSG-regel, Flödesriktning och Transportlagerprotokoll (TCP eller UDP) (Obs: Källporten är utesluten för aggregering) klubbas in i ett enda flöde av Traffic Analytics
4. Denna enda post är dekorerad (Detaljer i avsnittet nedan) och intas i Log Analytics av Traffic Analytics.This process kan ta upp till 1 timme max.
5. FlowStartTime_t fältet anger den första förekomsten av ett sådant aggregerat flöde (samma fyra-tuppel) i flödesloggens bearbetningsintervall mellan "FlowIntervalStartTime_t" och "FlowIntervalEndTime_t".
6. För alla resurser i TA är flödena som anges i användargränssnittet totala flöden som ses av NSG, men i Log Analytics visas endast den enda, reducerade posten. Om du vill visa alla flöden använder du fältet blob_id, som kan refereras från Lagring. Det totala flödesantalet för den posten matchar de enskilda flöden som visas i blobben.

Nedanstående fråga hjälper dig att titta på alla flödesloggar från lokala under de senaste 30 dagarna.
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s  
```
Om du vill visa blob-sökvägen för flödena i ovanstående fråga använder du frågan nedan:

```
let TableWithBlobId =
(AzureNetworkAnalytics_CL
   | where SubType_s == "Topology" and ResourceType == "NetworkSecurityGroup" and DiscoveryRegion_s == Region_s and IsFlowEnabled_b
   | extend binTime = bin(TimeProcessed_t, 6h),
            nsgId = strcat(Subscription_g, "/", Name_s),
            saNameSplit = split(FlowLogStorageAccount_s, "/")
   | extend saName = iif(arraylength(saNameSplit) == 3, saNameSplit[2], '')
   | distinct nsgId, saName, binTime)
| join kind = rightouter (
   AzureNetworkAnalytics_CL
   | where SubType_s == "FlowLog"  
   | extend binTime = bin(FlowEndTime_t, 6h)
) on binTime, $left.nsgId == $right.NSGList_s  
| extend blobTime = format_datetime(todatetime(FlowIntervalStartTime_t), "yyyy MM dd hh")
| extend nsgComponents = split(toupper(NSGList_s), "/"), dateTimeComponents = split(blobTime, " ")
| extend BlobPath = strcat("https://", saName,
                        "@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/", nsgComponents[0],
                        "/RESOURCEGROUPS/", nsgComponents[1],
                        "/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/", nsgComponents[2],
                        "/y=", dateTimeComponents[0], "/m=", dateTimeComponents[1], "/d=", dateTimeComponents[2], "/h=", dateTimeComponents[3],
                        "/m=00/macAddress=", replace(@"-", "", MACAddress_s),
                        "/PT1H.json")
| project-away nsgId, saName, binTime, blobTime, nsgComponents, dateTimeComponents;

TableWithBlobId
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s , BlobPath
```

Ovanstående fråga skapar en URL för att komma åt blobben direkt. Webbadressen med platshållare är nedan:

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>Fält som används i Traffic Analytics-schemat
  > [!IMPORTANT]
  > Traffic Analytics-schemat har uppdaterats den 22 augusti 2019. Det nya schemat ger käll- och mål-IP-adresser som tar bort behovet av att tolka FlowDirection-fält som gör frågor enklare. </br>
  > FASchemaVersion_s uppdaterad från 1 till 2. </br>
  > Inaktuella fält: VMIP_s, Subscription_s, Region_s, NSGRules_s, Subnet_s, VM_s, NIC_s, PublicIPs_s, FlowCount_d </br>
  > Nya fält: SrcPublicIPs_s, DestPublicIPs_s, NSGRule_s </br>
  > Inaktuella fält kommer att vara tillgängliga fram till den 22 november 2019.

Traffic Analytics bygger på logganalys, så att du kan köra anpassade frågor om data som dekoreras av Traffic Analytics och ställa in aviseringar på samma.

Nedan listas fälten i schemat och vad de betyder

| Field | Format | Kommentarer |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Tabell för Trafikanalysdata
| SubType_s | FlowLog (Flödeslogg) | Undertyp för flödesloggarna. Använd endast "FlowLog", andra värden för SubType_s är för internt arbete av produkten |
| FASchemaVersion_s |   2   | Schemaversion. Återspeglar inte NSG Flow Log version |
| TimeProcessed_t   | Datum och tid i UTC  | Tid då Traffic Analytics bearbetade råflödesloggarna från lagringskontot |
| FlowIntervalStartTime_t | Datum och tid i UTC |  Starttid för flödesloggbearbetningsintervallet. Detta är tid från vilken flödesintervall mäts |
| FlowIntervalEndTime_t | Datum och tid i UTC | Sluttid för flödesloggbearbetningsintervallet |
| FlowStartTime_t | Datum och tid i UTC |  Första förekomsten av flödet (som kommer att få aggregeras) i flödesloggen bearbetningsintervall mellan "FlowIntervalStartTime_t" och "FlowIntervalEndTime_t". Det här flödet aggregeras baserat på aggregeringslogik |
| FlowEndTime_t | Datum och tid i UTC | Den senaste förekomsten av flödet (som kommer att aggregeras) i flödesloggens bearbetningsintervall mellan "FlowIntervalStartTime_t" och "FlowIntervalEndTime_t". När det gäller flödeslogg v2 innehåller det här fältet den tid då det sista flödet med samma fyra-tuppel startade (markerat som "B" i raw-flödesposten) |
| FlowType_s |  * IntraVNet <br> * InterVNet (på andra sätt) <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternPublic <br> * MaliciousFlow <br> * Okänd Privat <br> * Okänd | Definition i anteckningar under tabellen |
| SrcIP_s | Källans IP-adress | Kommer att vara tomt vid AzurePublic och ExternalPublic flöden |
| DestIP_s | Mål-IP-adress | Kommer att vara tomt vid AzurePublic och ExternalPublic flöden |
| VMIP_s | IP för den virtuella datorn | Används för AzurePublic- och ExternalPublic-flöden |
| PublicIP_s | Offentliga IP-adresser | Används för AzurePublic- och ExternalPublic-flöden |
| DestPort_d | Målport | Port där trafiken är inkommande |
| L4Protocol_s  | * T <br> * U  | transportprotokollet. T = TCP <br> U = UDP |
| L7Protocol_s  | Protokollnamn | Härledd från destinationsport |
| FlowDirection_s | * Jag = Inkommande<br> * O = Utgående | Riktning av flödet in/ut ur NSG enligt flödesloggen |
| FlowStatus_s  | * A = Tillåts av NSG regel <br> * D = Nekas av NSG regel  | Status för tillåtna flöde/nblockerad av NSG enligt flödesloggen |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | Network Security Group (NSG) som är associerad med flödet |
| NSGRules_s | \<Indexvärde \| \<0)>NSG_RULENAME>\| \<flödesriktning \| \<>flödesstatus>\| \<FlowCount ProcessedByRule> |  NSG-regel som tillät eller nekade det här flödet |
| NSGRule_s | NSG_RULENAME |  NSG-regel som tillät eller nekade det här flödet |
| NSGRuleType_s | * Användardefinierad * Standard |   Den typ av NSG-regel som används av flödet |
| MACAddress_s | MAC-adress | MAC-adressen för nätverkskortet där flödet fångades |
| Subscription_s | Prenumeration på Azure virtuellt nätverk / nätverksgränssnitt / virtuell dator fylls i i det här fältet | Gäller endast för FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow och UnknownPrivate flödestyper (flödestyper där endast en sida är azurblå) |
| Subscription1_s | Prenumerations-ID:t | Prenumerations-ID för virtuellt nätverk/nätverksgränssnitt/virtuell dator som käll-IP:n i flödet tillhör |
| Subscription2_s | Prenumerations-ID:t | Prenumerations-ID för virtuellt nätverk/nätverksgränssnitt/virtuell dator som mål-IP i flödet tillhör |
| Region_s | Azure-regionen för virtuellt nätverk/nätverksgränssnitt/virtuell dator som IP i flödet tillhör | Gäller endast för FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow och UnknownPrivate flödestyper (flödestyper där endast en sida är azurblå) |
| Region1_s | Azure-region | Azure-regionen för virtuellt nätverk/nätverksgränssnitt/virtuell dator som käll-IP i flödet tillhör |
| Region2_s | Azure-region | Azure-regionen för virtuellt nätverk som mål-IP i flödet tillhör |
| NIC_s | \<resourcegroup_Name>\/ \<NetworkInterfaceName> |  Nätverkskortet som är associerat med att den virtuella datorn skickar eller tar emot trafiken |
| NIC1_s | <resourcegroup_Name>/\<NetworkInterfaceName> | Nätverkskortet som är associerat med käll-IP i flödet |
| NIC2_s | <resourcegroup_Name>/\<NetworkInterfaceName> | Nätverkskortet som är associerat med mål-IP i flödet |
| VM_s | <resourcegroup_Name>\/ \<NetworkInterfaceName> | Virtuell dator som är associerad med nätverksgränssnittet NIC_s |
| VM1_s | <resourcegroup_Name>/\<VirtualMachineName> | Virtuell dator som är associerad med käll-IP i flödet |
| VM2_s | <resourcegroup_Name>/\<VirtualMachineName> | Virtuell dator som är associerad med mål-IP i flödet |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<Undernätnamn> | Undernät som är associerat med NIC_s |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<Undernätnamn> | Undernät som är associerat med käll-IP i flödet |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<Undernätnamn>    | Undernät som är associerat med mål-IP i flödet |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Programgateway som är associerad med käll-IP i flödet |
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Programgateway som är associerad med mål-IP i flödet |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Belastningsutjämnare som är associerad med käll-IP i flödet |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Belastningsutjämnare som är associerad med mål-IP i flödet |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Lokal nätverksgateway som är associerad med käll-IP i flödet |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Lokal nätverksgateway som är associerad med mål-IP i flödet |
| ConnectionType_s | Möjliga värden är VNetPeering, VpnGateway och ExpressRoute |    Anslutningstyp |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | Anslutningsnamn. För flödestyp P2S formateras detta <gateway name>som _<VPN Client IP> |
| ConnectingVNets_s | Utrymmesavgränsad lista över virtuella nätverksnamn | I händelse av nav och talade topologi, hubv virtuella nätverk kommer att fyllas här |
| Country_s | Landskod med två bokstäver (ISO 3166-1 alfa-2) | Fylls i för flödestypen ExternalPublic. Alla IP-adresser i PublicIPs_s fält delar samma landskod |
| AzureRegion_s | Platser i Azure-regionen | Ifylld för flödestypen AzurePublic. Alla IP-adresser i fältet PublicIPs_s delar Azure-regionen |
| AllowedInFlows_d | | Antal inkommande flöden som var tillåtna. Detta representerar antalet flöden som delade samma fyra-tuppel inkommande till nätverksgränssnittet där flödet fångades |
| DeniedInFlows_d |  | Antal inkommande flöden som nekades. (Inkommande till nätverksgränssnittet där flödet fångades) |
| AllowedOutFlows_d | | Antal utgående flöden som var tillåtna (Utgående till nätverksgränssnittet där flödet fångades) |
| DeniedOutFlows_d  | | Antal utgående flöden som nekades (Utgående till nätverksgränssnittet där flödet fångades) |
| FlowCount_d | Inaktuellt. Totala flöden som matchade samma fyra-tuppel. När det gäller flödestyper ExternalPublic och AzurePublic, räknas kommer att omfatta flöden från olika PublicIP-adresser också.
| InboundPackets_d | Paket som tas emot som hämtade i nätverksgränssnittet där NSG-regeln tillämpades | Detta fylls i endast för version 2 av NSG-flödesloggschema |
| OutboundPackets_d  | Paket som skickas som hämtade i nätverksgränssnittet där NSG-regeln tillämpades | Detta fylls i endast för version 2 av NSG-flödesloggschema |
| InboundBytes_d |  Byte som tas emot som infångade vid nätverksgränssnittet där NSG-regeln tillämpades | Detta fylls i endast för version 2 av NSG-flödesloggschema |
| OutboundBytes_d | Byte som skickas som hämtade i nätverksgränssnittet där NSG-regeln tillämpades | Detta fylls i endast för version 2 av NSG-flödesloggschema |
| CompletedFlows_d  |  | Detta fylls i med icke-noll-värde endast för version 2 av NSG-flödesloggschema |
| PublicIPs_s | <\| \<PUBLIC_IP <><FLOW_STARTED_COUNT <<\| \<<>FLOW_ENDED_COUNT \| \<>OUTBOUND_PACKETS>\| \<INBOUND_PACKETS>OUTBOUND_BYTES \| \<>\| \<INBOUND_BYTES INBOUND_BYTES OUTBOUND_BYTES INBOUND_BYTES> OUTBOUND_BYTES INBOUND_BYTES INBOUND_BYTES OUTBOUND_BYTES INBOUND_BYTES INBOUND_BYTES | Poster avgränsade med staplar |
| SrcPublicIPs_s | <SOURCE_PUBLIC_IP>\| \<SOURCE_PUBLIC_IP FLOW_STARTED_COUNT FLOW_ENDED_COUNT \| \<>FLOW_ENDED_COUNT FLOW_ENDED_COUNT FLOW_ENDED_COUNT>FLOW_ENDED_COUNT \| \<INBOUND_PACKETS \| \<>OUTBOUND_PACKETS>\| \<OUTBOUND_BYTES>\| \<INBOUND_BYTES>>>>>>>>>>>>>>>>>>>>>> | Poster avgränsade med staplar |
| DestPublicIPs_s | \| \<<DESTINATION_PUBLIC_IP>FLOW_STARTED_COUNT \| \<>FLOW_ENDED_COUNT>\| \<>OUTBOUND_PACKETS \| \<>\| \<INBOUND_PACKETS>OUTBOUND_BYTES \| \<>INBOUND_BYTES> | Poster avgränsade med staplar |

### <a name="notes"></a>Anteckningar

1. När det gäller AzurePublic- och ExternalPublic-flöden fylls den kundägda Azure VM IP i VMIP_s fält, medan de offentliga IP-adresserna fylls i i fältet PublicIPs_s. För dessa två flödestyper bör vi använda VMIP_s och PublicIPs_s i stället för SrcIP_s och DestIP_s fält. För AzurePublic- och ExternalPublicIP-adresser sammanställer vi ytterligare, så att antalet poster som förtärs till arbetsytan för kundlogganalys är minimalt. (Det här fältet kommer att vara inaktuella snart och vi bör använda SrcIP_ och DestIP_s beroende på om azure VM var källan eller målet i flödet)
1. Information om flödestyper: Baserat på de IP-adresser som ingår i flödet kategoriserar vi flödena till följande flödestyper:
1. IntraVNet – Båda IP-adresserna i flödet finns i samma Virtuella Azure-nätverk.
1. InterVNet - IP-adresser i flödet finns i de två olika Virtuella Azure-nätverken.
1. S2S – (Plats till plats) En av IP-adresserna tillhör Azure Virtual Network medan den andra IP-adressen tillhör kundnätverk (Site) som är ansluten till Azure Virtual Network via VPN-gateway eller Express Route.
1. P2S - (Peka till plats) En av IP-adresserna tillhör Azure Virtual Network medan den andra IP-adressen tillhör kundnätverk (Site) som är ansluten till Azure Virtual Network via VPN-gatewayen.
1. AzurePublic - En av IP-adresserna tillhör Azure Virtual Network medan den andra IP-adressen tillhör Azure Internal Public IP-adresser som ägs av Microsoft. Kundägda offentliga IP-adresser kommer inte att ingå i den här flödestypen. Till exempel skulle alla kundägda virtuella datorer som skickar trafik till en Azure Service (Storage-slutpunkt) kategoriseras under den här flödestypen.
1. ExternalPublic - En av IP-adresserna tillhör Azure Virtual Network medan den andra IP-adressen är en offentlig IP som inte finns i Azure, rapporteras inte som skadlig i ASC-feeds som Traffic Analytics använder för bearbetningsintervallet mellan " FlowIntervalStartTime_t" och "FlowIntervalEndTime_t".
1. MaliciousFlow - En av IP-adresserna tillhör azure virtuellt nätverk medan den andra IP-adressen är en offentlig IP som inte finns i Azure och rapporteras som skadlig i ASC-feeds som Traffic Analytics förbrukar för bearbetningsintervallet mellan " FlowIntervalStartTime_t" och "FlowIntervalEndTime_t".
1. UnknownPrivate - En av IP-adresserna tillhör Azure Virtual Network medan den andra IP-adressen tillhör privat IP-intervall enligt definitionen i RFC 1918 och inte kunde mappas av Traffic Analytics till en kundägd plats eller Azure Virtual Network.
1. Okänd – Det går inte att mappa någon av IP-adresserna i flödena med kundtopologin i Azure samt lokalt (plats).
1. Vissa fältnamn läggs till \_med \_s eller d. Dessa betyder INTE källa och mål men anger datatyperna sträng respektive decimal.

### <a name="next-steps"></a>Efterföljande moment
Information om hur du får svar på vanliga frågor och svar finns i [Vanliga frågor om trafikanalys](traffic-analytics-faq.md) Om du vill se information om funktioner finns i [Dokumentation för trafikanalys](traffic-analytics.md)
