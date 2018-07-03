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
ms.openlocfilehash: ae4edb82fa5e192a30d297dae82199bb7efca0c2
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344979"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introduktion till flödesloggar för nätverkssäkerhetsgrupper

Flödesloggar för Network security group (NSG) är en funktion i Network Watcher där du kan visa information om ingående och utgående IP-trafik via en NSG. Flödesloggar skrivs i json-format och visa utgående och inkommande flöden på basis av per regel, nätverksgränssnitt (NIC) flödet som gäller för, 5-tuppel information om flödet (källa/mål-IP, källa/mål port och protokoll), och om trafiken var tillåts eller nekas.

![Översikt över flödet-loggar](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

När flödet loggar target NSG: er, de är inte visas samma som de andra loggarna. Flödesloggar lagras endast i ett storage-konto och följ Loggningssökvägen som visas i följande exempel:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Samma bevarandeprinciper som visas för andra loggar gäller flödesloggar. Du kan ange bevarandeprincip för log mellan 1 dag och 2147483647 dagar. Om en bevarandeprincip inte är inställd bevaras loggarna för evigt.

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
                    * **Trafik** – oavsett om trafik tillåts eller nekas. Giltiga värden är **A** för tillåtna och **D** för nekad.

Texten nedan är ett exempel på en flow-log. Som du ser finns det flera poster som följer egenskapslistan som beskrivs i föregående avsnitt.

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

- Läs hur du aktiverar flödesloggar i [aktiverar NSG-flödesloggar](network-watcher-nsg-flow-logging-portal.md).
- Läs mer om NSG loggning i [Log analytics för nätverkssäkerhetsgrupper (NSG)](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- För att avgöra om trafik tillåts eller nekas till eller från en virtuell dator, se [diagnostisera ett problem med VM network trafik filter](diagnose-vm-network-traffic-filtering-problem.md)
