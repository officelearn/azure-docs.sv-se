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
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60c0e1bfd457137ddfb8f3ae81fc9044098c1f8d
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809067"
---
# <a name="grant-access-to-other-administrators-to-manage-privileged-identity-management"></a>Ge åtkomst till andra administratörer för att hantera Privileged Identity Management

Den globala administratören som gör det möjligt för Privileged Identity Management (PIM) för en organisation att automatiskt hämta roll tilldelningar och åtkomst till Privileged Identity Management. Ingen annan i din Azure Active Directory (Azure AD)-organisation får skriv åtkomst som standard, men även andra globala administratörer. Andra globala administratörer, säkerhets administratörer och säkerhets läsare har skrivskyddad åtkomst till Privileged Identity Management. För att bevilja åtkomst till Privileged Identity Management kan den första användaren tilldela andra till rollen **administratör för privilegie rad roll** .

> [!NOTE]
> Hantering av Privileged Identity Management kräver Azure Multi-Factor Authentication. Eftersom Microsoft-konton inte kan registreras för Azure Multi-Factor Authentication kan en användare som loggar in med ett Microsoft-konto inte komma åt Privileged Identity Management.

Se till att det alltid finns minst två användare i rollen administratör för privilegierad roll, om en användare är utelåst eller om kontot har tagits bort.

## <a name="grant-access-to-manage-pim"></a>Bevilja åtkomst till hantera PIM

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Privileged Identity Management**i Azure AD.

1. Välj **Azure AD-roller**.

1. Välj **roller**.

    ![Privileged Identity Management Azure AD-roller – roller](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Välj rollen **privilegierad roll administratör** för att öppna sidan medlemmar.

    ![Privilegie rad roll administratör – medlemmar](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Välj **Lägg till medlem** för att öppna fönstret Lägg till hanterade medlemmar.

1. Välj **Välj medlemmar** för att öppna fönstret Välj medlemmar.

    ![Administratör för privilegie rad roll – Välj medlemmar](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Välj en medlem och klicka sedan på **Välj**.

1. Välj **OK** för att göra medlemmen berättigad till rollen **privilegie rad roll administratör** .

    När du tilldelar en ny roll till någon i Privileged Identity Management konfigureras de automatiskt som **giltiga** för att aktivera rollen.

1. Om du vill göra medlemmen permanent väljer du användaren i medlems listan privilegierad roll administratör.

1. Välj **mer** och **gör permanent** för att göra tilldelningen permanent.

    ![Privilegie rad roll administratör – gör permanenta](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Skicka en länk till användaren för att [börja använda Privileged Identity Management](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Ta bort åtkomst till hantera PIM

Innan du tar bort någon från rollen administratör för privilegie rad roll ser du alltid till att det fortfarande finns minst två användare som är tilldelade till den.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**.

1. Välj **Azure AD-roller**.

1. Välj **roller**.

1. Välj rollen **privilegierad roll administratör** för att öppna sidan medlemmar.

1. Markera kryss rutan bredvid den användare som du vill ta bort och välj sedan **ta bort medlem**.

    ![Privilegie rad roll administratör – ta bort medlem](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. När du uppmanas att bekräfta att du vill ta bort medlemmen från rollen väljer du **Ja**.

## <a name="next-steps"></a>Nästa steg

- [Börja använda Privileged Identity Management](pim-getting-started.md)
