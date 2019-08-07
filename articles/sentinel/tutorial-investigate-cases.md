---
title: Undersök incidenter med Azure Sentinel Preview | Microsoft Docs
description: Använd den här självstudien för att lära dig att undersöka incidenter med Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a493cd67-dc70-4163-81b8-04a9bc0232ac
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: bad3fddd6caf7e6eb455e59280f181c787b95a4e
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780386"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel-preview"></a>Självstudier: Undersök incidenter med Azure Sentinel Preview

> [!IMPORTANT]
> Azure Sentinel är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här självstudien hjälper dig att identifiera hot med Azure Sentinel.

När du har [anslutit dina data källor](quickstart-onboard.md) till Azure Sentinel vill du bli meddelad när något misstänkt inträffar. För att du ska kunna göra detta kan du använda Azure Sentinel för att skapa avancerade aviserings regler som genererar incidenter som du kan tilldela och använda för att skapa en djup undersökning av avvikelser och hot i din miljö. 

> [!div class="checklist"]
> * Skapa incidenter
> * Undersöka incidenter
> * Reagera på hot

## <a name="investigate-incidents"></a>Undersöka incidenter

en incident kan innehålla flera aviseringar. Det är en agg regering av alla relevanta bevis för en speciell undersökning. en incident skapas baserat på aviseringar som du har definierat på sidan **analys** . Egenskaperna som rör aviseringarna, till exempel allvarlighets grad och status, anges på incident nivå. När du har informerat Azure Sentinel vet vilka typer av hot du söker efter och hur du hittar dem kan du övervaka hot som upptäcks genom att undersöka incidenter. 

1. Välj **incidenter**. På sidan incidenter kan du se hur många incidenter du har, hur många som är öppna, hur många som är inställda på att **pågå**och hur många som är stängda. För varje incident kan du se tiden det har inträffat och status för incidenten. Titta på allvarlighets graden för att bestämma vad som ska hanteras först. På sidan **incidenter** klickar du på fliken **aviseringar** för att visa alla aviseringar som är relaterade till en incident. Entiteter som du har mappat tidigare som en del av incidenten kan visas på fliken entiteter.  Du kan filtrera incidenterna efter behov, till exempel efter status eller allvarlighets grad. När du tittar på fliken **incidenter** visas öppna incidenter som innehåller aviseringar som har utlösts av de identifierings regler som definierats i **Analytics**. Högst upp visas dina aktiva incidenter, nya incidenter och pågående incidenter. Du kan också se en översikt över alla dina incidenter efter allvarlighets grad.

   ![Aviserings instrument panel](./media/tutorial-investigate-cases/cases.png)

2. Du påbörjar en undersökning genom att klicka på en speciell incident. Till höger kan du se detaljerad information om incidenten, inklusive allvarlighets grad, sammanfattning av antalet entiteter som ingår (baserat på mappningen). Varje incident har ett unikt ID. Incidentens allvarlighets grad bestäms enligt den mest allvarliga avisering som ingår i incidenten.  

1. Om du vill visa mer information om aviseringar och entiteter i incidenten klickar du på **Visa fullständig information** på sidan incident och granskar de relevanta flikarna som sammanfattar incident informationen.  Vyn fullständig incident konsoliderar alla bevis i aviseringen, associerade aviseringar och entiteter.

1. På fliken **aviseringar** granskar du själva aviseringen – när den utlöstes och hur mycket den överskred tröskelvärdena som du har angett. Du kan se all relevant information om aviseringen – frågan som utlöste aviseringen, antalet resultat som returneras per fråga och möjligheten att köra spel böcker på aviseringarna. Om du vill öka detalj nivån ytterligare i incidenten klickar du på antalet träffar. Då öppnas den fråga som genererade resultaten och resultaten som utlöste aviseringen i Log Analytics.

3. På fliken **entiteter** kan du se alla entiteter som du har mappat som en del av varnings regel definitionen. 

4. Om du aktivt undersöker en incident, är det en bra idé att ställa in incident statusen på **pågående** tills du stänger den. Du kan också stänga incidenten, där **stängda lösta** är statusen för incidenter som indikerar att en incident hanterades, medan **stängningen stängs** är statusen för incidenter som inte kräver hantering. Förklaringar krävs för att förklara varför en incident stängs.

5. Incidenter kan tilldelas till en speciell användare. För varje incident kan du tilldela en ägare genom att ange fältet incident **ägare** . Alla incidenter börjar som otilldelade. Du kan gå till incidenterna och filtrera efter ditt namn för att se alla incidenter som du äger. 

5. Klicka på **Undersök** för att Visa undersöknings kartan och omfattningen av överträdelsen med reparations steg. 



## <a name="respond-to-threats"></a>Reagera på hot

Azure Sentinel ger dig två primära alternativ för att svara på hot med spel böcker. Du kan ställa in en Spelbok så att den körs automatiskt när en avisering utlöses, eller så kan du köra en Spelbok manuellt som svar på en avisering.

- Du kan ange att en Spelbok ska köras automatiskt när en avisering utlöses när du konfigurerar Spelbok. 

- Du kan köra en Spelbok manuellt inifrån aviseringen genom att klicka på **Visa spel böcker** och sedan välja en Spelbok som ska köras.




## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig hur du kommer igång med att undersöka incidenter med hjälp av Azure Sentinel. Fortsätt till självstudien för att [svara på hot med automatiserade spel böcker](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Svara på hot](tutorial-respond-threats-playbook.md) för att automatisera dina svar på hot.

