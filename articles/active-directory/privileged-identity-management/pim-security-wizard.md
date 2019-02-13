---
title: Säkerhetsguiden för Azure AD-roller i PIM | Microsoft Docs
description: Beskriver säkerhetsguiden som du kan använda för att konvertera permanenta Privilegierade rolltilldelningar för Azure AD till kvalificerade med hjälp av Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/09/2018
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: b66dcc834ab089f28e6bb47c2905e6a2a3907091
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56208766"
---
# <a name="azure-ad-roles-security-wizard-in-pim"></a>Säkerhetsguiden för Azure AD-roller i PIM

Om du är den första personen som kör Azure AD Privileged Identity Management (PIM) för din organisation kan visas med en guide. Guiden hjälper dig att förstå säkerhetsriskerna med Privilegierade identiteter och hur du använder PIM för att minska riskerna. Du behöver inte göra ändringar i befintliga rolltilldelningar i guiden om du vill göra det senare.

## <a name="wizard-overview"></a>Översikt över guiden

Innan din organisation kan börja använda PIM, alla rolltilldelningar vara permanenta: användarna är alltid i dessa roller även om de för närvarande inte behöver sina privilegier. Det första steget i guiden visar en lista över privilegierad roller och hur många användare som för närvarande i dessa roller. Du kan öka detaljnivån i en viss roll och Läs mer om användare om en eller flera av dem är okänd.

Det andra steget i guiden ger dig möjlighet att ändra administratörens rolltilldelningar.  

> [!WARNING]
> Det är viktigt att du har minst en Global administratör och mer än en privilegierad Rolladministratör med ett organisationskonto (inte ett Microsoft-konto). Om det finns bara en privilegierad Rolladministratör måste organisationen inte kan hantera PIM om kontot tas bort.
> Håll också nere rolltilldelningar permanent om en användare har ett Microsoft-konto (ett konto som de använder för att logga in på Microsoft-tjänster som Skype och Outlook.com). Om du planerar att kräva MFA för aktivering för rollen, kommer användaren utelåst.

## <a name="run-the-wizard"></a>Kör guiden

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure AD-roller** och klicka sedan på **guiden**.

    ![Azure AD-roller - guiden](./media/pim-security-wizard/wizard-start.png)

1. Klicka på **1 identifiera Privilegierade roller**.

1. Granska listan över Privilegierade roller för att se vilka användare som är permanenta eller berättigade.

    ![Identifiera Privilegierade roller användare](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Klicka på **nästa** att välja de medlemmar som du vill berättiga.

    ![Konvertera medlemmar till kvalificerade](./media/pim-security-wizard/convert-members-eligible.png)

1. När du har valt medlemmar, klickar du på **nästa**.

    ![Granska ändringar](./media/pim-security-wizard/review-changes.png)

1. Klicka på **OK** att konvertera permanent tilldelningar till kvalificerade.

    När konverteringen har slutförts visas ett meddelande.

    ![Meddelanden](./media/pim-security-wizard/notification-completion.png)

Om du vill konvertera andra Privilegierade rolltilldelningar till berättigade kan köra du guiden igen. Om du vill använda PIM-gränssnittet i stället för att guiden finns i [tilldela Azure AD-katalogroller i PIM](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure AD-katalogroller i PIM](pim-how-to-add-role-to-user.md)
- [Bevilja åtkomst till andra administratörer att hantera PIM](pim-how-to-give-access-to-pim.md)
