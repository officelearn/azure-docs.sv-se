---
title: Komma igång med Azure Scheduler på Azure-portalen | Microsoft-dokument
description: Komma igång med Azure Scheduler på Azure-portalen
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: e69542ec-d10f-4f17-9b7a-2ee441ee7d68
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/10/2016
ms.author: deli
ms.openlocfilehash: f03ddb475835b30e9b931b7f057c062b57ac45f3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-azure-scheduler-in-azure-portal"></a>Komma igång med Azure Scheduler på Azure-portalen
Det är enkelt att skapa schemalagda jobb i Azure Scheduler. I den här självstudiekursen lär du dig hur du skapar ett jobb. Du lär dig också om övervaknings- och hanteringsfunktionerna i Scheduler.

## <a name="create-a-job"></a>Skapa ett jobb
1. Logga in på [Azure-portalen](https://portal.azure.com/).  
2. Klicka på **+Nytt** > skriv *Scheduler* i sökrutan >  välj **Scheduler** i resultatet > klicka på **Skapa**.
   
    ![][marketplace-create]
3. Vi ska skapa ett jobb som bara skickar en GET-begäran mot http://www.microsoft.com/. Ange följande information på skärmen **Scheduler-jobb**:
   
   1. **Namn:** `getmicrosoft`  
   2. **Prenumeration:** Din Azure-prenumeration   
   3. **Jobbsamling:** Markera en befintlig jobbsamling eller klicka på **Skapa ny** > ange ett namn.
4. Nu ska du definiera följande värden i **Åtgärdsinställningar**:
   
   1. **Åtgärdstyp:** ` HTTP`  
   2. **Metod:** `GET`  
   3. **URL:** ` http://www.microsoft.com`  
      
      ![][action-settings]
5. Till sist ska vi definiera ett schema. Jobbet kan definieras som ett engångsjobb, men vi väljer ett upprepningsschema:
   
   1. **Återkommande**: `Recurring`
   2. **Start**: Dagens datum
   3. **Detta ska upprepas varje**: `12 Hours`
   4. **Sluta**: Två dagar från dagens datum  
      
      ![][recurrence-schedule]
6. Klicka på **Skapa**

## <a name="manage-and-monitor-jobs"></a>Hantera och övervaka jobb
När ett jobb har skapats visas det på den primära Azure-instrumentpanelen. Klicka på jobbet så öppnas ett nytt fönster med följande flikar:

1. Egenskaper  
2. Åtgärdsinställningar  
3. Schema  
4. Historik
5. Användare
   
   ![][job-overview]

### <a name="properties"></a>Egenskaper
Dessa skrivskyddade egenskaper beskriver hanteringsmetadata för Scheduler-jobbet.

   ![][job-properties]

### <a name="action-settings"></a>Åtgärdsinställningar
Om du klickar på ett jobb på skärmen **Jobb** kan du konfigurera jobbet. Du kan konfigurera avancerade inställningar om du inte konfigurerade dem i snabbregistreringsguiden.

För alla åtgärdstyper kan du ändra återförsöksprincipen och felåtgärden.

För HTTP- och HTTPS-jobbåtgärdstyper kan du ändra metoden till valfritt tillåtet HTTP-verb. Du kan också lägga till, ta bort eller ändra sidhuvudena och grundläggande autentiseringsinformation.

För åtgärdstyper för lagringskön kan du ändra lagringskontot, könamnet, SAS-token och brödtexten.

För Service Bus-åtgärdstyper kan du ändra namnrymden, ämnes-/kösökvägen, autentiseringsinställningarna, transporttypen, meddelandeegenskaperna och meddelandetexten.

   ![][job-action-settings]

### <a name="schedule"></a>Schema
Här kan du konfigurera om schemat om du vill ändra schemat som du skapade i snabbregistreringsguiden.

Här har du möjlighet att skapa [komplexa scheman och avancerad upprepning för jobbet](scheduler-advanced-complexity.md)

Du kan ändra startdatum och starttid, upprepningsschemat och slutdatumet och sluttiden (om jobbet är återkommande).

   ![][job-schedule]

### <a name="history"></a>Historik
Fliken **Historik** innehåller utvalda mätvärden för varje jobbkörning i systemet för det valda jobbet. Dessa mätvärden är realtidsvärden som beskriver följande hälsoindikatorer i Scheduler:

1. Status  
2. Information  
3. Antal återförsök
4. Förekomst: första, andra, tredje osv.
5. Starttid för körning  
6. Sluttid för körning
   
   ![][job-history]

Du kan klicka på en körning om du vill visa  dess **historikinformation**, inklusive hela svaret för varje körning. I den här dialogrutan kan du också kopiera svaret till Urklipp.

   ![][job-history-details]

### <a name="users"></a>Användare
Rollbaserad åtkomstkontroll (RBAC) i Azure ger tillgång till ingående åtkomsthantering för Azure Scheduler. Information om hur du använder fliken Användare finns i [Rollbaserad åtkomstkontroll i Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="see-also"></a>Se även
 [Vad är Scheduler?](scheduler-intro.md)

 [Begrepp, terminologi och entitetshierarki relaterade till Scheduler](scheduler-concepts-terms.md)

 [Prenumerationer och fakturering i Azure Scheduler](scheduler-plans-billing.md)

 [Skapa komplexa scheman och avancerad upprepning med Azure Scheduler](scheduler-advanced-complexity.md)

 [REST API-referens för Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Referens för PowerShell-cmdlets för Scheduler](scheduler-powershell-reference.md)

 [Hög tillgänglighet och tillförlitlighet i Scheduler](scheduler-high-availability-reliability.md)

 [Gränser, standardinställningar och felkoder i Scheduler](scheduler-limits-defaults-errors.md)

 [Utgående autentisering i Scheduler](scheduler-outbound-authentication.md)

[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png
