---
title: Konfigurera Azure AD Privileged Identity Management | Microsoft Docs
description: Ett avsnitt som förklarar vad Azure AD Privileged Identity Management är och hur du förbättrar säkerheten för molnet med hjälp av PIM.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 03/07/2018
ms.author: curtand
ms.custom: pim
ms.openlocfilehash: b8df65c969a37eb22589c297ecb9b0d0b840a52c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Vad är Azure AD Privileged Identity Management?

Med Azure Active Directory (AD) Privileged Identity Management kan du hantera, kontrollera och övervaka åtkomst inom din organisation. Detta inbegriper tillgång till resurser i Azure AD och Azure-resurser (förhandsversion) och andra Microsoft Online Services som Office 365 eller Microsoft Intune.

> [!NOTE]
> När du aktiverar Privileged Identity Management för din klient en giltig Azure AD Premium P2 eller Enterprise Mobility + Security E5 betald eller utvärderingslicens krävs för varje användare som interagerar med eller tar emot en fördel från tjänsten. Exempel användare och användare > en grupp som är:
>
>- Tilldelats rollen Administratör av Privilegierade roller 
>- Tilldelade som är berättigade till andra directory roller som hanteras via PIM 
>- Möjlighet att godkänna/avvisa förfrågningar i PIM 
>- En Azure-resurs-rollen med precis i tid, eller direkt (klockslag) tilldelningar  
>- Tilldelad till en åtkomst-granskning
>
>Mer information finns i [Azure Active Directory-versioner](active-directory-editions.md).

Organisationer som vill minimera antalet personer som har åtkomst till säkra information eller resurser, eftersom som minskar risken för att en obehörig användare komma att åtkomst eller en behörig användare oavsiktligt påverkar en känsliga resurs.  Dock användare fortfarande behöver utföra Privilegierade åtgärder i Azure AD Azure, Office 365 eller SaaS-appar. Organisationer kan ge användare privilegierad åtkomst till Azure-resurser som prenumerationer och Azure AD. Det finns behov av tillsyn för vad användarna gör med deras administratörsrättigheter. Azure AD Privileged Identity Management hjälper till att minska risken för långa, onödiga eller felanvändning åtkomsträttigheter.

Azure AD Privileged Identity Management kan din organisation:

- Se vilka användare som är tilldelade Privilegierade roller för att hantera Azure-resurser (förhandsgranskning), samt vilka användare tilldelas administrativa roller i Azure AD
- Aktivera på begäran, just-in-time ”administrativ åtkomst till Microsofts onlinetjänster som Office 365 och Intune och Azure-resurser (förhandsgranskning) av prenumerationer och resursgrupper enskilda resurser som virtuella datorer 
-   Visa historik för aktivering av administratör, inklusive vad administratörer ändringar som gjorts i Azure-resurser (förhandsgranskning)
- Få meddelanden om ändringar i administratörstilldelningar
- Kräv godkännande för att aktivera Azure AD privileged administratörsroller (förhandsgranskning) 
- Granska medlemskap i administrativa roller och användaren måste ange en motivering för fortsatt medlemskap

I Azure AD Azure AD Privileged Identity Management kan hantera användare som tilldelats inbyggt organisationens Azure AD-roller, som Global administratör. I Azure, kan Azure AD Privileged Identity Management hantera användare och grupper som har tilldelats via Azure RBAC-roller, inklusive ägare eller deltagare.

## <a name="just-in-time-administrator-access"></a>Precis i tid administratörsåtkomst

Tidigare kan du tilldela en användare till en administratörsroll via Azure portal, andra Microsoft Online Services-portaler eller Azure AD-cmdlets i Windows PowerShell. Därför blir en **permanent admin**alltid är aktiva i den tilldelade rollen. Azure AD Privileged Identity Management introducerar konceptet för en **berättigade admin**. Berättigad administratörer ska användare som behöver privilegierad åtkomst, men inte varar varje dag. Rollen är inaktiv tills användaren behöver åtkomst, och sedan de slutföra aktiveringsprocessen och bli administratör active för en förinställd tidsperiod. Fler och fler organisationer väljer att använda den här metoden för att minska eller eliminera ”stående administratörsåtkomst” Privilegierade roller.

## <a name="enable-privileged-identity-management-for-your-directory"></a>Aktivera Privileged Identity Management för din katalog

Du kan börja använda Azure AD Privileged Identity Management i den [Azure-portalen](https://portal.azure.com/).

> [!NOTE]
> Du måste vara en global administratör med ett organisationskonto (till exempel @yourdomain.com), inte ett Microsoft-konto (till exempel @outlook.com), för att aktivera Azure AD Privileged Identity Management för en katalog.

1. Logga in på [Azure-portalen](https://portal.azure.com/) som global administratör för din katalog.
2. Om din organisation har mer än en katalog väljer du ditt användarnamn längst upp till höger på Azure-portalen. Välj den katalog där du ska använda Azure AD Privileged Identity Management.
3. Välj **Alla tjänster** och använd textrutan Filter för att söka efter **Azure AD Privileged Identity Management**.
4. Markera **Fäst på instrumentpanelen** och klicka sedan på **Skapa**. Privileged Identity Management-programmet öppnas.

Om du är den första personen som använder Azure AD Privileged Identity Management i din katalog och du navigerar till Azure AD directory roller och du navigerar till Azure AD directory roller, en [säkerhetsguiden](active-directory-privileged-identity-management-security-wizard.md) vägleder dig genom första tilldelningsupplevelse. Därefter blir du automatiskt först **säkerhetsadministratör** och **administratör av Privilegierade roller** av katalogen.

Bara en användare som har rollen Administratör av Privilegierade roller kan hantera tilldelningar för andra administratörer i Azure AD PIM för Azure AD-roller. Du kan [ge andra användare möjlighet att hantera directory roller i PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md). Globala administratörer, säkerhetsadministratörer och säkerhet läsare kan visa tilldelningar till Azure AD-roller i Azure AD PIM.
Endast en prenumeration administratör, en resursägare eller administratör för användaråtkomst en resurs kan hantera tilldelningar för andra administratörer i Azure AD PIM för Azure RBAC-roller.  Användare som är den privilegierade rollen administratörer, säkerhetsadministratörer eller säkerhet läsare har som standard inte behörighet att visa tilldelningar Azure RBAC-roller i Azure AD PIM.

## <a name="privileged-identity-management-overview-entry-point"></a>Översikt över Privileged Identity Management (startpunkt)

Azure AD Privileged Identity Management stöder administration av Azure AD directory roller och roller för Azure-resurser (förhandsversion). Funktionen för roller för Azure-resurser skiljer sig från administrativa roller i Azure AD. Azure-resursroller ger detaljerade behörigheter för resursen som de är tilldelade och alla underordnade resurser i hierarkin resurs (kallas även arv). [Lär dig mer om hur RBAC, resurs hierarkin och arv](../role-based-access-control/role-assignments-portal.md). PIM för både Azure AD directory roller och resurser i Azure (förhandsversion) kan du administrera genom att öppna länken under avsnittet Hantera för PIM översikt post punkt vänstra navigeringsmenyn.

PIM ger åtkomst till Aktivera roller, visa väntande aktiveringar/begäranden väntande godkännanden (för Azure AD directory roller) och granskar väntar på ditt svar från avsnittet uppgifter i den vänstra navigeringsmenyn.

Vid åtkomst till alla menyalternativ uppgifter från översikt startpunkt innehåller resulterande vyn resultat för både roller för Azure AD-katalog och Azure-resurshanteraren roller (förhandsversion).

![Snabbstart](./media/active-directory-privileged-identity-management-configure/quick-start.png)

Min roller innehåller en lista över aktiva och berättigade rolltilldelningar för Azure AD directory roller och Azure-resurshanteraren roller (förhandsversion). [Mer information om hur du aktiverar berättigade rolltilldelningar](active-directory-privileged-identity-management-how-to-activate-role.md).

Aktivera roller för Azure-resurser (förhandsgranskning) introducerar en ny upplevelse som gör att medlemmar i en roll schemalägga aktivering för framtida datum/tid och väljer en specifik aktivering varaktighet inom det högsta tillåtna av administratörer.

![](./media/active-directory-privileged-identity-management-configure/activations.png)

Om en schemalagd aktivering krävs inte längre, kan användare avbryta väntande begäran genom att gå till väntande begäranden från den vänstra navigeringsmenyn och klicka på Avbryt-knappen i-raden med begäran.

![Väntande begäranden](./media/active-directory-privileged-identity-management-configure/pending-requests.png)

## <a name="privileged-identity-management-admin-dashboard"></a>Privileged Identity Management admin-instrumentpanelen

Azure AD Privileged Identity Manager innehåller en admin-instrumentpanel som innehåller viktig information såsom:

* Aviseringar som påpekas möjligheter att öka säkerheten
* Antalet användare som har tilldelats varje privilegierad roll  
* Antalet berättigade och permanenta administratörer
* Ett diagram över aktiveringar av Privilegierade roller i din katalog
*   Antalet Just-In-Time Tidsbundna och permanenta tilldelningar för Azure-resurshanteraren roller (förhandsgranskning)
*   Användare och grupper med nya rolltilldelningar under de senaste 30 dagarna (Azure-resurshanteraren roller)


![PIM instrumentpanelen – skärmbild][2]

## <a name="privileged-role-management"></a>Hantering av Privilegierade roller

Du kan hantera administratörer genom att lägga till eller ta bort permanent eller berättigade administratörer för varje roll för Azure AD directory roller med Azure AD Privileged Identity Management. Med PIM resurser för Azure (förhandsversion), ägare och administratörer för användaren globala administratörer som möjliggör hantering av prenumerationer i deras klient kan tilldela användare eller grupper till Azure-resursroller som kvalificerade (Just-In-Time-åtkomst), eller Tidsbundna ( -aktivering krävs inte) åtkomst med en start- och Slutdatum /-tid, eller permanent (om aktiverats i rollinställningarna för).

![PIM Lägg till/ta bort systemadministratörer – skärmbild][3]

## <a name="configure-the-role-activation-settings"></a>Konfigurera inställningar för roll-aktivering

Med hjälp av den [rollinställningar](active-directory-privileged-identity-management-how-to-change-default-settings.md) du kan konfigurera berättigade rollegenskaper aktivering för Azure AD directory roller, inklusive:

* Varaktighet för aktiveringsperioden roll
* Rollaktiveringsmeddelande
* Informationen om en användare måste ange under aktiveringen roll
* Biljett eller incident automatiskt
* [Krav för godkännande arbetsflöde - förhandsgranskning](./privileged-identity-management/azure-ad-pim-approval-workflow.md)

![PIM inställningar - aktivering av administratör – skärmbild][4]

Observera att i bild knapparna för **Multifaktorautentisering** har inaktiverats. För vissa, mycket Privilegierade roller, vi kräver MFA för förhöjd skydd.

Inställningar för Azure-resurshanteraren roller (förhandsgranskning) låter administratörer konfigurera Just-In-Time- och direkttilldelning inställningar, inklusive:

- Möjligheten att tilldela roller utan ett slutdatum /-tid (permanent tilldelning) användare eller grupper
- Standardlängden för en tilldelning (när det är inte permanent)
- Maximal aktivering varaktigheten (när en berättigad Rollmedlem aktiverar)
- Informationen om en användare måste ange under aktiveringen roll (Just-In-Time tilldelningar) eller tilldelningen (direct tilldelningar)

![](./media/active-directory-privileged-identity-management-configure/role-settings-details.png)

## <a name="role-activation"></a>Rollaktivering

Att [aktivera en roll](active-directory-privileged-identity-management-how-to-activate-role.md), administratör berättigade begär en Tidsbundna ”aktivering” för rollen. Aktiveringen kan begäras med hjälp av den **aktivera min roll** alternativ i Azure AD Privileged Identity Management.

En administratör som vill aktivera en roll måste initiera Azure AD Privileged Identity Management i Azure-portalen.

Rollaktivering kan anpassas. I PIM-inställningar kan du ange längden på aktivering och vad information som administratören behöver för att aktivera rollen.

![PIM administratör begäran rollaktivering – skärmbild][5]

## <a name="review-role-activity"></a>Granska rollen aktiviteten

Det finns två sätt att spåra hur dina anställda och administratörer använder Privilegierade roller. Med hjälp av alternativet först [Directory roller granskningshistorik](active-directory-privileged-identity-management-how-to-use-audit-log.md). Granskningshistoriken loggar spåra ändringar i Privilegierade rolltilldelningar rollen aktivering historik och och ändringar av inställningarna för Azure-resurshanteraren roller (förhandsversion). 

![Historik för aktivering av PIM - skärmbild][6]

Det andra alternativet är att konfigurera vanliga [åtkomst till granskningar](active-directory-privileged-identity-management-how-to-start-security-review.md). Rapporterna åtkomst kan utföras av och tilldelade granskare (till exempel ett team manager) eller anställda kan granska sig själva. Detta är det bästa sättet att övervaka som kräver fortfarande åtkomst och som inte längre finns.

## <a name="azure-ad-pim-at-subscription-expiration"></a>Azure AD PIM på prenumerationen upphör att gälla

En klient måste ha en Azure AD Premium P2 (eller EMS E5) utvärderingsversion eller betald prenumeration i klientorganisationen innan du använder Azure AD PIM.  Dessutom måste licenser tilldelas till administratörer för innehavaren.  Mer specifikt licenser måste vara tilldelade till administratörer i Azure AD-roller hanteras via Azure AD PIM administratörer i Azure RBAC-roller som hanteras via Azure AD PIM och vanliga användare som utför åtkomst granskningar.
Om din organisation inte förnya Azure AD Premium P2 eller din utvärderingsversion upphör att gälla, Azure AD PIM-funktioner kommer inte längre vara tillgängliga i din klientorganisation, berättigade rolltilldelningar tas bort och användare kommer inte längre att kunna aktivera roller. Du kan läsa mer i den [krav för Azure AD PIM-prenumeration](./privileged-identity-management/subscription-requirements.md)

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Admin_Overview.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_Settings_w_Approval_Disabled.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png
