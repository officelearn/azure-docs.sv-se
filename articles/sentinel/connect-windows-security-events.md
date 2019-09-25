---
title: Anslut Windows säkerhets händelse data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Windows säkerhets händelse data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 34762d561edfa5b37b1841c55f3fff6a4b8337a3
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240505"
---
# <a name="connect-windows-security-events"></a>Ansluta till Windows säkerhetshändelser 



Du kan strömma alla säkerhets händelser från Windows-servrar som är anslutna till din Azure Sentinel-arbetsyta. Med den här anslutningen kan du Visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Detta ger dig bättre insikt i din organisations nätverk och förbättrar dina säkerhets åtgärder.  Du kan välja vilka händelser som ska strömmas:

- **Alla händelser** – alla Windows-säkerhets-och AppLocker-händelser.
- **Vanliga** – en standard uppsättning händelser för gransknings syfte. En fullständig användar Gransknings logg ingår i den här uppsättningen. Den här uppsättningen innehåller till exempel både användar inloggnings-och användar inloggnings händelser (händelse-ID 4634). Vi tar granskning åtgärder som att ändringarna, viktiga domain controller Kerberos åtgärder och andra händelser som rekommenderas av organisationer inom.

Händelser som har mycket små volymer ingick i den gemensamma som huvudsakliga skälet att välja det över alla händelser är att minska mängden och inte för att filtrera bort specifika händelser.
- **Minimal** – en liten uppsättning händelser som kan tyda på potentiella hot. Genom att aktivera det här alternativet kan du inte ha en fullständig Gransknings logg.  Den här uppsättningen omfattar bara händelser som kan tyda på en lyckad överträdelse och viktiga händelser som har en mycket låg volym. Den här uppsättningen innehåller till exempel användaren lyckade och misslyckade inloggningar (händelse-ID 4624, 4625), men innehåller inte information om utloggning, vilket är viktigt för granskning men inte meningsfullt för identifiering och har en relativt hög volym. Merparten av data volymen för den här mängden är inloggnings händelser och händelse för att skapa processer (händelse-ID 4688).
- **Ingen** -inga säkerhets-eller AppLocker-händelser.

> [!NOTE]
> 
> - Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.
> - Om Azure Security Center och Azure Sentinel körs på samma arbets yta kan säkerhets händelse anslutningen bara anslutas från antingen Azure Security Center eller Azure Sentinel. Om du vill hantera dessa händelser från Azure Sentinel rekommenderar vi att du kopplar från det från Azure Security Center och ansluter det bara till Azure Sentinel.


I följande lista finns en fullständig analys av händelse-ID: n för säkerhet och app Locker för varje uppsättning:

| Datanivå | Insamlade händelser indikatorer |
| --- | --- |
| Minimalt | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Common | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

## <a name="set-up-the-windows-security-events-connector"></a>Konfigurera Windows Security Events Connector

För att helt integrera dina Windows-säkerhetshändelser med Azure Sentinel:

1. I Azure Sentinel-portalen väljer du **data kopplingar** och klickar sedan på panelen **Windows säkerhets händelser** . 
1. Välj vilka data typer du vill strömma.
1. Klicka på **Uppdatera**.
6. Om du vill använda det relevanta schemat i Log Analytics för säkerhets händelser i Windows söker du efter **SecurityEvent**.

## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta ungefär 20 minuter innan loggarna börjar visas i Log Analytics. 



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Windows säkerhets händelser till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).

