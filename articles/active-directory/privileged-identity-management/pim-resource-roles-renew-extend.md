---
title: Utöka och granska roller i Azure-resurser med hjälp av Privileged Identity Management | Microsoft Docs
description: Det här dokumentet beskriver hur du utökar och förnya Azure-resursroller för PIM-resurser.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: cd1524bf03256a2ed706d11a8702c7b5eff5dad4
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260513"
---
# <a name="extend-and-review-roles-in-azure-resources-by-using-privileged-identity-management"></a>Utöka och granska roller i Azure-resurser med hjälp av Privileged Identity Management

Privileged Identity Management (PIM) för Azure-resurser introducerar nya kontroller för att hantera livscykeln för åtkomst och tilldelning för Azure-resurser. Administratörer kan tilldela medlemskap med hjälp av start- och egenskaper för datum och tid. När det närmar sig slutet tilldelning, skickar PIM e-postmeddelanden till de berörda användare eller grupper. Även skickar den e-postaviseringar till administratörer av resursen så att lämplig åtkomst bibehålls. Tilldelningar kan förnyas och fortfarande att visas i en gått ut för upp till 30 dagar, även om åtkomst inte är utökat.

## <a name="who-can-extend-and-renew"></a>Vem som kan utöka och förnya?

Endast administratörer av resursen kan utöka eller förnya rolltilldelningar. Den berörda medlemmen kan begära för att utöka roller som håller på att förfalla och begäran om för att förnya roller som redan upphört att gälla.

## <a name="when-are-notifications-sent"></a>När skickas meddelanden?

PIM skickar e-postaviseringar till administratörer och berörda medlemmar i rollerna som går ut inom 14 dagar och en dag före förfallodatum. Ett ytterligare e-postmeddelande skickas när en tilldelning officiellt går ut. 

Administratörer få meddelanden när en medlem av en roll som upphör att gälla eller har upphört att gälla begär att utöka eller förnya. När en viss administratör matchar förfrågan, meddelas alla andra administratörer i beslutet upplösning (godkännas eller avvisas). Sedan meddelande den begärande medlemmen om beslutet. 

## <a name="extend-role-assignments"></a>Utöka rolltilldelningar

Följande steg beskriver processen för att begära, löst eller administrerar ett tillägg eller förnyelse av en rolltilldelning. 

### <a name="member-extend"></a>Utöka medlem

Medlemmar i en rolltilldelning kan utöka utgående rolltilldelningar direkt från den **berättigade** eller **Active** fliken på den **min roller** sidan av en resurs och från den översta nivån **Min roller** i PIM-portalen. Medlemmar kan begära för att utöka berättigade och aktiva (tilldelade) roller som går ut nästa 14 dagar.

![Utöka roller](media/azure-pim-resource-rbac/aadpim_rbac_extend_ui.png)

När tilldelningen datum sluttiden är inom 14 dagar, för att **utöka** blir en aktiv länk i användargränssnittet. I följande exempel förutsätter att det aktuella datumet infaller 27 mars.

![Knappen ”Utöka”](media/azure-pim-resource-rbac/aadpim_rbac_extend_within_14.png)

Om du vill begära en förlängning av den här rolltilldelningen Välj **utöka** att öppna formuläret.

![Öppna formuläret](media/azure-pim-resource-rbac/aadpim_rbac_extend_role_assignment_request.png)

Om du vill visa information om den ursprungliga tilldelningen av Expandera **tilldelningsinformation**. Ange en orsak till Tilläggsbegäran och välj sedan **utöka**.

>[!Note]
>Vi rekommenderar med detaljerad varför tillägget är nödvändiga, och för hur länge tillägget bör beviljas (om du har den här informationen).

![Utöka rolltilldelning](media/azure-pim-resource-rbac/aadpim_rbac_extend_form_complete.png)

I en fråga om stund administratörerna resurs en e-meddelande med en begäran att granska de Tilläggsbegäran. Om en begäran om att utöka har redan skickats, visas ett popup-meddelande längst upp i Azure-portalen förklarar felet.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_failed_existing_request.png)

Gå till den **väntande begäranden** fliken i den vänstra rutan för att visa status för din begäran eller Avbryt den.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_cancel_request.png)

### <a name="admin-approve"></a>Administratören godkänna

När en medlem skickar en begäran om att utöka en rolltilldelning, får resurs administratörer ett e-postmeddelande som innehåller information om den ursprungliga tilldelningen och orsaken till begäran. Meddelandet innehåller en direktlänk till begäran för administratören att godkänna eller neka. 

Förutom att använda följa länken från e-post, kan administratörer godkänna eller neka begäranden genom att gå till administration PIM portalen och markerar **godkänna begäranden** i den vänstra rutan.

![Skärmbild av fel](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

När en administratör väljer **Godkänn** eller **neka**, information om begäran visas, tillsammans med ett fält för att ange en motivering för granskningsloggarna.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

När du godkänner en begäran om att utöka rolltilldelning kan resurs administratörer välja ett nytt startdatum och slutdatum Tilldelningstyp. Ändra Tilldelningstyp kan vara nödvändigt om administratören vill bevilja begränsad åtkomst för att slutföra en viss uppgift (en dag, till exempel). I det här exemplet kan administratören ändra tilldelningen från **berättigade** till **Active**. Det innebär att de kan ge åtkomst till begäranden utan att aktivera.

### <a name="admin-extend"></a>Administratören utökade

Om en Rollmedlem glömmer eller inte går att begära ett tillägg för rollen medlemskap, kan en administratör utöka en tilldelning för medlemmen. Administrativa tillägg av rollmedlemskap kräver inte godkännande, men meddelanden skickas till andra administratörer när rollen har utökats.

Om du vill utöka en rollmedlemskap bläddrar du till rollen eller medlem vyn i PIM. Hitta den medlem som kräver ett tillägg. Välj sedan **utöka** i kolumnen åtgärd.

![Utöka en rollmedlemskap](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_extend.png)

## <a name="renew-role-assignments"></a>Förnya rolltilldelningar

Processen för att förnya ett utgånget rolltilldelning skiljer sig medan begreppsmässigt till processen för att begära ett tillägg. Med följande steg kan medlemmar och administratörer förnya åtkomst till utgångna roller vid behov.

### <a name="member-renew"></a>Förnya medlem

Medlemmar som kan inte längre att komma åt resurser kan komma åt upp till 30 dagar har upphört att gälla tilldelningshistorik. Om du vill göra detta, bläddrar till **Mina roller** i det vänstra fönstret och välj sedan den **upphört att gälla roller** fliken i Azure-resurs roller avsnittet.

![Fliken ”roller har upphört att gälla”](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_myroles.png)

Lista över roller som visas som standard **berättigade roller**. Använd nedrullningsbara menyn för att växla mellan kvalificerade och Active tilldela roller.

Om du vill begära förnyelse för någon av rolltilldelningen i listan, Välj den **förnya** åtgärd. Ange en orsak till begäran. Är det bra att ge en varaktighet utöver eventuella ytterligare kontext som hjälper till att resursadministratören vill godkänna eller neka.

![Förnya rolltilldelning](media/azure-pim-resource-rbac/aadpim_rbac_renew_request_form.png)

När begäran har skickats, meddelas resurs administratörer om en väntande begäran om att förnya en rolltilldelning.

### <a name="admin-approves"></a>Administratören godkänner

Resurs-administratörer kan komma åt begäranden om förnyelse från länken i e-postmeddelande eller genom att öppna PIM från Azure-portalen och välja **godkänna begäranden** i den vänstra rutan.

![Godkänn ansökningar](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

När en administratör väljer **Godkänn** eller **neka**, information om begäran visas tillsammans med ett fält för att ange en motivering för granskningsloggarna.

![Godkänn rolltilldelning](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

När du godkänner en begäran om att förnya rolltilldelning måste resurs administratörer ange ett nytt startdatum och slutdatum Tilldelningstyp. 

### <a name="admin-renew"></a>Administratören förnyade

Resurs-administratörer kan förnya utgångna rolltilldelningar från den **medlemmar** fliken i den vänstra navigeringsmenyn av en resurs. De kan också förnya utgångna rolltilldelningar inifrån den **utgångna** fliken roller av en resurs-roll.

Visa en lista över alla VD rolltilldelningar den **medlemmar** väljer **upphört att gälla roller**.

![Roller som har upphört](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_member_blade.png)

## <a name="next-steps"></a>Nästa steg

[Kräv godkännande för att aktivera](pim-resource-roles-approval-workflow.md)

[Aktivera en roll](pim-resource-roles-use-the-audit-log.md)


