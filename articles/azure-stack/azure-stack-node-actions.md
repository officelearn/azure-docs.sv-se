---
title: Skala enhet noden åtgärder i Azure Stack | Microsoft Docs
description: Lär dig mer om att visa status för noden och använder kraften i power av, inaktivera och Återuppta nod åtgärder på ett integrerat Azure Stack-system.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 01/22/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/22/2019
ms.openlocfilehash: 24c500251c8e91b7542c5c9d3e77676205c88c1f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58090975"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Skalningsåtgärder enhet noden i Azure Stack

*Gäller för: Integrerade Azure Stack-system*

Den här artikeln beskriver hur du visar status för en skalningsenhet. Du kan visa enhetens noder. Du kan köra noden åtgärder, till exempel power på power, stänga av, tömma, återuppta och reparera. Normalt använder du de här åtgärderna för noden under fältet ersättning av delar eller för att återställa en nod.

> [!Important]  
> Alla nod-åtgärder som beskrivs i den här artikeln bör rikta en nod i taget.

## <a name="view-the-node-status"></a>Visa status för noden

I administratörsportalen kan visa du status för en skalningsenhet och dess associerade noder.

Så här visar du statusen för en skalningsenhet:

1. På den **regionshantering** panelen, väljer du regionen.
2. Till vänster under **infrastrukturresurser**väljer **Skalningsenheter**.
3. Välj skalningsenheten i resultaten.
4. Till vänster under **Allmänt**väljer **noder**.

   Se följande information:

   - Lista över enskilda noder
   - Driftstatus (se listan nedan)
   - Energistatus (igång eller stoppad)
   - Server-modell
   - IP-adressen för huvudkortshantering (BMC)
   - Totalt antal kärnor
   - totala mängd minne

![status för en skalningsenhet](media/azure-stack-node-actions/multinodeactions.png)

### <a name="node-operational-states"></a>Operativa node-tillstånd

| Status | Beskrivning |
|----------------------|-------------------------------------------------------------------|
| Körs | Noden deltar aktivt i skalningsenheten. |
| Stoppad | Noden är inte tillgänglig. |
| Lägger till | Noden läggs aktivt till skalningsenheten. |
| Reparerar | Noden är aktivt repareras. |
| Underhåll | Noden har pausats och ingen aktiv användare arbetsbelastningen körs. |
| Kräver reparation | Ett fel har identifierats som kräver noden repareras. |

## <a name="scale-unit-node-actions"></a>Skalningsåtgärder enhet nod

När du visar information om en nod för skala enhet kan utföra du också noden åtgärder som:
 - Starta och stoppa (beroende på aktuella status)
 - Inaktivera och återuppta (beroende på driftsstatus)
 - Reparera
 - Avstängning

Driftstatus för noden avgör vilka alternativ som är tillgängliga.

Du måste installera Azure Stack PowerShell-moduler. Dessa cmdletar finns i den **Azs.Fabric.Admin** modulen. Om du vill installera eller verifiera installationen av PowerShell för Azure Stack, se [installera PowerShell för Azure Stack](azure-stack-powershell-install.md).

## <a name="stop"></a>Stoppa

Den **stoppa** stängs av noden. Det är samma som om du trycker på strömknappen. Det skickar inte en nedstängningssignal för operativsystemet. För planerade stop, Använd alltid nedstängningsåtgärden först. 

Den här åtgärden används vanligtvis när en nod är i ett låst tillstånd och inte längre svarar på begäranden.

Om du vill köra instruktionen stoppa, öppna en förhöjd PowerShell och kör följande cmdlet:

```PowerShell  
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Förmodan att stoppåtgärden inte fungerar, försök igen och om den inte en andra gång använda webbgränssnittet BMC i stället.

Mer information finns i [Stop-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/stop-azsscaleunitnode).

## <a name="start"></a>Start

Den **starta** stängs på noden. Det är samma som om du trycker på strömknappen. 
 
Om du vill köra instruktionen start, öppna en förhöjd PowerShell och kör följande cmdlet:

```PowerShell  
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Förmodan att startåtgärden inte fungerar, försök igen och om den inte en andra gång använda webbgränssnittet BMC i stället.

Mer information finns i [Start AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/start-azsscaleunitnode).

## <a name="drain"></a>Töm

Den **tömma** åtgärd flyttar alla aktiva arbetsbelastningar till de återstående noderna i den specifika skalningsenheten.

Den här åtgärden används vanligtvis under fältet ersättning av delar, till exempel att ersätta en hel nod.

> [!Important]
> Kontrollera att du använder en drain åtgärd på en nod under ett planerat underhållsfönster, där användarna har meddelats. Under vissa förhållanden kan aktiva arbetsbelastningar uppleva avbrott i verksamheten.

Om du vill köra instruktionen drain, öppna en förhöjd PowerShell och kör följande cmdlet:

```PowerShell  
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Mer information finns i [inaktivera AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/disable-azsscaleunitnode).

## <a name="resume"></a>Återuppta

Den **återuppta** åtgärden återupptar en inaktiverad nod och markerar den aktiva för placering av arbetsbelastning. Inte att återställa tidigare arbetsbelastningar som körs på noden. (Om du använder en drain åtgärd på en nod måste du stänga av. När du sätter noden tillbaka på den inte har markerats som aktiv för placering av arbetsbelastning. När du är klar, måste du använda återuppta åtgärder för att markera noden som aktiv.)

Om du vill köra instruktionen återuppta, öppna en förhöjd PowerShell och kör följande cmdlet:

```PowerShell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Mer information finns i [aktivera AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/enable-azsscaleunitnode).

## <a name="repair"></a>Reparera

Den **reparera** åtgärd reparerar en nod. Använd den bara för något av följande scenarier:
 - Fullständig noden ersättning (med eller utan nya datadiskar)
 - När du har maskinvara komponentfel och ersättning (om bäst i fältet replaceable enhet (FRU) dokumentationen).

> [!Important]  
> Se OEM maskinvara FRU dokumentationen från leverantören för de specifika stegen när du vill ersätta en nod eller enskilda maskinvarukomponenter. FRU-dokumentationen anger om du behöver köra reparationsåtgärden när du har ersatt en maskinvarukomponent. 

När du kör reparationsåtgärden kan behöva du ange BMC IP-adress. 

Öppna en förhöjd PowerShell för att köra reparationsåtgärden, och kör följande cmdlet:

  ```PowerShell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ```

## <a name="shutdown"></a>Avstängning

Den **avstängning** åtgärd fist flyttar alla aktiva arbetsbelastningar till de återstående noderna i samma skalningsenheten. Åtgärden stänger sedan gradvis av noden skala enhet.

När du startar en nod som stängdes, måste du köra den [återuppta](#resume) åtgärd. Inte att återställa tidigare arbetsbelastningar som körs på noden.

Om avstängningen misslyckas, försöker den [tömma](#drain) åtgärden följt av avstängningen.

Öppna en förhöjd PowerShell för att köra avstängningsåtgärden, och kör följande cmdlet:

  ```PowerShell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -Shutdown
  ```



## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Stack Fabric administratör modulen i [Azs.Fabric.Admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.6.0).
