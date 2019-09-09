---
title: Bevilja åtkomst till andra administratörer för att hantera PIM – Azure Active Directory | Microsoft Docs
description: Lär dig hur du beviljar åtkomst till andra administrationer för att hantera Azure AD Privileged Identity Management (PIM).
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
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3a0173108b6c884994ca25fd0495e9cb8d45186
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804363"
---
# <a name="grant-access-to-other-administrators-to-manage-pim"></a>Bevilja åtkomst till andra administratörer för att hantera PIM

Den globala administratören som gör det möjligt för Azure Active Directory (Azure AD) Privileged Identity Management (PIM) för en organisation att automatiskt hämta roll tilldelningar och åtkomst till PIM. Ingen annan får skriv åtkomst som standard, även om det finns andra globala administratörer. Andra globala administratörer, säkerhets administratörer och säkerhets läsare har skrivskyddad åtkomst till PIM. För att ge åtkomst till PIM, kan den första användaren tilldela andra till rollen **administratör för privilegie rad roll** .

> [!NOTE]
> Hantering av PIM kräver Azure MFA. Eftersom Microsoft-konton inte kan registreras för Azure MFA kan en användare som loggar in med ett Microsoft-konto inte komma åt PIM.

Se till att det alltid finns minst två användare i rollen administratör för privilegierad roll, om en användare är utelåst eller om kontot har tagits bort.

## <a name="grant-access-to-manage-pim"></a>Bevilja åtkomst till hantera PIM

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure AD-roller**.

1. Klicka på **roller**.

    ![PIM Azure AD-roller – roller](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Klicka på rollen **privilegierad roll administratör** för att öppna sidan medlemmar.

    ![Privilegie rad roll administratör – medlemmar](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Klicka på **Lägg till medlem** för att öppna fönstret Lägg till hanterade medlemmar.

1. Klicka på **Välj medlemmar** för att öppna fönstret Välj medlemmar.

    ![Administratör för privilegie rad roll – Välj medlemmar](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Välj en medlem och klicka sedan på **Välj**.

1. Klicka på **OK** för att göra medlemmen berättigad till rollen **privilegie rad roll administratör** .

    När du tilldelar en ny roll till någon i PIM konfigureras de automatiskt som **giltiga** för att aktivera rollen.

1. Om du vill göra medlemmen permanent klickar du på användaren i medlems listan privilegierad roll administratör.

1. Klicka på **mer** och **gör permanent** för att göra tilldelningen permanent.

    ![Privilegie rad roll administratör – gör permanenta](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Skicka en länk till användaren för att [börja använda PIM](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Ta bort åtkomst till hantera PIM

Innan du tar bort någon från rollen administratör för privilegie rad roll ser du alltid till att det fortfarande finns minst två användare som är tilldelade till den.

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure AD-roller**.

1. Klicka på **roller**.

1. Klicka på rollen **privilegierad roll administratör** för att öppna sidan medlemmar.

1. Lägg till en bock bredvid den användare som du vill ta bort och klicka sedan på **ta bort medlem**.

    ![Privilegie rad roll administratör – ta bort medlem](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. I meddelandet som visas där du tillfrågas om du vill ta bort medlemmen från rollen klickar du på **Ja**.

## <a name="next-steps"></a>Nästa steg

- [Börja använda PIM](pim-getting-started.md)
