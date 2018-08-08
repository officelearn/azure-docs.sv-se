---
title: Översikt över Azure PIM Resource RBAC | Microsoft Docs
description: Få en översikt över RBAC-funktionen i PIM inklusive terminologi och meddelanden
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: 9b4980681cb3e7442211b06255d6eefd8a1b1170
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622362"
---
# <a name="pim-for-azure-resources"></a>PIM för Azure-resurser

Med Azure Active Directory Privileged Identity Management (PIM) kan du nu hantera, kontrollera och övervaka åtkomst till Azure-resurser inom organisationen. Detta inkluderar prenumerationer, resursgrupper och även virtuella datorer. Alla resurser i Azure-portalen som utnyttjar Azure rollbaserad åtkomstkontroll (RBAC)-funktioner kan dra nytta av alla mycket hög säkerhet och hanteringsfunktioner för programlivscykel Azure AD PIM har att erbjuda och några stora nya funktioner vi planerar att göra Azure AD-roller snart. 

## <a name="pim-for-azure-resources-helps-resource-administrators"></a>PIM för Azure-resurser som hjälper administratörer att resursen

- Se vilka användare och grupper tilldelas roller för Azure-resurser du administrerar
- Aktivera på begäran, åtkomst just-in-time ”för att hantera resurser, till exempel prenumerationer och resursgrupper
- Upphör att gälla åtkomst till tilldelade användare/grupper automatiskt med nya Tilldelningsinställningar för Tidsbundna
- Tilldela tillfälligt resursåtkomst för Snabbuppgifter eller på anrop scheman
- Använda Multifaktorautentisering för åtkomst till resurser på alla inbyggda eller anpassade roller 
- Få rapporter om aktivitet för resursen åtkomst korrelerade resurs under en användares aktiva session
- Få aviseringar när nya användare eller grupper har tilldelats åtkomst till resurser, och när de aktiverar berättigade uppgifter

Azure AD PIM kan hantera inbyggda Azure-resursroller, samt anpassade (RBAC) roller, inklusive (men inte begränsat till):

- Ägare
- Administratör för användaråtkomst
- Deltagare
- Säkerhetsadministratör
- Säkerhetshanteraren och mycket mer

>[!NOTE]
Användare eller medlemmar i en grupp som tilldelats ägare eller administratör för användaråtkomst roller och globala administratörer som aktiverar prenumerationshantering i Azure AD är resursen administratörer. Dessa administratörer kan tilldela roller, konfigurera inställningar för serverrollen och granska åtkomst med hjälp av PIM för Azure-resurser. Visa en lista över [inbyggda roller för Azure-resurser](../../role-based-access-control/built-in-roles.md).

## <a name="tasks"></a>Uppgifter

PIM ger lämplig åtkomst till Aktivera roller kan visa väntande aktiveringar/begäranden väntande godkännanden (för [Azure AD-katalogroller](azure-ad-pim-approval-workflow.md)), och granskar väntar på ditt svar från avsnittet uppgifter i den vänstra navigeringsmenyn.

Vid åtkomst till någon av menyalternativ uppgifter från översikt startpunkt, innehåller den resulterande vyn resultat för både Azure AD-katalogroller och Azure-resursroller. 

![](media/azure-pim-resource-rbac/role-settings-details.png)

Mina roller innehåller en lista över dina aktiva och berättigade rolltilldelningar för Azure AD-katalogroller och Azure-resursroller.

## <a name="activate-roles"></a>Aktivera roller

Aktivera roller för Azure-resurser introducerar en ny upplevelse som gör att medlemmar av berättigad roll att schemalägga aktivering för ett framtida datum/tid och välja en specifik aktiveringsvaraktighet inom den Maxtid (konfigureras av administratörer). Lär dig mer om [aktivera Azure AD-roller här](pim-how-to-activate-role.md).

![](media/azure-pim-resource-rbac/contributor.png)

Ange önskad startdatum och tidpunkt för att aktivera rollen från menyn aktiveringar. Du kan också minska aktiveringens varaktighet (hur lång tid rollen är aktiv) och ange en motivering vid behov; Klicka på Aktivera.

Om startdatum och starttid inte ändras måste aktiveras rollen inom några sekunder. Du kan se en roll i kö för aktivering Banderollmeddelandet på sidan Mina roller. Klicka på Uppdatera för att ta bort det här meddelandet.

![](media/azure-pim-resource-rbac/my-roles.png)

Om aktiveringen är schemalagd för ett framtida datum-tid, visas den väntande begäran på fliken väntande begäranden i den vänstra navigeringsmenyn. I den händelse att rollaktivering krävs inte längre, kan du avbryta begäran genom att klicka på Avbryt om du till höger på sidan.

![](media/azure-pim-resource-rbac/pending-requests.png)

## <a name="discover-and-manage-azure-resources"></a>Identifiera och hantera Azure-resurser

Välj Azure-resurser för att hitta och hantera roller för en Azure-resurs under fliken hantera i den vänstra navigeringsmenyn. Använd filter eller sökfältet längst upp på sidan för att hitta en resurs.

![](media/azure-pim-resource-rbac/azure-resources.png)

## <a name="resource-dashboards"></a>Resurs-instrumentpaneler

Admin-visa instrumentpanelen har fyra huvudsakliga komponenter. En grafisk representation av resursen rollaktiveringar under de senaste sju dagarna. Dessa data är begränsad till den valda resursen och visar aktiveringar för de vanligaste rollerna (ägare, deltagare, administratör för användaråtkomst) och alla roller som kombineras.

Till höger om diagrammet aktiveringar finns två diagram som visar fördelningen av rolltilldelningar av Tilldelningstyp för både användare och grupper. Om du väljer en sektor i diagrammet ändras värdet till ett procentvärde (eller vice versa).

![](media/azure-pim-resource-rbac/admin-view.png)

Under diagrammen visas antalet användare och grupper med nya rolltilldelningar över de senaste 30 dagarna (vänster) och en lista över roller som sorterade efter Totalt antal tilldelningar (fallande).

![](media/azure-pim-resource-rbac/role-settings.png)

## <a name="manage-role-assignments"></a>Hantera rolltilldelningar

Administratörer kan hantera rolltilldelningar genom att välja roller eller medlemmar i det vänstra navigeringsfönstret. Att välja roller gör att administratörer kan definiera sina administrativa uppgifter till en specifik roll, medan medlemmar visas alla användare och grupper rolltilldelningar för resursen.

![](media/azure-pim-resource-rbac/roles.png)

![](media/azure-pim-resource-rbac/members.png)

>[!NOTE]
Om du har en roll som väntar på aktivering är en meddelandebanderoll visas överst på sidan när du visar medlemskap.

## <a name="assign-roles"></a>Tilldela roller

Välj rollen (om visning av roller) om du vill tilldela en användare eller grupp till en roll, eller klicka på Lägg till från Åtgärdsfältet (i vyn medlemmar).

![](media/azure-pim-resource-rbac/members2.png)

>[!NOTE]
Om att lägga till en användare eller grupp från fliken medlemmar, måste du välja en roll från menyn Lägg till innan du kan välja en användare eller grupp.

![](media/azure-pim-resource-rbac/select-role.png)

Välj en användare eller grupp från katalogen.

![](media/azure-pim-resource-rbac/choose.png)

Välj lämplig Tilldelningstyp från den nedrullningsbara menyn. 

**Just-In-Time-tilldelning:** den ger användare eller grupp medlemmarna berättigade men inte beständig åtkomst till rollen för en viss tid eller på obestämd tid (om konfigurerad i rollinställningar). 

**Direkttilldelning:** inte kräver att användaren eller gruppen medlemmar att aktivera rolltilldelningen (kallas beständig åtkomst). Microsoft rekommenderar att du använder direkttilldelning för kortvarig användning, till exempel på anrop SKIFT eller tid känsliga aktiviteter, där åtkomst inte är obligatoriska när aktiviteten har slutförts.

![](media/azure-pim-resource-rbac/membership-settings.png)

En kryssruta nedan i listrutan för tilldelning av typen kan du ange om tilldelningen ska vara permanent (permanent berättigad att aktivera Just-in-Time tilldelning/permanent aktiv för Direkttilldelning). Om du vill ange en viss tilldelningsvaraktighet, avmarkera kryssrutan och ändrar start och/eller avsluta datum och tid.

>[!NOTE]
Kryssrutan kanske unmodifiable om en annan administratör har angett maximalt tilldelningsvaraktighet för varje tilldelning i rollinställningarna för.

![](media/azure-pim-resource-rbac/calendar.png)

## <a name="view-activation-and-azure-resource-activity"></a>Visa aktivering och aktivitet för Azure-resurs

Om du vill se vilka åtgärder som en viss användare vidtog för olika resurser kan granska du aktiviteten Azure-resurs som är associerade med en viss aktiveringsperioden (för berättigade användare). Starta genom att välja en användare från vyn medlemmar eller från listan över medlemmar i en viss roll. Resultatet visar en grafisk representation av användarens åtgärder på Azure-resurser efter datum och de senaste rollaktiveringar under den samma tidsperioden.

![](media/azure-pim-resource-rbac/user-details.png)

Att välja en specifik rollaktivering visar rollaktiveringsinformation och motsvarande Azure Resource-aktivitet som inträffade när användaren var aktiv.

![](media/azure-pim-resource-rbac/audits.png)

## <a name="modify-existing-assignments"></a>Ändra befintliga tilldelningar

Välj Ändringsinställningar Åtgärdsfältet överst på sidan om du vill ändra befintliga tilldelningar från en användare/grupp detaljerad vy. Ändra Tilldelningstyp precis i tid-tilldelning eller Direkttilldelning.

![](media/azure-pim-resource-rbac/change-settings.png)

## <a name="review-who-has-access-in-a-subscription"></a>Granska vem som har åtkomst i en prenumeration

Välj fliken medlemmar i det vänstra navigeringsfönstret för att granska rolltilldelningar i din prenumeration, eller välja roller och välja en ansvarar för att granska medlemmar. 

Välj granska åtgärdsfält att visa befintliga åtkomstgranskningar och välj Lägg till att skapa en ny granskning.

![](media/azure-pim-resource-rbac/owner.png)

[Läs mer om åtkomstgranskningar](pim-how-to-perform-security-review.md)

>[!NOTE]
Granskningar stöds endast för resurstyper för prenumerationen just nu.

## <a name="configure-role-settings"></a>Konfigurera rollinställningar

Konfigurera rollinställningar definiera standardinställningarna tilldelningar i PIM-miljö. Välj fliken Inställningar från det vänstra navigeringsfönstret eller knappen rollen inställningar från Åtgärdsfältet någon roll att visa aktuella alternativ för att definiera dessa för din resurs.

Att klicka på Redigera från Åtgärdsfältet överst på sidan kan du ändra varje inställning.

![](media/azure-pim-resource-rbac/owner.png)

![](media/azure-pim-resource-rbac/owner02.png)

Ändringar i inställningar för loggas på sidan för inställningar för rollen, inklusive senast uppdaterade datum och den administratör som har ändrat inställningarna.

![](media/azure-pim-resource-rbac/role-settings-02.png)

## <a name="resource-audit"></a>Resursgranskning

Resursgranskning ger dig en överblick över alla roll-aktivitet för resursen. Du kan filtrera informationen med ett fördefinierat datum eller ett anpassat intervall.
![](media/azure-pim-resource-rbac/last-day.png) Resursgranskning tillhandahåller även snabb åtkomst om du vill visa en användares Aktivitetsinformation. I vyn är alla ”aktivera rollen” åtgärder länkar till aktivitet för den begärande-resurs.
![](media/azure-pim-resource-rbac/resource-audit.png)

## <a name="just-enough-administration"></a>Precis tillräcklig administration

Det är enkelt med PIM för Azure-resurser med metodtips precis tillräcklig administration (jea JUST) med din resurs rolltilldelningar. Användare och medlemmar i gruppen med tilldelningar i Azure-prenumerationer eller resursgrupper kan aktivera sina befintliga rolltilldelning i projektets omfattning. 

Hitta den underordnade resursen som du behöver hantera från sidan Sök efter.

![](media/azure-pim-resource-rbac/azure-resources-02.png)

Välj Mina roller från den vänstra navigeringsmenyn och välj rätt roll för att aktivera. Observera Tilldelningstyp ärvs eftersom tilldelades rollen på prenumerationen i stället för resursgruppen, enligt nedan.

![](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>Nästa steg

- [Inbyggda roller för Azure-resurser](../../role-based-access-control/built-in-roles.md)
- Lär dig mer om [aktivera här Azure AD-roller](pim-how-to-activate-role.md)
- [Arbetsflöden för godkännande av PIM](azure-ad-pim-approval-workflow.md)
