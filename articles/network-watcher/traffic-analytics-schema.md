---
title: Schema för Azure traffic analytics | Microsoft Docs
description: Förstå schemat för trafikanalys att analysera flödesloggar för nätverkssäkerhetsgruppen Azure-nätverk.
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
ms.openlocfilehash: ca7aba3d92542f1b73160c726a555487e17fd8e2
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "57012037"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Schema och data aggregering i trafikanalys

Trafikanalys är en molnbaserad lösning som ger insyn i användar- och aktiviteter i molnnätverk. Trafikanalys analyserar flödesloggar för Network Watcher network security group (NSG) för att ge insikter om trafikflöden i Azure-molnet. Med trafikanalys kan du:

- Visualisera nätverksaktivitet för alla Azure-prenumerationer och identifiera aktiva punkter.
- Identifiera säkerhetshot till och skydda ditt nätverk, med information, till exempel öppna portar, program som försöker Internetåtkomst och virtuella datorer (VM) som ansluter till obehöriga nätverk.
- Förstå trafikflödesmönster mellan Azure-regioner och internet för att optimera din nätverksdistribution för prestanda och kapacitet.
- Identifiera nätverket felkonfigurationer som leder till misslyckade anslutningar i nätverket.
- Vet nätverksanvändning i byte, paket eller flöden.

### <a name="data-aggregation"></a>Datasammanställning

1. Alla flödesloggar på en NSG mellan ”FlowIntervalStartTime_t” och ”FlowIntervalEndTime_t” som avbildas med minuts intervall i lagringskontot som blobar innan den bearbetas av trafikanalys. 
2. Standardintervallet för bearbetning av trafikanalys är 60 minuter. Det innebär att varje 60 min Traffic Analytics hämtar blobbar från lagring för aggregering.
3. Flöden som har samma käll-IP, mål-IP, målport, NSG-namnet, NSG-regel, Flow riktning och Transport layer protocol (TCP eller UDP) (Observera: Källporten är exkluderad för aggregering) är clubbed i ett enda flöde av trafikanalys
4. Den här enda posten är dekorerad (information finns i avsnittet nedan) och matas in i Log Analytics genom trafikanalys.
5. FlowStartTime_t fältet anger den första förekomsten av sådana ett sammansatt flöde (samma fyra-tuppel) i flow-loggen bearbetades intervall mellan ”FlowIntervalStartTime_t” och ”FlowIntervalEndTime_t”. 
6. För alla resurser i TA de flöden som anges i Användargränssnittet är totalt antal flöden som setts av NSG: N, men i loggen Anlaytics användaren ser endast den enda, minska posten. Om du vill se alla flöden, använder du fältet blob_id som kan refereras från Storage. Det totala flödet räknas för att posten kommer att matcha de enskilda flöden som visas i blobben.


### <a name="fields-used-in-traffic-analytics-schema"></a>Fält som används i schemat för trafikanalys

Trafikanalys är byggt på Log Analytics, så att du kan köra egna frågor på data dekorerad med trafikanalys och Ställ in aviseringar på samma.

Nedan visas fälten i schemat och vad de en obestämd

| Fält | Format | Kommentarer | 
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Tabell för trafik Anlaytics data
| SubType_s | FlowLog | Undertyp för flow-loggar |
| FASchemaVersion_s |   1   | Scehma version. Avspeglar inte NSG Flow-Log-version |
| TimeProcessed_t   | Datum och tid i UTC  | Tid då den trafikanalys bearbetas raw-flödesloggar från storage-kontot |
| FlowIntervalStartTime_t | Datum och tid i UTC |  Starttiden för medlemssäkerhet för flow-log. Det här är tiden från vilken flow intervall mäts |
| FlowIntervalEndTime_t | Datum och tid i UTC | Sluttid för medlemssäkerhet för flow-log |
| FlowStartTime_t | Datum och tid i UTC |  Första förekomsten av flödet (som ska få aggregerat) i flow log bearbetningsintervallet mellan ”FlowIntervalStartTime_t” och ”FlowIntervalEndTime_t”. Det här flödet hämtar aggregeras utifrån aggregering logik |
| FlowEndTime_t | Datum och tid i UTC | Sista förekomsten av flödet (som ska få aggregerat) i flow log bearbetningsintervallet mellan ”FlowIntervalStartTime_t” och ”FlowIntervalEndTime_t”. När det gäller flow log v2 innehåller det här fältet tidpunkten då det senaste flödet med samma fyra-tuppel startades (markerad som ”B” i posten raw flow) |
| FlowType_s |  * IntraVNet <br> * InterVNet <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * Okänd privat <br> * Okänd | Definition i informationen nedan tabellen |
| SrcIP_s | Källans IP-adress | Tom vid AzurePublic och ExternalPublic flöden |
| DestIP_s | Mål-IP-adress | Tom vid AzurePublic och ExternalPublic flöden |
| VMIP_s | IP-Adressen för den virtuella datorn | Används för AzurePublic och ExternalPublic flöden |
| PublicIP_S | Offentliga IP-adresser | Används för AzurePublic och ExternalPublic flöden |
| DestPort_d | |Målport| Porten som är inkommande trafik | 
| L4Protocol_s  | * T <br> * U  | Transport-protokoll. T = TCP <br> U = UDP | 
| L7Protocol_s  | Protokollnamn | Härleds från målport |
| FlowDirection_s | * Jag = inkommande<br> * O = utgående | Riktning för flödet in/ut från NSG enligt flödeslogg | 
| FlowStatus_s  | * A = som tillåts av NSG-regel <br> * D = nekas av NSG-regel  | Status för flow tillåtna/nblocked av NSG enligt flödeslogg |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | Nätverkssäkerhetsgrupp (NSG) som är associerad med flödet |
| NSGRules_s | \<Indexera värdet 0) >< NSG_RULENAME >\<Flow riktning >\<Flow Status >\<FlowCount ProcessedByRule > |  NSG-regel som tillåts eller nekas detta flöde |
| NSGRuleType_s | * Användardefinierade * standard |   Vilken typ av NSG-regel som används av flödet |
| MACAddress_s | MAC-adress | MAC-adressen för nätverkskortet som flödet har hämtats |
| Subscription_s | Prenumeration på Azure-nätverket / nätverksgränssnittet / VM fylls i automatiskt | Gäller endast för ExchangeRate för FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow och UnknownPrivate flow typer (flödestyper där endast en sida är azure) |
| Subscription1_s | Prenumerations-ID:t | Prenumerations-ID för virtuellt nätverk / nätverksgränssnittet / virtuell dator som käll-IP i flödet som hör till |
| Subscription2_s | Prenumerations-ID:t | Prenumerations-ID för virtuellt nätverk / nätverksgränssnittet / VM som mål-IP i flödet som hör till |
| Region_s | Azure-regionen för virtuellt nätverk / nätverksgränssnittet / virtuell dator som IP-Adressen i flödet som hör till | Gäller endast för ExchangeRate för FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow och UnknownPrivate flow typer (flödestyper där endast en sida är azure) |
| Region1_s | Azure-region | Azure-regionen för virtuellt nätverk / nätverksgränssnittet / virtuell dator som käll-IP i flödet som hör till |
| Region2_s | Azure-region | Azure-regionen för virtuellt nätverk som IP-Adressen för målet i flödet som hör till |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  Nätverkskort som är associerade med den virtuella datorn att skicka eller ta emot trafik |
| NIC1_s | <resourcegroup_Name>/\<NetworkInterfaceName> | Nätverkskort som är associerade med IP-källan i flödet |
| NIC2_s | <resourcegroup_Name>/\<NetworkInterfaceName> | Nätverkskort som är associerade med IP-Adressen för målet i flödet |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | Virtuell dator som är associerad med nätverksgränssnittet NIC_s |
| VM1_s | <resourcegroup_Name>/\<VirtualMachineName> | Virtuell dator som är associerade med IP-källan i flödet |
| VM2_s | <resourcegroup_Name>/\<VirtualMachineName> | Virtuella datorn med IP-Adressen för målet i flödet |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Undernät som är associerat med NIC_s |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Undernät som är associerat med den IP-källan i flödet |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName>    | Undernät som är associerat med IP-Adressen för målet i flödet |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Application gateway som är associerade med den IP-källan i flödet | 
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Application gateway som är associerade med IP-Adressen för målet i flödet |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Belastningsutjämnare som är associerade med den IP-källan i flödet |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Belastningsutjämnare som är associerade med IP-Adressen för målet i flödet |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Lokal nätverksgateway som är associerade med den IP-källan i flödet |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Lokal nätverksgateway som är associerade med IP-Adressen för målet i flödet |
| ConnectionType_s | Möjliga värden är VNetPeering VpnGateway och ExpressRoute |    Anslutningstyp |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | Anslutningsnamn |
| ConnectingVNets_s | Blankstegsavgränsad lista med namn på virtuellt nätverk | När det gäller topologi för NAV och ekrar är hub virtuella nätverk ifyllda |
| Country_s | Tvåstavig landskod (ISO 3166-1 alpha-2) | Fylls i för flow typen ExternalPublic. Alla IP-adresser i PublicIPs_s fältet delar samma landskod |
| AzureRegion_s | Azure-region platser | Fylls i för flow typen AzurePublic. Alla IP-adresser i PublicIPs_s fältet delar Azure-region |
| AllowedInFlows_d | | Antal inkommande flöden som tilläts. Detta representerar antalet flöden som delat samma fyra-tuppel inkommande till gränssnittet netweork då flödet har hämtats | 
| DeniedInFlows_d |  | Antal inkommande flöden som nekades. (Inkommande till nätverksgränssnittet då flödet avbildades) |
| AllowedOutFlows_d | | Antal utgående flöden som tilläts (utgående till nätverksgränssnittet då flödet avbildades) |
| DeniedOutFlows_d  | | Antal utgående flöden som nekades (utgående till nätverksgränssnittet då flödet avbildades) |
| FlowCount_d | Föråldrad. Totalt antal flöden som matchade samma fyra-tuppel. När det gäller typer av användarflöden ExternalPublic och AzurePublic innehåller antal flöden från olika PublicIP adresser samt.
| InboundPackets_d | Paket som tas emot som hämtats vid nätverksgränssnittet där NSG-regel har tillämpats | Det här fylls bara för Version 2 av NSG flow log schemat |
| OutboundPackets_d  | Paket som skickas som hämtats vid nätverksgränssnittet där NSG-regel har tillämpats | Det här fylls bara för Version 2 av NSG flow log schemat |
| InboundBytes_d |  Byte som mottagits som hämtats vid nätverksgränssnittet där NSG-regel har tillämpats | Det här fylls bara för Version 2 av NSG flow log schemat |
| OutboundBytes_d | Byte som skickats som hämtats vid nätverksgränssnittet där NSG-regel har tillämpats | Det här fylls bara för Version 2 av NSG flow log schemat |
| CompletedFlows_d  |  | Det fylls i med värdet noll endast för Version 2 av NSG flow log schemat |
| PublicIPs_s | <PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Poster avgränsade med staplar |
    
### <a name="notes"></a>Anteckningar
    
1. När det gäller AzurePublic och ExternalPublic flöden ägs kunden Azure VM-IP har fyllts i i VMIP_s fält, medan de offentliga IP-adresserna uppdateras i fältet PublicIPs_s. För dessa flödestyper av två använder vi VMIP_s och PublicIPs_s i stället SrcIP_s och DestIP_s fält. För AzurePublic och ExternalPublicIP aggregera vi ytterligare, så att antalet poster som matas in kund log analytics-arbetsytan är minimal. (Det här fältet upphör att gälla snart och vi ska använda SrcIP_ och DestIP_s beroende på om virtuell azure-dator var källan eller målet i flödet) 
2. Information om typer av användarflöden: Baserat på IP-adresser som ingår i flödet kan kategorisera vi flöden i till följande flödestyper av: 
 *  IntraVNet – båda IP-adresserna i flödet finns i samma Azure Virtual Network. 
 *  Saknar - IP-adresser i flödet finns i de två olika Azure-nätverk. 
*   S2S – (plats till plats) och en av IP-adresser som hör till Azure Virtual Network medan den andra IP-adressen tillhör kundens nätverk (plats) som är ansluten till det virtuella Azure-nätverket via VPN-gateway eller Expressroute. 
*   P2S - (punkt till plats) en av IP-adresser som hör till Azure Virtual Network medan den andra IP-adressen tillhör kundens nätverk (plats) som är anslutna till Azure-nätverk via VPN-gateway.
*   AzurePublic - en av IP-adresser som tillhör Azure Virtual Network medan den andra IP-adressen tillhör Azure interna offentliga IP-adresser som ägs av Microsoft. Kundägda offentliga IP-adresser inte kommer vara en del av den här flödestypen av. Exempelvis ägs vilken kund som VM som skickar trafik till en Azure-tjänst (slutpunkt för lagring) skulle kategoriseras under den här flödestypen av. <br><br>
*   ExternalPublic - en av IP-adresser tillhör Azure Virtual Network medan den andra IP-adressen är en offentlig IP-adress som inte är i Azure, har inte rapporterats som skadlig i ASC-flöden som trafikanalys förbrukar för bearbetning av intervallet mellan ” FlowIntervalStartTime_t ”och” FlowIntervalEndTime_t ”. 
*   MaliciousFlow - en av IP-adresser tillhör azure-nätverk medan den andra IP-adressen är en offentlig IP-adress som inte är i Azure och har rapporterats som skadlig i ASC-flöden som trafikanalys förbrukar för bearbetning av intervallet mellan ” FlowIntervalStartTime_t ”och” FlowIntervalEndTime_t ”. 
*   UnknownPrivate - en av IP-adresser tillhör Azure Virtual Network medan den andra IP-adressen tillhör intervall för privata IP-adresser som definieras i RFC 1918 och gick inte att mappa av trafikanalys till kundägda plats eller Azure Virtual Network.
*   Okänd – det går inte att mappa något av IP-adresser i flöden med kunden i Azure samt lokalt (plats).

### <a name="next-steps"></a>Nästa steg
Om du vill få svar på vanliga frågor och svar, se [Traffic analytics vanliga frågor och svar](traffic-analytics-faq.md) information om funktionerna finns i [Traffic analytics-dokumentation](traffic-analytics.md)
    


    


