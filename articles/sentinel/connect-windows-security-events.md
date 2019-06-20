---
title: Ansluta Windows security händelsedata till Sentinel-förhandsversion i Azure | Microsoft Docs
description: Lär dig hur du ansluter Windows security händelsedata till Sentinel-Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 36d38aa82b4f0ec8d7d9ef6ebb1145b1fcc334df
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190572"
---
# <a name="connect-windows-security-events"></a>Ansluta till Windows säkerhetshändelser 

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan strömma alla säkerhetshändelser från Windows-servrar som ansluter till din Azure Sentinel-arbetsyta. Den här anslutningen kan du visa instrumentpaneler, skapa anpassade varningar och förbättra undersökningen. Detta ger dig en överblick över nätverket och förbättrar din säkerhetsfunktioner för åtgärden.  Du kan välja vilka händelser att strömma:

- **Alla händelser** -alla Windows säkerhets- och AppLocker-händelser.
- **Vanliga** – en standarduppsättning av händelser i granskningssyfte. En fullständig användarspårning ingår i den här uppsättningen. Den här uppsättningen innehåller till exempel både användarinloggningar och utloggningar (händelse-ID 4634). Vi tar granskning åtgärder som att ändringarna, viktiga domain controller Kerberos åtgärder och andra händelser som rekommenderas av organisationer inom.

Händelser som har mycket små volymer ingick i den gemensamma som huvudsakliga skälet att välja det över alla händelser är att minska mängden och inte för att filtrera bort specifika händelser.
- **Minimal** – en liten uppsättning händelser som kan tyda på potentiella hot. Genom att aktivera det här alternativet kan du inte har fullständiga spårningen.  Den här uppsättningen innehåller endast de händelser som kan indikera en lyckad intrång och viktiga händelser som har en mycket låg volym. Exempelvis kan den här uppsättningen innehåller lyckade och misslyckade användarinloggning (händelse-ID 4624 4625), men den innehåller inte utloggning som är viktiga för granskning, men inte beskrivande för identifiering och har relativt stor volym. De flesta av datavolymen för den här uppsättningen är inloggningshändelser och skapa processhändelse (händelse-ID 4688).
- **Ingen** -inga säkerhets- eller AppLocker-händelser.

> [!NOTE]
> 
> - Data lagras i den geografiska platsen för arbetsytan där du kör Azure Sentinel.

I följande lista innehåller en fullständig uppdelning av säkerhets- och AppLocker händelse-ID för varje uppsättning:

| Datanivå | Insamlade händelser indikatorer |
| --- | --- |
| Minimalt | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Common | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

## <a name="set-up-the-windows-security-events-connector"></a>Konfigurera Windows security händelser connector

Att helt integrera dina säkerhetshändelser i Windows med Azure Sentinel:

1. I Sentinel-Azure-portalen väljer **datakopplingar** och klicka sedan på den **Windows säkerhetshändelser** panelen. 
1. Välj vilka datatyper som du vill spela.
1. Klicka på **Uppdatera**.
6. Om du vill använda relevanta schema i Log Analytics för Windows-säkerhetshändelser, Sök efter **SecurityEvent**.

## <a name="validate-connectivity"></a>Verifiera anslutningen

Det kan ta cirka 20 minuter tills loggarna börjar visas i Log Analytics. 



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Windows säkerhetshändelser till Sentinel-Azure. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).

