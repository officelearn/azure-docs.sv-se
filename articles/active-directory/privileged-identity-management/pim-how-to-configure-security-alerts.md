---
title: Konfigurera säkerhetsaviseringar för Azure AD-katalogroller i PIM | Microsoft Docs
description: Lär dig hur du konfigurerar säkerhetsaviseringar för Azure AD-katalogroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 11/01/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e7204c223681b9a33c439b0d9fc653167422384a
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51011705"
---
# <a name="configure-security-alerts-for-azure-ad-directory-roles-in-pim"></a>Konfigurera säkerhetsaviseringar för Azure AD-katalogroller i PIM

Azure AD Privileged Identity Management (PIM) genererar aviseringar när det finns misstänkt eller osäkra aktivitet i din miljö. När en avisering utlöses, visas det i PIM-instrumentpanelen. Välj vilken avisering du vill se en rapport som visar den användare eller de roller som utlöste aviseringen.

![Säkerhetsaviseringar PIM - skärmbild](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Säkerhetsaviseringar

Det här avsnittet visar en lista över alla säkerhetsvarningar för katalogroller, samt hur du åtgärdar och hur du förhindrar. Allvarlighetsgrad har följande betydelse:

* **Hög**: kräver omedelbara åtgärder på grund av en Policyöverträdelse.
* **Medel**: inte kräver omedelbar åtgärd men signalerar potentiella Policyöverträdelse.
* **Låg**: inte kräver omedelbar åtgärd men en ändring av en bättre.

### <a name="roles-are-being-assigned-outside-of-pim"></a>Roller tilldelas utanför PIM

| | |
| --- | --- |
| **Allvarlighetsgrad** | Hög |
| **Varför får jag den här aviseringen?** | Privilegierade rolltilldelningar som skapats utanför PIM övervakas inte korrekt och kan tyda på ett pågående angrepp. |
| **Så här åtgärdar du?** | Granska användare i listan och ta bort dem från Privilegierade roller som tilldelats utanför PIM. |
| **Dataförlustskydd** | Undersök där användare som tilldelas Privilegierade roller utanför PIM och förhindra framtida tilldelningar därifrån. |
| **I portalen minskning åtgärd** | Tar bort kontot från sina Privilegierade roller. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potentiella inaktuella konton i en privilegierad roll

| | |
| --- | --- |
| **Allvarlighetsgrad** | Medel |
| **Varför får jag den här aviseringen?** | Konton som inte har ändrat sitt lösenord kanske tjänsten nyligen eller delade konton som inte är kvar. Dessa konton i Privilegierade roller är sårbara för angripare. |
| **Så här åtgärdar du?** | Granska kontona i listan. Om de inte längre behöver åtkomst kan du ta bort dem från sina Privilegierade roller. |
| **Dataförlustskydd** | Se till att konton som delas rotera starka lösenord när det finns en ändring i de användare som känner till lösenordet. </br>Regelbundet granska konton med Privilegierade roller med hjälp av åtkomstgranskningar och ta bort rolltilldelningar som inte längre behövs. |
| **I portalen minskning åtgärd** | Tar bort kontot från sina Privilegierade roller. |

### <a name="users-arent-using-their-privileged-roles"></a>Användarna använder inte sina Privilegierade roller

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här aviseringen?** | Användare som har tilldelats Privilegierade roller som de inte behöver ökar risken för angrepp. Det är även lättare för angripare att förbli oupptäckt i konton som inte används aktivt. |
| **Så här åtgärdar du?** | Granska användare i listan och ta bort dem från Privilegierade roller som de inte behöver. |
| **Dataförlustskydd** | Endast tilldela Privilegierade roller till användare som har en motivering. </br>Schema för regelbundna åtkomstgranskningar att verifiera att användarna fortfarande måste deras åtkomst. |
| **I portalen minskning åtgärd** | Tar bort kontot från sina Privilegierade roller. |
| **Utlösare** | Utlöses om en användare går en viss tidsperiod utan att aktivera en roll. |
| **Antal dagar** | Den här inställningen anger hur många dagar från 0 till 100, som en användare kan gå utan att aktivera en roll.|

### <a name="there-are-too-many-global-administrators"></a>Det finns för många globala administratörer

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här aviseringen?** | Global administratör är den högsta Privilegierade rollen. Om en Global administratör komprometteras får angripare åtkomst till alla sina behörigheter, vilket innebär hela systemet utsatt för risk. |
| **Så här åtgärdar du?** | Granska användare i listan och tar bort alla som inte behöver är absolut rollen Global administratör. </br>Tilldela dessa användare lägre Privilegierade roller. |
| **Dataförlustskydd** | Tilldela användare till den minst Privilegierade roller som de behöver. |
| **I portalen minskning åtgärd** | Tar bort kontot från sina Privilegierade roller. |
| **Utlösare** | Utlöses om två olika villkor är uppfyllda och du kan konfigurera båda. Du måste först når ett visst tröskelvärde för globala administratörer. En viss procentandel av din totala rolltilldelningar måste dessutom vara globala administratörer. Om du bara uppfyller något av de här mätningarna visas aviseringen inte. |
| **Minsta antal globala administratörer** | Den här inställningen anger antal globala administratörer från 2 till 100, att du överväger belopp som osäkra. |
| **Procentandel globala administratörer** | Den här inställningen anger den lägsta procentandelen av administratörer som är globala administratörer från 0% till 100 procent, som inte är säkert i din miljö. |

### <a name="roles-are-being-activated-too-frequently"></a>Roller som aktiveras för ofta

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här aviseringen?** | Flera aktiveringar till samma privilegierad roll av samma användare är ett tecken på en attack. |
| **Så här åtgärdar du?** | Granska användare i listan och se till att den [aktiveringsvaraktighet](pim-how-to-change-default-settings.md) för sina Privilegierade roller är inställd tillräckligt länge för att utföra sina uppgifter. |
| **Dataförlustskydd** | Se till att den [aktiveringsvaraktighet](pim-how-to-change-default-settings.md) för privilegierade roller har angetts tillräckligt länge för användare att utföra sina uppgifter.</br>[Kräva MFA](pim-how-to-change-default-settings.md) för privilegierade roller som har ett konto som delas av flera administratörer. |
| **I portalen minskning åtgärd** | Gäller inte |
| **Utlösare** | Utlöses om en användare aktiverar rollen för samma Privilegierade flera gånger inom en angiven tidsperiod. Du kan konfigurera både hur lång tid och antalet aktiveringar. |
| **Aktiveringsförnyelse** | Den här inställningen anger i dagar, timmar, minuter och andra hur lång tid som du vill använda för att spåra misstänkta förnyelser. |
| **Antal förnyelser för aktivering** | Den här inställningen anger antalet aktiveringar från 2 till 100, som du anser vara värt aviseringens under den tidsperiod som du har valt. Du kan ändra den här inställningen genom att flytta skjutreglaget eller ange ett värde i textrutan. |

### <a name="roles-dont-require-mfa-for-activation"></a>Roller som inte kräver MFA för aktivering

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här aviseringen?** | Utan MFA, kan komprometterade användare aktivera Privilegierade roller. |
| **Så här åtgärdar du?** | Granska listan över roller och [kräva MFA](pim-how-to-change-default-settings.md) för varje roll. |
| **Dataförlustskydd** | [Kräva MFA](pim-how-to-change-default-settings.md) för varje roll.  |
| **I portalen minskning åtgärd** | Gör MFA krävs för aktivering av Privilegierade roller. |

## <a name="configure-security-alert-settings"></a>Konfigurera säkerhetsaviseringsinställningar

Du kan anpassa vissa av säkerhetsvarningar i PIM att arbeta med din miljö och säkerhetsmål. Följ dessa steg för att öppna säkerhetsaviseringsinställningar:

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure AD-roller**.

1. Klicka på **inställningar** och sedan **aviseringar**.

    ![Navigera till säkerhetsinställningar för aviseringar](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Klicka på ett namn på avisering och konfigurera inställningen för den här aviseringen.

    ![Säkerhetsaviseringsinställningar](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Azure AD directory rollinställningar i PIM](pim-how-to-change-default-settings.md)
