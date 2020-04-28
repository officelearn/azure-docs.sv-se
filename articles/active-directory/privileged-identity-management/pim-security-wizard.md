---
title: Säkerhetsguiden för Azure AD-roller i PIM – Azure Active Directory | Microsoft-dokument
description: Beskriver säkerhetsguiden som du kan använda för att konvertera permanenta privilegierade Azure AD-rolltilldelningar som är kvalificerade med hjälp av Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/21/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9f12b2b31da4c7fe67eef9674d96b517d4e2bfa
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867756"
---
# <a name="azure-ad-roles-security-wizard-in-privileged-identity-management"></a>Säkerhetsguiden för Azure AD-roller i privilegierad identitetshantering

Om du är den första personen som använder PIM (Privileged Identity Management) i din Azure Active Directory -organisation (Azure AD) visas en guide för att komma igång. Guiden hjälper dig att förstå säkerhetsriskerna med privilegierade identiteter och hur du använder privilegierad identitetshantering för att minska dessa risker. Du behöver inte göra några ändringar i befintliga rolltilldelningar i guiden om du föredrar att göra det senare.

> [!Important]
> Säkerhetsguiden är inte tillgänglig för tillfället. Tack för ert tålamod.

## <a name="wizard-overview"></a>Översikt över guiden

Innan din organisation börjar använda Privilegierad identitetshantering är alla rolltilldelningar permanenta: användarna är alltid i dessa roller även om de för närvarande inte behöver sina privilegier. Det första steget i guiden visar en lista över högprivilegiiserade roller och hur många användare som för närvarande befinner sig i dessa roller. Du kan gå in på en viss roll om du vill veta mer om användare om en eller flera av dem inte är bekant.

Det andra steget i guiden ger dig möjlighet att ändra administratörens rolltilldelningar.  

> [!WARNING]
> Det är viktigt att du har minst en global administratör och mer än en administratör för privilegierade roller med ett arbets- eller skolkonto (inte ett Microsoft-konto). Om det bara finns en administratör för privilegierad roll kan organisationen inte hantera privilegierad identitetshantering om kontot tas bort.
> Håll också rolltilldelningar permanenta om en användare har ett Microsoft-konto (med andra ord ett konto som de använder för att logga in på Microsoft-tjänster som Skype och Outlook.com). Om du planerar att kräva multifaktorautentisering för aktivering för den rollen, kommer den användaren att vara utelåst.

## <a name="run-the-wizard"></a>Kör guiden

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Välj **Azure AD-roller** och välj sedan **Guiden**.

    ![Azure AD-roller - Guidesida som visar de tre stegen för att köra guiden](./media/pim-security-wizard/wizard-start.png)

1. Välj **1 Upptäck privilegierade roller**.

1. Granska listan över privilegierade roller för att se vilka användare som är permanenta eller kvalificerade.

    ![Upptäck privilegierade roller – rollfönstret som visar permanenta och berättigade medlemmar](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Välj **Nästa** om du vill välja vilka användare eller grupper du vill göra kvalificerade.

    ![Konvertera medlemmar till kvalificerad sida med alternativ för att välja medlemmar som du vill göra kvalificerade för roller](./media/pim-security-wizard/convert-members-eligible.png)

1. När du har valt användarna eller grupperna väljer du **Nästa**.

    ![Sidan Granska ändringar som visar medlemmar med permanenta rolltilldelningar som ska konverteras](./media/pim-security-wizard/review-changes.png)

1. Välj **OK** om du vill konvertera de permanenta tilldelningarna till kvalificerade.

    När konverteringen är klar visas en avisering.

    ![Meddelande som visar status för en konvertering](./media/pim-security-wizard/notification-completion.png)

Om du behöver konvertera andra privilegierade rolltilldelningar till kvalificerade kan du köra guiden igen. Om du vill använda gränssnittet Privilegierad identitetshantering i stället för guiden läser du [Tilldela Azure AD-roller i Privilegierad identitetshantering](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure AD-roller i privilegierad identitetshantering](pim-how-to-add-role-to-user.md)
- [Bevilja åtkomst till andra administratörer för att hantera privilegierad identitetshantering](pim-how-to-give-access-to-pim.md)
