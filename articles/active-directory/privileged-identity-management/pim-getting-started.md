---
title: Börja använda PIM - Azure | Microsoft Docs
description: Lär dig hur du kommer igång med Azure AD Privileged Identity Management (PIM) i Azure-portalen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 08/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 5b3bff27821964648713b02589c941c99e3eb03d
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190097"
---
# <a name="start-using-pim"></a>Börja använda PIM

Med Azure Active Directory (Azure AD) Privileged Identity Management (PIM), som du kan hantera, kontrollera och övervaka åtkomst inom din organisation. Det här omfånget inkluderar åtkomst till Azure-resurser, Azure AD och andra Microsoft onlinetjänster som Office 365 eller Microsoft Intune.

Den här artikeln visar hur du lägger till appen Azure AD PIM på instrumentpanelen på Azure-portalen.

## <a name="first-person-to-use-pim"></a>Första personen som använder PIM

Om du är den första personen som använder PIM i din katalog så tilldelas du automatiskt den [säkerhetsadministratör](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) och [privilegierad rolladministratör](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) roller i katalogen. Endast privilegierade rolladministratörer kan hantera rolltilldelningar för användare i Azure AD-katalogen. Dessutom kan du välja att köra [Säkerhetsguiden.](pim-security-wizard.md) som från grunden lär dig hur du identifierar och tilldelar.

## <a name="add-pim-tile-to-the-dashboard"></a>Lägg till PIM-panel på instrumentpanelen

Om du vill göra det enklare att öppna PIM, bör du lägga till en PIM-panel på instrumentpanelen för Azure-portalen.

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som Global administratör för din katalog.

1. Klicka på **alla tjänster** och hitta den **Azure AD Privileged Identity Management** service.

    ![Azure AD Privileged Identity Management i alla tjänster](./media/pim-getting-started/pim-all-services-find.png)

1. Klicka för att öppna PIM-Snabbstart.

1. Kontrollera **Fäst bladet på instrumentpanelen** att fästa PIM Snabbstart-bladet på instrumentpanelen.

    ![Fäst bladet på instrumentpanelen](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    På Azure-instrumentpanelen visas en panel så här:

    ![Snabbstart för PIM-panel](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="navigate-to-your-tasks"></a>Gå till dina uppgifter

Du kan använda det här bladet för att utföra dina identitetshanteringsaktiviteter när PIM har ställts in.

![Toppnivåaktiviteter för PIM - skärmbild](./media/pim-getting-started/pim-quickstart-tasks.png)

| Uppgift + hantera | Beskrivning |
| --- | --- |
| **Mina roller**  | Visar en lista över tillgängliga och aktiva roller som är tilldelade till dig. Här kan du aktivera tilldelade berättigade roller. |
| **Mina begäranden** | Visar väntande förfrågningar om att aktivera kvalificerade rolltilldelningar. |
| **Programåtkomst** | Gör det möjligt för dig att minska potentiella fördröjningar och Använd en roll omedelbart efter aktiveringen. |
| **Godkänna förfrågningar** | Visar en lista över förfrågningar om att aktivera berättigade roller av användare i din katalog som du är gjorda för att godkänna. |
| **Granska åtkomst** | Visar en lista över aktiva åtkomstgranskningar som du har tilldelats slutföra, oavsett om du granskar åtkomst åt dig själv eller någon annan. |
| **Azure AD-katalogroller** | Visar en instrumentpanel och inställningar för privilegierade rolladministratörer att hantera rolltilldelningar för Azure AD-katalog. Instrumentpanelen är inaktiverad för alla som inte är en privilegierad rolladministratör. De här användarna har åtkomst till en särskild instrumentpanel som heter My view (Min vy). Instrumentpanelen My view (Min vy) visar endast information om användaren som har åtkomst till instrumentpanelen, inte hela klientorganisationen. |
| **Azure-resurser** | Visar en instrumentpanel och inställningar för privilegierade rolladministratörer att hantera rolltilldelningar i Azure-resurs. Instrumentpanelen är inaktiverad för alla som inte är en privilegierad rolladministratör. De här användarna har åtkomst till en särskild instrumentpanel som heter My view (Min vy). Instrumentpanelen My view (Min vy) visar endast information om användaren som har åtkomst till instrumentpanelen, inte hela klientorganisationen. |

## <a name="next-steps"></a>Nästa steg

- [Aktivera mitt Azure AD-katalogroller i PIM](pim-how-to-activate-role.md)
- [Aktivera Mina roller för Azure-resurs i PIM](pim-resource-roles-activate-your-roles.md)
