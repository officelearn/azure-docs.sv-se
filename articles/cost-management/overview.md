---
title: "Översikt över Azure Cost Management från Cloudyn | Microsoft Docs"
description: "Azure Cost Management från Cloudyn är en kostnadshanteringslösning med flera moln som hjälper dig att bättre använda Azure och andra molnresurser."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: overview
ms.service: cost-management
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 969340080bfe2b04704367c2225895728773119e
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2017
---
# <a name="what-is-azure-cost-management"></a>Vad är Azure Cost Management?

Azure Cost Management från Cloudyn gör det möjligt att spåra molnanvändning och utgifter för dina Azure-resurser och andra molnleverantörer, inklusive AWS och Google. Instrumentpanelsrapporter som är lätta att förstå hjälper dig med kostnadsallokering och kostnadsrapporter/återbetalningar. Cost Management hjälper dig att optimera molnutgifterna genom att identifiera underutnyttjade resurser som du sedan kan hantera och anpassa.

En introduktionsvideo finns i [Introduction to Azure Cost Management by Cloudyn](https://youtu.be/NWIRny6Wpsk) (Introduktion till Azure Cost Management från Cloudyn).

## <a name="monitor-usage-and-spending"></a>Övervaka användning och utgifter

Det är mycket viktigt att övervaka användning och utgifter för molninfrastrukturer, eftersom företag betalar för de resurser de förbrukar över tid. När användningen överskrider avtalets trösklar kan det gå snabbt att få oväntade överförbrukningskostnader. Det finns några viktiga faktorer som kan försvåra ad-hoc-övervakning. Kostnadsprognoser baserat på genomsnittlig användning antar att förbrukningen förblir konsekvent under en viss faktureringsperiod. För det andra är det viktigt att du får förebyggande aviseringar för att kunna anpassa utgifterna om kostnaderna ligger nära eller överskrider din budget. Dessutom kanske inte molntjänstleverantörer erbjuder kostnadsprognoser i förhållande till trösklar eller jämförelserapporter för olika perioder.

Rapporterna hjälper dig att övervaka utgifterna för att kunna analysera och följa molnanvändning, kostnader och trender. Med hjälp av rapporter för tidsperioder kan du upptäcka avvikelser som skiljer sig från normala trender. Ineffektivitet i molndistributionen syns i optimeringsrapporterna. Du kan även se ineffektivitet i kostnadsanalysrapporterna.

![Rapport för kostnad över tid](media\overview\cost-over-time-rpt.png)


## <a name="manage-costs"></a>Hantera kostnader

Historiska data kan hjälpa dig att hantera kostnader när du analyserar användning och kostnader över tid för att identifiera trender. Trender används sedan för att skapa prognoser för framtida utgifter. Cost Management innehåller även användbara rapporter för kostnadsprognoser.

Kostnadsallokering hanterar kostnader genom att analysera kostnaderna baserat på din taggningsprincip. Du kan använda taggar på anpassade konton, resurser och entiteter för att förfina kostnadsallokeringen. Category Manager organiserar taggarna för att ge ytterligare styrning. Du kan även använda kostnadsallokering för kostnadsrapporter/återbetalning för att visa resursanvändning och kopplade kostnader för att påverka förbrukningsbeteenden eller debitera klientkunder.

Åtkomstkontroll hjälper till att hantera kostnader genom att se till att användare och team endast har åtkomst till de kostnadshanteringsdata som de behöver. Du kan använda entitetsstruktur, användarhantering och schemalagda rapporter med mottagarlistor för att tilldela åtkomst.

Aviseringar kan hjälpa till att hantera kostnader genom att meddela dig automatiskt vid ovanliga utgifter eller för höga utgifter. Aviseringar kan även meddela andra intressenter automatiskt vid avvikelser i utgifter och risk för höga utgifter. Olika rapporter har stöd för aviseringar baserat på budget- och kostnadströsklar. Aviseringar har dock för närvarande inte stöd för CSP-partnerkonton eller prenumerationer.

## <a name="improve-efficiency"></a>Förbättra effektiviteten

Du kan fastställa optimal användning av virtuella datorer och identifiera virtuella datorer som inte används, eller ta bort virtuella datorer som inte används och ej anslutna diskar med Cost Management. Med hjälp av informationen i rapporterna för storleksoptimering och ineffektivitet kan du skapa en plan för att minska storleken eller ta bort virtuella datorer som inte används. Optimeringsrapporter stöds dock för närvarande inte för CSP-partnerkonton eller prenumerationer.

![storleksrekommendationer](.\media\overview\sizing.png)

Om du har etablerat reserverade AWS-instanser kan du förbättra användningen av de reserverade instanserna med optimeringsrapporter där du kan se köprekommendationer, ändra oanvända reservationer och planera etablering.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med Cost Management är nästa steg att registrera molnmiljön och börja utforska dina data.

- [Registrera en enskild Azure-prenumeration](quick-register-azure-sub.md)
