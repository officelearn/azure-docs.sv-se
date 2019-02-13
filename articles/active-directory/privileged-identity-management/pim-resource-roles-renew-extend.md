---
title: Utöka eller förnya rolltilldelningar i Azure-resurs i PIM | Microsoft Docs
description: Lär dig mer om att utöka eller förnya Azure-resurs rolltilldelningar i Azure AD Privileged Identity Management (PIM).
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
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cec018eec6e298e27329c03964dccf5809c1791
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56190644"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-pim"></a>Utöka eller förnya rolltilldelningar i Azure-resurs i PIM

Privileged Identity Management (PIM) för Azure-resurser introducerar nya kontroller för att hantera livscykeln för åtkomst och tilldelning för Azure-resurser. Administratörer kan tilldela medlemskap med hjälp av start- och datum / tid-egenskaper. När det närmar sig slutet tilldelning, skickar PIM e-postmeddelanden till de berörda användare eller grupper. Den skickar också e-postaviseringar till administratörer av resursen för att säkerställa att rätt upprätthålls. Tilldelningar kan förnyas och fortsätter att visas upphört att gälla för upp till 30 dagar, även om åtkomst inte har utökats.

## <a name="who-can-extend-and-renew"></a>Vem som kan utöka och förnya?

Endast administratörer av resursen kan utöka eller förnya rolltilldelningar. Den berörda medlemmen kan begära för att utöka roller som är på väg att upphöra att gälla och begära för att förnya roller som redan har gått ut.

## <a name="when-are-notifications-sent"></a>När skickas meddelanden?

PIM skickar e-postaviseringar till administratörer och berörda medlemmarna i roller som går ut inom 14 dagar och en dag före förfallodatumet. Ett ytterligare e-postmeddelande skickas när en uppgift förfaller officiellt. 

Administratörer får meddelanden när en medlem i en roll som upphör att gälla eller har upphört att gälla begär att utöka eller förnya. När en viss administratör matchar förfrågan, meddelas alla andra administratörer av lösning beslutet (godkännas eller avvisas). Den begärande medlemmen är sedan ett meddelande om beslutet. 

## <a name="extend-role-assignments"></a>Utöka rolltilldelningar

Följande steg beskriver hur du begär, hur du löser eller administrerar ett tillägg eller förnyelse av en rolltilldelning. 

### <a name="member-extend"></a>Utöka medlem

Medlemmar i en rolltilldelning kan utöka upphör rolltilldelningar direkt från den **berättigade** eller **Active** fliken på den **Mina roller** sidan av en resurs och från den översta nivån **Mina roller** i PIM-portalen. Medlemmar kan begära för att få tillgängliga och aktiva (tilldelade) roller som går ut inom 14 dagar.

![Utöka roller](media/azure-pim-resource-rbac/aadpim_rbac_extend_ui.png)

När tilldelningen Slutdatum /-tid är inom 14 dagar, för att **utöka** blir en aktiv länk i användargränssnittet. I följande exempel antar vi att det aktuella datumet infaller den 27 mars.

![Knappen ”Utöka”](media/azure-pim-resource-rbac/aadpim_rbac_extend_within_14.png)

Om du vill begära en förlängning av den här rolltilldelningen, Välj **utöka** att öppna formuläret för begäran.

![Öppna formuläret för begäran](media/azure-pim-resource-rbac/aadpim_rbac_extend_role_assignment_request.png)

Om du vill visa information om den ursprungliga tilldelningen, expandera **tilldelningsinformation**. Ange en orsak till förfrågan för domännamnstillägget och välj sedan **utöka**.

>[!Note]
>Vi rekommenderar, inklusive information om varför tillägget krävs, och för hur länge tillägget ska beviljas (om du har den här informationen).

![Rolltilldelning](media/azure-pim-resource-rbac/aadpim_rbac_extend_form_complete.png)

På bara några minuter får resurs-administratörer ett e-meddelande med en begäran att granska de förfrågan för domännamnstillägget. Om en begäran om att utöka redan har skickat visas ett popup-meddelande längst upp i Azure Portal som förklarar felet.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_failed_existing_request.png)

Gå till den **väntande begäranden om** fliken i den vänstra rutan att visa statusen för din begäran eller avbryter du den.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_cancel_request.png)

### <a name="admin-approve"></a>Administratören godkänna

När en medlem skickar en begäran till en rolltilldelning kan får resurs-administratörer ett e-postmeddelande som innehåller information om den ursprungliga tilldelningen och orsaken till begäran. Meddelandet innehåller en direktlänk till begäran för en administratör att godkänna eller neka. 

Förutom att använda följa länken från e-post, kan administratörer godkänna eller neka förfrågningar genom att gå till PIM-administration portal och välja **godkänna förfrågningar** i den vänstra rutan.

![Skärmbild av fel](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

När en administratör väljer **Godkänn** eller **neka**, information om begäran visas, tillsammans med ett fält att ange en motivering för granskningsloggar.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

När du godkänner en begäran om rolltilldelning kan resource administratörer välja ett nytt startdatum och slutdatum Tilldelningstyp. Ändra Tilldelningstyp kan vara nödvändigt om administratören vill bevilja begränsad åtkomst för att slutföra en viss uppgift (en dag, till exempel). I det här exemplet kan administratören ändra tilldelningen från **berättigade** till **Active**. Det innebär att de kan ge åtkomst till begäranden utan att behöva aktivera.

### <a name="admin-extend"></a>Administratören utökade

Om en Rollmedlem glömmer eller kunde inte begära en förlängning för roll-medlemskap, kan en administratör utöka en tilldelning för medlemmen. Administrativa tillägg av rollmedlemskap kräver inte godkännande, men skickas meddelanden till alla administratörer när rollen har utökats.

Bläddra till resursvy för rollen eller medlem i PIM för att utöka en rollmedlemskap. Hitta den medlem som kräver ett tillägg. Välj sedan **utöka** i kolumnen åtgärder.

![Utöka en rollmedlemskap](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_extend.png)

## <a name="renew-role-assignments"></a>Förnya rolltilldelningar

Medan begreppsmässigt liknas vid processen för att begära ett tillägg skiljer sig processen för att förnya en rolltilldelning som har upphört att gälla. Med följande steg, kan medlemmar och administratörer förnya åtkomst till utgångna roller vid behov.

### <a name="member-renew"></a>Förnya medlem

Medlemmar som inte längre kan komma åt resurser kan komma åt upp till 30 dagars tilldelningshistorik av har upphört att gälla. Om du vill göra detta måste de gå till **Mina roller** i den vänstra rutan och välj sedan den **har upphört att gälla roller** fliken i avsnittet roles Azure-resurs.

![Fliken ”roller har upphört att gälla”](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_myroles.png)

I listan över roller visas standard **berättigade roller**. Använd den nedrullningsbara menyn för att växla mellan berättigade och aktiv tilldelade roller.

Om du vill begära förnyelse för någon av rolltilldelningar i listan, Välj den **förnya** åtgärd. Ange sedan ett skäl för begäran. Nu är det bra att ange en varaktighet utöver eventuella ytterligare kontext som hjälper till att resursadministratören vill godkänna eller neka.

![Förnya rolltilldelning](media/azure-pim-resource-rbac/aadpim_rbac_renew_request_form.png)

När begäran har skickats, meddelas resource administratörer av en väntande begäran om att förnya en rolltilldelning.

### <a name="admin-approves"></a>Administratören godkänner

Resurs-administratörer kan komma åt begäran om förnyelse från länken i e-postmeddelande eller genom att få åtkomst till PIM från Azure-portalen och välja **godkänna förfrågningar** i den vänstra rutan.

![Godkänn förfrågningar](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

När en administratör väljer **Godkänn** eller **neka**, information om begäran är visas tillsammans med ett fält för att ange en motivering för granskningsloggar.

![Godkänn rolltilldelning](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

När du godkänner en begäran om förnyad rolltilldelning måste resource administratörer ange ett nytt startdatum och slutdatum Tilldelningstyp. 

### <a name="admin-renew"></a>Administratören förnyade

Resurs-administratörer kan förnya utgångna rolltilldelningar från den **medlemmar** flik i den vänstra navigeringsmenyn för en resurs. De kan också förnya utgångna rolltilldelningar inifrån den **har upphört att gälla** fliken roller av en resurs-roll.

Visa en lista över alla upphörde rolltilldelningar den **medlemmar** väljer **har upphört att gälla roller**.

![Roller som har upphört](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_member_blade.png)

## <a name="next-steps"></a>Nästa steg

- [Godkänn eller neka begäranden för Azure-resursroller i PIM](pim-resource-roles-approval-workflow.md)
- [Konfigurera Azure-resurs rollinställningar i PIM](pim-resource-roles-configure-role-settings.md)
