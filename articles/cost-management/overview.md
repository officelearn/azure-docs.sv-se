---
title: Översikt över Cloudyn i Azure | Microsoft Docs
description: Cloudyn är en kostnadshanteringslösning med flera moln som hjälper dig att använda Azure och andra molnresurser.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2019
ms.topic: overview
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 0bdf47991435a9a28a4db12f3aa89478b18bb9f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61020275"
---
# <a name="what-is-the-cloudyn-service"></a>Vad är tjänsten Cloudyn?

Cloudyn, ett dotterbolag till Microsoft, gör det möjligt att spåra molnanvändning och utgifter för dina Azure-resurser och andra molnleverantörer, inklusive AWS och Google. Instrumentpanelsrapporter som är lätta att förstå hjälper dig med kostnadsallokering och kostnadsrapporter/återbetalningar. Cloudyn hjälper dig att optimera molnutgifterna genom att identifiera underutnyttjade resurser som du sedan kan hantera och anpassa.

En introduktionsvideo finns i [Introduction to Azure Cloudyn ](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo) (Introduktion till Azure Cloudyn).

Azure Cost Management innehåller liknande funktioner som Cloudyn. Azure Cost Management är en lösning för intern kostnadshantering i Azure. Du får hjälp att analysera kostnader, skapa och hantera budgetar, exportera data samt granska och arbeta med optimeringsrekommendationer för att spara pengar. Mer information finns i [Azure Cost Management](overview-cost-mgt.md).

## <a name="cloudyn-features-moving-to-azure-cost-management"></a>Cloudyn-funktioner som flyttar till Azure Cost Management

Microsoft har förvärvat Cloudyn och migrera dess kostnaden hanteringsfunktioner från Cloudyn-portalen internt i Azure. Om du vill använda de nya funktionerna, logga in på Azure-portalen och gå till [kostnadshantering och fakturering](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) i listan över Azure-tjänster. Den inbyggda upplevelsen erbjuder jämfört med Cloudyn, bättre prestanda och lägre datafördröjningen av cirka åtta timmar.

Nyckelfunktion migreringen för Enterprise-avtal, betala per användning och MSDN-erbjudandet kategorier för att Azure Cost Management är klar. CSP-prenumerationer håller på att migreras till Azure Cost Management.

Om du har ännu inte migrerade en erbjudandekategori, bör du fortsätta att använda Cloudyn-portalen. Alla andra kan använda Azure Cost Management.

| Microsoft Azure-erbjudanden och funktioner | Rekommenderade cost management-tjänsten |
| --- | --- |
| Azure Enterprise Agreement | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web-Direct (PAYG/MSDN) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |
| Molnöverskridande cost analysis-stöd för AWS (i förhandsversion) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| AWS-rekommendationer | [Cloudyn](https://azure.cloudyn.com) |

Några av de följande funktionerna är tillgängliga i Cloudyn, men alla är nu tillgängligt i Azure Cost Management.

- API:er
- Rekommendationer för Azure-beräkning
- Azure Reservation-rekommendationer
- Budgetar
- Kostnadsanalys
- Exportera data till ett Azure storage-konto
- Mindre fördröjning
- Power BI-Innehållspaketet och anslutningen
- Tagg-resursstöd

## <a name="monitor-usage-and-spending"></a>Övervaka användning och utgifter

Det är mycket viktigt att övervaka användning och utgifter för molninfrastrukturer, eftersom företag betalar för de resurser de förbrukar över tid. När användningen överskrider avtalets trösklar kan det gå snabbt att få oväntade överförbrukningskostnader. Några viktiga faktorer kan göra ad hoc-övervakning svårt. Kostnadsprognoser baserat på genomsnittlig användning antar att förbrukningen förblir konsekvent under en viss faktureringsperiod. För det andra är det viktigt att du får förebyggande aviseringar för att kunna anpassa utgifterna om kostnaderna ligger nära eller överskrider din budget. Dessutom kanske inte molntjänstleverantörer erbjuder kostnadsprognoser i förhållande till trösklar eller jämförelserapporter för olika perioder.

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

- [Registrera en enskild Azure-prenumeration](quick-register-azure-sub.md)
