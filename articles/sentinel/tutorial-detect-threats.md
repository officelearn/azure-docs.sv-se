---
title: Undersök aviseringar med Azure Sentinel Preview | Microsoft Docs
description: Använd den här självstudien för att lära dig att undersöka aviseringar med Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: b5fbc5ac-68b2-4024-9c1b-bd3cc41a66d0
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2019
ms.author: rkarlin
ms.openlocfilehash: ad9c752898733286701db2d0f0b1fc40029b7521
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370704"
---
# <a name="tutorial-detect-threats-with-azure-sentinel-preview"></a>Självstudier: Identifiera hot med Azure Sentinel Preview

> [!IMPORTANT]
> Azure Sentinel är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

När du har [anslutit dina data källor](quickstart-onboard.md) till Azure Sentinel vill du bli meddelad när något misstänkt inträffar. För att du ska kunna göra detta kan du använda Azure Sentinel för att skapa avancerade aviserings regler, som genererar ärenden som du kan tilldela och använda för att skapa en djup undersökning av avvikelser och hot i din miljö. 

Den här självstudien hjälper dig att identifiera hot med Azure Sentinel.
> [!div class="checklist"]
> * Skapa identifierings regler
> * Automatisera hot svar

## <a name="create-detection-rules"></a>Skapa identifierings regler

Om du vill undersöka ärenden måste du först skapa identifierings regler. 

> [!NOTE]
> Aviseringar som genereras i Azure Sentinel är tillgängliga via [Microsoft Graph säkerhet](https://aka.ms/securitygraphdocs). Mer information och integrerings partner finns i [dokumentationen om Microsoft Graph säkerhets aviseringar](https://aka.ms/graphsecurityreferencebetadocs) .

Identifierings regler baseras på de typer av hot och avvikelser som kan vara misstänkta i din miljö som du vill veta mer om direkt, vilket säkerställer att de är på plats, undersökas och åtgärdas. 

1. I Azure Portal under Azure Sentinel väljer du **analys**.

   ![Analytics](./media/tutorial-detect-threats/alert-rules.png)

2. Klicka på **+ Lägg till**i den översta meny raden.  

   ![Skapa aviseringsregel](./media/tutorial-detect-threats/create-alert-rule.png)

3. Under **skapa aviserings regel**anger du ett beskrivande namn och anger **allvarlighets graden** vid behov. 

4. Skapa frågan i Log Analytics och klistra in den i fältet **Ange aviserings regel** . Här är en exempel fråga som varnar dig när ett avvikande antal resurser skapas i Azure Activity.

        AzureActivity
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"
        | where ActivityStatus == "Succeeded"
        | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller

   > [!NOTE]
   > Frågans längd ska vara mellan 1 och 10000 tecken och får inte innehålla "search *" och "union *".


5. I avsnittet **entitets mappning** använder du fälten under **entitetstyp** för att mappa kolumnerna i din fråga till entitetsfält som identifieras av Azure Sentinel. För varje fält mappar du relevant kolumn i frågan som du skapade i Log Analytics till lämpligt entitetsfält. Välj det relevanta kolumn namnet under **egenskapen**. Varje entitet innehåller flera fält, till exempel SID, GUID, osv. Du kan mappa entiteten enligt något av fälten, inte bara entiteten på den översta nivån.

6. Definiera villkor för aviserings Utlös under aviserings utlösare. Detta definierar de villkor som utlöser aviseringen. 

7. Ange **frekvensen** för hur ofta frågan ska köras – så ofta som var femte minut eller som sällan som en gång om dagen. 

8. Ange **perioden** för att kontrol lera tidsfönstret för hur mycket data frågan körs på, till exempel kan den köras varje timme i 60 minuter med data.

9. Du kan också ställa in undertrycket. Under tryckning är användbart när du vill förhindra att dubbla aviseringar utlöses för samma incident. På så sätt kan du förhindra att aviseringar utlöses under en viss period. Detta kan hjälpa dig att undvika dubbla aviseringar för samma incident och gör att du kan ignorera aviseringar i följd under en viss tids period. Om till exempel frekvensen för **aviserings schemaläggning**  är inställt på 60 minuter och tids gränsen för **aviserings perioden** är inställd på två timmar och frågeresultaten överskrider det definierade tröskelvärdet utlöses en avisering två gånger, en gång när den identifieras första gången under de senaste 60 minuterna och igen när det är inom de första 60 minuterna av de 2 timmars data som samplas. Vi rekommenderar att om en avisering utlöses bör under tryckningen vara för den mängd tid som anges i aviserings perioden. I vårt exempel kanske du vill ställa in Undertryckning i 60 minuter, så att aviseringar endast utlöses för händelser som har inträffat under den senaste timmen.

8. När du har klistrat in frågan i fältet **Ange aviserings regel** kan du direkt se en simulering av aviseringen under **logik aviserings simulering** så att du kan få reda på hur mycket data som genereras under ett angivet tidsintervall för aviseringen du har skapat. Detta beror på vad du anger för **frekvens** och **tröskel**. Om du ser detta i genomsnitt utlöses aviseringen för ofta, du vill ange antalet resultat högre så att det är högre än den genomsnittliga bas linjen.

9. Klicka på **skapa** för att initiera aviserings regeln. När aviseringen har skapats skapas ett ärende som innehåller aviseringen. Du kan se de definierade identifierings reglerna som rader på fliken **säkerhets analys** . Du kan också se antalet matchningar för varje regel – aviseringarna utlöses. I den här listan kan du aktivera, inaktivera eller ta bort varje regel. Du kan också högerklicka på ellipsen (...) i slutet av raden för varje avisering för att redigera, inaktivera, klona, Visa matchningar eller ta bort en regel. **Analytics** -sidan är ett galleri med alla aktiva aviserings regler, inklusive mallar som du aktiverar och aviserings regler som du skapar baserat på mallar.

1. Resultaten av aviserings reglerna kan visas på sidan **ärenden** där du kan prioritering, [undersöka ärenden](tutorial-investigate-cases.md)och åtgärda hot.



## <a name="automate-threat-responses"></a>Automatisera hot svar

SIEM/SOC-team kan vara översvämmas med regelbundna säkerhets aviseringar. Volymen av de aviseringar som genereras är så stor att de tillgängliga säkerhets administratörerna är överbelastade. Detta resulterar i en alltför ofta i situationer där många aviseringar inte kan undersökas, vilket gör att organisationen blir sårbar för attacker som går förlorade. 

Många, om inte de flesta, av dessa aviseringar följer återkommande mönster som kan åtgärdas med hjälp av bestämda och definierade reparations åtgärder. Med Azure Sentinel kan du definiera din reparation i spel böcker. Det är också möjligt att ställa in real tids automatisering som en del av din Spelbok-definition så att du fullständigt kan automatisera ett definierat svar på specifika säkerhets aviseringar. Genom att använda real tids automatisering kan svars grupper avsevärt minska sin arbets belastning genom att helt automatisera de rutinmässiga svaren på återkommande typer av aviseringar, så att du kan koncentrera dig på unika aviseringar, analysera mönster, Hot jakt och mycket annat.

Automatisera svar:

1. Välj den avisering som du vill automatisera svaret för.
1. Från navigerings menyn i Azure Sentinel-arbetsytan väljer du **analys**.
1. Välj den avisering som du vill automatisera. 
1. På sidan **Redigera aviserings regel** under **real tids automatisering**väljer du den **Utlös ande Spelbok** som du vill köra när den här varnings regeln matchas.
1. Välj **Spara**.

   ![Real tids automatisering](./media/tutorial-detect-threats/rt-configuration.png)


Du kan dessutom manuellt reparera en avisering genom att köra en Spelbok inifrån aviseringen genom att klicka på **Visa spel böcker** och sedan välja en Spelbok som ska köras. Information om hur du skapar en ny Spelbok eller redigerar en befintlig finns i [arbeta med spel böcker i Azure Sentinel](tutorial-respond-threats-playbook.md).



## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig hur du kommer igång med att identifiera hot med Azure Sentinel. 

Information om hur du automatiserar dina svar på hot, [hur du svarar på hot med automatiserade spel böcker](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Svara på hot](tutorial-respond-threats-playbook.md) för att automatisera dina svar på hot.

