---
title: Introduktion till flödesloggning för NSG:er
titleSuffix: Azure Network Watcher
description: I den här artikeln beskrivs hur du använder funktionen NSG-flödesloggar i Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: fb4a55b9757748581e26f3d6594f9be2139658cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228262"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introduktion till flödesloggning för nätverkssäkerhetsgrupper

NSG-flödesloggar (nätverkssäkerhetsgrupp) är en funktion i Network Watcher som gör att du kan visa information om inkommande och utgående IP-trafik via en nätverkssäkerhetsgrupp. Flödesloggar skrivs i JSON-format och visar utgående och inkommande flöden per regel, det nätverksgränssnitt (NIC) som flödet gäller för, 5-tupelinformation om flödet (käll-/mål-IP, käll-/målport och protokoll), huruvida trafiken tilläts eller nekades samt, i version 2, dataflödesinformation (byte och paket).


![översikt över flödesloggar](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Flödesloggar är mål-NSG:er, men de visas inte på samma sätt som de andra loggarna. Flödesloggar lagras endast i ett lagringskonto och följer loggningssökvägen som visas i följande exempel:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
Du kan analysera flödesloggar och få insikter i nätverkstrafiken med hjälp av [trafikanalys](traffic-analytics.md).

Samma bevarandeprinciper som visas för andra loggar gäller för flödesloggar. Du kan ange logglagringsprincipen från 1 dag till 365 dagar. Om en bevarandeprincip inte har angetts underhålls loggarna för alltid.

## <a name="log-file"></a>Loggfil

Flödesloggar innehåller följande egenskaper:

* **tid** - Tid då händelsen loggades
* **systemId** - Resurs-ID för nätverkssäkerhetsgruppen.
* **kategori** - Kategorin för händelsen. Kategorin är alltid **NetworkSecurityGroupFlowEvent**
* **resourceid** - NSG:s resurs-ID
* **operationName** - Alltid NetworkSecurityGroupFlowEvents
* **egenskaper** - En samling egenskaper för flödet
    * **Version** - Versionsnummer för händelseschemat för flödesloggen
    * **flöden** - En samling flöden. Den här egenskapen har flera poster för olika regler
        * **regel** - Regel för vilken flödena visas
            * **flöden** - en samling flöden
                * **mac** - MAC-adressen för nätverkskortet för den virtuella datorn där flödet samlades in
                * **flowTuples** - En sträng som innehåller flera egenskaper för flödestuppeln i kommaavgränsat format
                    * **Tidsstämpel** - Det här värdet är tidsstämpeln för när flödet inträffade i UNIX-epokformat
                    * **Käll-IP** - Käll-IP
                    * **Destination IP** - Målet IP
                    * **Källport** - Källporten
                    * **Destinationsport** - Destinationsporten
                    * **Protokoll** - Protokollet för flödet. Giltiga värden är **T** för TCP och **U** för UDP
                    * **Trafikflöde** - Riktningen på trafikflödet. Giltiga värden är **jag** för inkommande och **O** för utgående.
                    * **Trafikbeslut** - Om trafiken var tillåten eller nekad. Giltiga värden är **A** för tillåtna och **D** för nekad.
                    * **Flödestillstånd - Endast version 2** - fångar flödets tillstånd. Möjliga tillstånd är **B**: Börja, när ett flöde skapas. Statistik tillhandahålls inte. **C**: Fortsätter (Continuing) för en pågående flöde. Statistik tillhandahålls med 5 minuters mellanrum. **E**: Slutet (End), när ett flöde avslutas. Statistik tillhandahålls.
                    * **Paket - Endast källa till mål - Endast version 2** Det totala antalet TCP- eller UDP-paket som skickats från källa till mål sedan den senaste uppdateringen.
                    * **Antal skickade - Endast källa till mål - Endast version 2** Det totala antalet TCP- eller UDP-paketbyte som skickats från källa till mål sedan den senaste uppdateringen. Paketbyte omfattar paketets huvud och nyttolast.
                    * **Paket - Mål till källa - Endast version 2** Det totala antalet TCP- eller UDP-paket som skickats från mål till källa sedan den senaste uppdateringen.
                    * **Antal skickade - Destination till källa - Endast version 2** Det totala antalet TCP- och UDP-paketbyte som skickats från mål till källa sedan den senaste uppdateringen. Paketbyte omfattar paketets huvud och nyttolast.

## <a name="nsg-flow-logs-version-2"></a>NSG flödesloggar version 2

Version 2 av loggarna introducerar flödestillstånd. Du kan konfigurera vilken version av flödesloggar du får. Mer information om hur du aktiverar flödesloggar finns i [Aktivera NSG-flödesloggning](network-watcher-nsg-flow-logging-portal.md).

Flödestillstånd *B* registreras när ett flöde initieras. Flödestillstånd *C* och flödestillstånd *E* är tillstånd som markerar fortsättningen av ett flöde respektive flödesavslutning. Både *C-* och *E-tillstånd* innehåller information om trafikbandbredd.

**Exempel**: Flödesupplar från en TCP-konversation mellan 185.170.185.105:35370 och 10.2.0.4:23:

"1493763938 185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I, A,E,52,29952,47,27072"

För *fortsättnings-C-* och *slut-E-flödestillstånd* räknas antal byte och paket från tiden för föregående flödesuppppelpost. Med hänvisning till föregående exempelkonversation är det totala antalet överförda paket 1021+52+8005+47 = 9125. Det totala antalet överförda byte är 588096+29952+4610880+27072 = 5256000.

Texten som följer är ett exempel på en flödeslogg. Som du kan se finns det flera poster som följer egenskapslistan som beskrivs i föregående avsnitt.

## <a name="nsg-flow-logging-considerations"></a>NSG-flödesloggning

**Överväganden för lagringskonto:** 

- Plats: Lagringskontot som används måste finnas i samma region som NSG.
- Självhantera tangentrotation: Om du ändrar/roterar åtkomstnycklarna till ditt lagringskonto slutar NSG:s flödesloggar att fungera. För att åtgärda problemet måste du inaktivera och sedan återaktivera NSG-flödesloggar.

**Aktivera NSG-flödesloggning på alla NSG-enheter som är kopplade till en resurs**: Flödesloggning i Azure är konfigurerad på NSG-resursen. Ett flöde associeras bara till en NSG-regel. I scenarier där flera NSG-grupper används rekommenderar vi att NSG-flödesloggning är aktiverat på alla NSG-enheter som tillämpas på en resurs undernät eller nätverksgränssnitt för att säkerställa att all trafik registreras. Mer information finns i [hur trafiken utvärderas](../virtual-network/security-overview.md#how-traffic-is-evaluated) i Nätverkssäkerhetsgrupper.

**Flödesloggningskostnader:** NSG-flödesloggning faktureras på volymen av producerade loggar. Hög trafikvolym kan resultera i stora flödesloggvolym och tillhörande kostnader. NSG Flow log prissättning inkluderar inte de underliggande kostnaderna för lagring. Att använda bevarandeprincipfunktionen med NSG-flödesloggning innebär att du ådrar sig separata lagringskostnader under längre tidsperioder. Om du inte behöver funktionen för bevarandeprincip rekommenderar vi att du anger det här värdet till 0. Mer information finns i [Priser för Network Watcher](https://azure.microsoft.com/pricing/details/network-watcher/) och [Azure Storage Priser](https://azure.microsoft.com/pricing/details/storage/) för ytterligare information.

**Inkommande flöden som loggas från internet-IP:er till virtuella datorer utan offentliga IP-adresser:** virtuella datorer som inte har en offentlig IP-adress som tilldelats via en offentlig IP-adress som är associerad med nätverkskortet som en offentlig IP-ip på instansnivå, eller som ingår i en grundläggande sluten belastningsutjämningspool, använder [standard-SNAT](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) och har en IP-adress som tilldelats av Azure för att underlätta utgående anslutning. Därför kan du se flödesloggposter för flöden från IP-adresser på Internet, om flödet är avsett för en port i intervallet portar som tilldelats för SNAT. Azure tillåter inte dessa flöden till den virtuella datorn, men försöket loggas och visas i Network Watchers NSG-flödeslogg efter design. Vi rekommenderar att oönskad inkommande internettrafik uttryckligen blockeras med NSG.

**Felaktiga byte- och paketantal för tillståndslösa flöden**: [NSG (Network Security Groups)](https://docs.microsoft.com/azure/virtual-network/security-overview) implementeras som en [tillståndskänslig brandvägg](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true). Men många standard / interna regler som styr flödet av trafik genomförs på ett tillståndslöst sätt. På grund av plattformsbegränsningar registreras inte antalet byte och paket för tillståndslösa flöden (det vill säga trafikflöden som går igenom tillståndslösa regler), de registreras endast för tillståndskänsliga flöden. Följaktligen kan antalet byte och paket som rapporteras i NSG-flödesloggar (och Traffic Analytics) skilja sig från faktiska flöden. Denna begränsning beräknas vara fastställd i juni 2020.

## <a name="sample-log-records"></a>Exempel på loggposter

Texten som följer är ett exempel på en flödeslogg. Som du kan se finns det flera poster som följer egenskapslistan som beskrivs i föregående avsnitt.


> [!NOTE]
> Värden i egenskapen **flowTuples* är en kommaavgränsad lista.
 
### <a name="version-1-nsg-flow-log-format-sample"></a>Exempel på NSG-flödesloggformat för version 1 NSG
```json
{
    "records": [
        {
            "time": "2017-02-16T22:00:32.8950000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A",
                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A",
                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A",
                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2017-02-16T22:01:32.8960000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",
                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",
                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
    "records":
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        ,
        ...
```
### <a name="version-2-nsg-flow-log-format-sample"></a>Exempel på NSG-flödesloggformat för version 2 NSG
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        ...
```

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du aktiverar flödesloggar finns i [Aktivera NSG-flödesloggning](network-watcher-nsg-flow-logging-portal.md).
- Mer information om hur du läser flödesloggarna finns i [Läsa NSG-flödesloggar](network-watcher-read-nsg-flow-logs.md).
- Mer information om NSG-loggning finns i [Azure Monitor-loggar för nätverkssäkerhetsgrupper (NSG)](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Information om huruvida trafik tillåts eller nekas till eller från en virtuell dator finns i Diagnostisera ett problem med ett problem med [vm-nätverkstrafikfilter](diagnose-vm-network-traffic-filtering-problem.md)
