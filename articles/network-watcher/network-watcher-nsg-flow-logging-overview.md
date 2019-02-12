---
title: Introduktion till flödesloggar för nätverkssäkerhet grupper med Azure Network Watcher | Microsoft Docs
description: Den här artikeln beskriver hur du använder funktionen NSG flödet loggar i Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: ff3c1e51bcf0b7d730019fad66ed8b0c3fe17a20
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56097656"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introduktion till flödesloggar för nätverkssäkerhetsgrupper

Flödesloggar för Network security group (NSG) är en funktion i Network Watcher där du kan visa information om ingående och utgående IP-trafik via en NSG. Flödesloggar skrivs i JSON-format och visa utgående och inkommande flöden på basis av per regel nätverksgränssnitt (NIC) flödet gäller för 5-tuppel information om flödet (källa/mål-IP, källa/mål port och protokoll), om trafiken var tillåtna eller nekade, och i Version 2, dataflödesinformationen (byte och paket).


![Översikt över flödet-loggar](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

När flödet loggar target NSG: er, de är inte visas samma som de andra loggarna. Flödesloggar lagras endast i ett storage-konto och följ Loggningssökvägen som visas i följande exempel:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
Du kan analysera flödesloggar och få insikter om ditt nätverk trafik med hjälp av [traffic analytics](traffic-analytics.md).

Samma bevarandeprinciper som visas för andra loggar gäller flödesloggar. Du kan ange bevarandeprincip för log mellan 1 dag och 2147483647 dagar. Om en bevarandeprincip inte är inställd bevaras loggarna för evigt.

> [!NOTE] 
> Använder funktionen kvarhållning princip med NSG Flow loggning kan resultera i ett stort antal lagringsåtgärder och relaterade kostnader. Om du inte behöver principfunktionen kvarhållning, rekommenderar vi att du ställer in det här värdet till 0.


## <a name="log-file"></a>Loggfil

Flödesloggar innehålla följande egenskaper:

* **tid** – tid när händelsen loggades
* **system-ID** -Nätverkssäkerhetsgrupp resurs-Id.
* **kategori** -händelsens kategori. Kategorin är alltid **NetworkSecurityGroupFlowEvent**
* **ResourceId** -resurs-Id för NSG: N
* **operationName** -alltid NetworkSecurityGroupFlowEvents
* **Egenskaper för** – en uppsättning egenskaper för flödet
    * **Version** -versionsnumret för det Flow händelseschemat
    * **flöden** – en samling av. Den här egenskapen har flera poster för olika regler
        * **regeln** -regel för som flöden visas
            * **flöden** – en samling av
                * **Mac** -MAC-adressen för nätverkskortet för den virtuella datorn där flödet har hämtats
                * **flowTuples** – en sträng som innehåller flera egenskaper för flow-tuppel i CSV-format
                    * **Tidsstämpel** – det här värdet är tidsstämpeln för när flödet har inträffat i UNIX EPOCH format
                    * **Käll-IP** -käll-IP
                    * **Målets IP** -mål-IP
                    * **Källport** -källporten
                    * **Målport** -målet Port
                    * **Protokollet** -protokollet för flödet. Giltiga värden är **T** för TCP och **U** för UDP
                    * **Traffic Flow** -trafikflödet riktning. Giltiga värden är **jag** för inkommande och **O** för utgående.
                    * **Trafik beslut** – oavsett om trafik tillåts eller nekas. Giltiga värden är **A** för tillåtna och **D** för nekad.
                    * **Läget för energiflöde - Version 2 endast** -samlar in tillståndet för flödet. Möjliga tillstånd är **B**: Början när ett flöde skapas. Statistik tillhandahålls inte. **C**: Fortsätter för ett pågående flöde. Statistik tillhandahålls med 5 minuters mellanrum. **E**: Slutet (End), när ett flöde avslutas. Statistik tillhandahålls.
                    * **Paket - källa till mål - Version 2 endast** det totala antalet TCP eller UDP-paket som skickats från källa till mål sedan senaste uppdateringen.
                    * **Byte som skickats - källa till mål - Version 2 endast** det totala antalet TCP eller UDP-paket byte som skickats från källan till målet sedan senaste uppdateringen. Paketbyte omfattar paketets huvud och nyttolast.
                    * **Paket - mål-källan – Version 2 endast** det totala antalet TCP eller UDP-paket som skickats från mål till källa sedan senaste uppdateringen.
                    * **Byte som skickats - mål-källan – Version 2 endast** det totala antalet TCP och UDP-paket byte som skickats från mål till källa sedan senaste uppdateringen. Paketbyte omfattar paketets huvud och nyttolast.

## <a name="nsg-flow-logs-version-2"></a>NSG-flödesloggarna version 2
> [!NOTE] 
> Flödesloggar av version 2 är endast tillgängliga i regionen USA, västra centrala. Om du aktiverar loggar av version 2 i en region som inte stöds matas loggar av version 1 in i ditt lagringskonto.

Version 2 av loggarna introducerar flow tillstånd. Du kan konfigurera vilken version av flödesloggar felmeddelandet. Läs hur du aktiverar flödesloggar i [aktiverar NSG-flödesloggar](network-watcher-nsg-flow-logging-portal.md).

Flow tillstånd *B* registreras när ett flöde ska initieras. Flow tillstånd *C* och läget för energiflöde *E* är tillstånd som markerar fortsättning av ett flöde och flow avslutning, respektive. Båda *C* och *E* tillstånd innehåller information om trafik bandbredd.

För fortsatt *C* och *E* flow anger antal byte och paket är sammanställda antalen från tidpunkten för föregående flow tuppel post. Refererar till den tidigare exempel konversationen, är det totala antalet paket som överförts 1021 + 52 + 8005 + 47 = 9125. Det totala antalet byte som överfördes är 588096 + 29952 + 4610880 + 27072 = 5256000.

**Exempel**: Flow tupplar från en TCP-konversation mellan 185.170.185.105:35370 och 10.2.0.4:23:

”1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,” ”1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880” ”1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072”

För fortsatt *C* och *E* flow anger antal byte och paket är sammanställda antalen från tidpunkten för föregående flow tuppel post. Refererar till den tidigare exempel konversationen, är det totala antalet paket som överförts 1021 + 52 + 8005 + 47 = 9125. Det totala antalet byte som överfördes är 588096 + 29952 + 4610880 + 27072 = 5256000.

Texten nedan är ett exempel på en flow-log. Som du ser finns det flera poster som följer egenskapslistan som beskrivs i föregående avsnitt.

## <a name="nsg-flow-logging-considerations"></a>NSG Flow loggning överväganden

**Aktivera NSG Flow loggning på alla Nätverkssäkerhetsgrupper som är kopplade till en resurs**: Flow loggning i Azure konfigureras på NSG-resurs. Ett flöde kan endast kopplas till en NSG-regel. I scenarier där flera NSG: er används, rekommenderar vi att NSG-flödesloggar är aktiverad på alla Nätverkssäkerhetsgrupper tillämpas resursens undernät eller ett gränssnitt för att se till att all trafik registreras. Se [hur trafik utvärderas](../virtual-network/security-overview.md#how-traffic-is-evaluated) mer information om Nätverkssäkerhetsgrupper. 

**Flow loggning kostnader**: NSG-flödesloggar debiteras mängden loggar som producerats. Hög trafik kan resultera i stora flow loggvolymen och relaterade kostnader. NSG-Flödesloggar log priserna inkluderar inte de underliggande kostnaderna för lagring. Använder funktionen kvarhållning princip med NSG Flow loggning kan resultera i ett stort antal lagringsåtgärder och relaterade kostnader. Om du inte behöver principfunktionen kvarhållning, rekommenderar vi att du ställer in det här värdet till 0. Se [Watcher prissättningen](https://azure.microsoft.com/en-us/pricing/details/network-watcher/) och [priser för Azure Storage](https://azure.microsoft.com/en-us/pricing/details/storage/) för ytterligare information.

## <a name="sample-log-records"></a>Exempel loggposter

Texten nedan är ett exempel på en flow-log. Som du ser finns det flera poster som följer egenskapslistan som beskrivs i föregående avsnitt.


> [!NOTE]
> Värdena i den **flowTuples* egenskapen är en kommaavgränsad lista.
 
### <a name="version-1-nsg-flow-log-format-sample"></a>Format för version 1 NSG flow loggexemplet
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
### <a name="version-2-nsg-flow-log-format-sample"></a>Format för version 2 NSG flow loggexemplet
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

- Läs hur du aktiverar flödesloggar i [aktiverar NSG-flödesloggar](network-watcher-nsg-flow-logging-portal.md).
- Läs hur du läser flödesloggar i [Läs NSG-flödesloggar](network-watcher-read-nsg-flow-logs.md).
- Läs mer om NSG loggning i [Log analytics för nätverkssäkerhetsgrupper (NSG)](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- För att avgöra om trafik tillåts eller nekas till eller från en virtuell dator, se [diagnostisera ett problem med VM network trafik filter](diagnose-vm-network-traffic-filtering-problem.md)
