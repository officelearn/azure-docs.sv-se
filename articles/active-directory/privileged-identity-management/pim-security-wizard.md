---
title: Säkerhets guiden för Azure AD-roller i Privileged Identity Management-Azure Active Directory | Microsoft Docs
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
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5d1c9905047cb4b234bf80ba82f2395f72f67f1
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895224"
---
# <a name="azure-ad-roles-security-wizard-in-privileged-identity-management"></a>Säkerhets guiden för Azure AD-roller i Privileged Identity Management

Om du är den första personen som använder Privileged Identity Management (PIM) i din Azure Active Directory (Azure AD)-organisation visas en guide för att komma igång. Guiden hjälper dig att förstå säkerhets riskerna med privilegierade identiteter och hur du använder Privileged Identity Management för att minska riskerna. Du behöver inte göra några ändringar i de befintliga roll tilldelningarna i guiden, om du vill göra det senare.

## <a name="wizard-overview"></a>Översikt över guiden

Innan organisationen börjar använda Privileged Identity Management, är alla roll tilldelningar permanenta: användarna är alltid i dessa roller, även om de inte redan för närvarande behöver sina privilegier. Det första steget i guiden visar en lista över privilegierade roller och hur många användare som för närvarande finns i dessa roller. Du kan gå vidare till en viss roll för att lära dig mer om användare om en eller flera av dem är okända.

I det andra steget i guiden får du möjlighet att ändra administratörs roll tilldelningar.  

> [!WARNING]
> Det är viktigt att du har minst en global administratör och fler än en privilegie rad roll administratör med ett organisations konto (inte en Microsoft-konto). Om det bara finns en privilegie rad roll administratör kan organisationen inte hantera Privileged Identity Management om kontot tas bort.
> Håll också roll tilldelningarna permanenta om en användare har en Microsoft-konto (med andra ord, ett konto som de använder för att logga in på Microsoft-tjänster som Skype och Outlook.com). Om du planerar att kräva Multi-Factor Authentication för aktivering för den rollen kommer användaren att låsas upp.

## <a name="run-the-wizard"></a>Kör guiden

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure AD-roller** och välj sedan **Guide**.

    ![Azure AD-roller – guide sidan visar de tre stegen för att köra guiden](./media/pim-security-wizard/wizard-start.png)

1. Välj **1 identifiera privilegierade roller**.

1. Granska listan över privilegierade roller för att se vilka användare som är permanenta eller berättigade.

    ![Identifiera privilegierade roller – roll fönstret som visar permanenta och berättigade medlemmar](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Välj **Nästa** för att välja de användare eller grupper som du vill göra berättigade.

    ![Konvertera medlemmar till berättigade sidor med alternativ för att välja medlemmar som du vill ska vara berättigade till roller](./media/pim-security-wizard/convert-members-eligible.png)

1. När du har valt användare eller grupper väljer du **Nästa**.

    ![Sidan granska ändringar som visar medlemmar med permanenta roll tilldelningar som kommer att konverteras](./media/pim-security-wizard/review-changes.png)

1. Välj **OK** för att konvertera de permanenta tilldelningarna till stödberättigade.

    När konverteringen är klar visas ett meddelande.

    ![Meddelande som visar status för en konvertering](./media/pim-security-wizard/notification-completion.png)

Om du behöver konvertera andra privilegierade roll tilldelningar till giltig kan du köra guiden igen. Om du vill använda Privileged Identity Management-gränssnittet i stället för guiden, se [tilldela Azure AD-roller i Privileged Identity Management](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure AD-roller i Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Ge åtkomst till andra administratörer för att hantera Privileged Identity Management](pim-how-to-give-access-to-pim.md)
