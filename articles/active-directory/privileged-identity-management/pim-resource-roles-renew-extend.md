---
title: Privileged Identity Management resurser för Azure - utöka och förnya roller | Microsoft Docs
description: Det här dokumentet beskriver hur du utökar och förnya Azure-resursroller för PIM-resurser.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 2547b3793688eb51a4114f30bfcf61a9402f2cd2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---extend-or-renew"></a>Privileged Identity Management - resursroller - utöka eller förnya

PIM för Azure-resurser introducerar nya kontroller för att hantera livscykeln för åtkomst och tilldelning för Azure-resurser. Administratörer kan tilldela medlemskap med hjälp av start- och egenskaper för datum och tid. När det närmar sig slutet tilldelning, PIM skickar e-postmeddelanden till de berörda medlemmarna (det kan vara en användare eller grupp) och för administratörer av resursen så åtkomstbehörighet upprätthålls. Om åtkomst inte är utökat på grund av inaktivitet, tilldelningar kan förnyas och är synligt i ett tillståndet för upp till 30 dagar.

## <a name="who-can-extend-and-renew"></a>Vem som kan utöka och förnya?

Endast administratörer av resursen kan utöka eller förnya rolltilldelningar. Den berörda medlemmen kan begära för att utöka roller snart att gälla och begäran om förnyelse av roller upphört att gälla redan.

## <a name="when-are-notifications-sent"></a>När skickas meddelanden?

PIM skickar e-postaviseringar till administratörer och berörda medlemmar i rollerna som går ut inom 14 dagar och en dag före förfallodatum. Ett ytterligare e-postmeddelande skickas när en tilldelning officiellt har gått ut. 

Administratörer få meddelanden när en medlem av en roll som upphör att gälla eller har upphört att gälla begär att utöka eller förnya. När en viss administratör matchar förfrågan andra administratörer informeras om beslutet upplösning (godkännas eller avvisas) och begärande medlemmen får ett meddelande om beslutet. 

## <a name="extend-role-assignments"></a>Utöka rolltilldelningar

Stegen nedan beskriver stegen och användargränssnitt som ingår i begär, löst eller administrerar ett tillägg eller förnyelse av en rolltilldelning. 

### <a name="member-extend"></a>Utöka medlem

Medlemmar i en rolltilldelning kan begära för att utöka utgående rolltilldelningar direkt från ”kvalificerade” eller ”aktiv” fliken på den ”min roller” sidan av en resurs och från den översta nivån min roller av PIM-portalen. Medlemmar kan begära för att utöka berättigade och aktiva (tilldelade) roller som går ut nästa 14 dagar.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_ui.png)

När tilldelningen datum sluttiden är inom 14 dagar, blir för att ”utöka” en aktiv länk i användargränssnittet. I exemplet nedan förutsätter att det aktuella datumet infaller 27 mars.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_within_14.png)

Om du vill begära en förlängning av den här rollen tilldelningen klickar du på ”utöka” för att öppna formuläret.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_role_assignment_request.png)

Expandera ”tilldelning information” att visa information om den ursprungliga tilldelningen. Ange en orsak till Tilläggsbegäran och klicka på ”utöka”.

>[!Note]
>Vi rekommenderar med detaljerad varför tillägget är nödvändiga, och för hur länge tillägget bör vara (om känt).

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_form_complete.png)

På några ögonblick får resurs administratörer ett e-postmeddelande som begär de igenom Tilläggsbegäran. Om en begäran om att utöka har redan skickats, visas ett popup-meddelande längst upp i Azure-portalen förklarar felet.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_failed_existing_request.png)

Besök fliken ”väntande begäranden” i den vänstra navigeringsmenyn för att visa status för eller Avbryt din förfrågan.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_cancel_request.png)

### <a name="admin-approve"></a>Administratören godkänna

När en medlem skickar en begäran om att utöka en rolltilldelning, får resurs administratörer ett e-postmeddelande som innehåller information om den ursprungliga tilldelningen och orsaken tillhandahålls av förfrågaren. Meddelandet innehåller en direktlänk till begäran för administratören att godkänna eller neka. 

Förutom att följa länken från e-post, kan administratörer godkänna eller neka begäranden genom att gå till PIM-administrationsportalen och välja ”Godkänn begäran” från den vänstra navigeringsmenyn.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

När en administratör väljer godkänna eller neka visas information om begäran tillsammans med ett fält till att ange en motivering för granskningsloggarna.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

När du godkänner en begäran om att utöka rolltilldelning kan resurs administratörer välja en ny start- och Slutdatum tid och tilldelning typ. Ändra Tilldelningstyp kan vara nödvändigt om administratören vill bevilja begränsad åtkomst för att slutföra en viss uppgift (till exempel en dag). I det här exemplet ändra administratören tilldelningen från berättigad till aktiv ger den begärande åtkomsten utan att aktivera.

### <a name="admin-extend"></a>Utöka Admin

Rollmedlem glömmer eller går inte att begära medlemskap tillägg för rollen kan administratör utöka en tilldelning för medlemmen. Administrativa tillägg av rollmedlemskap kräver inte godkännande men meddelanden skickas till andra administratörer vid slutförandet av utöka rollen.

Om du vill utöka en roll medlemskap navigerar du till rollen eller medlem vyn i PIM. Medlemmen som kräver ett tillägg, och klicka på ”utöka” i kolumnen åtgärd.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_extend.png)

## <a name="renew-role-assignments"></a>Förnya rolltilldelningar

Medan begreppsmässigt, är processen för att förnya ett utgånget rolltilldelning annorlunda än begär ett tillägg för både medlemmar och administratörer. Med hjälp av stegen nedan medlemmar och administratörer kan förnya åtkomst till utgångna roller vid behov.

### <a name="member-renew"></a>Förnya medlem

Medlemmar som kan inte längre att komma åt resurser kan komma åt upp till 30 dagar har upphört att gälla tilldelningshistorik genom att gå till min roller i det vänstra navigeringsfönstret för PIM och välja fliken ”roller har upphört att gälla” i avsnittet Azure-resurs roller.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_myroles.png)

Lista över roller som visas som standard berättigade tilldelningar. Använd listrutan för att växla mellan kvalificerade och aktiv tilldelats roller.

Om du vill begära förnyelse av rollen tilldelningar i listan väljer du åtgärden ”förnya” och ange ett skäl för begäran. Är det bra att ge en varaktighet utöver eventuella ytterligare kontext som hjälper resursadministratören bestämmer dig för att godkänna eller neka.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_request_form.png)

När du har skickat begäran meddelas resurs administratörer om en väntande begäran om att förnya en rolltilldelning.

### <a name="admin-approves"></a>Administratören godkänner

Resurs-administratörer kan komma åt begäranden om förnyelse från länken i e-postmeddelande eller genom att komma åt PIM från portalen Auzre och välja ”Godkänn begäran” från den vänstra navigeringsmenyn.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

När en administratör väljer godkänna eller neka visas information om begäran tillsammans med ett fält till att ange en motivering för granskningsloggarna.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

När du godkänner en begäran om att förnya rolltilldelning måste resurs administratörer ange en ny start- och Slutdatum tid och tilldelning typ. 

### <a name="admin-renew"></a>Förnya Admin

Resurs-administratörer kan förnya utgångna rolltilldelningar fliken medlemmar i den vänstra navigeringsmenyn av en resurs eller i fliken utgångna roller av en resurs-roll.

Välj roller som har upphört att gälla ska visa en lista över alla utgångna rolltilldelningar skärmen medlemmar.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_member_blade.png)

## <a name="next-steps"></a>Nästa steg

[Kräv godkännande för att aktivera](pim-resource-roles-approval-workflow.md)

[Aktivera en roll](pim-resource-roles-use-the-audit-log.md)


