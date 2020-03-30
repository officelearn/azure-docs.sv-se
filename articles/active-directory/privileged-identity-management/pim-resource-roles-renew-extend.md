---
title: Förnya Azure-resursrolltilldelningar i PIM - Azure AD | Microsoft-dokument
description: Lär dig hur du utökar eller förnyar Azure-resursrolltilldelningar i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 577c028582bc3b23d13e71522bb83db558065ee2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022901"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-privileged-identity-management"></a>Utöka eller förnya Azure-resursrolltilldelningar i Privilegierad identitetshantering

Azure Active Directory (Azure AD) Privilegierad identitetshantering (PIM) innehåller kontroller för att hantera åtkomst- och tilldelningslivscykeln för Azure-resurser. Administratörer kan tilldela roller med hjälp av datumtidsegenskaper för start och. När tilldelningen avslutas skickas e-postmeddelanden till de berörda användarna eller grupperna. Det skickar också e-postmeddelanden till administratörer av resursen för att säkerställa att lämplig åtkomst upprätthålls. Tilldelningar kan förnyas och förbli synliga i ett utgånget tillstånd i upp till 30 dagar, även om åtkomsten inte utökas.

## <a name="who-can-extend-and-renew"></a>Vem kan förlänga och förnya?

Endast administratörer av resursen kan utöka eller förnya rolltilldelningar. Den berörda användaren eller gruppen kan begära att utöka roller som håller på att upphöra att gälla och begära att förnya roller som redan har upphört att gälla.

## <a name="when-are-notifications-sent"></a>När skickas meddelanden?

Privilegierad identitetshantering skickar e-postmeddelanden till administratörer och berörda användare eller grupper av roller som löper ut inom 14 dagar och en dag före förfallodatumet. Den skickar ytterligare ett e-postmeddelande när en tilldelning officiellt upphör att gälla.

Administratörer får meddelanden när en användare eller grupp har tilldelat en rollbegäran om att förlänga eller förnya en utgående eller utgångna roll. När en viss administratör löser begäran meddelas alla andra administratörer om beslutet om lösning (godkänt eller nekat). Därefter meddelas den begärande användaren eller gruppen om beslutet.

## <a name="extend-role-assignments"></a>Utöka rolltilldelningar

I följande steg beskrivs processen för att begära, lösa eller administrera ett tillägg eller en förnyelse av en rolltilldelning.

### <a name="self-extend-expiring-assignments"></a>Självutförlängande tilldelningar

Användare eller grupper som tilldelats en roll kan utöka förfallobara rolltilldelningar direkt från fliken **Kvalificerad** eller **Aktiv** på sidan **Mina roller** i en resurs och från sidan Mina roller på den högsta nivån **i** portalen För privilegierad identitetshantering. Användare eller grupper kan begära att utöka kvalificerade och aktiva (tilldelade) roller som upphör att gälla inom de närmaste 14 dagarna.

![Sidan Azure-resurser – sidan Mina roller med kvalificerade roller med kolumnen Åtgärd](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

När tilldelningens slutdatumtid är inom 14 dagar blir knappen **till Extend** en aktiv länk i användargränssnittet. I följande exempel antar du att det aktuella datumet är den 27 mars.

![Åtgärdskolumn med länkar till Aktivera eller Utöka](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Om du vill begära en tillägg av den här rolltilldelningen väljer du **Utöka** för att öppna formuläret för begäran.

![Utöka rolltilldelningsfönstret med rutan Orsak](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Om du vill visa information om den ursprungliga tilldelningen expanderar du **Tilldelningsinformation**. Ange en orsak till tilläggsbegäran och välj sedan **Utöka**.

>[!NOTE]
>Vi rekommenderar att du inkluderar information om varför tillägget är nödvändigt och hur länge tillägget ska beviljas (om du har den här informationen).

![Utöka tilldelningsfönstret för roll med tilldelningsinformation expanderad](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

I några ögonblick får resursadministratörer ett e-postmeddelande där de begär att de ska granska tilläggsbegäran. Om en begäran om att utöka redan har skickats visas ett Azure-meddelande i portalen.

![Meddelande som förklarar att det redan finns ett befintligt väntande tillägg till rolltilldelning](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Gå till sidan **Väntande begäranden** för att visa status för din begäran eller avbryta den.

![Azure-resurser – Väntande begäranden visar alla väntande begärda och en länk till Avbryt](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Administratörsgodkändt tillägg

När en användare eller grupp skickar en begäran om att utöka en rolltilldelning får resursadministratörer ett e-postmeddelande som innehåller information om den ursprungliga tilldelningen och orsaken till begäran. Meddelandet innehåller en direkt länk till begäran om att administratören ska godkänna eller neka.

Förutom att följa länken från e-post kan administratörer godkänna eller neka begäranden genom att gå till administrationsportalen för privilegierad identitetshantering och välja **Godkänn begäranden** i den vänstra rutan.

![Azure-resurser – Godkänn begäranden som anger begäranden och länkar för att godkänna eller neka](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

När en administratör väljer **Godkänn** eller **Neka**visas information om begäran tillsammans med ett fält för att ge granskningsloggarna en affärsmotivering.

![Godkänna rolltilldelningsbegäran med requestor-orsak, tilldelningstyp, starttid, sluttid och orsak](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

När du godkänner en begäran om att utöka rolltilldelningen kan resursadministratörer välja ett nytt startdatum, slutdatum och tilldelningstyp. Det kan vara nödvändigt att ändra tilldelningstyp om administratören vill ge begränsad åtkomst för att slutföra en viss uppgift (till exempel en dag). I det här exemplet kan administratören ändra tilldelningen från **Kvalificerad** till **Aktiv**. Detta innebär att de kan ge åtkomst till beställaren utan att kräva att de aktiveras.

### <a name="admin-initiated-extension"></a>Administratör initierat tillägg

Om en användare som tilldelats en roll inte begär ett tillägg för rolltilldelningen kan en administratör utöka en tilldelning för användarens räkning. Administrativa tillägg av rolltilldelning kräver inte godkännande, men meddelanden skickas till alla andra administratörer när rollen har utökats.

Om du vill utöka en rolltilldelning bläddrar du till resursrollen eller tilldelningsvyn i Privilegierad identitetshantering. Hitta tilldelningen som kräver ett tillägg. Välj sedan **Utöka** i åtgärdskolumnen.

![Azure-resurser – tilldelningar sida med kvalificerade roller med länkar för att utöka](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Förnya rolltilldelningar

Även begreppsmässigt liknar processen för att begära ett tillägg, är processen för att förnya en förfallen roll tilldelning annorlunda. Med hjälp av följande steg kan tilldelningar och administratörer förnya åtkomsten till utgångna roller vid behov.

### <a name="self-renew"></a>Självförnyande

Användare som inte längre kan komma åt resurser kan komma åt upp till 30 dagar med förfallen tilldelningshistorik. För att göra detta bläddrar de till **Mina roller** i den vänstra rutan och väljer sedan fliken **Utgångna roller** i avsnittet Azure-resursroller.

![Sidan Mina roller - Fliken Utgångna roller](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

Listan över roller som visas som standard **för kvalificerade roller**. Använd den nedrullningsbara menyn för att växla mellan kvalificerade och aktiva tilldelade roller.

Om du vill begära förnyelse för någon av rolltilldelningarna i listan väljer du åtgärden **Förnya.** Ange sedan en orsak till begäran. Det är bra att ange en varaktighet utöver eventuella ytterligare kontexter eller en affärsmotivering som kan hjälpa resursadministratören att välja att godkänna eller neka.

![Fönstret Förnya rolltilldelning med rutan Orsak](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

När begäran har skickats meddelas resursadministratörer om en väntande begäran om att förnya en rolltilldelning.

### <a name="admin-approves"></a>Administratör godkänner

Resursadministratörer kan komma åt förnyelsebegäran från länken i e-postmeddelandet eller genom att komma åt privilegierad identitetshantering från Azure-portalen och välja **Godkänn begäranden** från den vänstra rutan.

![Azure-resurser – Godkänn begäranden som anger begäranden och länkar för att godkänna eller neka](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

När en administratör väljer **Godkänn** eller **Neka**visas information om begäran tillsammans med ett fält för att ange en affärsmotivering för granskningsloggarna.

![Godkänna rolltilldelningsbegäran med requestor-orsak, tilldelningstyp, starttid, sluttid och orsak](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

När du godkänner en begäran om att förnya rolltilldelningen måste resursadministratörer ange ett nytt startdatum, slutdatum och tilldelningstyp.

### <a name="admin-renew"></a>Admin förnya

Resursadministratörer kan förnya utgångna rolltilldelningar från fliken **Medlemmar** på den vänstra navigeringsmenyn för en resurs. De kan också förnya utgångna rolltilldelningar inifrån fliken **Utgångna** roller för en resursroll.

Om du vill visa en lista över alla utgångna rolltilldelningar väljer du **Utgångna roller**på skärmen **Medlemmar** .

![Azure-resurser – sidoring av medlemmar har utgångna roller med länkar för att förnya](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Nästa steg

- [Godkänna eller neka begäranden om Azure-resursroller i Privilegierad identitetshantering](pim-resource-roles-approval-workflow.md)
- [Konfigurera Azure-resursrollinställningar i Privilegierad identitetshantering](pim-resource-roles-configure-role-settings.md)
