---
title: Azure Resource Health vanliga frågor och svar | Microsoft Docs
description: Översikt över Azure Resource Health
services: Resource health
documentationcenter: dev-center-name
author: BernardoAMunoz
manager: ''
editor: ''
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 03/27/2018
ms.author: BernardoAMunoz
ms.openlocfilehash: 2a0758f5db381f721120cbc1c018a491c018aa8d
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="azure-resource-health-faq"></a>Azure Resource Health vanliga frågor och svar
Lär dig svar på vanliga frågor om Azure Resource Health.

## <a name="what-is-azure-resource-health"></a>Vad är Azure Resource Health?
Resource Health hjälper dig att diagnostisera och få support när ett problem med Azure påverkar dina resurser. Det informerar dig om det aktuella och tidigare hälsotillståndet för dina resurser och hjälper dig att åtgärda problem. Resource Health ger teknisk support när du behöver hjälp med problem med Azure-tjänster.  

## <a name="what-is-the-resource-health-intended-for"></a>Vad är Resource Health avsedd för?
När ett problem med en resurs har identifierats hjälper Resource Health dig att diagnostisera orsaken. Det ger hjälpa till att minska problem och teknisk support om du behöver mer hjälp med problem med Azure-tjänsten.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Vilka kontroll utförs av Resource Health?
Resurshälsa kontrollerar olika baserat på de [resurstypen](resource-health-checks-resource-types.md). Dessa kontroller är utformade för att implementera tre typer av problem: 
- Oplanerad händelser, till exempel en oväntade värden omstart
- Planerad händelser, t.ex. uppdateringar schemalagda värdens operativsystem
- Händelser som utlösts av användaråtgärder, till exempel en användare startar om en virtuell dator

## <a name="what-does-each-of-the-health-status-mean"></a>Vad innebär varje hälsostatus?
Det finns tre olika hälsotillstånd statusar:
- Tillgänglig: Det finns inga kända problem i Azure-plattformen som kan påverka den här resursen
- Inte tillgänglig: Resource health har upptäckt problem som påverkar resursen
- Okänd: Resource health kan inte fastställa hälsotillståndet för en resurs eftersom den har stoppats får information om den. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>Vad innebär okänd status? Något är fel med min resurs?
Hälsostatus anges till okänd när Resource Health stoppar tar emot information om en viss resurs. Denna status är inte en slutgiltig indikation på tillståndet för resursen i fall där du får problem, kan det indikera det har uppstått ett problem med Azure.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Hur kan jag få hjälp för en resurs som inte är tillgänglig?
Du kan skicka en begäran från Resource Health-bladet. Du behöver inte ett supportavtal med Microsoft för att öppna en begäran när resursen är inte tillgänglig eftersom plattformen händelser.

## <a name="does-resource-health-differentiate-between-unavailability-cased-by-platform-problems-versus-something-i-did"></a>Resource Health skilja mellan inte finns alltid i av plattform problem jämfört med något som jag gjorde?
När en resurs är tillgänglig, identifierar Resource Health Ja, den grundläggande orsaken i en av dessa kategorier: 
-   Användarinitierad åtgärd
-   Planerad åtgärd 
-   Oplanerad händelse

I portalen visas användarinitierad åtgärder med en blå meddelandeikonen medan planerade och oplanerade händelser visas med en röd varningsikon. Mer information finns i den [Resource Health översikt](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Kan jag integrera Resource Health med min övervakningsverktyg?
Resurshälsotillståndet är en tjänst som hjälper dig att diagnostisera och åtgärda problem med Azure-tjänsten som påverkar dina resurser. Medan du kan använda API: et för Resource Health att genom programmering få hälsostatus, rekommenderar vi att du använder mått för att övervaka dina resurser. När ett problem upptäcks Resource Health hjälper dig att avgöra den bakomliggande orsaken och hjälper dig att åtgärder för att behandla dem. Besök [Azure-Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) lära dig mer om hur du kan använda mått för att kontrollera dina resurser.

## <a name="where-do-i-find-resource-health"></a>Var hittar Resource Health?
När du loggar in på Azure-portalen, finns det flera sätt som du kan komma åt Resource Health:
- Gå till din resurs. I det vänstra navigeringsfönstret väljer **resurshälsa**
- Gå till Azure-Monitor-bladet.  I det vänstra navigeringsfönstret väljer **Resource health**.
- Öppna den **hjälp + stöd** bladet genom att välja ett frågetecken i det övre högra hörnet av portalen och sedan välja **hjälp + stöd**. Då öppnas bladet välj **resurshälsa**

Du kan också använda Resource Health API för att hämta information om hälsotillståndet för dina resurser.

## <a name="is-resource-health-available-for-all-resource-types"></a>Är Resource Health tillgängligt för alla typer av resurser?
Listan över hälsokontroller och resurstyper som stöds via Resource Health finns [här](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>Vad gör jag om min resurs visas tillgängliga men jag tror att det inte är ”?
Kontrollerar hälsotillståndet för en resurs direkt under hälsostatus du klicka på **Rapportera felaktig hälsostatus**. Innan du skickar rapporten har du möjlighet att ange ytterligare information om varför du tror att det aktuella hälsotillståndet är felaktig.

## <a name="is-resource-health-available-for-all-azure-regions"></a>Är Resource Health tillgängligt för alla Azure-regioner? 
Resurshälsa finns tillgänglig i alla regioner som Azure förutom följande områden:
- Virginia (USA-förvaltad region)
- Iowa (USA-förvaltad region)
- US DoD, östra
- US DoD, centrala
- Centrala Tyskland
- Nordöstra Tyskland

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Hur skiljer sig Resource Health från Azure status eller tjänstens hälsa för instrumentpanelen?
Den information som tillhandahålls av Resource Health är mer specifik än vad som tillhandahålls av Azure status eller tjänstens hälsa för instrumentpanelen.

Medan [Azure status](https://status.azure.com) och tjänstens hälsa för instrumentpanelen ger information om tjänsten problem som påverkar ett stort antal kunder (till exempel en Azure-region), Resource Health exponerar mer detaljerade händelser som är relevanta för endast den viss resurs. Om en värd oväntat startar om aviseringar Resource Health kunder vars virtuella datorer körs på värden.

Det är viktigt att Observera att för att ge dig en fullständig överblick över händelser som påverkar dina resurser, Resource Health tillhandahåller även händelser som har publicerats i tjänstens hälsa för instrumentpanelen.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>Behöver jag aktivera Resource Health för varje resurs?
Nej, hälsoinformation är tillgängliga för alla resurstyper som är tillgängliga via Resource Health. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Vi behöver du aktivera Resource Health för min organisation?
Nej.  Azure Resource Health är tillgänglig i Azure-portalen utan att alla krav för nätverksinstallation.

## <a name="is-resource-health-available-free-of-charge"></a>Finns Resource Health kostnadsfritt?
Ja.  Azure Resource Health är kostnadsfritt.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Vilka är de rekommendationer som Resource Health tillhandahåller?
Baserat på hälsostatus ger Resource Health dig rekommendationer med målet att minska tid som du har använt för felsökning. Fokus rekommendationer om hur du löser de flesta vanliga problem kunderna får för tillgängliga resurser. Om resursen inte är tillgänglig på grund av en Azure oplanerade händelse kommer fokus att bistå du under och efter återställningen. 

## <a name="next-steps"></a>Nästa steg

Läs mer om Resource Health:
-  [Översikt över Azure Resource Health](Resource-health-overview.md)
-  [Resurstyper och hälsokontroller är tillgängliga genom Azure Resource Health](resource-health-checks-resource-types.md)
