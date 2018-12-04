---
title: Konfigurera Azure AD directory rollinställningar i PIM | Microsoft Docs
description: Lär dig hur du konfigurerar inställningar för Azure AD directory roll i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 11/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 87003a053823cfc257a3b6f41699feefadbbcc66
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52835173"
---
# <a name="configure-azure-ad-directory-role-settings-in-pim"></a>Konfigurera Azure AD directory rollinställningar i PIM

En administratör av Privilegierade roller kan anpassa Azure AD Privileged Identity Management (PIM) i deras organisation, inklusive ändra upplevelsen för en användare som är att aktivera en berättigad rolltilldelning.

## <a name="open-role-settings"></a>Öppna rollinställningar

Följ stegen nedan för att öppna inställningarna för en Azure AD directory-roll.

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure AD-katalogroller**.

1. Klicka på **inställningar**.

    ![Azure AD-katalogroller - inställningar](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Klicka på **roller**.

1. Klicka på den roll som du vill konfigurera inställningarna.

    ![Azure AD-katalogroller - inställningar för roller](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Det finns flera inställningar som du kan konfigurera på inställningssidan för varje roll. De här inställningarna påverkar endast användare som är **berättigade** tilldelningar, inte **permanent** tilldelningar.

## <a name="activations"></a>Aktiveringar

Använd den **aktiveringar** skjutreglaget för att ange den maximala tiden, i timmar, att en roll förblir aktiv innan den upphör. Det här värdet kan vara mellan 1 och 72 timmar.

## <a name="notifications"></a>Meddelanden

Använd den **meddelanden** växel för att ange om administratörer ska ta emot e-postaviseringar när roller är aktiverade. Detta kan vara användbart för identifiering av obehöriga eller otillåtna aktiveringar.

När värdet **aktivera**, skickas meddelanden till:

- Privilegierad rolladministratör
- Säkerhetsadministratör
- Global administratör

Mer information finns i [e-postmeddelanden i PIM](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Biljett för incident/begäran

Använd den **biljett för Incident/begäran** växel för att ange om du vill kräva berättigade administratörer att inkludera en Biljettnummer när de aktiverar sina roller. Detta kan vara användbart när du utför rollen åtkomst granskningar.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Använd den **Multifaktorautentisering** växel för att ange om användarna ska verifiera sin identitet med MFA innan du kan aktivera sina roller. De behöver bara verifiera det här en gång per session, inte varje gång de aktivera en roll. Det finns två tips att tänka på när du aktiverar MFA:

* Användare som har Microsoft-konton för sina e-postadresser (vanligtvis @outlook.com, men inte alltid) det går inte att registrera dig för Azure MFA. Om du vill tilldela roller till användare med Microsoft-konton bör du göra dem permanenta administratörer eller inaktivera MFA för rollen.
* Du kan inte inaktivera MFA för mycket Privilegierade roller för Azure AD och Office 365. Det här är en säkerhetsfunktion eftersom dessa roller noggrant ska skyddas:  
  
  * Faktureringsadministratör
  * Molnprogramadministratör
  * Efterlevnadsadministratör
  * Administratör för villkorsstyrd åtkomst
  * Administratör för CRM-tjänst
  * Godkännare av åtkomst till Customer LockBox
  * Katalogskrivare
  * Exchange-administratör
  * Global administratör
  * Information Protection-administratör
  * Administratör för Intune-tjänsten
  * Power BI-tjänstadministratör
  * Privilegierad rolladministratör
  * Säkerhetsadministratör
  * SharePoint-tjänstadministratör
  * Skype för Business Administrator
  * Användaradministratör

Mer information finns i [multifaktorautentisering (MFA) och PIM](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Godkännande krävs

Följ dessa steg om du vill kräva godkännande för att aktivera en roll.

1. Ange den **kräver godkännande** växla till **aktiverad**. Fönstret expanderar med alternativ att välja godkännare.

    ![Azure AD-katalogroller - Settings - godkännande](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Om du **inte** anger alla godkännare, Privilegierade Rolladministratörer blir standard godkännare. Administratörer med privilegierad roll krävs för att godkänna **alla** aktiveringsbegäranden för den här rollen.

1. Om du vill ange godkännare, klickar du på **Välj godkännare**.

    ![Azure AD-katalogroller - Settings - godkännande](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Välj en eller flera godkännare och klicka sedan på **Välj**. Du kan välja användare eller grupper. Minst 2 godkännare rekommenderas. Självgodkännande är inte tillåtet.

    Dina val visas i listan över valda godkännare.

1. När du har angett alla dina rollinställningarna, klickar du på **spara** att spara dina ändringar.


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure AD-katalogroller i PIM](pim-how-to-add-role-to-user.md)
- [Konfigurera säkerhetsaviseringar för Azure AD-katalogroller i PIM](pim-how-to-configure-security-alerts.md)
