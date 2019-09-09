---
title: Utöka eller förnya Azure-resursens roll tilldelningar i PIM-Azure Active Directory | Microsoft Docs
description: Lär dig hur du utökar eller förnyar roll tilldelningar för Azure-resurser i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/02/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eeba148945f7aa52dd32edc0fec4c45e2ab0748
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804126"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-pim"></a>Utöka eller förnya Azure-resursens roll tilldelningar i PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) introducerar nya kontroller för att hantera livs cykeln för åtkomst och tilldelning för Azure-resurser. Administratörer kan tilldela medlemskap med start-och Slutdatum/tid-egenskaper. När tilldelnings sluten närmar sig, skickar PIM e-postmeddelanden till de berörda användarna eller grupperna. Den skickar också e-postaviseringar till administratörer av resursen för att säkerställa att lämplig åtkomst upprätthålls. Tilldelningar kan förnyas och förbli synliga i ett utgånget tillstånd i upp till 30 dagar, även om åtkomsten inte är utökad.

## <a name="who-can-extend-and-renew"></a>Vem kan utöka och förnya?

Endast administratörer av resursen kan utöka eller förnya roll tilldelningar. Den berörda medlemmen kan begära att utöka roller som håller på att gå ut och begära att förnya roller som redan har upphört att gälla.

## <a name="when-are-notifications-sent"></a>När skickas meddelanden?

PIM skickar e-postaviseringar till administratörer och berörda medlemmar av roller som upphör att gälla inom 14 dagar och en dag innan de upphör att gälla. Den skickar ytterligare ett e-postmeddelande när en tilldelning officiellt upphör att gälla. 

Administratörer får aviseringar när en medlem i en förfallen eller utgångna roll begär Anden kan utökas eller förnyas. När en enskild administratör löser begäran meddelas alla andra administratörer om lösnings beslutet (godkänd eller nekad). Sedan meddelas den begär ande medlemmen av beslutet. 

## <a name="extend-role-assignments"></a>Utöka roll tilldelningar

Följande steg beskriver processen för att begära, matcha eller administrera ett tillägg eller förnya en roll tilldelning. 

### <a name="member-extend"></a>Utöka medlem

Medlemmar i en roll tilldelning kan utöka förfallna roll tilldelningar direkt från fliken **berättigade** eller **aktiva** på sidan **Mina roller** i en resurs och på sidan **Mina roller** på den översta nivån i PIM-portalen. Medlemmar kan begära att utöka kvalificerade och aktiva (tilldelade) roller som upphör att gälla under de kommande 14 dagarna.

![Azure-resurser – sidan mina roller visar kvalificerade roller med en åtgärds kolumn](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

När Tilldelningens slutdatum-tid är inom 14 dagar, blir knappen som **utöka** en aktiv länk i användar gränssnittet. I följande exempel antar vi att det aktuella datumet är 27 mars.

![Åtgärds kolumn med länkar att aktivera eller utöka](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

Om du vill begära en förlängning av den här roll tilldelningen väljer du **utöka** för att öppna formuläret för begäran.

![Utöka roll tilldelnings fönstret med en orsaks ruta](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

Om du vill visa information om den ursprungliga tilldelningen expanderar du **tilldelnings information**. Ange en orsak till tilläggs förfrågan och välj sedan **utöka**.

>[!Note]
>Vi rekommenderar att du inkluderar information om varför tillägget är nödvändigt och hur länge tillägget ska beviljas (om du har den här informationen).

![Utöka roll tilldelnings fönstret med tilldelnings Detaljer expanderat](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

Vid en stund får resurs administratörer ett e-postmeddelande som begär att de granskar tilläggs förfrågan. Om en begäran om utökning redan har skickats visas ett popup-meddelande överst i Azure Portal som förklarar felet.

![Meddelande som förklarar att det redan finns en befintlig väntande roll tilldelnings tillägg](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

Gå till sidan **väntande förfrågningar** i den vänstra rutan om du vill visa statusen för din begäran eller avbryta den.

![Sidan Azure-resurser – väntande förfrågningar visar alla väntande begär Anden och en länk för att avbryta](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approve"></a>Godkänn administratör

När en medlem skickar en begäran om att utöka en roll tilldelning får resurs administratörer ett e-postmeddelande som innehåller information om den ursprungliga tilldelningen och orsaken till begäran. Meddelandet innehåller en direkt länk till förfrågan för administratören om att godkänna eller neka. 

Förutom att använda följande länk från e-post kan administratörer godkänna eller Neka förfrågningar genom att gå till PIM-administrations portalen och välja **Godkänn begär Anden** i det vänstra fönstret.

![Azure-resurser – sidan Godkänn begär Anden visar begär Anden och länkar till Godkänn eller neka](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

När en administratör väljer **Godkänn** eller **neka**visas information om begäran, tillsammans med ett fält för att ange motivering för gransknings loggarna.

![Godkänn begäran om roll tilldelning med begär ande orsak, tilldelnings typ, start tid, slut tid och orsak](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

När en begäran om att utöka roll tilldelningen godkänns kan resurs administratörer välja ett nytt start datum, slutdatum och tilldelnings typ. Det kan vara nödvändigt att ändra tilldelnings typ om administratören vill ge begränsad åtkomst för att slutföra en viss uppgift (till exempel en dag). I det här exemplet kan administratören ändra tilldelningen från **tillgänglig** till **aktiv**. Det innebär att de kan ge åtkomst till begär Ande utan att behöva aktivera dem.

### <a name="admin-extend"></a>Administratören utökade

Om en roll medlem glömmer bort eller inte kan begära ett tillägg för roll medlemskap, kan en administratör utöka en tilldelning för medlemmens räkning. Administrativa tillägg för roll medlemskap kräver inte godkännande, men meddelanden skickas till alla andra administratörer när rollen har utökats.

Om du vill utöka ett roll medlemskap bläddrar du till resurs rollen eller vyn medlem i PIM. Hitta den medlem som kräver ett tillägg. Välj sedan **utöka** i kolumnen åtgärd.

![Sidan Azure-resurser – medlemmar som visar kvalificerade roller med länkar att utöka](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>Förnya roll tilldelningar

Medan det konceptuellt liknar processen för att begära ett tillägg är processen att förnya en förfallen roll tilldelning annorlunda. Med hjälp av följande steg kan medlemmar och administratörer förnya åtkomsten till utgångna roller vid behov.

### <a name="member-renew"></a>Förnya medlem

Medlemmar som inte längre har åtkomst till resurser kan komma åt upp till 30 dagar från den förfallna tilldelnings historiken. Det gör du genom att bläddra till **Mina roller** i den vänstra rutan och sedan välja fliken **Utgångna roller** i avsnittet Azure-resurs roller.

![Sidan mina roller-fliken roller har förfallit](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

Listan över roller som visas som standard för **berättigade roller**. Använd den nedrullningsbara menyn för att växla mellan kvalificerade och aktiva tilldelade roller.

Om du vill begära förnyelse för någon av roll tilldelningarna i listan väljer du åtgärden **förnya** . Ange sedan en orsak för begäran. Det är praktiskt att ange en varaktighet Förutom eventuell ytterligare kontext som hjälper resurs administratören att bestämma att godkänna eller neka.

![Fönstret förnya roll tilldelning visar rutan orsak](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

När begäran har skickats meddelas resurs administratörer om en väntande begäran om att förnya en roll tilldelning.

### <a name="admin-approves"></a>Admin godkänner

Resurs administratörer kan komma åt begäran om förnyelse från länken i e-postmeddelandet eller genom att komma åt PIM från Azure Portal och välja **Godkänn förfrågningar** i den vänstra rutan.

![Azure-resurser – sidan Godkänn begär Anden visar begär Anden och länkar till Godkänn eller neka](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

När en administratör väljer **Godkänn** eller **neka**visas information om begäran tillsammans med ett fält för att ange motivering för gransknings loggarna.

![Godkänn begäran om roll tilldelning med begär ande orsak, tilldelnings typ, start tid, slut tid och orsak](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

När en begäran om att förnya roll tilldelningen godkänns måste resurs administratörer ange ett nytt start datum, slutdatum och tilldelnings typ. 

### <a name="admin-renew"></a>Administratören förnyade

Resurs administratörer kan förnya förfallna roll tilldelningar från fliken **medlemmar** i den vänstra navigerings menyn i en resurs. De kan också förnya förfallna roll tilldelningar från fliken **Utgångna** roller i en resurs roll.

Om du vill visa en lista över alla förfallna roll tilldelningar på skärmen **medlemmar** väljer du **förfallna roller**.

![Azure-resurser – sidan medlemmar har förfallit roller med länkar att förnya](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>Nästa steg

- [Godkänn eller neka begär Anden för Azure Resource roles i PIM](pim-resource-roles-approval-workflow.md)
- [Konfigurera inställningar för Azure-resurs roller i PIM](pim-resource-roles-configure-role-settings.md)
