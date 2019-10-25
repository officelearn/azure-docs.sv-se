---
title: Konfigurera säkerhets aviseringar för Azure AD-roller i PIM-Azure Active Directory | Microsoft Docs
description: Lär dig hur du konfigurerar säkerhets aviseringar för Azure AD-roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e188ef651f6fe539932cf1670f914e8b57564567
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809081"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>Konfigurera säkerhets aviseringar för Azure AD-roller i Privileged Identity Management

Privileged Identity Management (PIM) genererar aviseringar när det finns misstänkt eller osäker aktivitet i din Azure Active Directory (Azure AD) organisation. När en avisering utlöses visas den på Privileged Identity Management instrument panelen. Välj aviseringen om du vill visa en rapport med en lista över de användare eller roller som utlöste aviseringen.

![Azure AD-roller – aviserings fönstret visar aviseringar och allvarlighets grad](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Säkerhetsaviseringar

I det här avsnittet visas alla säkerhets aviseringar för Azure AD-roller, tillsammans med hur du åtgärdar och förhindrar. Allvarlighets graden har följande betydelse:

- **Hög**: kräver omedelbar åtgärd på grund av en princip överträdelse.
- **Medels Tor**: kräver ingen omedelbar åtgärd, men signalerar en eventuell princip överträdelse.
- **Låg**: kräver ingen omedelbar åtgärd, men föreslår en princip ändring som föredras.

### <a name="administrators-arent-using-their-privileged-roles"></a>Administratörer använder inte sina privilegierade roller

| | |
| --- | --- |
| **Allvarlighets grad** | Låg |
| **Varför får jag den här aviseringen?** | Användare som har tilldelats privilegierade roller som de inte behöver ökar risken för angrepp. Det är också enklare för angripare att befinna sig i konton som inte används aktivt. |
| **Så här löser du?** | Granska användarna i listan och ta bort dem från privilegierade roller som de inte behöver. |
| **Åtgärder** | Tilldela endast privilegierade roller till användare som har en affärs motivering. </br>Schemalägg vanliga [åtkomst granskningar](pim-how-to-start-security-review.md) för att kontrol lera att användarna fortfarande behöver åtkomst. |
| **Åtgärd som åtgärdar åtgärder i portalen** | Tar bort kontot från deras privilegierade roller. |
| **Utlösare** | Utlöses om en användare går över ett angivet antal dagar utan att aktivera en roll. |
| **Antal dagar** | Den här inställningen anger det maximala antalet dagar, från 0 till 100, som en användare kan gå utan att aktivera en roll.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Roller kräver inte Multi-Factor Authentication för aktivering

| | |
| --- | --- |
| **Allvarlighets grad** | Låg |
| **Varför får jag den här aviseringen?** | Utan Multi-Factor Authentication kan komprometterade användare aktivera privilegierade roller. |
| **Så här löser du?** | Granska listan över roller och [Kräv Multi-Factor Authentication](pim-how-to-change-default-settings.md) för varje roll. |
| **Åtgärder** | [KRÄV MFA](pim-how-to-change-default-settings.md) för varje roll.  |
| **Åtgärd som åtgärdar åtgärder i portalen** | Gör Multi-Factor Authentication nödvändigt för aktivering av den privilegierade rollen. |

### <a name="the-tenant-doesnt-have-azure-ad-premium-p2"></a>Klienten har inte Azure AD Premium P2

| | |
| --- | --- |
| **Allvarlighets grad** | Låg |
| **Varför får jag den här aviseringen?** | Den aktuella klient organisationen har inte Azure AD Premium P2. |
| **Så här löser du?** | Läs mer om [Azure AD-versioner](../fundamentals/active-directory-whatis.md). Uppgradera till Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potentiella inaktuella konton i en privilegie rad roll

| | |
| --- | --- |
| **Allvarlighets grad** | Medium |
| **Varför får jag den här aviseringen?** | Konton i en privilegie rad roll har inte ändrat sitt lösen ord under de senaste 90 dagarna. Dessa konton kan vara tjänst eller delade konton som inte underhålls och är utsatta för angripare. |
| **Så här löser du?** | Granska kontona i listan. Om de inte längre behöver åtkomst tar du bort dem från deras privilegierade roller. |
| **Åtgärder** | Se till att konton som delas roterar starka lösen ord när det sker en ändring i användare som känner till lösen ordet. </br>Granska regelbundet konton med privilegierade roller med [åtkomst granskningar](pim-how-to-start-security-review.md) och ta bort roll tilldelningar som inte längre behövs. |
| **Åtgärd som åtgärdar åtgärder i portalen** | Tar bort kontot från deras privilegierade roller. |
| **Bästa praxis** | Konton för delade, tjänster och nödfalls åtkomst som autentiserar med ett lösen ord och som har tilldelats privilegierade administrativa roller som global administratör eller säkerhets administratör bör ha sina lösen ord roterade för följande fall:<ul><li>Efter en säkerhets incident som inbegriper missbruk eller komprometterande av administrativa rättigheter</li><li>När en användares behörigheter har ändrats så att de inte längre är administratörer (till exempel efter att en medarbetare som var administratör lämnar den eller lämnar organisationen)</li><li>Med jämna mellanrum (till exempel kvartals vis eller årliga), även om det inte fanns något känt intrång eller ändra till IT-personal</li></ul>Eftersom flera personer har åtkomst till dessa konto uppgifter bör autentiseringsuppgifterna roteras så att personer som har lämnat sina roller inte längre kan komma åt kontona. [Läs mer](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>Roller tilldelas utanför Privileged Identity Management

| | |
| --- | --- |
| **Allvarlighets grad** | Hög |
| **Varför får jag den här aviseringen?** | Privilegierade roll tilldelningar som görs utanför Privileged Identity Management övervakas inte korrekt och kan tyda på en aktiv attack. |
| **Så här löser du?** | Granska användarna i listan och ta bort dem från privilegierade roller som tilldelats utanför Privileged Identity Management. |
| **Åtgärder** | Undersök var användarna tilldelas privilegierade roller utanför Privileged Identity Management och förhindra framtida tilldelningar därifrån. |
| **Åtgärd som åtgärdar åtgärder i portalen** | Tar bort användaren från den privilegierade rollen. |

### <a name="there-are-too-many-global-administrators"></a>Det finns för många globala administratörer

| | |
| --- | --- |
| **Allvarlighets grad** | Låg |
| **Varför får jag den här aviseringen?** | Global administratör är den högsta privilegierade rollen. Om en global administratör komprometteras får angriparen åtkomst till alla deras behörigheter, vilket innebär att hela systemet är utsatt för risk. |
| **Så här löser du?** | Granska användarna i listan och ta bort alla som inte absolut behöver rollen som global administratör. </br>Tilldela de här användarna lägre privilegierade roller i stället. |
| **Åtgärder** | Tilldela användare den minst privilegierade rollen som de behöver. |
| **Åtgärd som åtgärdar åtgärder i portalen** | Tar bort kontot från deras privilegierade roller. |
| **Utlösare** | Utlöses om två olika villkor uppfylls och du kan konfigurera båda. Först måste du uppnå ett visst tröskelvärde för globala administratörer. För det andra måste en viss procent andel av dina totala roll tilldelningar vara globala administratörer. Om du bara uppfyller en av dessa mått visas inte aviseringen. |
| **Minsta antal globala administratörer** | Den här inställningen anger antalet globala administratörer, från 2 till 100, som du anser vara för få för din Azure AD-organisation. |
| **Procent andel globala administratörer** | Den här inställningen anger den lägsta procent andelen av administratörer som är globala administratörer, från 0 till 100% under vilka du inte vill att din Azure AD-organisation ska vara DIP. |

### <a name="roles-are-being-activated-too-frequently"></a>Roller aktive ras för ofta

| | |
| --- | --- |
| **Allvarlighets grad** | Låg |
| **Varför får jag den här aviseringen?** | Flera aktiveringar till samma privilegierade roll av samma användare är ett tecken på ett angrepp. |
| **Så här löser du?** | Granska användarna i listan och kontrol lera att [aktiverings tiden](pim-how-to-change-default-settings.md) för deras privilegierade roll är tillräckligt lång för att de ska kunna utföra sina uppgifter. |
| **Åtgärder** | Kontrol lera att [aktiverings tiden](pim-how-to-change-default-settings.md) för privilegierade roller är tillräckligt lång för att användarna ska kunna utföra sina uppgifter.</br>[Kräv Multi-Factor Authentication](pim-how-to-change-default-settings.md) för privilegierade roller som har konton som delas av flera administratörer. |
| **Åtgärd som åtgärdar åtgärder i portalen** | Gäller inte |
| **Utlösare** | Utlöses om en användare aktiverar samma privilegierade roll flera gånger inom en angiven period. Du kan konfigurera både tids perioden och antalet aktiveringar. |
| **Tidsram för aktiverings förnyelse** | Den här inställningen anger i dagar, timmar, minuter och sekund den tids period som du vill använda för att spåra misstänkta förnyelser. |
| **Antal aktiverings förnyelser** | Den här inställningen anger antalet aktiveringar från 2 till 100, där du vill bli meddelad inom den tidsram som du har valt. Du kan ändra den här inställningen genom att flytta skjutreglaget eller skriva ett tal i text rutan. |

## <a name="configure-security-alert-settings"></a>Konfigurera säkerhets aviserings inställningar

Du kan anpassa några av säkerhets aviseringarna i Privileged Identity Management att arbeta med organisationens behov och säkerhets mål. Följ de här stegen för att öppna säkerhets aviserings inställningarna:

1. Öppna **Privileged Identity Management** i Azure AD.

1. Välj **Azure AD-roller**.

1. Välj **Inställningar** och sedan **aviseringar**.

    ![Azure AD-roller – inställningar med valda aviseringar](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Välj ett aviserings namn för att konfigurera inställningen för den aviseringen.

    ![För den valda aviseringen, fönstret säkerhets varnings inställningar](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera inställningar för Azure AD-roller i Privileged Identity Management](pim-how-to-change-default-settings.md)
