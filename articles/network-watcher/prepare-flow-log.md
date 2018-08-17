---
title: Azure network security flow log ändringar | Microsoft Docs
description: Läs mer om Azure network security flow log ändringar.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: jdial
ms.openlocfilehash: b3a5ca929c83f70c31d667c2d9c811623691cff8
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "40180904"
---
# <a name="network-security-group-flow-logs--version-2--upcoming-changes"></a>Network flödesloggarna security group - Version 2 – kommande ändringar

Network security group flödet loggar-format ändras börjar den 15 September 2018. Extra fält ger dig information på flödet tillstånd och inkrementella antalet byte och paket som överförts i varje riktning. Program som Parsar flödesloggar kommer att påverkas av den här ändringen och måste därför uppdateras. Den här artikeln innehåller information om ändringen.

## <a name="what-is-changing"></a>Vad ändras?

Versionen av loggar för nätverkssäkerhetsgrupper flow kommer att ändras från ”Version”: 1 för att ”Version”: 2, med ytterligare fält som har lagts till i den *flowTuples* -egenskapen i loggarna. Information om formatet finns i [hur ett flöde modelleras i version 2](#how-is-a-flow-modeled-in-version-2).

### <a name="version-1-flow-tuple-format"></a>Version 1 flow tuppel format

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,D"
```

### <a name="version-2-flow-tuple-format"></a>Version 2 flow tuppel format

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1,103,1,186"
```

## <a name="when-will-this-change-take-place"></a>När den här ändringen sker?

Den här ändringen hamnar i början av effekt **den 15 September 2018** i regionen västra centrala USA. Distribution av ändringen i regioner för offentliga moln slutförs med November 31 maj 2018, efter vilken endast version 2 loggar blir tillgängliga.

## <a name="am-i-affected-by-the-change"></a>Påverkas jag ändringen?

Du kan påverkas av den här ändringen om du skapar eller Använd ett program som bearbetar loggdata för network security group flöde.

### <a name="if-i-use-traffic-analytics"></a>Om jag använder trafikanalys?

Nej. Trafikanalys påverkas inte under övergången från version 1 till flödesloggar för version 2. Förbättringar kommer snart att dra nytta av ytterligare session och bandbredd informationen i version 2 flödesloggar.

### <a name="if-im-using-third-party-tooling"></a>Om jag använder tredjeparts-verktyg?

Ändringen av loggformat har meddelats i förväg med alla [Network Watcher partner](https://azure.microsoft.com/services/network-watcher). Kontakta leverantören för information.

### <a name="if-i-have-built-a-custom-application-or-integration"></a>Om jag har byggt ett anpassat program eller integrering?

**Ja**, behöver du ändra ditt programmet Hantera Flow NSG-loggar i det nya formatet.

## <a name="what-is-the-new-flow-logging-format"></a>Vad är det nya flöde loggning formatet?

Flödet loggar version 2 innehåller flow tupplar i följande format:

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1,103,1,186".
```

Tabellen nedan innehåller egenskapsnamn och beskrivningar för network security group version 2 flow tuppel. Fullständigt Exempelposter finns i [Version 2 exempelhändelse](#version2sampleevent).

| Egenskapsnamn                        | Värde           | Beskrivning                                                                                                                                                 |
| ------------------------------------ | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tidsstämpel                           | 1493763938      | Tidsstämpel för flow-post. UNIX EPOCH format används.                                                                                       |
| Källans IP-adress                    | 185.170.185.105 |                                                                                                                                                             |
| Mål-IP-adress               | 10.2.0.4        |                                                                                                                                                             |
| Källport                          | 35370           |                                                                                                                                                             |
| Målport                     | 23              |                                                                                                                                                             |
| Trafik-protokollet                     | T               | **T**: TCP och **U**: UDP.                                                                                                                                  |
| Trafik flödesriktning               | I               | **Jag**: inkommande trafik och **O**: utgående trafik.                                                                                                         |
| Trafik beslut                     | A               | **En**: flödet tilläts och **D**: Flow nekades.                                                                                                         |
| Läget för energiflöde                           | C               | **B**: börjar när ett flöde har skapats. Statistik tillhandahålls inte. **C**: fortsätter för en pågående flow. Statistik tillhandahålls med 5 minuters mellanrum. **E**: slutet, när ett flöde har avslutats. Statistik tillhandahålls.                                                                                                                                                        |
| Paket - källan till målet      | 1               | Det totala antalet TCP och UDP-paket som skickats från källa till mål sedan senaste uppdateringen.                                                                  |
| Byte som skickats - källan till målet   | 103             | Det totala antalet TCP och UDP-paket byte som skickats från källan till målet sedan senaste uppdateringen. Paket byte är nätverkspaketets huvud och nyttolast.         |
| Paket som skickats - mål till källa | 1               | Det totala antalet TCP och UDP-paket som skickats från mål till källa sedan senaste uppdateringen.                                                                  |
| Byte som skickats - mål till källa   | 186             | Det totala antalet TCP och UDP-paket byte som skickats från mål till källa sedan senaste uppdateringen. Paket byte är nätverkspaketets huvud och nyttolast.             |

## <a name="how-is-a-flow-modeled-in-version-2"></a>Hur modelleras ett flöde i version 2?

Version 2 av loggarna introducerar flow tillstånd. Flow tillstånd *B* registreras när ett flöde ska initieras. Flow tillstånd *C* och läget för energiflöde *E* är tillstånd som markerar fortsättning av ett flöde och flow avslutning, respektive. Båda *C* och *E* tillstånd innehåller information om trafik bandbredd.

**Exempel**: Flow tupplar från en TCP-konversation mellan 185.170.185.105:35370 och 10.2.0.4:23:

”1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,” ”1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880” ”1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072”

### <a name="how-does-the-format-differ-from-version-1"></a>Hur skiljer sig formatet från version 1?

I version 1 var en flow-tuppel [”1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,D”] skapas varje gång ett flöde är etablerat eller försökte upprättas (neka fall).

### <a name="how-are-bytes-and-packets-accounted-for"></a>Hur är byte och paket som behövs?

För fortsatt *C* och *E* flow anger antal byte och paket är sammanställda antalen från tidpunkten för föregående flow tuppel post. Refererar till den tidigare exempel konversationen, är det totala antalet paket som överförts 1021 + 52 + 8005 + 47 = 9125. Det totala antalet byte som överfördes är 588096 + 29952 + 4610880 + 27072 = 5256000.

### <a name="if-my-application-doesnt-understand-the-traffic-bandwidth-fields-how-does-version-2-affect-the-application"></a>Om mitt program inte förstår fälten trafik bandbredd, version 2 påverkar programmet?

Program som använder version 1 nätverkssäkerhet gruppera flow loggning vanligtvis räkna antalet unika flöden. Om ingen ändring parsa görs för att kompensera för läget för energiflöde, kan det hända att en felaktig ökning av antalet flöden som rapporteras. Räkna unika flöden kan åstadkommas genom att ignorera flow tupplar i *C* och *E* flow tillstånd.

## <a name="can-i-control-the-version-format-i-receive"></a>Kan jag styra versionsformat som jag har fått?

Nej. Aktivera och inaktivera flödesloggar påverkar inte loggarna utdataformat. Ändring från version 1 till version 2 loggar sker på basis av region efter region. När en region uppgraderas från version 1 för version 2, kan du se NSG-flödesloggarna i båda formaten. När distributionen är klar kommer att finnas version 2-loggar.

## <a name="while-the-change-occurs-can-i-see-both-formats-for-the-same-network-security-group"></a>Kan jag se båda formaten för samma nätverkssäkerhetsgrupp medan ändringen genomförs?

Ja. Inom en region inträffar övergången på basis av per mac-adress. Eftersom en nätverkssäkerhetsgrupp kan tillämpas på många datorer, kan du se loggar i båda formaten som skrivs till lagring. Loggar ska antingen vara version 1 eller 2.

## <a name="will-i-see-duplicate-data"></a>Kommer jag att få duplicerade data?

Det kommer inte att duplicering av flödet loggningsdata över format. Att kommer läggas till ett flöde i Version 1 eller Version 2-format, medan ändringen genomförs.

## <a name="how-can-i-test-the-new-format-ahead-of-time"></a>Hur kan jag testa det nya formatet förväg?

Ja. Du kan [hämta](https://aka.ms/nsgflowlogsv2blobsample) en version 2 flow log exempelfil att använda för att testa din lösning.

## <a name="are-there-changes-to-configuration-and-management-of-network-security-group-flow-logging"></a>Finns det några ändringar i konfiguration och hantering av nätverket flödesloggar för nätverkssäkerhetsgrupper?

Nej. Stöd för Azure Storage-konton i alla prenumerationer som delar samma Azure Active Directory-klient har [är](https://azure.microsoft.com/blog/new-azure-network-watcher-integrations-and-network-security-group-flow-logging-updates/) tidigare i år. Den här ändringen hjälper dig att minska antalet lagringskonton som behövs när du hanterar nätverk flödesloggar för nätverkssäkerhetsgruppen.

## <a name="questions-or-feedback"></a>Frågor eller kommentarer?

Vi ser fram emot att höra om din upplevelse med flödesloggar för nätverkssäkerhetsgruppen nätverk och Network Watcher. Du kan ge feedback eller förslag online eller via e-post till AzureNetworkWatcher@microsoft.com.

## <a name="version-2-sample"></a>Version 2-exempel

```json
{

"records": [

{

"time": "2018-08-03T17:00:54.5657764Z",

"systemId": "bbd48473-8ce0-4834-99bb-2e13b9b23ff8",

"category": "NetworkSecurityGroupFlowEvent",

"resourceId":
"/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FLOWLOGSV2DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MULTITIERAPP2-NSG",

"operationName": "NetworkSecurityGroupFlowEvents",

"properties": {

"Version": 2,

"flows": [

{

"rule": "DefaultRule_AllowInternetOutBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315592,10.1.1.6,204.79.197.200,62375,80,T,O,A,B,,,,",

"1533315595,10.1.1.6,204.79.197.200,62373,80,T,O,A,E,30,1784,92,123631",

"1533315597,10.1.1.6,204.79.197.200,62376,80,T,O,A,B,,,,",

"1533315601,10.1.1.6,204.79.197.200,62374,80,T,O,A,E,13,866,87,123079",

"1533315603,10.1.1.6,204.79.197.200,62377,80,T,O,A,B,,,,",

"1533315604,10.1.1.6,204.79.197.200,62375,80,T,O,A,E,33,1946,90,123247",

"1533315608,10.1.1.6,204.79.197.200,62378,80,T,O,A,B,,,,",

"1533315610,10.1.1.6,204.79.197.200,62376,80,T,O,A,E,20,1244,92,123355",

"1533315613,10.1.1.6,204.79.197.200,62379,80,T,O,A,B,,,,",

"1533315616,10.1.1.6,204.79.197.200,62377,80,T,O,A,E,24,1460,92,123355",

"1533315619,10.1.1.6,204.79.197.200,62380,80,T,O,A,B,,,,",

"1533315622,10.1.1.6,204.79.197.200,62378,80,T,O,A,E,23,1406,93,123409",

"1533315624,10.1.1.6,204.79.197.200,62381,80,T,O,A,B,,,,",

"1533315628,10.1.1.6,204.79.197.200,62379,80,T,O,A,E,16,1028,88,123133",

"1533315630,10.1.1.6,204.79.197.200,62382,80,T,O,A,B,,,,",

"1533315631,10.1.1.6,204.79.197.200,62380,80,T,O,A,E,13,866,87,123079",

"1533315635,10.1.1.6,204.79.197.200,62384,80,T,O,A,B,,,,",

"1533315637,10.1.1.6,204.79.197.200,62381,80,T,O,A,E,15,974,86,123025",

"1533315640,10.1.1.6,204.79.197.200,62385,80,T,O,A,B,,,,",

"1533315643,10.1.1.6,204.79.197.200,62382,80,T,O,A,E,16,1028,88,123139",

"1533315646,10.1.1.6,204.79.197.200,62386,80,T,O,A,B,,,,",

"1533315649,10.1.1.6,204.79.197.200,62384,80,T,O,A,E,21,1298,92,123355",

"1533315651,10.1.1.6,204.79.197.200,62387,80,T,O,A,B,,,,"

]

}

]

}

]

}

},

{

"time": "2018-08-03T17:01:54.5668880Z",

"systemId": "bbd48473-8ce0-4834-99bb-2e13b9b23ff8",

"category": "NetworkSecurityGroupFlowEvent",

"resourceId":
"/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FLOWLOGSV2DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MULTITIERAPP2-NSG",

"operationName": "NetworkSecurityGroupFlowEvents",

"properties": {

"Version": 2,

"flows": [

{

"rule": "DefaultRule_DenyAllInBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315685,80.211.83.37,10.1.1.6,45321,81,T,I,D,B,,,,"

]

}

]

},

{

"rule": "DefaultRule_AllowInternetOutBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315655,10.1.1.6,204.79.197.200,62385,80,T,O,A,E,20,1244,87,123079",

"1533315657,10.1.1.6,204.79.197.200,62388,80,T,O,A,B,,,,",

"1533315658,10.1.1.6,204.79.197.200,62386,80,T,O,A,E,26,1568,92,123355",

"1533315662,10.1.1.6,204.79.197.200,62389,80,T,O,A,B,,,,",

"1533315664,10.1.1.6,204.79.197.200,62387,80,T,O,A,E,15,974,88,123133",

"1533315667,10.1.1.6,204.79.197.200,62390,80,T,O,A,B,,,,",

"1533315670,10.1.1.6,204.79.197.200,62388,80,T,O,A,E,18,1136,88,123139",

"1533315673,10.1.1.6,204.79.197.200,62391,80,T,O,A,B,,,,",

"1533315676,10.1.1.6,204.79.197.200,62389,80,T,O,A,E,14,920,87,123079",

"1533315678,10.1.1.6,204.79.197.200,62392,80,T,O,A,B,,,,",

"1533315679,10.1.1.6,204.79.197.200,62390,80,T,O,A,E,31,1838,94,123739",

"1533315684,10.1.1.6,204.79.197.200,62393,80,T,O,A,B,,,,",

"1533315685,10.1.1.6,204.79.197.200,62391,80,T,O,A,E,28,1676,101,141199",

"1533315689,10.1.1.6,204.79.197.200,62394,80,T,O,A,B,,,,",

"1533315691,10.1.1.6,204.79.197.200,62392,80,T,O,A,E,21,1298,93,123409",

"1533315694,10.1.1.6,204.79.197.200,62395,80,T,O,A,B,,,,",

"1533315697,10.1.1.6,204.79.197.200,62393,80,T,O,A,E,26,1568,91,123393",

"1533315700,10.1.1.6,204.79.197.200,62396,80,T,O,A,B,,,,",

"1533315703,10.1.1.6,204.79.197.200,62394,80,T,O,A,E,14,920,89,123187",

"1533315705,10.1.1.6,204.79.197.200,62397,80,T,O,A,B,,,,",

"1533315706,10.1.1.6,204.79.197.200,62395,80,T,O,A,E,13,866,87,123079",

"1533315711,10.1.1.6,204.79.197.200,62398,80,T,O,A,B,,,,"

]

}

]

}

]

}

}

]

}
```

## <a name="version-1-sample"></a>Version 1-exempel

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

        } 

    ] 
}
```