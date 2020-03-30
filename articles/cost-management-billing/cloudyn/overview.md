---
title: Översikt över Cloudyn i Azure
description: Cloudyn är en kostnadshanteringslösning med flera moln som hjälper dig att använda Azure och andra molnresurser.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: overview
ms.service: cost-management-billing
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 9e1acee631316ec961af364769daa1f2a3001e72
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80065856"
---
# <a name="what-is-the-cloudyn-service"></a>Vad är tjänsten Cloudyn?

Cloudyn, ett dotterbolag till Microsoft, gör det möjligt att spåra molnanvändning och utgifter för dina Azure-resurser och andra molnleverantörer, inklusive AWS och Google. Instrumentpanelsrapporter som är lätta att förstå hjälper dig med kostnadsallokering och kostnadsrapporter/återbetalningar. Cloudyn hjälper dig att optimera molnutgifterna genom att identifiera underutnyttjade resurser som du sedan kan hantera och anpassa.

En introduktionsvideo finns i [Introduction to Azure Cloudyn ](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo/) (Introduktion till Azure Cloudyn).
 
Azure Cost Management innehåller liknande funktioner som Cloudyn. Azure Cost Management är en lösning för intern kostnadshantering i Azure. Du får hjälp att analysera kostnader, skapa och hantera budgetar, exportera data samt granska och arbeta med optimeringsrekommendationer för att spara pengar. Mer information finns i [Azure Cost Management](../cost-management-billing-overview.md).
 
[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

Titta på [videon om Azure Cost Management och Cloudyn](https://www.youtube.com/watch?v=15DzKPMBRxM) för att se rekommendationer om när du ska använda antingen Azure Cost Management eller Cloudyn, baserat på dina affärsbehov.
 
>[!VIDEO https://www.youtube.com/embed/15DzKPMBRxM]

## <a name="monitor-usage-and-spending"></a>Övervaka användning och utgifter

Det är mycket viktigt att övervaka användning och utgifter för molninfrastrukturer, eftersom företag betalar för de resurser de förbrukar över tid. När användningen överskrider avtalets trösklar kan det gå snabbt att få oväntade överförbrukningskostnader. Det finns några viktiga faktorer som kan försvåra oplanerad övervakning. Kostnadsprognoser baserat på genomsnittlig användning antar att förbrukningen förblir konsekvent under en viss faktureringsperiod. För det andra är det viktigt att du får förebyggande aviseringar för att kunna anpassa utgifterna om kostnaderna ligger nära eller överskrider din budget. Dessutom kanske inte molntjänstleverantörer erbjuder kostnadsprognoser i förhållande till trösklar eller jämförelserapporter för olika perioder.

Rapporterna hjälper dig att övervaka utgifterna för att kunna analysera och följa molnanvändning, kostnader och trender. Med hjälp av rapporter för tidsperioder kan du upptäcka avvikelser som skiljer sig från normala trender. Ineffektivitet i molndistributionen syns i optimeringsrapporterna. Du kan även se ineffektivitet i kostnadsanalysrapporterna.

## <a name="manage-costs"></a>Hantera kostnader

Historiska data kan hjälpa dig att hantera kostnader när du analyserar användning och kostnader över tid för att identifiera trender. Trender används sedan för att skapa prognoser för framtida utgifter. Cloudyn innehåller även användbara rapporter för kostnadsprognoser.

Kostnadsallokering hanterar kostnader genom att analysera kostnaderna baserat på din taggningsprincip. Du kan använda taggar på anpassade konton, resurser och entiteter för att förfina kostnadsallokeringen. Category Manager organiserar taggarna för att ge ytterligare styrning. Du kan även använda kostnadsallokering för kostnadsrapporter/återbetalning för att visa resursanvändning och kopplade kostnader för att påverka förbrukningsbeteenden eller debitera klientkunder.

Åtkomstkontroll hjälper till att hantera kostnader genom att se till att användare och team endast har åtkomst till de kostnadshanteringsdata som de behöver. Du kan använda entitetsstruktur, användarhantering och schemalagda rapporter med mottagarlistor för att tilldela åtkomst.

Aviseringar kan hjälpa till att hantera kostnader genom att meddela dig automatiskt vid ovanliga utgifter eller för höga utgifter. Aviseringar kan även meddela andra intressenter automatiskt vid avvikelser i utgifter och risk för höga utgifter. Olika rapporter har stöd för aviseringar baserat på budget- och kostnadströsklar. Aviseringar har dock för närvarande inte stöd för CSP-partnerkonton eller prenumerationer.

## <a name="improve-efficiency"></a>Förbättra effektiviteten

Du kan fastställa optimal användning av virtuella datorer och identifiera virtuella datorer som inte används, eller ta bort virtuella datorer som inte används och ej anslutna diskar med Cloudyn. Med hjälp av informationen i rapporterna för storleksoptimering och ineffektivitet kan du skapa en plan för att minska storleken eller ta bort virtuella datorer som inte används. Optimeringsrapporter stöds dock för närvarande inte för CSP-partnerkonton eller prenumerationer.

Om du har etablerat reserverade AWS-instanser kan du förbättra användningen av de reserverade instanserna med optimeringsrapporter där du kan se köprekommendationer, ändra oanvända reservationer och planera etablering.


## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med Cloudyn är nästa steg att registrera molnmiljön och börja utforska dina data.

- [Registrera dig för CSP-partnerprogrammet och visa kostnadsdata](quick-register-csp.md)
