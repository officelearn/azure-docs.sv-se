---
title: "Vad är Azure Scheduler? | Microsoft Docs"
description: "Med Azure Scheduler kan du deklarativt beskriva åtgärder som ska köras i molnet. Tjänsten schemalägger och kör sedan dessa åtgärder automatiskt."
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 52aa6ae1-4c3d-43fb-81b0-6792c84bcfae
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/18/2016
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f2b21e24a5f692fa315db7b58d2d2824f7145729


---
# <a name="what-is-azure-scheduler"></a>Vad är Azure Scheduler?
Med Azure Scheduler kan du deklarativt beskriva åtgärder som ska köras i molnet. Tjänsten schemalägger och kör sedan dessa åtgärder automatiskt.  Scheduler gör detta med hjälp av [Azure-portalen](scheduler-get-started-portal.md), kod, [REST-API:et](https://msdn.microsoft.com/library/mt629143.aspx) eller Azure PowerShell.

Scheduler skapar, underhåller och anropar schemalagt arbete.  Scheduler är inte värd för några arbetsbelastningar och kör ingen kod. Tjänsten *anropar* bara kod som finns på en annan plats – i Azure, lokalt eller med en annan provider. Den anropar via HTTP, HTTPS, en lagringskö, en Service Bus-kö eller ett Service Bus-ämne.

Scheduler schemalägger [jobb](scheduler-concepts-terms.md), sparar en historik över resultaten från jobbkörningar som du kan granska och schemalägger arbetsbelastningar som ska köras deterministiskt och med hög tillförlitlighet. Azure WebJobs (del av Web Apps-funktionen i Azure App Service) och andra schemaläggningsfunktioner i Azure använder Scheduler i bakgrunden. [REST-API:et för Scheduler](https://msdn.microsoft.com/library/mt629143.aspx) hjälper till att hantera kommunikationen för dessa åtgärder. Scheduler har stöd för [komplexa scheman och avancerad upprepning](scheduler-advanced-complexity.md).

Scheduler är användbart i många olika scenarier. Exempel:

* *Åtgärder för återkommande program:* Samla in data från Twitter till en feed med jämna mellanrum.
* *Dagligt underhåll:* Daglig loggrensning, säkerhetskopiering och andra underhållsåtgärder. En administratör kan exempelvis välja att säkerhetskopiera databasen klockan 01:00 varje dag under de kommande nio månaderna.

Med Scheduler kan du skapa, uppdatera, ta bort, visa och hantera jobb och [jobbsamlingar](scheduler-concepts-terms.md) via programmering med hjälp av skript och på portalen.

## <a name="see-also"></a>Se även
 [Begrepp, terminologi och entitetshierarki relaterade till Azure Scheduler](scheduler-concepts-terms.md)

 [Komma igång med Scheduler på Azure-portalen](scheduler-get-started-portal.md)

 [Prenumerationer och fakturering i Azure Scheduler](scheduler-plans-billing.md)

 [Skapa komplexa scheman och avancerad upprepning med Azure Scheduler](scheduler-advanced-complexity.md)

 [Referens för REST-API:et för Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Referens för PowerShell-cmdlets för Azure Scheduler](scheduler-powershell-reference.md)

 [Hög tillgänglighet och tillförlitlighet i Azure Scheduler](scheduler-high-availability-reliability.md)

 [Gränser, standardinställningar och felkoder i Azure Scheduler](scheduler-limits-defaults-errors.md)

 [Utgående autentisering i Azure Scheduler](scheduler-outbound-authentication.md)




<!--HONumber=Nov16_HO2-->


