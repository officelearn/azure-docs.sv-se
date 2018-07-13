---
title: Konfigurera Azure AD Privileged Identity Management | Microsoft Docs
description: En artikel som förklarar vad Azure AD Privileged Identity Management är och hur du använder PIM för att förbättra molnsäkerheten.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: protection
ms.topic: overview
ms.date: 03/07/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 927d2ccef6a250d489273a17b070a4a0aa820bee
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37767587"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Vad är Azure AD Privileged Identity Management?

Med Azure Active Directory (AD) Privileged Identity Management kan du hantera, kontrollera och övervaka åtkomst inom din organisation. Detta inkluderar åtkomst till resurser i Azure AD, Azure Resources (förhandsversion) och andra Microsoft Online-tjänster som Office 365 eller Microsoft Intune.

> [!NOTE]
> När du aktiverar Privileged Identity Management för din klientorganisation krävs en giltig Azure AD Premium P2- eller Enterprise Mobility + Security E5-licens som är betald eller för utvärdering för varje användare som interagerar med eller får en förmån från tjänsten. Exempel innefattar användare/användare i en grupp som:
>
>- Tilldelas till rollen Privilegierad rolladministratör 
>- Tilldelas som berättigade till andra katalogroller som hanteras via PIM 
>- Kan godkänna/neka begäranden i PIM 
>- Tilldelas till en Azure-resursroll med Just-in-time- eller direkttilldelningar (tidsbaserade)  
>- Tilldelade till en åtkomstgranskning
>
>Mer information finns i [Azure Active Directory-versioner](fundamentals/active-directory-whatis.md).

Organisationer vill minimera det antal personer som har åtkomst till säker information eller säkra resurser, eftersom det minskar risken att en obehörig användare får den åtkomsten eller att en auktoriserad användare oavsiktligt påverkar en känslig resurs.  Användare behöver dock fortfarande utföra privilegierade åtgärder i Azure AD, Azure, Office 365 eller SaaS-appar. Organisationer kan ge användare privilegierad åtkomst till Azure-resurser som prenumerationer och Azure AD. Det finns ett behov av tillsyn för vad användarna gör med sina administratörsprivilegier. Azure AD Privileged Identity Management hjälper till att minska risken för överdrivna, onödiga eller missbrukade åtkomsträttigheter.

Azure AD Privileged Identity Management hjälper din organisation att:

- Se vilka användare som är tilldelade privilegierade roller för att hantera Azure-resurser (förhandsversion) samt vilka användare som tilldelas administrativa roller i Azure AD
- Administrativ åtkomst som är enable-on-demand och just-in-time till Microsoft Online-tjänster som Office 365 och Intune, och till Azure-resurser (förhandsversion) för prenumerationer, resursgrupper och enskilda resurser, till exempel virtuella datorer 
-   Se historik över administratörsaktivering, inklusive vilka ändringar administratörer har gjort i Azure-resurser (förhandsversion)
- Få meddelanden om ändringar i administratörstilldelningar
- Kräv godkännande för att aktivera privilegierade administratörsroller i Azure AD (förhandsversion) 
- Granska medlemskap i administrativa roller och kräv att användare anger en motivering för fortsatt medlemskap

I Azure AD kan Azure AD Privileged Identity Management hantera de användare som är tilldelade till inbyggda Azure AD-organisationsroller såsom Global administratör. I Azure kan Azure AD Privileged Identity Management hantera de användare och grupper som är tilldelade via Azure RBAC-roller, inklusive Ägare och Deltagare.

## <a name="just-in-time-administrator-access"></a>Just-in-time-administratörsåtkomst

Förut gick det att tilldela en användare till en administratörsroll via Azure-portalen, andra Microsoft Online Services-portaler eller Azure AD-cmdletar i Windows PowerShell. Därför blir den användaren en **permanent administratör** som alltid är aktiv i den tilldelade rollen. Azure AD Privileged Identity Management introducerar begreppet **berättigad administratör**. Berättigade administratörer bör vara användare som behöver privilegierad åtkomst då och då men inte på heltid. Rollen är inaktiv tills användaren behöver åtkomst. Därefter slutför användaren en aktiveringsprocess och blir aktiv administratör under en förinställd tidsperiod. Allt fler organisationer väljer att använda den här metoden för att minska eller eliminera ”stående administratörsåtkomst” till privilegierade roller.

## <a name="enable-privileged-identity-management-for-your-directory"></a>Aktivera Privileged Identity Management för din katalog

Du kan börja använda Azure AD Privileged Identity Management i [Azure-portalen](https://portal.azure.com/).

> [!NOTE]
> Du måste vara en global administratör med ett organisationskonto (till exempel @yourdomain.com), inte ett Microsoft-konto (till exempel @outlook.com), för att aktivera Azure AD Privileged Identity Management för en katalog.

1. Logga in på [Azure-portalen](https://portal.azure.com/) som global administratör för din katalog.
2. Om din organisation har mer än en katalog väljer du ditt användarnamn längst upp till höger på Azure-portalen. Välj den katalog där du kommer att använda Azure AD Privileged Identity Management.
3. Välj **Alla tjänster** och använd textrutan Filter för att söka efter **Azure AD Privileged Identity Management**.
4. Markera **Fäst på instrumentpanelen** och klicka sedan på **Skapa**. Privileged Identity Management-programmet öppnas.

Om du är den första som använder Azure AD Privileged Identity Management i din katalog och du navigerar till Azure AD-katalogroller kommer en [säkerhetsguide](active-directory-privileged-identity-management-security-wizard.md) att vägleda dig genom den första tilldelningen. Därefter blir du automatiskt katalogens första **säkerhetsadministratör** och **privilegierade rolladministratör**.

För Azure AD-roller kan endast en användare som har rollen Privilegierad rolladministratör hantera tilldelningar för andra administratörer i Azure AD PIM. Du kan [ge andra användare möjligheten att hantera katalogroller i PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md). Globala administratörer, säkerhetsadministratörer och säkerhetsläsare kan visa tilldelningar till Azure AD-roller i Azure AD PIM.
För Azure RBAC-roller kan endast en prenumerationsadministratör, en resursägare eller en administratör för resursanvändaråtkomst hantera tilldelningar för andra administratörer i Azure AD PIM.  Användare som är privilegierade rolladministratörer, säkerhetsadministratörer eller säkerhetsläsare har som standard inte åtkomst att visa tilldelningar för Azure RBAC-roller i Azure AD PIM.

## <a name="privileged-identity-management-overview-entry-point"></a>Privileged Identity Management – översikt (startpunkt)

Azure AD Privileged Identity Management har stöd för administration av Azure AD-katalogroller och roller för Azure-resurser (förhandsversion). Funktionen för roller för Azure-resurser skiljer sig från administrativa roller i Azure AD. Azure-resursroller ger detaljerade behörigheter för den resurs där de är tilldelade samt alla underordnade resurser i resurshierarkin (detta kallas arv). [Lär dig mer om RBAC, resurshierarkin och arv](../role-based-access-control/role-assignments-portal.md). PIM för både Azure AD-katalogroller och Azure-resurser (förhandsversion) kan administreras via tillämplig länk under avsnittet Hantera i navigeringsmenyn i PIM-översiktens startpunkt.

PIM ger smidig åtkomst till att aktivera roller, visa väntande aktiveringar/begäranden, väntande godkännanden (för Azure AD-katalogroller) samt granskningar som väntar på ditt svar i avsnittet Uppgifter i den vänstra navigeringsmenyn.

Vid åtkomst till något av menyalternativen i Uppgifter från översiktens startpunkt innehåller den resulterande vyn resultat för både Azure AD-katalogroller och Azure-resursroller (förhandsversion).

![Snabbstart](./media/active-directory-privileged-identity-management-configure/quick-start.png)

Mina roller innehåller en lista över aktiva och berättigade rolltilldelningar för Azure AD-katalogroller och Azure-resursroller (förhandsversion). [Lär dig mer om att aktivera berättigade rolltilldelningar](active-directory-privileged-identity-management-how-to-activate-role.md).

Aktivering av roller för Azure-resurser (förhandsversion) introducerar en ny upplevelse som gör att berättigade medlemmar i en roll kan schemalägga aktivering till ett framtida datum eller tid och välja en specifik aktiveringsvaraktighet inom den maxgräns som tillåts av administratörer.

![](./media/active-directory-privileged-identity-management-configure/activations.png)

Om en schemalagd aktivering inte längre behövs kan användare avbryta sin väntande begäran genom att gå till väntande begäranden på den vänstra navigeringsmenyn och klicka på knappen Avbryt på samma rad som begäran.

![Väntande begäranden](./media/active-directory-privileged-identity-management-configure/pending-requests.png)

## <a name="privileged-identity-management-admin-dashboard"></a>Privileged Identity Management – administratörsinstrumentpanel

Azure AD Privileged Identity Manager innehåller en administratörsinstrumentpanel som visar viktig information som:

* Aviseringar som påpekar möjligheter att förbättra säkerheten
* Det antal användare som har tilldelats varje privilegierad roll  
* Antalet berättigade och permanenta administratörer
* Ett diagram över aktiveringar av privilegierad roll i din katalog
*   Antalet tilldelningar som är just-in-time, tidsbegränsade och permanenta för Azure-resursroller (förhandsversion)
*   Användare och grupper med nya rolltilldelningar under de senaste 30 dagarna (Azure-resursroller)


![PIM-instrumentpanel – skärmbild][2]

## <a name="privileged-role-management"></a>Hantering av privilegierad roll

Med Azure AD Privileged Identity Management kan du hantera administratörerna genom att lägga till eller ta bort permanenta eller berättigade administratörer till varje roll för Azure AD-katalogroller. Med PIM för Azure-resurser (förhandsversion) kan ägare, administratörer för användaråtkomst och globala administratörer som aktiverar hantering av prenumerationer i deras klientorganisationer tilldela användare eller grupper till Azure-resursroller som berättigad (just-in-time-åtkomst), eller tidsbunden (kräver ingen aktivering) åtkomst med ett start- och slutdatum/tid, eller permanent (om det är aktiverat i rollinställningarna).

![PIM: lägg till/ta bort administratörer – skärmbild][3]

## <a name="configure-the-role-activation-settings"></a>Konfigurera inställningar för rollaktivering

Med hjälp av [rollinställningarna](active-directory-privileged-identity-management-how-to-change-default-settings.md) kan du konfigurera egenskaperna för berättigad rollaktivering för Azure AD-katalogroller, inklusive:

* Varaktigheten för rollaktiveringsperioden
* Rollaktiveringsmeddelandet
* Den information som en användare måste ange under rollaktiveringsprocessen
* Tjänstbiljett eller incidentnummer
* [Krav för arbetsflöde för godkännande – förhandsversion](./privileged-identity-management/azure-ad-pim-approval-workflow.md)

![PIM-inställningar – administratörsaktivering – skärmbild][4]

Observera att knapparna för **multifaktorautentisering** i bilden är inaktiverade. För vissa mycket privilegierade roller kräver vi MFA för höjt skydd.

Ned rollinställningar för Azure-resursroller (förhandsversion) kan administratörer konfigurera inställningar för just-in-time- och direkttilldelning inklusive:

- Möjligheten att tilldela användare eller grupper till roller utan ett slutdatum/tid (permanent tilldelning)
- Standardvaraktigheten för en tilldelning (när den är inte permanent)
- Den maximala aktiveringsvaraktigheten (när en medlem i berättigad roll aktiveras)
- Den information som en användare måste ange under rollaktivering (just-in-time-tilldelningar) eller tilldelningsprocessen (direkttilldelningar)

![](./media/active-directory-privileged-identity-management-configure/role-settings-details.png)

## <a name="role-activation"></a>Rollaktivering

För att [aktivera en roll](active-directory-privileged-identity-management-how-to-activate-role.md) begär en berättigad administratör en tidsbunden ”aktivering” för rollen. Aktiveringen kan begäras med hjälp av alternativet **Aktivera min roll** i Azure AD Privileged Identity Management.

En administratör som vill aktivera en roll måste initiera Azure AD Privileged Identity Management i Azure-portalen.

Rollaktivering kan anpassas. I PIM-inställningarna kan du fastställa aktiveringens längd och vilken information som administratören behöver ange för att aktivera rollen.

![PIM-administratörsbegäran om rollaktivering – skärmbild][5]

## <a name="review-role-activity"></a>Granska rollaktivitet

Det finns två sätt att spåra hur anställda och administratörer använder privilegierade roller. Det första alternativet är att använda [Granskningshistorik för katalogroller](active-directory-privileged-identity-management-how-to-use-audit-log.md). Granskningshistorikloggarna spårar ändringar i privilegierade rolltilldelningar, rollaktiveringshistorik samt ändringar av inställningar för Azure-resursroller (förhandsversion). 

![PIM-aktiveringshistorik – skärmbild][6]

Det andra alternativet är att konfigurera vanliga [åtkomstgranskningar](active-directory-privileged-identity-management-how-to-start-security-review.md). Dessa åtkomstgranskningar kan utföras av en tilldelad granskare (till exempel en gruppchef), eller så kan de anställda granska själva. Det här är det bästa sättet att övervaka vem som fortfarande behöver åtkomst och vem som inte längre har det.

## <a name="azure-ad-pim-at-subscription-expiration"></a>Azure AD PIM på upphörande av prenumeration

En klientorganisation måste ha en utvärderingsprenumeration eller en betald prenumeration på Azure AD Premium P2 (eller EMS E5) i klientorganisationen innan Azure AD PIM används.  Utöver det måste licenser tilldelas till administratörerna för klientorganisationen.  Mer specifikt måste licenser tilldelas till administratörer i Azure AD-roller som hanteras via Azure AD PIM, administratörer i Azure RBAC-roller som hanteras via Azure AD PIM samt alla icke-administratörsroller som utför åtkomstgranskningar.
Om din organisation inte förnyar Azure AD Premium P2 eller om utvärderingsversionen upphör att gälla kommer Azure AD PIM-funktionerna inte längre att vara tillgängliga i din klientorganisation, berättigade rolltilldelningar tas bort och användare kommer inte längre att kunna aktivera roller. Du kan läsa mer i [kraven för Azure AD PIM-prenumeration](./privileged-identity-management/subscription-requirements.md)

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Admin_Overview.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_Settings_w_Approval_Disabled.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png
