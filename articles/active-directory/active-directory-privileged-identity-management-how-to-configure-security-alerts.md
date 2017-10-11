---
title: "Så här konfigurerar du säkerhetsaviseringar | Microsoft Docs"
description: "Lär dig hur du konfigurerar du säkerhetsvarningar för Azure Privileged Identity Management-tillägg."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 4e0c911a-36c6-42a0-8f79-a01c03d2d04f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: e057120e31eeebc3da274536c09d6d9972854338
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-configure-security-alerts-in-azure-ad-privileged-identity-management"></a>Så här konfigurerar du säkerhetsaviseringar i Azure AD Privileged Identity Management
## <a name="security-alerts"></a>Säkerhetsaviseringar
Azure Privileged Identity Management (PIM) genererar aviseringar när det är misstänkt eller unsafe aktivitet i din miljö. När en avisering utlöses visas den på instrumentpanelen PIM. Välj vilken avisering du vill se en rapport som listar användare eller roller som utlöste aviseringen.

![PIM instrumentpanelen säkerhetsaviseringar – skärmbild][1]

| Varning | Utlösare | Rekommendation |
| --- | --- | --- |
| **Roller tilldelas utanför PIM** |En administratör har permanent tilldelade till en roll, utanför PIM-gränssnittet. |Granska ny rolltilldelning. Eftersom andra tjänster kan endast tilldela permanenta administratörer måste du ändra den till en berättigad tilldelning om det behövs. |
| **Roller som ska aktiveras för ofta** |Det fanns för många omaktiveringar av samma roll inom den tiden som tillåts i inställningarna. |Kontakta användaren för att se varför de har aktiverat rollen så många gånger. Tidsgränsen är kanske för kort för att slutföra sina uppgifter eller kanske de använder skript aktivera rollen automatiskt. |
| **Roller som inte kräver autentisering på flera plan för aktivering** |Det finns roller utan MFA är aktiverat i inställningarna. |Vi kräver MFA för de flesta mycket Privilegierade roller, men rekommenderar starkt att du aktiverar MFA för aktivering av alla roller. |
| **Administratörer som inte använder sina Privilegierade roller** |Det finns tillgängliga administratörer som inte har aktiverat sina roller nyligen. |Starta en åtkomst-granskning för att avgöra vilka användare som inte längre behöver åtkomst. |
| **Det finns för många globala administratörer** |Det finns fler globala administratörer än vad som rekommenderas. |Om du har ett stort antal globala administratörer är det troligt att användare får fler behörigheter än de behöver. Flytta användare till mindre privilegierade roller eller se några av dem tillgängliga för rollen i stället för permanent tilldelade. |

## <a name="configure-security-alert-settings"></a>Konfigurera säkerhetsinställningar för avisering
Du kan anpassa vissa av säkerhetsaviseringar i PIM att arbeta med din miljö och säkerhetsmål. Följ dessa steg för att komma åt inställningsbladet:

1. Logga in på den [Azure-portalen](https://portal.azure.com/) och välj den **Azure AD Privileged Identity Management** panelen från instrumentpanelen.
2. Välj **hanteras Privilegierade roller** > **inställningar** > **aviseringar inställningar**.
   
    ![Navigera till säkerhetsinställningar för aviseringar][2]

### <a name="roles-are-being-activated-too-frequently-alert"></a>”Roller som ska aktiveras för ofta” avisering
Den här aviseringen utlöses om en användare aktiverar samma Privilegierade roll flera gånger inom en angiven tidsperiod. Du kan konfigurera både tidsperioden och antalet aktiveringar.

* **Aktivera förnyelse tidsram**: Ange i dagar, timmar, minuter och andra hur lång tid som du vill använda för att spåra misstänkta förnyelser.
* **Antal aktivering förnyelser**: Ange antalet aktiveringar från 2 till 100, som du anser vara worthy för avisering under den tidsperiod som du har valt. Du kan ändra den här inställningen genom att flytta skjutreglaget eller ange ett värde i textrutan.

### <a name="there-are-too-many-global-administrators-alert"></a>”Det finns för många globala administratörer” avisering
PIM utlöser den här aviseringen om två olika villkor är uppfyllda och du kan konfigurera båda. Du måste först att nå ett tröskelvärde för globala administratörer. En viss procentandel av din totala rolltilldelningar måste dessutom vara globala administratörer. Om du bara uppfyller något av dessa mätningar aviseringen inte visas.  

* **Minsta antal globala administratörer**: Ange antalet globala administratörer från 2 till 100, ett belopp som osäkra du.
* **Procentandelen globala administratörer**: Ange vilken procentandel av administratörer som är globala administratörer är från 0 till 100%, som osäkra i din miljö.

### <a name="administrators-arent-using-their-privileged-roles-alert"></a>”Administratörer inte använder sina Privilegierade roller” avisering
Den här aviseringen utlöses om en användare blir mängden tid utan att aktivera en roll.

* **Antal dagar**: Ange hur många dagar från 0 till 100, som en användare kan gå utan att aktivera en roll.

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_dash.png
[2]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_settings.png
