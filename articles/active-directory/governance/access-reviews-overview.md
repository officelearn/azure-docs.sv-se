---
title: Vad är åtkomstgranskningar? - Azure Active Directory | Microsoft Docs
description: Med hjälp av Azure Active Directory åtkomst granskningar kan du kontrol lera grupp medlemskap och program åtkomst för att möta styrning, riskhantering och regelefterlevnad i din organisation.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 01/10/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5499c8808c3916842071df1f03a865efd98719f6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262092"
---
# <a name="what-are-azure-ad-access-reviews"></a>Vad är Azure AD-åtkomstgranskningar?

Azure Active Directory (Azure AD) åtkomst granskningar gör det möjligt för organisationer att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användarens åtkomst kan granskas regelbundet för att se till att endast rätt personer har fortsatt åtkomst.

Här är en video som ger en snabb översikt över åtkomst granskningar:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Varför är åtkomst granskningar viktiga?

Med Azure AD kan du samar beta internt i din organisation och med användare från externa organisationer, till exempel partner. Användare kan ansluta till grupper, bjuda in gäster, ansluta till molnappar och arbeta via fjärr anslutning från sina arbets-eller personliga enheter. Bekvämligheten med att dra nytta av självbetjänings kraften har lett till ett behov av bättre åtkomst hanterings funktioner.

- Hur ser du till att de har rätt åtkomst för att vara produktiv när du ansluter till nya anställda?
- Hur kan personer flytta team eller lämna företaget, hur ser du till att deras gamla åtkomst tas bort, särskilt när det handlar om gäster?
- För hög åtkomst behörighet kan leda till gransknings resultat och kompromisser när de visar att det inte finns någon kontroll över åtkomsten.
- Du måste proaktivt kommunicera med resurs ägare för att se till att de regelbundet granskar vem som har åtkomst till sina resurser.

## <a name="when-to-use-access-reviews"></a>När ska jag använda åtkomst granskningar?

- **För många användare i privilegierade roller:** Det är en bra idé att kontrol lera hur många användare som har administrativ åtkomst, hur många av dem som är globala administratörer och om det finns inbjudna gäster eller partners som inte har tagits bort efter att ha tilldelats en administrativ uppgift. Du kan omcertifiera roll tilldelnings användare i [Azure AD-roller](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) som globala administratörer eller [Azure-resurser](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , t. ex. administratör för användar åtkomst i [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) .
- **När Automation är ogenomförbart:** Du kan skapa regler för dynamiskt medlemskap i säkerhets grupper eller Office 365-grupper, men vad händer om data i HR inte finns i Azure AD eller om användarna fortfarande behöver åtkomst efter att gruppen har lämnats för att träna utbyte av dem? Du kan sedan skapa en granskning av den gruppen för att säkerställa att de som fortfarande behöver åtkomst har fortsatt åtkomst.
- **När en grupp används för ett nytt syfte:** Om du har en grupp som ska synkroniseras med Azure AD, eller om du planerar att aktivera programmet Salesforce för alla i försäljnings grupp gruppen, är det praktiskt att be grupp ägaren att granska grupp medlemskapet innan gruppen används i ett annat risk innehåll.
- **Affärs kritisk data åtkomst:** för vissa resurser kan det vara nödvändigt att be personer utanför IT att regelbundet logga ut och ge en motivering för varför de behöver åtkomst till gransknings syfte.
- **För att underhålla undantags listan för en princip:** I en idealisk värld följer alla användare åtkomst principerna för att skydda åtkomsten till organisationens resurser. Ibland finns det dock affärs fall där du behöver göra undantag. Som IT-administratör kan du hantera den här uppgiften, undvika överblick av princip undantag och tillhandahålla granskare med bevis på att dessa undantag granskas regelbundet.
- **Be grupp ägare att bekräfta att de fortfarande behöver gäster i sina grupper:** Åtkomst till anställda kan automatiseras med vissa lokalt IAM, men inte inbjudna gäster. Om en grupp ger gäster åtkomst till företagets känsliga innehåll, är det grupp ägarens ansvar att bekräfta att gästerna fortfarande har till gång till ett legitimt verksamhets behov.
- **Låt granskningar upprepas regelbundet:** Du kan ställa in återkommande åtkomst granskningar av användare i angivna frekvenser, till exempel varje vecka, varje kvartal eller varje kvartal, och granskarna meddelas i början av varje granskning. Granskare kan godkänna eller neka åtkomst med ett vänligt gränssnitt och med hjälp av smarta rekommendationer.

## <a name="where-do-you-create-reviews"></a>Var skapar du granskningar?

Beroende på vad du vill granska kommer du att skapa din åtkomst granskning i Azure AD åtkomst granskningar, Azure AD Enterprise-appar (i för hands versionen) eller Azure AD PIM.

| Åtkomst rättigheter för användare | Granskare kan vara | Granskning skapad i | Granskare upplevelse |
| --- | --- | --- | --- |
| Medlemmar i säkerhets grupp</br>Office-gruppmedlemmar | Angivna granskare</br>Grupp ägare</br>Själv granskning | Azure AD-åtkomstgranskningar</br>Azure AD-grupper | Åtkomstpanel |
| Tilldelad till en ansluten app | Angivna granskare</br>Själv granskning | Azure AD-åtkomstgranskningar</br>Azure AD Enterprise-appar (för hands version) | Åtkomstpanel |
| Azure AD-roll | Angivna granskare</br>Själv granskning | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure Portal |
| Resurs roll för Azure | Angivna granskare</br>Själv granskning | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure Portal |


## <a name="create-access-reviews"></a>Skapa åtkomst granskningar

Följ dessa steg om du vill skapa en åtkomst granskning:

1. Gå till [Azure Portal](https://portal.azure.com) om du vill hantera åtkomst granskningar och logga in som global administratör eller användar administratör.

1. Sök efter och välj **Azure Active Directory**.

      ![Azure Portal Sök efter Azure Active Directory](media/access-reviews-overview/search-azure-active-directory.png)

1. Välj **identitets styrning**.

1. På sidan komma igång klickar du på knappen **skapa en åtkomst granskning** .

   ![Start sida för åtkomst granskningar](./media/access-reviews-overview/access-reviews-overview-create-access-reviews.png) 


## <a name="learn-about-access-reviews"></a>Lär dig mer om åtkomst granskningar

Om du vill veta mer om hur du skapar och utför åtkomst granskningar kan du titta på den här korta demon:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Om du är redo att distribuera åtkomst granskningar i din organisation följer du de här stegen i videon för att publicera, träna dina administratörer och skapa din första åtkomst granskning!

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Licenskrav

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Hur många licenser måste du ha?

Se till att katalogen har minst så många Azure AD Premium P2-licenser som du har anställda som ska utföra följande uppgifter:

- Medlem och gäst användare som tilldelas som granskare
- Medlem och gäst användare som utför en själv granskning
- Grupp ägare som utför en åtkomst granskning
- Program ägare som utför en åtkomst granskning

Azure AD Premium P2-licenser krävs **inte** för följande uppgifter:

- Det krävs inga licenser för användarna med rollerna global administratör eller användar administratör som konfigurerar åtkomst granskningar, konfigurerar inställningar eller tillämpar besluten från granskningarna.

För varje betald Azure AD Premium P2-licens som du tilldelar till en av dina egna organisations användare kan du använda Azure AD Business-to-Business (B2B) för att bjuda in upp till fem gäst användare under tilldelningen av externa användare. Dessa gäst användare kan också använda Azure AD Premium P2-funktioner. Mer information finns i [rikt linjer för Azure AD B2B-samarbets licensiering](../b2b/licensing-guidance.md).

Mer information om licenser finns i [tilldela eller ta bort licenser med hjälp av Azure Active Directory portalen](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Exempel på licens scenarier

Här följer några exempel på licens scenarier som hjälper dig att fastställa antalet licenser som du måste ha.

| Scenario | Beräkning | Antal licenser |
| --- | --- | --- |
| En administratör skapar en åtkomst granskning av grupp A med 75 användare och 1 grupp ägare och tilldelar grupp ägaren som granskaren. | 1 licens för grupp ägaren som granskare | 1 |
| En administratör skapar en åtkomst granskning av grupp B med 500 användare och tre grupp ägare och tilldelar de tre grupp ägarna som granskare. | 3 licenser för varje grupp ägare som granskare | 3 |
| En administratör skapar en åtkomst granskning av grupp B med 500 användare. Gör det till en själv granskning. | 500 licenser för varje användare som självgranskare | 500 |
| En administratör skapar en åtkomst granskning av grupp C med 50 medlems användare och 25 gäst användare. Gör det till en själv granskning. | 50 licenser för varje användare som självgranskare.<br/>(gäst användare omfattas av den obligatoriska 1:5-kvoten) | 50 |
| En administratör skapar en åtkomst granskning av grupp D med 6 medlems användare och 108 gäst användare. Gör det till en själv granskning. | 6 licenser för varje användare som självgranskare och 16 ytterligare licenser för att hantera alla 108 gäst användare i det begärda 1:5-förhållandet. 6 licenser, som avser 6\*5 = 30 gäst användare. För återstående (108-6\*5) = 78 gäst användare, 78/5 = 16 ytterligare licenser krävs. Därför krävs 6 + 16 = 22 licenser. | 22 |

## <a name="next-steps"></a>Nästa steg

- [Skapa en åtkomst granskning av grupper eller program](create-access-review.md)
- [Skapa en åtkomstgranskning av användarna i en administrativ roll i Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Granska åtkomst till grupper eller program](perform-access-review.md)
- [Slutför en åtkomst granskning av grupper eller program](complete-access-review.md)
