---
title: Börja använda PIM - Azure Active Directory | Microsoft-dokument
description: Lär dig hur du aktiverar och kommer igång med Azure AD Privileged Identity Management (PIM) i Azure-portalen.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 03/13/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cbb9b4340a7cdb9be5039722a8f75e09288ec48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472868"
---
# <a name="start-using-privileged-identity-management"></a>Börja använda Privileged Identity Management

Med PIM (Privilegierad identitetshantering) kan du hantera, kontrollera och övervaka åtkomsten i din Azure Active Directory-organisation (Azure AD). Det här scopet omfattar åtkomst till Azure-resurser, Azure AD och andra Microsoft-onlinetjänster som Office 365 eller Microsoft Intune.

I den här artikeln beskrivs hur du aktiverar och kommer igång med privilegierad identitetshantering.

## <a name="prerequisites"></a>Krav

Om du vill använda Privilegierad identitetshantering måste du ha någon av följande licenser:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Mer information finns i [Licenskrav för att använda Privilegierad identitetshantering](subscription-requirements.md).

## <a name="sign-up-pim-for-azure-ad-roles"></a>Registrera PIM för Azure AD-roller

När du har aktiverat Privilegierad identitetshantering för din katalog måste du registrera privilegierad identitetshantering för att hantera Azure AD-roller.

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Välj **Azure AD-roller**.

    ![Registrera privilegierad identitetshantering för Azure AD-roller](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Välj **Registrera dig**.

1. I meddelandet som visas klickar du på **Ja** för att registrera privilegierad identitetshantering för att hantera Azure AD-roller.

    ![Registrera privilegierad identitetshantering för Azure AD-roller meddelande](./media/pim-getting-started/sign-up-pim-message.png)

    När registreringen är klar aktiveras Azure AD-alternativen. Du kan behöva uppdatera portalen.

    Information om hur du identifierar och väljer Azure-resurser som ska skyddas med privilegierad identitetshantering finns [i Identifiera Azure-resurser för att hantera i Privilegierad identitetshantering](pim-resource-roles-discover-resources.md).

## <a name="navigate-to-your-tasks"></a>Gå till dina uppgifter

När privilegierad identitetshantering har konfigurerats kan du starta dina identitetshanteringsuppgifter.

![Navigeringsfönster i Privilegierad identitetshantering som visar uppgifts- och hanteringsalternativ](./media/pim-getting-started/pim-quickstart-tasks.png)

| Uppgift + Hantera | Beskrivning |
| --- | --- |
| **Mina roller**  | Visar en lista över kvalificerade och aktiva roller som tilldelats dig. Här kan du aktivera tilldelade berättigade roller. |
| **Mina önskemål** | Visar väntande begäranden för att aktivera kvalificerade rolltilldelningar. |
| **Godkänna förfrågningar** | Visar en lista över begäranden om att aktivera kvalificerade roller för användare i katalogen som du har angett att godkänna. |
| **Granska åtkomst** | Visar en lista över aktiva åtkomstgranskningar som du har tilldelats för att slutföra, oavsett om du granskar åtkomsten för dig själv eller någon annan. |
| **Azure AD-roller** | Visar en instrumentpanel och inställningar för privilegierade rolladministratörer för att hantera Azure AD-rolltilldelningar. Instrumentpanelen är inaktiverad för alla som inte är en privilegierad rolladministratör. De här användarna har åtkomst till en särskild instrumentpanel som heter My view (Min vy). Instrumentpanelen My view (Min vy) visar endast information om användaren som har åtkomst till instrumentpanelen, inte hela klientorganisationen. |
| **Azure-resurser** | Visar en instrumentpanel och inställningar för privilegierade rolladministratörer för att hantera Azure-resursrolltilldelningar. Instrumentpanelen är inaktiverad för alla som inte är en privilegierad rolladministratör. De här användarna har åtkomst till en särskild instrumentpanel som heter My view (Min vy). Instrumentpanelen My view (Min vy) visar endast information om användaren som har åtkomst till instrumentpanelen, inte hela klientorganisationen. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Lägga till en PIM-panel på instrumentpanelen

Om du vill göra det enklare att öppna Privilegierad identitetshantering lägger du till en panel för privilegierad identitetshantering på instrumentpanelen för Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **Alla tjänster** och hitta Azure AD **Privileged Identity** Management-tjänsten.

    ![Azure AD-privilegierad identitetshantering i alla tjänster](./media/pim-getting-started/pim-all-services-find.png)

1. Välj snabbstart för privilegierad identitetshantering.

1. Markera **Fäst bladet på instrumentpanelen** om du vill fästa snabbstartsbladet för privilegierad identitetshantering på instrumentpanelen.

    ![Pushpin-ikonen för att fästa bladet privilegierad identitetshantering på instrumentpanelen](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    På Azure-instrumentpanelen ser du en panel som denna:

    ![Snabbstartspanel för privilegierad identitetshantering på instrumentpanelen](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure AD-roller i privilegierad identitetshantering](pim-how-to-add-role-to-user.md)
- [Identifiera Azure-resurser för hantering i privilegierad identitetshantering](pim-resource-roles-discover-resources.md)
