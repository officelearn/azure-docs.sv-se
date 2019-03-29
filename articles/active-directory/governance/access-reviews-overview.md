---
title: Vad är åtkomstgranskningar? - Azure Active Directory | Microsoft Docs
description: Med Azure Active Directory-åtkomstgranskningar kan kontrollera du gruppmedlemskap och programåtkomst åtkomst för att uppfylla styrning, riskhantering och efterlevnadsinitiativ i din organisation.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 01/18/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1563a023f397999deb5c6abd40843d6a376b0492
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576130"
---
# <a name="what-are-azure-ad-access-reviews"></a>Vad är Azure AD-åtkomst granskar?

Azure Active Directory (Azure AD)-åtkomstgranskningar kan organisationer effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användarens åtkomst kan granskas regelbundet att kontrollera att rätt personer ha fortsatt åtkomst.

Här är en video som ger en snabb översikt över åtkomstgranskningar:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Varför är åtkomst granskar viktigt?

Azure AD kan du samarbeta internt inom organisationen och med användare från externa organisationer som partners. Användare kan ansluta till grupper, bjuda in gäster, ansluta till molnbaserade appar och arbeta fjärranslutet från sina arbets- eller personliga enheter. Praktiskt utnyttjar Self Service har lett till ett behov av bättre hanteringsfunktioner för åtkomst.

- När nya nyanställd person, hur undviker du att de har rätt behörighet att vara produktiva?
- När personer flytta team eller lämnar företaget, hur undviker du att åtkomsten gamla tas bort, särskilt när det innebär att gäster?
- Långa åtkomsträttigheter kan leda till att granska resultaten och kompromisser eftersom de tyda på brist på kontroll över åtkomst.
- Du måste proaktivt interagera med resursägare att se till att de regelbundet granska vem som har åtkomst till sina resurser.

## <a name="when-to-use-access-reviews"></a>När du ska använda åtkomst går igenom?

- **För många användare i Privilegierade roller:** Det är en bra idé att kontrollera hur många användare har administrativ åtkomst, hur många av dem är globala administratörer och om det finns några bjuds in gäster eller partners som inte har tagits bort som har tilldelats att göra en administrativ åtgärd. Du kan certifiera om rollen tilldelning av användare i [Azure AD-roller](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , till exempel globala administratörer eller [Azure-resursroller](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) som administratör för användaråtkomst i den [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) upplevelse.
- **När automation är omöjligt:** Du kan skapa regler för dynamiskt medlemskap för säkerhetsgrupper eller Office 365-grupper, men vad händer om HR data är inte i Azure AD eller om användare fortfarande behöver åtkomst efter att ha lämnat gruppen för att träna sina ersättning? Du kan sedan skapa en granskning på den gruppen för att se till att de som fortfarande behöver åtkomst bör ha fortsatt åtkomst.
- **När en grupp används för ett nytt syfte:** Om du har en grupp som ska synkroniseras till Azure AD, eller om du planerar att aktivera programmet Salesforce för alla i teamet försäljningsgruppen, skulle det vara praktiskt att be gruppägare att granska gruppmedlemskap före gruppen som används i en annan risk co chning.
- **Företag kritiska dataåtkomst:** för vissa resurser, kan det krävas att ställa personer utanför IT-avdelningen att regelbundet logga ut och ge en motivering på varför de behöver åtkomst i granskningssyfte.
- **Att underhålla en princip undantagslistan:** I en perfekt värld följer alla användare åtkomst principer för säker åtkomst till organisationens resurser. Men finns ibland företagsfall som uppmanar dig att göra undantag. Som IT-administratör kan du hantera den här uppgiften, undvika översyn av rutinundantag och ge granskare bevis att sådana undantag granskas regelbundet.
- **Be gruppägare att bekräfta att de fortfarande behöver gäster i deras grupper:** Medarbetarnas åtkomst kan automatiseras med några lokala IAM, men inte de inbjudna. Om en grupp får gäster företag känsligt innehåll och sedan dess gruppägarens ansvar att bekräfta gästerna fortfarande har en legitima företagsbehov för åtkomst.
- **Har granskningar som utför regelbundet:** Du kan ställa in återkommande åtkomstgranskningar av användare i set-frekvenser som varje vecka, månadsvis, Kvartalsvis eller årligen och granskarna kommer att meddelas i början av varje granskning. Granskare kan godkänna eller neka åtkomst med ett eget gränssnitt och med hjälp av smarta rekommendationer.

## <a name="where-do-you-create-reviews"></a>Där skapas granskningar?

Beroende på vad du vill granska, skapar du din åtkomstgranskning i Azure AD åtkomst till granskningar, Azure AD-företagsappar (i förhandsversion) eller Azure AD PIM.

| Behörighet för åtkomst av användare | Granskare kan vara | Granska som skapats i | Granskare-upplevelse |
| --- | --- | --- | --- |
| Medlemmar i gruppen</br>Office-gruppmedlemmar | Angivna granskare</br>Gruppägare</br>Själv granska | Azure AD-åtkomstgranskningar</br>Azure AD-grupper | Åtkomstpanel |
| Tilldelad till en ansluten app | Angivna granskare</br>Själv granska | Azure AD-åtkomstgranskningar</br>Azure AD-företagsappar (i förhandsversion) | Åtkomstpanel |
| Azure AD-roll | Angivna granskare</br>Själv granska | Azure AD PIM | Azure Portal |
| Azure-resurs-roll | Angivna granskare</br>Själv granska | Azure AD PIM | Azure Portal |

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda åtkomstgranskningar, måste du ha en av följande licenser:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5 license

Mer information finns i [Gör så här: Registrera dig för Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) eller [Enterprise Mobility + Security E5-utvärderingsversion](https://aka.ms/emse5trial).

## <a name="get-started-with-access-reviews"></a>Kom igång med åtkomstgranskningar

Titta på den här kort demonstration om du vill veta mer om att skapa och utföra åtkomstgranskningar:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Om du är redo att distribuera åtkomstgranskningar i din organisation kan följa stegen i videon att publicera, träna dina administratörer och skapa din första åtkomstgranskning!

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="enable-access-reviews"></a>Aktivera att åtkomstgranskningar

Följ dessa steg om du vill aktivera att åtkomstgranskningar.

1. Som Global administratör eller Användaradministratör kan logga in på den [Azure-portalen](https://portal.azure.com) där du vill använda åtkomst går igenom.

1. Klicka på **alla tjänster** och hitta åtkomstgranskningar service.

1. Klicka på **Åtkomstgranskningar**.

    ![Alla tjänster - åtkomstgranskningar](./media/access-reviews-overview/all-services-access-reviews.png)

1. I listan över navigeringsfönstret klickar du på **publicera** att öppna den **publicera åtkomstgranskningar** sidan.

    ![Publicera åtkomstgranskningar](./media/access-reviews-overview/onboard-button.png)

1. Klicka på **skapa** att aktivera åtkomst går du igenom i den aktuella katalogen.

    ![Publicera åtkomstgranskningar](./media/access-reviews-overview/onboard-access-reviews.png)

    Nästa gång du startar access granskar, granskningsalternativ åtkomst kommer att aktiveras.

    ![Åtkomstgranskningar aktiverat](./media/access-reviews-overview/access-reviews-enabled.png)

## <a name="next-steps"></a>Nästa steg

- [Skapa en åtkomstgranskning av grupper eller program](create-access-review.md)
- [Skapa en åtkomstgranskning av användarna i en administrativ roll i Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Granska åtkomst till grupper eller program](perform-access-review.md)
- [Slutför en åtkomstgranskning av grupper eller program](complete-access-review.md)
