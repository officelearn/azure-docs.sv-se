---
title: Azure Lighthouse i företagsscenarier
description: Funktionerna i Azure Lighthouse kan användas för att förenkla hantering av flera innehavare inom ett företag som använder flera Azure AD-klienter.
ms.date: 09/25/2019
ms.topic: conceptual
ms.openlocfilehash: 91089a6fb1a965191489e87027ef508c7ebe2aa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749203"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Azure Lighthouse i företagsscenarier

Det vanligaste scenariot för [Azure Lighthouse](../overview.md) är en tjänsteleverantör som hanterar resurser i sina kunders Azure Active Directory -klienter (Azure AD). Funktionerna i Azure Lighthouse kan dock också användas för att förenkla hantering av flera klientinnehavare inom ett företag som använder flera Azure AD-klienter.

## <a name="single-vs-multiple-tenants"></a>Enkel-jämfört med flera klienter

För de flesta organisationer är hantering enklare med en enda Azure AD-klientorganisation. Om du har alla resurser inom en klient kan centralisering av hanteringsuppgifter av angivna användare, användargrupper eller tjänsthuvudnamn inom den klienten. Vi rekommenderar att du använder en klient för din organisation när det är möjligt.

Samtidigt finns det situationer som kan kräva att en organisation underhåller flera Azure AD-klienter. I vissa fall kan detta vara en tillfällig situation, som när förvärv har ägt rum och en långsiktig strategi för klientkonsolidering kommer att ta lite tid att definiera. En organisation kan också behöva behålla flera hyresgäster löpande (på grund av helt oberoende dotterbolag, geografiska eller juridiska krav, och så vidare). I de fall där en arkitektur med flera innehavare krävs kan Azure-delegerad resurshantering användas för att centralisera och effektivisera hanteringsåtgärder. Prenumerationer från flera klienter kan vara inskrivna för [Azure-delegerad resurshantering,](azure-delegated-resource-management.md)vilket gör att utsedda användare i en hanterande klient kan utföra [hanteringsfunktioner för flera innehavare](cross-tenant-management-experience.md) på ett centraliserat och skalbart sätt.

## <a name="tenant-management-architecture"></a>Arkitektur för klienthantering

När du centraliserar hanteringsåtgärder över flera klienter måste du bestämma vilken klientorganisation som ska inkludera de användare som utför hanteringsåtgärder för de andra klienterna. Med andra ord måste du bestämma vilken klient som ska hantera klienten för andra klienter.

Anta till exempel att din organisation har en enda klient som vi anropar *klientorganisation A*. Din organisation förvärvar sedan ytterligare två klienter, *klient B* och *klient C,* och du har affärsskäl som kräver att du underhåller dem som separata klienter.

Din organisation vill använda samma principdefinitioner, säkerhetskopieringspraxis och säkerhetsprocesser för alla klienter. Eftersom du redan har användare (inklusive användargrupper och tjänsthuvudnamn) som ansvarar för att utföra dessa uppgifter i klient A, kan du gå ombord på alla prenumerationer inom klient B och klient C så att samma användare i klient A kan utföra dessa Uppgifter.

![Användare i klient A hantera resurser i klient B och klient C](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Säkerhets- och åtkomstöverväganden

I de flesta företagsscenarier vill du delegera en fullständig prenumeration för Azure-delegerad resurshantering, även om du också bara kan delegera specifika resursgrupper inom en prenumeration.

Hursomhelst, se till att [följa principen om minst privilegium när du definierar vilka användare som ska ha tillgång till resurser](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege). Detta bidrar till att säkerställa att användarna bara har de behörigheter som krävs för att utföra de nödvändiga uppgifterna och minskar risken för oavsiktliga fel.

Azure Lighthouse och Azure delegerad resurshantering ger bara logiska länkar mellan en hanterande klient och hanterade klienter, i stället för att fysiskt flytta data eller resurser. Dessutom går åtkomsten alltid i bara en riktning, från den förvaltade klienten till de hanterade klienterna.  Användare och grupper i den hanterande klienten bör fortsätta att använda multifaktorautentisering när hanteringsåtgärder utförs på hanterade klientresurser.

Företag med interna eller externa styrnings- och efterlevnadsskydd kan använda [Azure Activity-loggar](../../azure-monitor/platform/platform-logs-overview.md) för att uppfylla sina transparenskrav. När företagsklienter har upprättat hantera och hanterade klientrelationer kan användare i varje klient övervaka och få insyn i åtgärder som vidtas av användarna i den andra klienten genom att visa loggad aktivitet.

## <a name="onboarding-process-considerations"></a>Hänsyn till introduktionsprocessen

Prenumerationer (eller resursgrupper inom en prenumeration) kan registreras i Azure-delegerad resurshantering antingen genom att distribuera Azure Resource Manager-mallar eller via erbjudanden om hanterade tjänster som publiceras på Azure Marketplace, antingen privat eller Offentligt.

Eftersom företagsanvändare normalt kan få direkt åtkomst till företagets klienter, och det inte finns något behov av att marknadsföra eller marknadsföra ett hanteringserbjudande, är det i allmänhet snabbare och enklare att distribuera direkt med Azure Resource Manager-mallar. Även om vi hänvisar till tjänsteleverantörer och kunder i [onboarding-vägledningen](../how-to/onboard-customer.md)kan företag använda samma processer.

Om du vill kan klienter inom ett företag vara inbyggda genom [att publicera ett erbjudande om hanterade tjänster till Azure Marketplace](../how-to/publish-managed-services-offers.md). Se till att erbjudandet endast är tillgängligt för lämpliga klienter genom att se till att dina planer är markerade som privata. Med en privat plan kan du ange prenumerations-ID:t för varje klient som du planerar att vara ombord på, och ingen annan kan få ditt erbjudande.

## <a name="terminology-notes"></a>Terminologianteckningar

För hantering över flera innehavare inom företaget kan referenser till tjänsteleverantörer i Azure Lighthouse-dokumentationen förstås gälla för hantering av klienten i ett företag, det vill än den klientorganisation som innehåller de användare som ska hantera resurser i andra klienter via Azure-delegerad resurshantering. På samma sätt kan referenser till kunder förstås att gälla för klienter som delegerar resurser som ska hanteras via användare i den hanterande klienten.

I exemplet som beskrivs ovan kan klient A till exempel ses som tjänstleverantörens klientorganisation (den hanterande klienten) och klient B och klient C kan ses som kundklienter.

I det exemplet kan klient-A-användare med rätt behörighet [visa och hantera delegerade resurser](../how-to/view-manage-customers.md) på sidan Mina **kunder** på Azure-portalen. På samma sätt kan användare av klientN B och Klient C med rätt behörighet [visa och hantera de resurser som har delegerats](../how-to/view-manage-service-providers.md) till klient A på sidan **Tjänstleverantörer** i Azure-portalen.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hanteringsupplevelser mellan klienter.](cross-tenant-management-experience.md)
- Lär dig mer om [Azure-delegerad resurshantering](azure-delegated-resource-management.md).