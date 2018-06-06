---
title: Översikt över Azure PIM resurs RBAC | Microsoft Docs
description: Få en översikt över funktionen RBAC i PIM inklusive terminologi och meddelanden
services: active-directory
documentationcenter: ''
author: barclayn
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: f19be17cccc3d1bf0e94fe1c156afb546909d565
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699231"
---
# <a name="pim-for-azure-resources"></a>PIM för Azure-resurser

Med Azure Active Directory Privileged Identity Management (PIM), kan du nu hantera, kontrollera och övervaka åtkomst till Azure-resurser i din organisation. Detta inkluderar prenumerationer och resursgrupper även virtuella datorer. Alla resurser i Azure-portalen som använder Azure rollbaserad åtkomstkontroll (RBAC)-funktioner kan dra nytta av alla bra säkerhets- och hanteringsfunktioner för livscykel Azure AD PIM har att erbjuda och vissa nya funktionerna som vi planerar att göra Azure AD-roller snart. 

## <a name="pim-for-azure-resources-helps-resource-administrators"></a>PIM för Azure-resurser som hjälper administratörer att resurs

- Se vilka användare och grupper är tilldelade roller för Azure-resurser du administrerar
- Aktivera på begäran, just-in-time ”tillgång till att hantera resurser, till exempel prenumerationer och resursgrupper
- Gälla tilldelade användare eller grupper resursåtkomst automatiskt med nya inställningar för Tidsbundna tilldelning
- Tilldela tillfällig åtkomst för snabb uppgifter eller -samtal scheman
- Använda Multifaktorautentisering för åtkomst till resurser på någon inbyggda eller anpassade roll 
- Få rapporter om resursen access korrelerade resurs-aktivitet under en användares aktiva session
- Få meddelanden när nya användare eller grupper som har tilldelats resursåtkomst och när de aktiverar berättigade tilldelningar

Azure AD PIM kan hantera inbyggda roller i Azure-resurshanteraren, samt anpassade (RBAC) roller, inklusive (men inte begränsat till):

- Ägare
- Administratör för användaråtkomst
- Deltagare
- Säkerhetsadministratör
- Säkerhetshanteraren och mycket mer

>[!NOTE]
Användare eller medlemmar i en grupp som tilldelats ägare eller administratör för användaråtkomst roller och globala administratörer som möjliggör prenumerationshantering av i Azure AD är administratörer för resursen. Dessa administratörer kan tilldela roller, konfigurera rollinställningar för och granska åtkomst med hjälp av PIM för Azure-resurser. Visa en lista över [inbyggda roller för Azure-resurser](../../role-based-access-control/built-in-roles.md).

## <a name="tasks"></a>Uppgifter

PIM ger åtkomst till Aktivera roller, visa väntande aktiveringar/begäranden väntande godkännanden (för [Azure AD directory roller](azure-ad-pim-approval-workflow.md)), och granskar väntar på ditt svar från avsnittet uppgifter i den vänstra navigeringsmenyn.

Vid åtkomst till alla menyalternativ uppgifter från översikt startpunkt innehåller resulterande vyn resultat för både roller för Azure AD-katalog och Azure-resurshanteraren roller. 

![](media/azure-pim-resource-rbac/role-settings-details.png)

Min roller innehåller en lista över dina aktiva och berättigade rolltilldelningar för Azure AD directory roller och Azure-resurshanteraren roller.

## <a name="activate-roles"></a>Aktivera roller

Aktivera roller för Azure-resurser introducerar en ny upplevelse som berättigade rollmedlemmar kan schemalägga aktivering för framtida datum/tid och välj en specifik aktivering varaktighet inom den Maxtid (konfigurerad av administratörer). Lär dig mer om [aktivera Azure AD-roller här](../active-directory-privileged-identity-management-how-to-activate-role.md).

![](media/azure-pim-resource-rbac/contributor.png)

Ange önskade startdatum och tidpunkt för att aktivera rollen från menyn aktiveringar. Du kan också minska varaktigheten aktivering (hur lång tid rollen är aktiv) och ange en motivering om det behövs; Klicka på Aktivera.

Om startdatum och starttid inte har ändrat, aktiveras rollen inom några sekunder. En roll i kö för aktivering Banderollmeddelandet på Mina rollsidan visas. Klicka på Uppdatera om du vill avmarkera det här meddelandet.

![](media/azure-pim-resource-rbac/my-roles.png)

Om aktiveringen är schemalagd vid en senare tid, visas en väntande begäran på fliken väntande begäranden i den vänstra navigeringsmenyn. Om rollaktivering krävs inte längre, kan användaren avbryta begäran genom att klicka på knappen Avbryt till höger på sidan.

![](media/azure-pim-resource-rbac/pending-requests.png)

## <a name="discover-and-manage-azure-resources"></a>Identifiera och hantera Azure-resurser

Välj Azure-resurser i den vänstra navigeringsmenyn hantera fliken för att hitta och hantera roller för en Azure-resurs. Använda filter eller sökfältet överst på sidan för att hitta en resurs.

![](media/azure-pim-resource-rbac/azure-resources.png)

## <a name="resource-dashboards"></a>Resursen instrumentpaneler

Visning av instrumentpanelen har fyra huvudsakliga komponenter. En grafisk representation av resursen rollen aktiveringar under de senaste sju dagarna. Dessa data är begränsad till den valda resursen och visar aktiveringar för de vanligaste rollerna (ägare, deltagare, administratör för användaråtkomst) och alla roller som kombineras.

Till höger om diagrammet aktiveringar finns två diagram som visar fördelningen av rolltilldelningar med Tilldelningstyp för både användare och grupper. Om du väljer en sektor i diagrammet ändras värdet till ett procentvärde (eller vice versa).

![](media/azure-pim-resource-rbac/admin-view.png)

Under diagrammen se antalet användare och grupper med nya rolltilldelningar över de senaste 30 dagarna (vänster) och en lista över roller sorterade efter totala tilldelningar (fallande).

![](media/azure-pim-resource-rbac/role-settings.png)

## <a name="manage-role-assignments"></a>Hantera rolltilldelningar

Administratörer kan hantera rolltilldelningar genom att välja roller eller medlemmar i det vänstra navigeringsfönstret. Att välja roller kan administratörer att definiera omfattningen av sina administrativa uppgifter till en specifik roll, medan medlemmar visas alla användare och grupper rolltilldelningar för resursen.

![](media/azure-pim-resource-rbac/roles.png)

![](media/azure-pim-resource-rbac/members.png)

>[!NOTE]
Om du har en roll väntande aktivering visas en meddelandebanderoll överst på sidan när du visar medlemskap.

## <a name="assign-roles"></a>Tilldela roller

Om du vill tilldela en användare eller grupp till en roll, väljer du rollen (om visar roller) eller klicka på Lägg till från Åtgärdsfältet (på vyn medlemmar).

![](media/azure-pim-resource-rbac/members2.png)

>[!NOTE]
Om du lägger till en användare eller grupp från fliken medlemmar, måste du välja en roll på menyn Lägg till innan du kan välja en användare eller grupp.

![](media/azure-pim-resource-rbac/select-role.png)

Välj en användare eller grupp från katalogen.

![](media/azure-pim-resource-rbac/choose.png)

Välj lämplig Tilldelningstypen från listrutan. 

**Precis i Tidstilldelning:** den ger användare eller grupp medlemmar berättigade men inte beständig åtkomst till rollen under en viss tid eller på obestämd tid (om konfigurerad i rollinställningar). 

**Direkttilldelning:** kräver inte medlemmarna användare eller grupp för att aktivera rolltilldelning (kallas även permanent åtkomst). Microsoft rekommenderar att du använder direkttilldelning för kortvarig användning, till exempel på anrop SKIFT eller tid känsliga aktiviteter, där åtkomst inte är nödvändiga när uppgiften har slutförts.

![](media/azure-pim-resource-rbac/membership-settings.png)

En kryssruta under listrutan för tilldelning av typen kan du ange om tilldelningen vara permanenta (permanent berättigad att aktivera precis i tid tilldelning/permanent active för Direkttilldelning). Avmarkera kryssrutan om du vill ange en viss tilldelning varaktighet och ändrar start och avsluta datum och tid.

>[!NOTE]
Kryssrutan kanske unmodifiable om en annan administratör har angett maximalt tilldelning varaktighet för varje Tilldelningstyp av i rollinställningarna för.

![](media/azure-pim-resource-rbac/calendar.png)

## <a name="view-activation-and-azure-resource-activity"></a>Visa aktivering och Azure-resurshanteraren aktivitet

Om du behöver se vilka åtgärder som en viss användare tog på olika resurser kan du granska Azure-resurshanteraren aktiviteten som associeras med en viss aktiveringsperioden (för behöriga användare). Starta genom att välja en användare från vyn medlemmar eller från listan över medlemmar i en viss roll. Resultatet visar en grafisk vy av användarens åtgärder på Azure-resurser efter datum och de senaste rollen aktiveringarna via samma tidsperioden.

![](media/azure-pim-resource-rbac/user-details.png)

Att välja en specifik rollaktivering visas roll aktiveringsinformation och motsvarande Azure-resurshanteraren aktivitet som inträffade medan användaren var aktiv.

![](media/azure-pim-resource-rbac/audits.png)

## <a name="modify-existing-assignments"></a>Ändra befintliga tilldelningar

Välj Åtgärdsfältet överst på sidan Ändra inställningar för att ändra befintliga tilldelningar från detaljvy användare/grupp. Ändra tilldelning till bara i tid-tilldelning eller Direkttilldelning.

![](media/azure-pim-resource-rbac/change-settings.png)

## <a name="review-who-has-access-in-a-subscription"></a>Granska vem som har åtkomst till i en prenumeration

Välj fliken medlemmar från det vänstra navigeringsfönstret för att granska rolltilldelningar i din prenumeration, eller välj roller och välja en specifik roll att granska medlemmar. 

Välj granska Åtgärdsfältet att visa befintliga åtkomst omdömen och välj Lägg till att skapa en ny granskning.

![](media/azure-pim-resource-rbac/owner.png)

[Mer information om åtkomst granskningar](../active-directory-privileged-identity-management-how-to-perform-security-review.md)

>[!NOTE]
Granskningar stöds endast för resurstyper för prenumerationen just nu.

## <a name="configure-role-settings"></a>Konfigurera inställningar för serverrollen

Konfigurera inställningar för serverrollen definiera standardvärdena tilldelningar i PIM-miljö. Välj fliken Inställningar från det vänstra navigeringsfönstret eller knappen rollen inställningar från Åtgärdsfältet någon roll som du vill visa aktuella alternativ för att definiera dessa för din resurs.

Klicka på Redigera från Åtgärdsfältet överst på sidan kan du ändra varje inställning.

![](media/azure-pim-resource-rbac/owner.png)

![](media/azure-pim-resource-rbac/owner02.png)

Ändringar av inställningarna är inloggade på inställningssidan roll, inklusive senast uppdaterade datum och den administratör som har ändrat inställningarna.

![](media/azure-pim-resource-rbac/role-settings-02.png)

## <a name="resource-audit"></a>Resursgranskning

Granskning för resursen ger dig en överblick över alla aktiviteter i rollen för resursen. Du kan filtrera informationen genom att använda ett fördefinierat datum eller ett anpassat intervall.
![](media/azure-pim-resource-rbac/last-day.png) Resursen audit också ger snabb åtkomst om du vill visa information om en användare-aktivitet. I vyn är alla ”aktivera rollen” åtgärder länkar till den begärande resurs aktiviteten.
![](media/azure-pim-resource-rbac/resource-audit.png)

## <a name="just-enough-administration"></a>Bara tillräckligt med administration

Med din resurs rolltilldelningar bara tillräckligt med bästa praxis för administration (JEA) är enkelt med PIM för Azure-resurser. Användare och medlemmar i gruppen med tilldelningar i Azure-prenumerationer eller resursgrupper kan aktivera sina befintliga rolltilldelning i projektets omfattning. 

Hitta den underordnade resursen som du behöver hantera från sidan Sök efter.

![](media/azure-pim-resource-rbac/azure-resources-02.png)

Välj min roller från den vänstra navigeringsmenyn och väljer sedan rätt roll för att aktivera. Meddelande Tilldelningstypen ärvs eftersom tilldelades rollen på prenumerationen i stället för resursgrupp, enligt nedan.

![](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>Nästa steg

- [Inbyggda roller för Azure-resurser](../../role-based-access-control/built-in-roles.md)
- Lär dig mer om [aktiverar här Azure AD-roller](../active-directory-privileged-identity-management-how-to-activate-role.md)
- [PIM godkännandearbetsflöden](azure-ad-pim-approval-workflow.md)
