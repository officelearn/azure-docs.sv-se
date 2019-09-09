---
title: Guiden Azure AD-roller säkerhet i PIM-Azure Active Directory | Microsoft Docs
description: Beskriver säkerhets guiden som du kan använda för att konvertera permanenta privilegierade Azure AD-roll tilldelningar till kvalificerade med Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d6d94df29ba16ecee06d70f5edac15a96a4299d
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804009"
---
# <a name="azure-ad-roles-security-wizard-in-pim"></a>Guiden Azure AD-roller säkerhet i PIM

Om du är den första personen som ska köra Azure Active Directory (Azure AD) Privileged Identity Management (PIM) för din organisation visas en guide. Guiden hjälper dig att förstå säkerhets riskerna med privilegierade identiteter och hur du använder PIM för att minska riskerna. Du behöver inte göra några ändringar i de befintliga roll tilldelningarna i guiden, om du vill göra det senare.

## <a name="wizard-overview"></a>Översikt över guiden

Innan din organisation börjar använda PIM är alla roll tilldelningar permanenta: användarna är alltid i dessa roller, även om de inte redan för närvarande behöver sina privilegier. Det första steget i guiden visar en lista över privilegierade roller och hur många användare som för närvarande finns i dessa roller. Du kan gå vidare till en viss roll för att lära dig mer om användare om en eller flera av dem är okända.

I det andra steget i guiden får du möjlighet att ändra administratörs roll tilldelningar.  

> [!WARNING]
> Det är viktigt att du har minst en global administratör och fler än en privilegie rad roll administratör med ett organisations konto (inte en Microsoft-konto). Om det bara finns en privilegie rad roll administratör kommer organisationen inte att kunna hantera PIM om kontot har tagits bort.
> Håll också roll tilldelningarna permanenta om en användare har ett Microsoft-konto (ett konto som de använder för att logga in på Microsoft-tjänster som Skype och Outlook.com). Om du planerar att kräva MFA för aktivering för rollen kommer den användaren att låsas upp.

## <a name="run-the-wizard"></a>Kör guiden

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure AD-roller** och klicka sedan på **Guide**.

    ![Azure AD-roller – guide sidan visar de tre stegen för att köra guiden](./media/pim-security-wizard/wizard-start.png)

1. Klicka på **1 identifiera privilegierade roller**.

1. Granska listan över privilegierade roller för att se vilka användare som är permanenta eller berättigade.

    ![Identifiera privilegierade roller – roll fönstret som visar permanenta och berättigade medlemmar](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Klicka på **Nästa** för att välja de medlemmar som du vill göra berättigade.

    ![Konvertera medlemmar till berättigade sidor med alternativ för att välja medlemmar som du vill ska vara berättigade till roller](./media/pim-security-wizard/convert-members-eligible.png)

1. När du har valt medlemmar klickar du på **Nästa**.

    ![Sidan granska ändringar som visar medlemmar med permanenta roll tilldelningar som kommer att konverteras](./media/pim-security-wizard/review-changes.png)

1. Klicka på **OK** för att konvertera de permanenta tilldelningarna till stödberättigade.

    När konverteringen är klar visas ett meddelande.

    ![Meddelande som visar status för en konvertering](./media/pim-security-wizard/notification-completion.png)

Om du behöver konvertera andra privilegierade roll tilldelningar till giltig kan du köra guiden igen. Om du vill använda PIM-gränssnittet i stället för guiden går du till [tilldela Azure AD-roller i PIM](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure AD-roller i PIM](pim-how-to-add-role-to-user.md)
- [Bevilja åtkomst till andra administratörer för att hantera PIM](pim-how-to-give-access-to-pim.md)
