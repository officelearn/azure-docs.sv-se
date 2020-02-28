---
title: 'Introduktion till flödes loggning för NSG: er'
titleSuffix: Azure Network Watcher
description: Den här artikeln förklarar hur du använder funktionen NSG Flow-loggar i Azure Network Watcher.
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
ms.openlocfilehash: a7f30a5a70f71ff958d9b222d2a9e21d03d85e06
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670771"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introduktion till flödes loggning för nätverks säkerhets grupper

Flödes loggar för nätverks säkerhets grupper (NSG) är en funktion i Network Watcher som gör att du kan visa information om inkommande och utgående IP-trafik via en NSG. Flödes loggar skrivs i JSON-format och visar utgående och inkommande flöden per regel, nätverks gränssnittet (NIC) flödet gäller för 5-tuple-information om flödet (käll-/mål-IP, käll-och mål Port och protokoll), om trafiken tilläts eller nekades, och i version 2, data flödes information (byte och paket).


![Översikt över flödes loggar](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Vid flödes loggningens mål NSG: er, visas de inte på samma sätt som de andra loggarna. Flödes loggar lagras bara inom ett lagrings konto och följer loggnings Sök vägen som visas i följande exempel:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
Du kan analysera flödes loggar och få insikter om din nätverks trafik med hjälp av [trafik analys](traffic-analytics.md).

Samma bevarande principer som visas för andra loggar gäller för flödes loggar. Du kan ställa in logg bevarande princip från 1 dag till 365 dagar. Om ingen bevarande princip anges bibehålls loggarna för alltid.

## <a name="log-file"></a>Loggfil

Flödes loggar innehåller följande egenskaper:

* **tid** när händelsen loggades
* **systemId** -nätverks säkerhets grupp resurs-ID.
* **kategori** – händelsens kategori. Kategorin är alltid **NetworkSecurityGroupFlowEvent**
* **ResourceID** -resurs-ID för NSG
* **operationName** -Always NetworkSecurityGroupFlowEvents
* **Egenskaper** -en samling av flödets egenskaper
    * **Version** – versions nummer för flödes loggens händelse schema
    * **flöden** – en samling flöden. Den här egenskapen har flera poster för olika regler
        * **regel** – regel för vilken flödena visas
            * **flöden** – en samling flöden
                * **Mac** – Mac-adressen för nätverkskortet för den virtuella dator där flödet samlades in
                * **flowTuples** – en sträng som innehåller flera egenskaper för flödes tuppeln i kommaavgränsat format
                    * **Tidsstämpel** – det här värdet är tidsstämpeln för när flödet inträffade i formatet UNIX-epok
                    * **Käll-IP** – käll-IP
                    * **Mål-IP** -mål-IP
                    * **Källport** – käll porten
                    * **Målport** – mål porten
                    * **Protokoll** – protokollet för flödet. Giltiga värden är **T** för TCP och **U** för UDP
                    * **Trafikflöde** – riktningen för trafikflödet. Giltiga värden är **i** för inkommande och **O** för utgående trafik.
                    * **Trafik beslut** – huruvida trafik tillåts eller nekas. Giltiga värden är **A** för tillåtna och **D** för nekad.
                    * **Flödes tillstånd-version 2 endast** – fångar in flödets tillstånd. Möjliga tillstånd är **B**: Börja, när ett flöde skapas. Statistik tillhandahålls inte. **C**: Fortsätter (Continuing) för en pågående flöde. Statistik tillhandahålls med 5 minuters mellanrum. **E**: Slutet (End), när ett flöde avslutas. Statistik tillhandahålls.
                    * **Paket-källa till mål-version 2** Det totala antalet TCP-eller UDP-paket som har skickats från källan till målet sedan den senaste uppdateringen.
                    * **Skickade byte-källa till mål version 2** Det totala antalet TCP-eller UDP-paketfilter som har skickats från källan till målet sedan den senaste uppdateringen. Paketbyte omfattar paketets huvud och nyttolast.
                    * **Paket-mål till källa-endast version 2** Det totala antalet TCP-eller UDP-paket som har skickats från målet till källan sedan senaste uppdateringen.
                    * **Skickade byte – målet till källa-version 2** Det totala antalet byte för TCP-och UDP-paket som skickats från målet till källan sedan senaste uppdateringen. Paketbyte omfattar paketets huvud och nyttolast.

## <a name="nsg-flow-logs-version-2"></a>NSG Flow-loggar version 2

Version 2 av loggarna introducerar flödes tillstånd. Du kan konfigurera vilken version av flödes loggar du får. Information om hur du aktiverar flödes loggar finns i [Aktivera NSG flödes loggning](network-watcher-nsg-flow-logging-portal.md).

Flödets tillstånd *B* registreras när ett flöde initieras. Flow State *C* och Flow State *E* är lägen som markerar fortsättningen för en flödes-och flödes avslutning. Både *C* och *E* -tillstånd innehåller bandbredds information.

**Exempel**: Flow-tupler från en TCP-konversation mellan 185.170.185.105:35370 och 10.2.0.4:23:

"1493763938, 185.170.185.105, 10.2.0.4, 35370, 23, T, A, B,,,," "1493695838, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, C, 1021, 588096, 8005, 4610880" "1493696138, 185.170.185.105, 10.2.0.4, 35370, 23, T, A, E, 52, 29952, 47, 27072"

För tilläggs- *C* -och slut- *E* -flöde-tillstånd är antalet byte och paket sammanställda från tiden för den föregående flödes tupel posten. Referens till föregående exempel konversation, det totala antalet överförda paket är 1021 + 52 + 8005 + 47 = 9125. Det totala antalet överförda byte är 588096 + 29952 + 4610880 + 27072 = 5256000.

Texten som följer är ett exempel på en flödes logg. Som du kan se finns det flera poster som följer egenskaps listan som beskrivs i föregående avsnitt.

## <a name="nsg-flow-logging-considerations"></a>NSG flödes loggnings överväganden

**Överväganden för lagrings konto**: 

- Plats: det lagrings konto som används måste finnas i samma region som NSG.
- Själv hantering av nyckel rotation: om du ändrar/roterar åtkomst nycklarna till ditt lagrings konto slutar NSG Flow-loggar att fungera. För att åtgärda det här problemet måste du inaktivera och sedan återaktivera NSG Flow-loggar.

**Aktivera NSG flödes loggning på alla NSG: er som är kopplade till en resurs**: flödes loggning i Azure konfigureras på NSG-resursen. Ett flöde kommer bara att associeras med en NSG-regel. I scenarier där flera NSG: er används, rekommenderar vi att loggning av NSG-flöde är aktiverat på alla NSG: er använder en resurss undernät eller nätverks gränssnitt för att säkerställa att all trafik registreras. Mer information finns i [hur trafiken utvärderas](../virtual-network/security-overview.md#how-traffic-is-evaluated) i nätverks säkerhets grupper.

**Flödes loggnings kostnader**: NSG flödes loggning debiteras på volymen av loggar som skapats. Hög trafik volym kan resultera i stor flödes logg volym och tillhör ande kostnader. NSG Flow logg priser omfattar inte de underliggande lagrings kostnaderna. Om du använder funktionen bevarande princip med NSG flödes loggning kan det leda till en stor mängd lagrings åtgärder och tillhör ande kostnader. Om du inte behöver funktionen bevarande princip rekommenderar vi att du anger värdet till 0. Mer information finns i [Network Watcher priser](https://azure.microsoft.com/pricing/details/network-watcher/) och [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/) för ytterligare information.

**Inkommande flöden som loggats från Internet-IP-adresser till virtuella datorer utan offentliga**IP-adresser: virtuella datorer som inte har en offentlig IP-adress som tilldelats via en offentlig IP-adress som är kopplad till nätverkskortet som en offentlig IP-adress på instans nivå eller som är en del av en grundläggande belastningsutjämnare, använder [standard SNAT](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) och har en IP-adress som tilldelats av Azure för att under lätta Därför kan du se flödes logg poster för flöden från Internet-IP-adresser, om flödet är avsett för en port i intervallet för de portar som tilldelats för SNAT. Även om Azure inte tillåter dessa flöden till den virtuella datorn, loggas försöket och visas i Network Watcher flödes loggen för NSG efter design. Vi rekommenderar att oönskad inkommande Internet trafik uttryckligen blockeras med NSG.

**Felaktig byte och antal paket för tillstånds lösa flöden**: [nätverks säkerhets grupper (NSG: er)](https://docs.microsoft.com/azure/virtual-network/security-overview) implementeras som en [tillstånds känslig brand vägg](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true). Dock är många standard-/interna regler som styr trafik flödet implementerade i ett tillstånds lös läge. På grund av plattforms begränsningar registreras inga byte och paket för tillstånds lösa flöden (det vill säga trafik flöden genom tillstånds lösa regler), registreras bara för tillstånds känsliga flöden. Därför kan antalet byte och paket som rapporter ATS i NSG Flow-loggar (och Trafikanalys) skilja sig från faktiska flöden. Den här begränsningen är schemalagd för att fastställas senast 2020 juni.

## <a name="sample-log-records"></a>Exempel på logg poster

Texten som följer är ett exempel på en flödes logg. Som du kan se finns det flera poster som följer egenskaps listan som beskrivs i föregående avsnitt.


> [!NOTE]
> Värdena i **flowTuples* -egenskapen är en kommaavgränsad lista.
 
### <a name="version-1-nsg-flow-log-format-sample"></a>Version 1 NSG flödes logg format exempel
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
### <a name="version-2-nsg-flow-log-format-sample"></a>Version 2 NSG flöde logg format exempel
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

- Information om hur du aktiverar flödes loggar finns i [Aktivera NSG flödes loggning](network-watcher-nsg-flow-logging-portal.md).
- Information om hur du läser flödes loggarna finns i [läsa NSG Flow-loggar](network-watcher-read-nsg-flow-logs.md).
- Läs mer om NSG-loggning i [Azure Monitor loggar för nätverks säkerhets grupper (NSG: er)](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Information om hur du avgör om trafik tillåts eller nekas till eller från en virtuell dator finns i [diagnostisera ett problem med trafik filter för virtuella dator nätverk](diagnose-vm-network-traffic-filtering-problem.md)
