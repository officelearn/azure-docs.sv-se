---
title: Bevilja åtkomst till hantering av PIM - Azure Active Directory | Microsoft-dokument
description: Lär dig hur du beviljar åtkomst till andra förvaltningar för att hantera Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7424e92f8520d13137b6ac8787523095058a005f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022119"
---
# <a name="grant-access-to-other-administrators-to-manage-privileged-identity-management"></a>Bevilja åtkomst till andra administratörer för att hantera privilegierad identitetshantering

Den globala administratör som aktiverar PIM (Privileged Identity Management) för en organisation får automatiskt rolltilldelningar och åtkomst till privilegierad identitetshantering. Ingen annan i azure Active Directory-organisationen (Azure AD) får skrivåtkomst som standard, inklusive andra globala administratörer. Andra globala administratörer, säkerhetsadministratörer och säkerhetsläsare har skrivskyddad åtkomst till privilegierad identitetshantering. Om du vill bevilja åtkomst till privilegierad identitetshantering kan den första användaren tilldela andra till rollen **Privilegierad rolladministratör.**

> [!NOTE]
> För att hantera privilegierad identitetshantering krävs Azure Multi-Factor-autentisering. Eftersom Microsoft-konton inte kan registrera sig för Azure Multi-Factor Authentication kan en användare som loggar in med ett Microsoft-konto inte komma åt privilegierad identitetshantering.

Kontrollera att det alltid finns minst två användare i rollen Administratör för privilegierad roll, om en användare är utelåst eller deras konto tas bort.

## <a name="grant-access-to-manage-pim"></a>Bevilja åtkomst till hantering av PIM

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Privilegierad identitetshantering i**Azure AD .

1. Välj **Azure AD-roller**.

1. Välj **Roller**.

    ![Azure AD-roller för privilegierad identitetshantering – roller](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Välj rollen **Privilegierad rolladministratör** för att öppna medlemssidan.

    ![Privilegierad rolladministratör - Medlemmar](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Välj **Lägg till medlem** om du vill öppna fönstret Lägg till hanterade medlemmar.

1. Välj **Välj medlemmar** om du vill öppna fönstret Välj medlemmar.

    ![Privilegierad rolladministratör – Välj medlemmar](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Markera en medlem och klicka sedan på **Välj**.

1. Välj **OK** om du vill göra medlemmen kvalificerad för rollen **Privilegierad rolladministratör.**

    När du tilldelar en ny roll till någon i Privilegierad identitetshantering konfigureras de automatiskt som **kvalificerade** att aktivera rollen.

1. Om du vill göra medlemmen permanent väljer du användaren i listan Medlemsadministratör för privilegierad roll.

1. Välj **Mer** och gör sedan **permanent** för att göra tilldelningen permanent.

    ![Privilegierad rolladministratör – Gör permanent](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Skicka en länk till användaren till [Start med privilegierad identitetshantering](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Ta bort åtkomst för att hantera PIM

Innan du tar bort någon från rollen Privilegierad rolladministratör kontrollerar du alltid att det fortfarande finns minst två användare tilldelade.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Välj **Azure AD-roller**.

1. Välj **Roller**.

1. Välj rollen **Privilegierad rolladministratör** för att öppna medlemssidan.

1. Markera kryssrutan bredvid den användare som du vill ta bort och välj sedan **Ta bort medlem**.

    ![Privilegierad rolladministratör – ta bort medlem](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. När du uppmanas att bekräfta att du vill ta bort medlemmen från rollen väljer du **Ja**.

## <a name="next-steps"></a>Nästa steg

- [Börja använda Privileged Identity Management](pim-getting-started.md)
