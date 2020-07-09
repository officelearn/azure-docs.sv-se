---
title: Azure Lighthouse i företagsscenarier
description: Funktionerna i Azure Lighthouse kan användas för att förenkla hanteringen av flera innehavare i ett företag som använder flera Azure AD-klienter.
ms.date: 07/06/2020
ms.topic: conceptual
ms.openlocfilehash: 9f9a7aa81772a1edda5fd1915918b547a3066455
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86114150"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Azure Lighthouse i företagsscenarier

Det vanligaste scenariot för [Azure Lighthouse](../overview.md) är en tjänst leverantör som hanterar resurser i sina kunders Azure Active Directory (Azure AD)-klient organisationer. Funktionerna i Azure Lighthouse kan dock också användas för att förenkla hanteringen av flera innehavare i ett företag som använder flera Azure AD-klienter.

## <a name="single-vs-multiple-tenants"></a>En eller flera klienter

För de flesta organisationer är hanteringen enklare med en enda Azure AD-klient. Med alla resurser inom en klient organisation kan centralisering hanterings uppgifter hanteras av användare, användar grupper eller tjänstens huvud namn inom den klient organisationen. Vi rekommenderar att du använder en klient organisation för din organisation närhelst det är möjligt.

På samma tid finns det situationer som kan kräva att en organisation underhåller flera Azure AD-klienter. I vissa fall kan detta vara en tillfällig situation, precis som när förvärv har ägt rum och en långsiktig konsoliderings strategi för innehavare kan ta lite tid att definiera. En organisation kan också behöva upprätthålla flera klient organisationer fort löp ande (på grund av helt oberoende dotter bolag, geografiska eller juridiska krav och så vidare). I de fall där en arkitektur för flera innehavare krävs kan Azure Lighthouse användas för att centralisera och effektivisera hanterings åtgärder. Prenumerationer från flera klienter kan registreras för Azure- [delegerad resurs hantering](azure-delegated-resource-management.md), så att användare i en hanterings klient kan utföra [hanterings funktioner för flera innehavare](cross-tenant-management-experience.md) på ett centraliserat och skalbart sätt.

## <a name="tenant-management-architecture"></a>Arkitektur för klient hantering

När du centraliserar hanterings åtgärder över flera klienter måste du bestämma vilken klient som ska innehålla de användare som utför hanterings åtgärder för de andra klient organisationerna. Med andra ord måste du bestämma vilken klient som ska vara hanterings klient för andra klienter.

Anta till exempel att din organisation har en enda klient som vi ska kalla *klient a*. Din organisation kommer sedan att förvärva ytterligare två klienter, *klient B* och *klient C*och du har affärs skäl som kräver att du underhåller dem som separata klienter.

Organisationen vill använda samma princip definitioner, säkerhets kopierings metoder och säkerhets processer för alla klienter. Eftersom du redan har användare (inklusive användar grupper och tjänstens huvud namn) som ansvarar för att utföra dessa uppgifter i klient organisationen A, kan du publicera alla prenumerationer i klient B och klient C så att samma användare i klient organisationen kan utföra dessa uppgifter.

![Användare i klient organisation A hanterar resurser i klient B och klient organisation C](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Säkerhets-och åtkomst överväganden

I de flesta företags scenarier vill du delegera en fullständig prenumeration till Azure-Lighthouse, men du kan också delegera endast vissa resurs grupper i en prenumeration.

Något av följande sätt måste du [följa principen om minsta behörighet när du definierar vilka användare som ska ha åtkomst till resurser](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege). På så sätt kan du se till att användarna bara har de behörigheter som krävs för att utföra de uppgifter som krävs och minskar risken för oavsiktliga fel.

Azure Lighthouse tillhandahåller endast logiska länkar mellan en hanterande klient organisation och hanterade klienter i stället för att fysiskt flytta data eller resurser. Dessutom går åtkomsten alltid i en riktning, från hanterings klienten till de hanterade klient organisationerna.  Användare och grupper i hanterings klienten bör fortsätta att använda Multi-Factor Authentication när de utför hanterings åtgärder på hanterade klient resurser.

Företag med interna eller externa styrnings-och guardrails kan använda [Azures aktivitets loggar](../../azure-monitor/platform/platform-logs-overview.md) för att uppfylla sina genomskinlighets krav. När företags klienter har upprättat hantera och hanterade klient relationer, kan användare i varje klient övervaka och få insyn i de åtgärder som vidtas av användarna i den andra klienten genom att Visa loggad aktivitet.

## <a name="onboarding-process-considerations"></a>Att tänka på vid onboarding

Prenumerationer (eller resurs grupper inom en prenumeration) kan läggas till i Azure-Lighthouse antingen genom att distribuera Azure Resource Manager mallar eller via hanterade tjänster, som publiceras på Azure Marketplace, antingen privat eller offentligt.

Eftersom företags användare vanligt vis kommer att kunna få direkt åtkomst till företagets klienter, och det inte finns något behov av att marknadsföra eller marknadsföra ett hanterings erbjudande, är det vanligt vis snabbare och mer enkelt att distribuera direkt med Azure Resource Manager mallar. Även om vi refererar till tjänst leverantörer och kunder i [onboarding-vägledningen](../how-to/onboard-customer.md)kan företag använda samma processer.

Om du vill kan klient organisationer inom ett företag registreras genom [att publicera ett erbjudande för hanterade tjänster på Azure Marketplace](../how-to/publish-managed-services-offers.md). För att säkerställa att erbjudandet endast är tillgängligt för lämpliga klienter, se till att dina planer är markerade som privata. Med en privat plan kan du ange prenumerations-ID: n för varje klient som du planerar att publicera, och ingen annan kan få ditt erbjudande.

## <a name="terminology-notes"></a>Terminologi-anteckningar

För hantering av flera innehavare i företaget kan referenser till tjänst leverantörer i Azure Lighthouse-dokumentationen förstås för hantering av klient organisationer i ett företag, det vill säga den klient som innehåller de användare som ska hantera resurser i andra klienter via Azure delegerad resurs hantering. På samma sätt kan referenser till kunder förstås för de klienter som delegerar resurser som ska hanteras via användare i hanterings klienten.

I det exempel som beskrivs ovan kan klient A betraktas som tjänst leverantörens klient organisation (hanterings klienten) och klient B och klient C kan betraktas som kund innehavare.

I det exemplet kan klienter som har rätt behörighet [Visa och hantera delegerade resurser](../how-to/view-manage-customers.md) på sidan **mina kunder** i Azure Portal. På samma sätt kan klient B-och klient organisation C-användare med lämpliga behörigheter [Visa och hantera de resurser som har delegerats](../how-to/view-manage-service-providers.md) till klient organisation A på sidan **tjänst leverantörer** i Azure Portal.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [hanterings upplevelser mellan flera innehavare](cross-tenant-management-experience.md).
- Lär dig mer om [Azure-delegerad resurshantering](azure-delegated-resource-management.md).