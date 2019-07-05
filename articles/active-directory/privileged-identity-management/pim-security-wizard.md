---
title: Säkerhetsguiden för Azure AD-roller i PIM - Azure Active Directory | Microsoft Docs
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
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa4fd850ac2116dc7f353eea87845501fff020bb
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476226"
---
# <a name="azure-ad-roles-security-wizard-in-pim"></a>Säkerhetsguiden för Azure AD-roller i PIM

Om du är den första personen som kör Azure Active Directory (Azure AD) Privileged Identity Management (PIM) för din organisation kan visas med en guide. Guiden hjälper dig att förstå säkerhetsriskerna med Privilegierade identiteter och hur du använder PIM för att minska riskerna. Du behöver inte göra ändringar i befintliga rolltilldelningar i guiden om du vill göra det senare.

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

    ![Azure AD-roller - sidan i guiden som visar 3 steg för att köra guiden](./media/pim-security-wizard/wizard-start.png)

1. Klicka på **1 identifiera Privilegierade roller**.

1. Granska listan över Privilegierade roller för att se vilka användare som är permanenta eller berättigade.

    ![Identifiera Privilegierade roller - rollen fönster som visar permanent och dessa medlemmar](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Klicka på **nästa** att välja de medlemmar som du vill berättiga.

    ![Konvertera medlemmar till berättigade sida med alternativ att välja medlemmar som du vill göra berättigade för roller](./media/pim-security-wizard/convert-members-eligible.png)

1. När du har valt medlemmar, klickar du på **nästa**.

    ![Granska ändringar sidan som visar medlemmar med permanent rolltilldelningar som ska konverteras](./media/pim-security-wizard/review-changes.png)

1. Klicka på **OK** att konvertera permanent tilldelningar till kvalificerade.

    När konverteringen har slutförts visas ett meddelande.

    ![Meddelande som visar status för en konvertering](./media/pim-security-wizard/notification-completion.png)

Om du vill konvertera andra Privilegierade rolltilldelningar till berättigade kan köra du guiden igen. Om du vill använda PIM-gränssnittet i stället för att guiden finns i [tilldela Azure AD-roller i PIM](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure AD-roller i PIM](pim-how-to-add-role-to-user.md)
- [Bevilja åtkomst till andra administratörer att hantera PIM](pim-how-to-give-access-to-pim.md)
