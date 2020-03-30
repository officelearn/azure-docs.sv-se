---
title: Vad är åtkomstgranskningar? - Azure Active Directory | Microsoft Docs
description: Med hjälp av Azure Active Directory-åtkomstgranskningar kan du styra gruppmedlemskap och programåtkomst för att uppfylla styrnings-, riskhanterings- och efterlevnadsinitiativ i din organisation.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262092"
---
# <a name="what-are-azure-ad-access-reviews"></a>Vad är Azure AD-åtkomstgranskningar?

Azure Active Directory (Azure AD) åtkomstgranskningar gör det möjligt för organisationer att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användarens åtkomst kan granskas regelbundet för att se till att endast rätt personer har fortsatt åtkomst.

Här är en video som ger en snabb översikt över åtkomstrecensioner:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Varför är åtkomstgranskningar viktiga?

Med Azure AD kan du samarbeta internt inom organisationen och med användare från externa organisationer, till exempel partner. Användare kan gå med i grupper, bjuda in gäster, ansluta till molnappar och arbeta på distans från sina arbets- eller personliga enheter. Bekvämligheten med att utnyttja kraften i självbetjäning har lett till ett behov av bättre åtkomsthanteringsfunktioner.

- När nya medarbetare ansluter sig, hur ser du till att de har rätt tillgång för att vara produktiva?
- När människor flyttar team eller lämnar företaget, hur ser du till att deras gamla åtkomst tas bort, särskilt när det gäller gäster?
- Överdrivna åtkomsträttigheter kan leda till granskningsresultat och kompromisser eftersom de tyder på bristande kontroll över åtkomsten.
- Du måste proaktivt samarbeta med resursägare för att säkerställa att de regelbundet granskar vem som har tillgång till sina resurser.

## <a name="when-to-use-access-reviews"></a>När ska du använda åtkomstgranskningar?

- **För många användare i privilegierade roller:** Det är en bra idé att kontrollera hur många användare som har administrativ åtkomst, hur många av dem som är globala administratörer och om det finns några inbjudna gäster eller partner som inte har tagits bort efter att ha tilldelats för att utföra en administrativ uppgift. Du kan certifiera om rolltilldelningsanvändare i [Azure AD-roller,](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) till exempel globala administratörer eller [Azure-resursroller](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) som User Access Administrator i [AZURE AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) .
- **När automatisering är omöjligt:** Du kan skapa regler för dynamiskt medlemskap i säkerhetsgrupper eller Office 365-grupper, men vad händer om HR-data inte finns i Azure AD eller om användarna fortfarande behöver åtkomst efter att ha lämnat gruppen för att träna sin ersättning? Du kan sedan skapa en granskning av den gruppen för att säkerställa att de som fortfarande behöver åtkomst ska ha fortsatt åtkomst.
- **När en grupp används för ett nytt ändamål:** Om du har en grupp som ska synkroniseras till Azure AD, eller om du planerar att aktivera programmet Salesforce för alla i gruppen Säljteam, är det lämpligt att be gruppägaren att granska gruppmedlemskapet innan gruppen används i ett annat riskinnehåll.
- **Affärskritisk dataåtkomst:** för vissa resurser kan det krävas att be personer utanför IT att regelbundet logga ut och motivera varför de behöver åtkomst för granskning.
- **Så här upprätthåller du en princips undantagslista:** I en idealisk värld följer alla användare åtkomstprinciperna för att skydda åtkomsten till organisationens resurser. Men ibland finns det affärsfall som kräver att du gör undantag. Som IT-administratör kan du hantera den här uppgiften, undvika övervakning av principundantag och ge revisorerna bevis på att dessa undantag granskas regelbundet.
- **Be gruppägare bekräfta att de fortfarande behöver gäster i sina grupper:** Åtkomst till anställda kan automatiseras med vissa lokala IAM, men inte inbjudna gäster. Om en grupp ger gästerna tillgång till affärskänsligt innehåll är det gruppägarens ansvar att bekräfta att gästerna fortfarande har ett legitimt affärsbehov för åtkomst.
- **Låt recensioner återkomma regelbundet:** Du kan ställa in återkommande åtkomstgranskningar av användare vid inställda frekvenser, till exempel veckovis, månadsvis, kvartalsvis eller årligen, och granskarna meddelas i början av varje granskning. Granskare kan godkänna eller neka åtkomst med ett användarvänligt gränssnitt och med hjälp av smarta rekommendationer.

## <a name="where-do-you-create-reviews"></a>Var skapar du recensioner?

Beroende på vad du vill granska skapar du din åtkomstgranskning i Azure AD-åtkomstgranskningar, Azure AD-företagsappar (i förhandsversion) eller Azure AD PIM.

| Användarnas åtkomsträttigheter | Granskare kan | Recension som skapats i | Erfarenhet av granskare |
| --- | --- | --- | --- |
| Medlemmar i säkerhetsgruppen</br>Medlemmar i Office-gruppen | Angivna granskare</br>Gruppägare</br>Självuppsikt | Azure AD-åtkomstgranskningar</br>Azure AD-grupper | Åtkomstpanel |
| Tilldelad till en ansluten app | Angivna granskare</br>Självuppsikt | Azure AD-åtkomstgranskningar</br>Azure AD-företagsappar (i förhandsversion) | Åtkomstpanel |
| Azure AD-roll | Angivna granskare</br>Självuppsikt | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure Portal |
| Azure-resursroll | Angivna granskare</br>Självuppsikt | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure Portal |


## <a name="create-access-reviews"></a>Skapa åtkomstgranskningar

Så här skapar du en åtkomstgranskning:

1. Gå till [Azure-portalen](https://portal.azure.com) för att hantera åtkomstgranskningar och logga in som global administratör eller användaradministratör.

1. Sök efter och välj **Azure Active Directory**.

      ![Azure portal sökning efter Azure Active Directory](media/access-reviews-overview/search-azure-active-directory.png)

1. Välj **Identitetsstyrning**.

1. Klicka på knappen Skapa **en åtkomstgranskning** på sidan Komma igång.

   ![Startsida för Access-granskningar](./media/access-reviews-overview/access-reviews-overview-create-access-reviews.png) 


## <a name="learn-about-access-reviews"></a>Läs mer om åtkomstgranskningar

Om du vill veta mer om hur du skapar och utför åtkomstrecensioner kan du titta på den här korta demon:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Om du är redo att distribuera åtkomstgranskningar i din organisation följer du dessa steg i videon till ombord, tränar administratörer och skapar din första åtkomstgranskning!

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Licenskrav

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Hur många licenser måste du ha?

Kontrollera att katalogen har minst lika många Azure AD Premium P2-licenser som du har anställda som utför följande uppgifter:

- Medlems- och gästanvändare som tilldelas som granskare
- Medlems- och gästanvändare som gör en självgranskning
- Gruppägare som utför en åtkomstgranskning
- Programägare som utför en åtkomstgranskning

Azure AD Premium P2-licenser krävs **inte** för följande uppgifter:

- Inga licenser krävs för användare med de globala administratörs- eller användaradministratörsrollerna som konfigurerar åtkomstgranskningar, konfigurerar inställningar eller tillämpar besluten från granskningarna.

För varje betald Azure AD Premium P2-licens som du tilldelar till en av din egen organisations användare kan du använda Azure AD business-to-business (B2B) för att bjuda in upp till fem gästanvändare under den externa användartillägget. Dessa gästanvändare kan också använda Azure AD Premium P2-funktioner. Mer information finns i [Azure AD B2B-licensieringsvägledning](../b2b/licensing-guidance.md)för samarbete .

Mer information om licenser finns i [Tilldela eller ta bort licenser med Azure Active Directory-portalen](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Exempel på licensscenarier

Här är några exempel på licensscenarier som hjälper dig att avgöra hur många licenser du måste ha.

| Scenario | Beräkning | Antal licenser |
| --- | --- | --- |
| En administratör skapar en åtkomstgranskning av grupp A med 75 användare och en gruppägare och tilldelar gruppägaren som granskare. | 1 licens för gruppägaren som granskare | 1 |
| En administratör skapar en åtkomstgranskning av grupp B med 500 användare och 3 gruppägare och tilldelar de tre gruppägarna som granskare. | 3 licenser för varje gruppägare som granskare | 3 |
| En administratör skapar en åtkomstgranskning av grupp B med 500 användare. Gör det till en självgranskning. | 500 licenser för varje användare som självgranskare | 500 |
| En administratör skapar en åtkomstgranskning av grupp C med 50 medlemsanvändare och 25 gästanvändare. Gör det till en självgranskning. | 50 licenser för varje användare som självgranskare.<br/>(gästanvändare omfattas av det erforderliga förhållandet 1:5) | 50 |
| En administratör skapar en åtkomstgranskning av grupp D med 6 medlemsanvändare och 108 gästanvändare. Gör det till en självgranskning. | 6 licenser för varje användare som självgranskare + 16 ytterligare licenser för att täcka alla 108 gästanvändare i det erforderliga förhållandet 1:5. 6 licenser, som täcker\*6 5 = 30 gästanvändare. För de återstående (108-6\*5)=78 gästanvändare krävs ytterligare 78/5=16 licenser. Således totalt krävs 6+16=22 licenser. | 22 |

## <a name="next-steps"></a>Nästa steg

- [Skapa en åtkomstgranskning av grupper eller program](create-access-review.md)
- [Skapa en åtkomstgranskning av användarna i en administrativ roll i Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Granska åtkomsten till grupper eller program](perform-access-review.md)
- [Slutföra en åtkomstgranskning av grupper eller program](complete-access-review.md)
