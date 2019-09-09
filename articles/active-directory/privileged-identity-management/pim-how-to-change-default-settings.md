---
title: Konfigurera inställningar för Azure AD-roller i PIM-Azure Active Directory | Microsoft Docs
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
ms.date: 05/31/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9252e3bb8ccddb810074b485f6f073f1bda3f05
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804433"
---
# <a name="configure-azure-ad-role-settings-in-pim"></a>Konfigurera inställningar för Azure AD-roller i PIM

En privilegie rad roll administratör kan anpassa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) i organisationen, inklusive att ändra upplevelsen för en användare som aktiverar en berättigad roll tilldelning.

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

## <a name="notifications"></a>Aviseringar

Använd växeln **meddelanden** för att ange om administratörer ska få e-postaviseringar när roller aktive ras. Detta kan vara användbart för att upptäcka otillåtna eller illegitimatea aktiveringar.

När inställningen är **aktive**rad skickas meddelanden till:

- Privilegierad rolladministratör
- Säkerhetsadministratör
- Global administratör

Mer information finns i [e-postmeddelanden i PIM](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Incident/begär biljett

Använd växeln **incident/begär biljett** för att ange om du vill att berättigade administratörer ska inkludera ett biljett nummer när de aktiverar sin roll. Detta kan vara användbart när du utför granskning av roll åtkomst.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Använd växeln **Multi-Factor Authentication** för att ange om du vill kräva att användarna verifierar sin identitet med MFA innan de kan aktivera sina roller. De behöver bara verifiera denna gång per session, inte varje gång de aktiverar en roll. Det finns två tips att tänka på när du aktiverar MFA:

* Användare som har Microsoft-konton för sina e-postadresser @outlook.com(vanligt vis, men inte alltid) kan inte registreras för Azure MFA. Om du vill tilldela roller till användare med Microsoft-konton bör du antingen göra dem permanenta administratörer eller inaktivera MFA för den rollen.
* Du kan inte inaktivera MFA för privilegierade roller för Azure AD och Office365. Detta är en säkerhetsfunktion eftersom rollerna bör skyddas noggrant:  
  
  * Azure Information Protection administratör
  * Faktureringsadministratör
  * Molnprogramadministratör
  * Efterlevnadsadministratör
  * Administratör för villkorsstyrd åtkomst
  * Tjänstadministratör för CRM
  * Godkännare av åtkomst till Customer LockBox
  * Katalogskrivare
  * Exchange-administratör
  * Global administratör
  * Administratör för Intune-tjänsten
  * Power BI-tjänstadministratör
  * Privilegierad rolladministratör
  * Säkerhetsadministratör
  * SharePoint-tjänstadministratör
  * Skype for Business-administratör
  * Användaradministratör

Mer information finns i [Multi-Factor Authentication (MFA) och PIM](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Godkännande krävs

Följ dessa steg om du vill kräva godkännande för att aktivera en roll.

1. Ange växeln **Kräv godkännande** till **aktive rad**. Fönstret expanderar med alternativ för att välja god kännare.

    ![Azure AD-roller – inställningar – Kräv godkännande](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Om du **inte** anger några god kännare blir de privilegierade roll administratörerna som standard god kännare. Privilegierade roll administratörer måste godkänna **alla** aktiverings begär Anden för den här rollen.

1. Om du vill ange god kännare klickar du på **Välj god kännare**.

    ![Azure AD-roller – inställningar – Kräv godkännande](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Välj en eller flera god kännare och klicka sedan på **Välj**. Du kan välja användare eller grupper. Minst 2 god kännare rekommenderas. Själv godkännande är inte tillåtet.

    Dina val kommer att visas i listan över valda god kännare.

1. När du har angett alla dina roll inställningar klickar du på **Spara** för att spara ändringarna.


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure AD-roller i PIM](pim-how-to-add-role-to-user.md)
- [Konfigurera säkerhets aviseringar för Azure AD-roller i PIM](pim-how-to-configure-security-alerts.md)
