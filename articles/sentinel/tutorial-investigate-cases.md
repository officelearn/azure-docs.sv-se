---
title: Undersöka fall med Azure Sentinel-förhandsgranskning | Microsoft Docs
description: Använd den här självstudiekursen om du vill lära dig att undersöka fall med Sentinel-Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a493cd67-dc70-4163-81b8-04a9bc0232ac
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: 7c4994863f3c145c7095bcc12dd69ff02fab8455
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57540291"
---
# <a name="tutorial-investigate-cases-with-azure-sentinel-preview"></a>Självstudier: Undersöka fall med Azure Sentinel-förhandsgranskning

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här kursen hjälper dig att identifiera hot med Azure Sentinel.

När du [anslutna datakällor](quickstart-onboard.md) Azure Sentinel du vill meddelas när misstänkta något händer. Så att du kan göra detta, avancerade Sentinel-Azure kan du skapa aviseringsregler som genererar ärenden som du kan tilldela och använda djupt undersöka avvikelser och hot i din miljö. 

> [!div class="checklist"]
> * Skapa ärenden
> * Undersöka ärenden
> * Reagera på hot

## <a name="investigate-cases"></a>Undersöka ärenden

Ett ärende kan innehålla flera aviseringar. Det är en sammanställning av alla relevanta bevis för specifika undersökningar. Ett ärende skapas baserat på aviseringar som du har definierat i den **Analytics** sidan. De egenskaper som rör aviseringar, till exempel allvarlighetsgrad och status är inställda på fallnivå. När du låter Azure Sentinel veta vilka typer av hot som du letar efter och du hittar dem, kan du övervaka hot som identifieras genom att undersöka fall. 

1. Välj **fall**. Den **fall** sidan får du reda på hur många fall som du har, hur många är öppna, hur många som du angett till **pågår**, och hur många är stängda. För varje enskilt fall ser du den tid som felet uppstod och status för ärendet. Titta på allvarlighetsgraden till Bestäm vad du vill hantera först. I den **fall** klickar du på den **aviseringar** flik för att visa alla aviseringar som är relaterade till ett ärende. Entiteter som du tilldelade tidigare som en del av fallet kan visas i den **entiteter** fliken.  Du kan filtrera fall som behövs, till exempel efter status eller allvarlighetsgrad. När du granskar den **fall** fliken visas öppna ärenden som innehåller aviseringar som utlöses av dina regler för identifiering som definierats i **Analytics**. Överst ser du din aktiva ärenden, nya ärenden och i förloppet fall. Du kan också se en översikt över alla fall efter allvarlighetsgrad.

  ![Aviseringsinstrumentpanel](./media/tutorial-investigate-cases/cases.png)

2. Starta en undersökning genom att klicka på specifika fall. Du kan se detaljerad information om fallet inklusive dess allvarlighetsgrad, Sammanfattning av antalet enheter som ingår (baserat på dina mappning) till höger. Varje enskilt fall har ett unikt ID. Ärendets allvarlighetsgrad bestäms beroende på allvarligaste aviseringen som ingår i fallet.  

1. Om du vill visa mer information om aviseringar och entiteter i fallet klickar du på **Visa fullständig information** om sidan och granska de relevanta flikarna som sammanfattar ärendeinformation.  Fullständig fallet vyn samlas alla bevis på aviseringen, tillhörande aviseringar och entiteter.

1. I den **aviseringar** fliken kan du granska själva - avisering när den har utlösts och hur mycket den överskred de tröskelvärden som du anger. Du kan se all relevant information om aviseringen – den fråga som utlöste aviseringen, antalet resultat som returneras per fråga och möjligheten att köra spelböcker på aviseringarna. Att öka detaljnivån ned ytterligare i fallet klickar du på antal träffar. Då öppnas den fråga som genererade resultaten och de resultat som utlöste aviseringen i Log Analytics.

3. I den **entiteter** och du kan se alla entiteter som du tilldelade som en del av definitionen varningsregeln. 

4. Om du undersöker aktivt fall, är det en bra idé att ställa in ärendets status på **pågår** tills du stänger den. Du kan också stänga fallet, där **stängd löst** är statusen för de fall som indikerar att en incident har hanterats, medan **stängd avvisade** är statusen för de fall som inte kräver hantering. Förklaringar krävs förklarar din resonemang för att stänga ett ärende.

5. Fall kan tilldelas till en specifik användare. För varje scenario kan du tilldela en ägare genom att ange fallet **ägare** fält. Alla fall start som otilldelade. Du kan gå till ärenden och filtrera efter namnet för att se alla ärenden som du äger. 

5. Klicka på **Undersök** att visa undersökningskartan och på med åtgärdsförslag med överträdelsens omfattning. 



## <a name="respond-to-threats"></a>Reagera på hot

Azure Sentinel ger dig två huvudsakliga alternativ för att svara på hot med hjälp av spelböcker. Du kan ange en spelbok ska köras automatiskt när en avisering utlöses eller du kan köra en spelbok manuellt som svar på en avisering.

- Du kan ange en spelbok ska köras automatiskt när en avisering utlöses när du konfigurerar spelboken. 

- Du kan köra en spelbok från inuti aviseringen manuellt genom att klicka på **visa spelböcker** och sedan välja en spelbok ska köras.




## <a name="next-steps"></a>Nästa steg
I den här självstudien lärde du dig att komma igång med att undersöka fall med hjälp av Azure Sentinel. Fortsätta till självstudien för [hur du svarar på hot med hjälp av automatiserade spelböcker](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Svara på hot](tutorial-respond-threats-playbook.md) att automatisera dina svar på hot.

