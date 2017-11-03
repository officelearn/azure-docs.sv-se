---
title: "Skala enhet noden åtgärder i Azure-stacken | Microsoft Docs"
description: "Lär dig mer om att visa status för noden och använda på stänga av tömning och Återuppta nod åtgärder på ett Azure-stacken integrerat system."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: dbb68b10-c721-4188-aa07-584d0cd63138
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: twooley
ms.openlocfilehash: 3696cd0da0859bebf001f7749ac8874efd574046
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Skalningsåtgärder enhet noden i Azure-stacken

*Gäller för: Azure Stack integrerat system*

Den här artikeln beskriver hur du visar status för en skalningsenhet och dess associerade noder och hur du använder de tillgängliga nod åtgärderna. Inkludera slå på strömmen av noden åtgärder tömmer återuppta och reparera. Normalt använder du åtgärderna nod under fältet ersättning av delar eller för noden återställningsscenarier.

## <a name="view-the-status-of-a-scale-unit-and-its-nodes"></a>Visa status för en skalningsenhet och noder

Du kan enkelt visa status för en skalningsenhet och dess associerade noder i administratörsportalen.

Visa status för en skalningsenhet:

1. På den **Region management** panelen, väljer du regionen.
2. Till vänster under **infrastrukturresurser**väljer **Skalningsenheter**.
3. Välj skalningsenheten i resultaten.
 
Här kan du visa följande information:

- namn på område
- typ av system
- Totalt antal logiska kärnor
- Total mängd minne
- listan över enskilda noder och deras status. antingen igång eller stoppad.

![Skala körs sida vid sida som visar statusen för varje nod](media/azure-stack-node-actions/ScaleUnitStatus.PNG)

## <a name="view-information-about-a-scale-unit-node"></a>Visa information om en enhet skala-nod

Om du väljer en enskild nod kan du visa följande information:

- namn på område
- Server-modell
- IP-adressen för den hanteringsstyrenhet för baskort (BMC)
- Användningsstatus
- Totalt antal kärnor
- Total mängd minne
 
![Skala körs sida vid sida som visar statusen för varje nod](media/azure-stack-node-actions/NodeActions.PNG)

Du kan också utföra skalningsåtgärder enhet nod härifrån.

## <a name="scale-unit-node-actions"></a>Skalningsåtgärder enhet nod

När du visar information om en nod för skala enhet kan utföra du också noden åtgärder som:

- Slå på och stänga av
- tömning och återuppta
- reparera

Driftstatus för noden avgör vilka alternativ som är tillgängliga.

### <a name="power-off"></a>Stäng av

Den **stängs** stängs av noden. Det är samma som om du trycker på strömknappen. Det gör **inte** skicka en avstängningssignal till operativsystemet. Kontrollera du tömmer en scale unit nod först planerad stänga av åtgärder.

Den här åtgärden används vanligtvis när en nod är i låst tillstånd och inte längre svarar på förfrågningar.  

Så här kör du stänga av åtgärden med hjälp av PowerShell:

  ````PowerShell
  Stop-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ```` 

Använd webbgränssnitt BMC i stället förmodan som inte fungerar stänga av åtgärden.

### <a name="power-on"></a>Slå på

Den **slå på strömmen** stängs på noden. Det är samma som om du trycker på strömknappen. 

Att köra kraften på åtgärden med hjälp av PowerShell:

  ````PowerShell
  Start-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

Använd webbgränssnitt BMC i stället förmodan som slå på åtgärden inte fungerar.

### <a name="drain"></a>Tömmer

Den **tömmer** åtgärd evacuates alla aktiva arbetsbelastningar genom att distribuera dem bland de återstående noderna i den särskilda skalningsenheten.

Den här åtgärden används vanligtvis under fältet ersättning av delar, till exempel ersätts en hel nod.

> [!IMPORTANT]
> Kontrollera att du tömma en nod endast under en planerad underhållsperiod, där användarna meddelas. Under vissa förhållanden upplevelse aktiva arbetsbelastningar avbrott.

Att köra instruktionen tömning via PowerShell:

  ````PowerShell
  Disable-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

### <a name="resume"></a>Återuppta

Den **återuppta** åtgärden återupptar en dränerad nod och markerar det aktiva för placering av arbetsbelastning. Tidigare arbetsbelastningar som körs på noden inte återställas. (Om du tömmer noden och sedan stänga av när du startar noden tillbaka den inte har markerats som aktiv för placering av arbetsbelastning. När du är klar, måste du använda åtgärden återuppta för att markera nod som aktiv.)

Att köra åtgärden återuppta via PowerShell:

  ````PowerShell
  Enable-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

### <a name="repair"></a>Reparation

Den **reparera** åtgärd reparerar en nod. Använd den för något av följande scenarier:

- Fullständig nod ersättning (med eller utan att nya datadiskar)
- Efter fel i maskinvara och ersättning (om bäst i fältet utbytbara enhet (FRU) dokumentationen).

> [!IMPORTANT]
> Dokumentationen din OEM maskinvaruleverantören FRU för de specifika stegen när du vill ersätta en nod eller enskilda maskinvarukomponenter. FRU-dokumentation kommer att ange om du behöver köra reparationsåtgärden när du ersätter en maskinvarukomponent.  

När du kör reparationsåtgärden måste du ange BMC IP-adress. 

Så här kör reparationsåtgärden via PowerShell:

  ````PowerShell
  Repair-AzsScaleUnitNode -Region <RegionName> -Name <NodeName> -BMCIPAddress <BMCIPAddress>
  ````


