---
title: "Schemaläggaren hög tillgänglighet och tillförlitlighet"
description: "Schemaläggaren hög tillgänglighet och tillförlitlighet"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 5ec78e60-a9b9-405a-91a8-f010f3872d50
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/16/2016
ms.author: deli
ms.openlocfilehash: 7e7fe49de7814b6058468d630f8638720e5864f3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="scheduler-high-availability-and-reliability"></a>Schemaläggaren hög tillgänglighet och tillförlitlighet
## <a name="azure-scheduler-high-availability"></a>Azure Scheduler hög tillgänglighet
Som en kärna Azure-plattformstjänsten Azure Schemaläggaren har hög tillgänglighet och funktioner både geo-redundant service-distributionen och geo regionala jobbet replikering.

### <a name="geo-redundant-service-deployment"></a>GEO-redundant tjänstdistribution
Azure Schemaläggaren är tillgänglig via Användargränssnittet i nästan alla geografisk region som är i Azure idag. Listan över regioner som Azure Schemaläggaren finns i är [som listas här](https://azure.microsoft.com/regions/#services). Om ett datacenter i en värdbaserad region återges inte tillgänglig, är funktioner för redundans av Azure-schemaläggare så att tjänsten är tillgänglig från en annan datacenter.

### <a name="geo-regional-job-replication"></a>GEO-regionala jobbet replikering
Är inte bara Azure Schemaläggaren frontend tillgänglig för av hanteringsbegäranden, men din egen jobbet är också georeplikerad. När det finns ett avbrott i en region, Azure Scheduler växlas över och ser till att jobbet körs från en annan datacenter i parad geografiska region.

Till exempel om du har skapat ett jobb i södra centrala USA, replikerar Azure Scheduler automatiskt det jobbet i norra centrala USA. När det finns ett fel i södra centrala USA, säkerställer Azure Schemaläggaren att jobbet körs från norra centrala USA. 

![][1]

Därför garanterar Azure Scheduler att dina data håller sig inom samma bredare geografiska region om ett fel uppstår på Azure. Därför kan du inte behöver duplicera jobbet bara att lägga till hög tillgänglighet – Azure Scheduler innehåller automatiskt funktioner för hög tillgänglighet för dina jobb.

## <a name="azure-scheduler-reliability"></a>Azure Scheduler tillförlitlighet
Azure Scheduler garanterar sin egen hög tillgänglighet och använder en annan metod till användarskapade jobb. Jobbet kan till exempel anropa en HTTP-slutpunkt som inte är tillgänglig. Azure Schemaläggaren försöker dock köras ditt jobb, genom att ge dig alternativ att åtgärda felet. Azure Scheduler gör detta på två sätt:

### <a name="configurable-retry-policy-via-retrypolicy"></a>Konfigurerbar försök princip via ”retryPolicy”
Azure Schemaläggaren kan du konfigurera en princip för återförsök. Som standard om ett jobb misslyckas försöker Schemaläggaren jobbet igen fyra gånger, med 30 sekunders intervall. Du kan konfigurera principen så att mer aggressivt (till exempel tio gånger med 30 sekunders mellanrum) försök igen eller glesare (till exempel två gånger med daglig intervall.)

Som ett exempel på när det kan hjälpa dig, kan du skapa ett jobb som körs en gång i veckan och anropar en HTTP-slutpunkt. Om HTTP-slutpunkten avser några timmar när jobbet körs, kan du inte vill vänta en mer vecka för jobbet ska köras igen eftersom även standardprincipen för nytt försök kommer att misslyckas. I sådana fall kan du konfigurera om principen standard försök att försöka igen var tredje timme (till exempel) i stället för med 30 sekunders mellanrum.

Om du vill veta hur du konfigurerar en återförsöksprincip avser [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoint-configurability-via-erroraction"></a>Den alternativa slutpunkten konfigurationsmöjligheter via ”errorAction”
Mål-slutpunkten för din Azure Scheduler-jobbet är inte kan nås, faller Azure Scheduler tillbaka till den alternativa slutpunkten felhantering när du har följt av dess återförsöksprincip. Om en annan felhantering slutpunkt har konfigurerats, anropar det Azure Schemaläggaren. Med en annan slutpunkt är egna jobb hög tillgänglighet i händelse av fel.

Exempelvis i diagrammet nedan, följer Azure Scheduler policyn försök igen om du vill nådde en New York-webbtjänst. När de nya försök misslyckas kontrollerar om det finns ett alternativ. Sedan går vidare och startar gör begäranden till alternativa med samma återförsöksprincip.

![][2]

Observera att samma återförsöksprincip gäller både den ursprungliga åtgärden och den alternativa felåtgärden. Du kan också har den alternativa felåtgärd åtgärdstyp skilja sig från den huvudsakliga åtgärden åtgärdstyp. Till exempel när huvudåtgärden kan anropa en HTTP-slutpunkt, kanske felåtgärden i stället queue storage, service bus-kö och service bus avsnittet åtgärd som har fel-loggning.

Information om hur du konfigurerar en annan slutpunkt, referera till [errorAction](scheduler-concepts-terms.md#action-and-erroraction).

## <a name="see-also"></a>Se även
 [Vad är Scheduler?](scheduler-intro.md)

 [Begrepp, terminologi och entitetshierarki relaterade till Azure Scheduler](scheduler-concepts-terms.md)

 [Komma igång med Scheduler på Azure-portalen](scheduler-get-started-portal.md)

 [Prenumerationer och fakturering i Azure Scheduler](scheduler-plans-billing.md)

 [Skapa komplexa scheman och avancerad upprepning med Azure Scheduler](scheduler-advanced-complexity.md)

 [Referens för REST-API:et för Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Referens för PowerShell-cmdlets för Azure Scheduler](scheduler-powershell-reference.md)

 [Gränser, standardinställningar och felkoder i Azure Scheduler](scheduler-limits-defaults-errors.md)

 [Utgående autentisering i Azure Scheduler](scheduler-outbound-authentication.md)

[1]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png

[2]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png
