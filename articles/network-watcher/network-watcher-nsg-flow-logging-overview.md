---
title: Introduktion till flödet loggning för nätverkssäkerhet grupper med Azure Nätverksbevakaren | Microsoft Docs
description: Den här artikeln beskriver hur du använder funktionen NSG flödet loggar i Azure Nätverksbevakaren.
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
ms.openlocfilehash: c6a24fbca37d6aa1d775a70c708a139dfb70b813
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182433"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introduktion till flödet loggning för nätverkssäkerhetsgrupper

Nätverket säkerhetsloggar grupp (NSG) flöde är en funktion i Nätverksbevakaren där du kan visa information om ingående och utgående IP-trafik via en NSG. Flödet loggar skrivs i json-format och visa utgående och inkommande flöden på grundval av per regel, nätverksgränssnitt (NIC) flödet gäller, 5-tuppel information om flödet (källan/målet IP-källan/målet port och protokoll) och om trafiken var tillåts eller nekas.

![Översikt över flödet-loggar](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Medan flödet loggar mål NSG: er, som inte visas samma som de andra loggarna. Flödet loggar lagras i ett lagringskonto och följ Loggningssökvägen som visas i följande exempel:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Samma bevarandeprinciper för andra loggar gäller flödet loggar. Du kan ange princip för loggperiod mellan 1 dag och 365 dagar. Om en bevarandeprincip inte är inställd bevaras loggarna för evigt.

## <a name="log-file"></a>Loggfil

Flödet loggar innehåller följande egenskaper:

* **tid** - tid när händelsen loggades.
* **system-ID** -Nätverkssäkerhetsgruppen resurs-ID.
* **kategori** -kategori för händelsen. Kategorin är alltid **NetworkSecurityGroupFlowEvent**
* **ResourceId** -resurs-Id för NSG: N
* **operationName** -alltid NetworkSecurityGroupFlowEvents
* **Egenskaper för** – en samling av egenskaper i flöde
    * **Version** -versionsnumret för Händelseschema flöda logg
    * **flöden** – en samling av flöden. Den här egenskapen har flera poster för olika regler
        * **regeln** -regel för flöden är listade
            * **flöden** – en samling av flöden
                * **Mac** -MAC-adressen för nätverkskortet för den virtuella datorn där flödet har hämtats
                * **flowTuples** -en sträng som innehåller flera egenskaper för flöde tuppel i CSV-format
                    * **Tidsstämpel** -värdet är tidsstämpeln för när flödet uppstod i UNIX EPOK format
                    * **Käll-IP** -käll-IP
                    * **Mål IP** -mål-IP
                    * **Källport** -källport
                    * **Målport** -målet Port
                    * **Protokollet** -protokollet för flödet. Giltiga värden är **T** för TCP och **U** för UDP
                    * **Infrastrukturtrafiken rör** -riktningen för trafikflödet. Giltiga värden är **jag** för inkommande och **O** för utgående.
                    * **Trafik** – oavsett om trafik tillåts eller nekas. Giltiga värden är **A** för tillåtna och **D** för nekad.

Den text som visas nedan är ett exempel på en logg i flödet. Som du ser finns det flera poster som följer egenskapslistan som beskrivs i föregående avsnitt.

> [!NOTE]
> Värdena i den **flowTuples* egenskapen är en kommaavgränsad lista.
 
```json
{
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

## <a name="next-steps"></a>Nästa steg

- Information om hur du aktiverar flödet loggar finns [aktiverar NSG flödet loggning](network-watcher-nsg-flow-logging-portal.md).
- Läs mer om NSG loggning i [logga analytics för nätverkssäkerhetsgrupper (NSG: er)](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- För att fastställa om trafik tillåts eller nekas till eller från en virtuell dator, se [diagnostisera VM-trafik filter nätverksproblem](diagnose-vm-network-traffic-filtering-problem.md)