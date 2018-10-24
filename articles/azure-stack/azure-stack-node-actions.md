---
title: Skala enhet noden åtgärder i Azure Stack | Microsoft Docs
description: Lär dig mer om att visa nodstatus, och använd styrkan på Stäng av drain och Återuppta nod åtgärder på ett integrerat Azure Stack-system.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: f299cca57e3eaddd2a9d4868f3bae822392e45a0
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49944944"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Skalningsåtgärder enhet noden i Azure Stack

*Gäller för: integrerade Azure Stack-system*

Den här artikeln beskrivs hur du visar status för en skalningsenhet och dess associerade noder och hur du använder de tillgängliga nod åtgärderna. Inkludera ström på power av noden åtgärder tömma, återuppta och reparera. Normalt använder du de här åtgärderna för noden under fältet ersättning av delar eller för noden återställningsscenarier.

> [!Important]  
> Alla nod-åtgärder som beskrivs i den här artikeln bör endast mål en nod i taget.


## <a name="view-the-node-status"></a>Visa status för noden

I administratörsportalen kan visa du enkelt status för en skalningsenhet och dess associerade noder.

Visa status för en skalningsenhet:

1. På den **regionshantering** panelen, väljer du regionen.
2. Till vänster under **infrastrukturresurser**väljer **Skalningsenheter**.
3. Välj skalningsenheten i resultaten.
 
Här, kan du visa följande information:

- regionnamn. Områdesnamnet refereras med **-plats** i PowerShell-modulen.
- typ av systemet
- Totalt antal logiska kärnor
- totalt minne
- Lista över enskilda noder och deras status. antingen **kör** eller **stoppas**

![Skala körs panelen som visar statusen för varje nod](media/azure-stack-node-actions/ScaleUnitStatus.PNG)

## <a name="view-node-information"></a>Visa information om noden

Om du väljer en enskild nod kan du visa följande information:

- Regionnamn
- Server-modell
- IP-adressen för huvudkortshantering (BMC)
- Användningsstatus
- Totalt antal kärnor
- totala mängd minne
 
![Skala körs panelen som visar statusen för varje nod](media/azure-stack-node-actions/NodeActions.PNG)

Du kan också utföra skalningsåtgärder enhet noden härifrån.

## <a name="scale-unit-node-actions"></a>Skalningsåtgärder enhet nod

När du visar information om en nod för skala enhet kan utföra du också noden åtgärder som:

- Drain och återuppta
- Reparera

Driftstatus för noden avgör vilka alternativ som är tillgängliga.

### <a name="power-off"></a>Stäng av

Den **stängs av** stängs av noden. Det är samma som om du trycker på strömknappen. Den gör **inte** skicka en nedstängningssignal till operativsystemet. Kontrollera att du tömma en nod för skala enhet först för planerad Stäng av åtgärder.

Den här åtgärden används vanligtvis när en nod är i ett låst tillstånd och inte längre svarar på begäranden.

> [!Important] 
> Den här funktionen är bara tillgänglig via PowerShell. Den blir tillgänglig i Azure Stack-administratörsportalen igen vid ett senare tillfälle.


Så här kör avstängning åtgärd via PowerShell:

````PowerShell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```` 

Använd BMC-webbgränssnitt i stället förmodan att Stäng av åtgärden inte fungerar.

### <a name="power-on"></a>Ström på

Den **sätter på** stängs på noden. Det är samma som om du trycker på strömknappen. 

> [!Important] 
> Den här funktionen är bara tillgänglig via PowerShell. Den blir tillgänglig i Azure Stack-administratörsportalen igen vid ett senare tillfälle.

Att köra power Åtgärd via PowerShell:

````PowerShell
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
````

Använd BMC-webbgränssnitt i stället förmodan som ström på åtgärden inte fungerar.

### <a name="drain"></a>Töm

Den **tömma** åtgärd evacuates alla aktiva arbetsbelastningar genom att distribuera dem bland de återstående noderna i den specifika skalningsenheten.

Den här åtgärden används vanligtvis under fältet ersättning av delar, till exempel att ersätta en hel nod.

> [!IMPORTANT]  
> Se till att du tömma en nod endast under ett planerat underhållsfönster, där användarna har meddelats. Under vissa förhållanden kan aktiva arbetsbelastningar uppleva avbrott i verksamheten.

Att köra instruktionen drain via PowerShell:

  ````PowerShell
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
  ````

### <a name="resume"></a>Återuppta

Den **återuppta** åtgärden återupptar en dränerad nod och markerar den aktiva för placering av arbetsbelastning. Inte att återställa tidigare arbetsbelastningar som körs på noden. (Om du tömma en nod och Stäng av när du startar noden tillbaka den inte har markerats som aktiv för placering av arbetsbelastning. När du är klar, måste du använda återuppta åtgärder för att markera noden som aktiv.)

Att köra åtgärden återuppta via PowerShell:

  ````PowerShell
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
  ````

### <a name="repair"></a>Reparera

Den **reparera** åtgärd reparerar en nod. Använd den bara för något av följande scenarier:

- Fullständig noden ersättning (med eller utan nya datadiskar)
- När du har maskinvara komponentfel och ersättning (om bäst i fältet replaceable enhet (FRU) dokumentationen).

> [!IMPORTANT]  
> Se OEM maskinvara FRU dokumentationen från leverantören för de specifika stegen när du vill ersätta en nod eller enskilda maskinvarukomponenter. FRU-dokumentationen anger om du behöver köra reparationsåtgärden när du har ersatt en maskinvarukomponent.  

När du kör reparationsåtgärden kan behöva du ange BMC IP-adress. 

Att köra reparationsåtgärden via PowerShell:

  ````PowerShell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPAddress <BMCIPAddress>
  ````

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Stack Fabric administratör modulen i [Azs.Fabric.Admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.4.0).