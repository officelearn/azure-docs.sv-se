---
title: Undersöka aviseringar med Azure Sentinel-förhandsgranskning | Microsoft Docs
description: Använd den här självstudiekursen om du vill lära dig hur du undersöker aviseringar med Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: b5fbc5ac-68b2-4024-9c1b-bd3cc41a66d0
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 35268d8001f8869838745d14f0e8c0ba7253d4c9
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "56993204"
---
# <a name="tutorial-detect-threats-with-azure-sentinel-preview"></a>Självstudier: Identifiera hot med Azure Sentinel-förhandsgranskning

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här kursen hjälper dig att identifiera hot med Azure Sentinel.

När du [anslutna datakällor](quickstart-onboard.md) Azure Sentinel du vill meddelas när misstänkta något händer. Så att du kan göra detta, avancerade Sentinel-Azure kan du skapa aviseringsregler som genererar ärenden som du kan tilldela och använda djupt undersöka avvikelser och hot i din miljö. 


> [!div class="checklist"]
> * Skapa regler för identifiering
> * Svara på hot

## <a name="create-detection-rules"></a>Skapa regler för identifiering

Om du vill undersöka fall måste du först skapa regler för identifiering. Regler för identifiering baseras på de typer av hot och avvikelser som kan vara misstänkta i din miljö som du vill veta om utan att se till att de visas, undersökas och åtgärdas. 

1. I Azure-portalen under Azure Sentinel väljer **Analytics**.

  ![Analytics](./media/tutorial-detect-threats/alert-rules.png)

2. I den övre menyraden klickar du på **+ Lägg till**.  

  ![Skapa aviseringsregel](./media/tutorial-detect-threats/create-alert-rule.png)

3. Under **skapa varningsregel**, ange ett beskrivande namn och ange den **allvarlighetsgrad** efter behov. 

4. Skapa frågan i Log Analytics och klistra in den i den **Set varningsregel** fält. Här är en exempelfråga som skulle varnar dig när en avvikande antal resurser skapas i Azure-aktivitet.

        AzureActivity
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"
        | where ActivityStatus == "Succeeded"
        | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller

5. I den **Entitetsmappning** använder fälten under **entitetstypen** att mappa kolumnerna i frågan till entitetsfält som identifieras av Azure Sentinel. Mappa relevant kolumn i frågan som du skapade i Log Analytics till lämplig entitetsfält för varje fält. Välj relevant kolumnnamnet under den **egenskapen**. Varje entitet innehåller flera fält, till exempel SID, GUID, osv. Du kan mappa entiteten enligt något av fälten, inte bara den övre nivån entiteten.

6. Definiera villkor för aviseringsutlösare **aviseringsutlösare**. Detta definierar villkoren som utlöser aviseringen. 

7. Ange den **frekvens** för hur ofta frågan ska köras - som ofta som var femte minut eller sällan en gång om dagen. 

8. Ange den **Period** för att styra tidsperioden för hur mycket data som frågan körs på – till exempel det kan köras varje timme över 60 minuters data.

9. Du kan också ange den **Undertryckning**. Undertryckning är användbart när du vill stoppa dubbla aviseringar från utlöses för samma incident. På så sätt kan stoppa du aviseringar från utlöses under en viss period. Detta kan hjälpa dig att undvika dubbla aviseringar för samma incident och gör att du kan utelämna på varandra följande aviseringar för en viss tidsperiod. Till exempel om den **Avisera schemaläggning** **frekvens** har angetts till 60 minuter, och **Avisera schemaläggning Period** är inställd på två timmar, och resultatet av frågan omvandlingsjobbet det definierade Tröskelvärde för den utlöser en avisering två gånger, när när först har identifierats under de senaste 60 minuterna och igen när den är i de första 60 minuterna 2 timmars data håller på att samplas. Vi rekommenderar att Undertryckning ska vara den mängd tid som anges i aviseringen perioden om en avisering utlöses. I vårt exempel kanske du vill ange Undertryckning i 60 minuter så att aktiveras aviseringar endast händelser som inträffade under den senaste timmen.

8. När du klistrar in frågan i den **Set varningsregel** fältet kan du direkt se en simulering av aviseringen under **alert Logic-simulering** så att du kan få förståelse av hur mycket data kommer att genererade under ett visst tidsintervall för aviseringen som du skapade. Detta beror på vad du har angett för **frekvens** och **tröskelvärdet**. Om du ser att i genomsnitt aviseringen utlöses för ofta, kommer du att ange hur många resultat som är högre så att den är över din genomsnittliga baslinje.

9. Klicka på **skapa** att initiera dina varningsregeln. När aviseringen har skapats skapas ett ärende som innehåller aviseringen. Du kan se de definierade identifieringsreglerna som rader i den **säkerhetsanalyser** fliken. Du kan också se antalet matchningar för varje regel - aviseringar som har utlösts. I den här listan kan du aktivera, inaktivera eller ta bort varje regel. Du kan också höger-Välj ellipsen (...) i slutet av raden för varje avisering redigera, inaktivera, klona, visa matchningar eller ta bort en regel. Den **Analytics** är ett galleri med alla aktiva Varningsregler, inklusive mallar du aktiverar och aviseringsregler skapas från mallar.

1. Resultatet av varningsreglerna kan ses i den **fall** sidan där du kan sortera, [undersöka fall](tutorial-investigate-cases.md), och åtgärda hoten.



## <a name="respond-to-threats"></a>Svara på hot

Azure Sentinel ger dig två huvudsakliga alternativ för att svara på hot med hjälp av spelböcker. Du kan ange en spelbok ska köras automatiskt när en avisering utlöses eller du kan köra en spelbok manuellt som svar på en avisering.

- Ange en spelbok ska köras automatiskt när en avisering utlöses när du konfigurerar spelboken. 

- Kör en spelbok från inuti aviseringen manuellt genom att klicka på **visa spelböcker** och sedan välja en spelbok ska köras.




## <a name="next-steps"></a>Nästa steg
I den här självstudien lärde du dig att komma igång med att identifiera hot med hjälp av Azure Sentinel. Fortsätta till självstudien för [hur du svarar på hot med hjälp av automatiserade spelböcker](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Svara på hot](tutorial-respond-threats-playbook.md) att automatisera dina svar på hot.

