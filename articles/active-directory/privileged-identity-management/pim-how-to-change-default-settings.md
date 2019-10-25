---
title: Konfigurera inställningar för Azure AD-roller i Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Lär dig hur du konfigurerar inställningar för Azure AD-roller i Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: cff298e24ac185767e6290e396818ccece7b9b55
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809147"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Konfigurera inställningar för Azure AD-roller i Privileged Identity Management

En privilegie rad roll administratör kan anpassa Privileged Identity Management (PIM) i sin Azure Active Directory (Azure AD) organisation, inklusive ändra upplevelsen för en användare som aktiverar en berättigad roll tilldelning.

## <a name="open-role-settings"></a>Öppna roll inställningar

Följ de här stegen för att öppna inställningarna för en Azure AD-roll.

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure AD-roller**.

1. Klicka på **Inställningar**.

    ![Azure AD-roller – inställningar](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Klicka på **roller**.

1. Klicka på den roll vars inställningar du vill konfigurera.

    ![Azure AD-roller – inställnings roller](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    På sidan Inställningar för varje roll finns det flera inställningar som du kan konfigurera. De här inställningarna påverkar endast användare som är **berättigade** tilldelningar, inte **permanenta** tilldelningar.

## <a name="activations"></a>Aktiveringar

Använd skjutreglaget **aktiveringar** för att ange den maximala tiden, i timmar, som en roll förblir aktiv innan den upphör att gälla. Värdet kan vara mellan 1 och 72 timmar.

## <a name="notifications"></a>Meddelanden

Använd växeln **meddelanden** för att ange om administratörer ska få e-postaviseringar när roller aktive ras. Det här meddelandet kan vara användbart för att upptäcka otillåtna eller illegitimatea aktiveringar.

När inställningen är **aktive**rad skickas meddelanden till:

- Privilegie rad roll administratör
- Säkerhetsadministratör
- Global administratör

Mer information finns i [e-postmeddelanden i Privileged Identity Management](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Incident/begär biljett

Använd växeln **incident/begär biljett** för att kräva att berättigade administratörer inkluderar ett biljett nummer när de aktiverar sin roll. Den här metoden kan göra granskningar av roll åtkomsten mer effektiva.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Använd växeln **Multi-Factor Authentication** för att ange om du vill kräva att användarna verifierar sin identitet med MFA innan de kan aktivera sina roller. De behöver bara verifiera sin identitet en gång per session, inte varje gång de aktiverar en roll. Det finns två tips att tänka på när du aktiverar MFA:

- Användare som har Microsoft-konton för sina e-postadresser (vanligt vis @outlook.com, men inte alltid) kan inte registrera sig för Azure Multi-Factor Authentication. Om du vill tilldela roller till användare med Microsoft-konton bör du antingen göra dem permanenta administratörer eller inaktivera Multi-Factor Authentication för rollen.
- Det går inte att inaktivera Azure-Multi-Factor Authentication för mycket privilegierade roller för Azure AD och Office 365. Den här säkerhetsfunktionen hjälper till att skydda följande roller:  
  
  - Azure Information Protection administratör
  - Faktureringsadministratör
  - Moln program administratör
  - Administratör för efterlevnad
  - Administratör för villkorsstyrd åtkomst
  - Dynamics 365-administratör
  - Åtkomst god kännare för kund lås
  - Katalog skrivare
  - Exchange-administratör
  - Global administratör
  - Intune-administratör
  - Power BI administratör
  - Privilegie rad roll administratör
  - Säkerhetsadministratör
  - SharePoint-administratör
  - Skype för företag-administratör
  - Användar administratör

Mer information finns i [Multi-Factor Authentication och Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Kräv godkännande

Följ dessa steg om du vill delegera det godkännande som krävs för att aktivera en roll.

1. Ange växeln **Kräv godkännande** till **aktive rad**. Fönstret expanderar med alternativ för att välja god kännare.

    ![Azure AD-roller – inställningar – Kräv godkännande](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Om du inte anger några god kännare blir den privilegierade roll administratören som standard god kännare och krävs sedan för att godkänna alla aktiverings begär Anden för den här rollen.

1. Om du vill ange god kännare klickar du på **Välj god kännare**.

    ![Azure AD-roller – inställningar – Kräv godkännande](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Välj en eller flera god kännare förutom den privilegierade roll administratören och klicka sedan på **Välj**. Du kan välja användare eller grupper. Vi rekommenderar minst två god kännare. Även om du lägger till dig själv som god kännare kan du inte själv godkänna en roll aktivering. Dina val kommer att visas i listan över valda god kännare.

1. När du har angett alla dina roll inställningar väljer du **Spara** för att spara ändringarna.

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure AD-roller i Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Konfigurera säkerhets aviseringar för Azure AD-roller i Privileged Identity Management](pim-how-to-configure-security-alerts.md)
