---
title: Börja använda PIM – Azure Active Directory | Microsoft Docs
description: Lär dig hur du aktiverar och kommer igång med Azure AD Privileged Identity Management (PIM) i Azure Portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 018cc54a770a0d18fa9af3d93fec2f5f720ce17b
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804527"
---
# <a name="start-using-pim"></a>Börja använda PIM

Med Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kan du hantera, kontrol lera och övervaka åtkomst i din organisation. Det här omfånget inkluderar åtkomst till Azure-resurser, Azure AD och andra Microsoft onlinetjänster som Office 365 eller Microsoft Intune.

I den här artikeln beskrivs hur du aktiverar och kommer igång med PIM.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda PIM måste du ha någon av följande licenser:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Mer information finns i [licens krav för att använda PIM](subscription-requirements.md).

## <a name="first-person-to-use-pim"></a>Första personen som använder PIM

Om du är den första personen som använder PIM i din katalog, tilldelas du automatiskt rollen [säkerhets administratör](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) och [administratör för privilegie rad roll](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) i katalogen. Endast privilegierade roll administratörer kan hantera Azure AD-roll tilldelningar för användare. Dessutom kan du välja att köra [säkerhets guiden](pim-security-wizard.md) som vägleder dig genom den inledande identifierings-och tilldelnings upplevelsen.

## <a name="enable-pim"></a>Aktivera PIM

Om du vill börja använda PIM i din katalog måste du först aktivera PIM.

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din katalog.

    Du måste vara global administratör med ett organisations konto (till exempel @yourdomain.com), inte en Microsoft-konto (till @outlook.comexempel) för att aktivera PIM för en katalog.

1. Klicka på **alla tjänster** och hitta **Azure AD Privileged Identity Management** tjänsten.

    ![Azure AD Privileged Identity Management i alla tjänster](./media/pim-getting-started/pim-all-services-find.png)

1. Klicka för att öppna PIM-snabb starten.

1. I listan klickar du på **medgivande till PIM**.

    ![Medgivande till PIM för att aktivera PIM](./media/pim-getting-started/consent-pim.png)

1. Klicka på **verifiera min identitet** för att verifiera din identitet med Azure MFA. Du uppmanas att välja ett konto.

    ![Välj ett konto fönster för att verifiera din identitet](./media/pim-getting-started/pick-account.png)

1. Om det krävs mer information för verifieringen vägleds du genom processen. Mer information finns i [få hjälp med tvåstegsverifiering](https://go.microsoft.com/fwlink/p/?LinkId=708614).

    ![Mer information krävs-fönstret om din organisation behöver mer information](./media/pim-getting-started/more-information-required.png)

    Du kan till exempel uppmanas att tillhandahålla telefon verifiering.

    ![Sidan ytterligare säkerhets verifiering där du tillfrågas om hur du kontaktar dig](./media/pim-getting-started/additional-security-verification.png)

1. När du har slutfört verifierings processen klickar du på knappen **medgivande** .

1. I meddelandet som visas klickar du på **Ja** för att godkänna PIM-tjänsten.

    ![Medgivande till PIM-meddelande för att slutföra medgivande processen](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>Registrera PIM för Azure AD-roller

När du har aktiverat PIM för din katalog måste du registrera PIM för att hantera Azure AD-roller.

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure AD-roller**.

    ![Registrera PIM för Azure AD-roller](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Klicka på **Registrera**.

1. I meddelandet som visas klickar du på **Ja** för att registrera PIM för att hantera Azure AD-roller.

    ![Registrera PIM för Azure AD roles-meddelande](./media/pim-getting-started/sign-up-pim-message.png)

    När registreringen är klar aktive ras Azure AD-alternativen. Du kan behöva uppdatera portalen.

    Information om hur du identifierar och väljer de Azure-resurser som ska skyddas med PIM finns i [identifiera Azure-resurser som ska hanteras i PIM](pim-resource-roles-discover-resources.md).

## <a name="navigate-to-your-tasks"></a>Gå till dina uppgifter

När PIM har kon figurer ATS kan du utföra dina identitets hanterings uppgifter.

![Navigerings fönster i PIM som visar uppgifter och hantera alternativ](./media/pim-getting-started/pim-quickstart-tasks.png)

| Aktivitet + hantera | Beskrivning |
| --- | --- |
| **Mina roller**  | Visar en lista över giltiga och aktiva roller som har tilldelats dig. Här kan du aktivera tilldelade berättigade roller. |
| **Mina förfrågningar** | Visar väntande begär Anden om att aktivera kvalificerade roll tilldelningar. |
| **Godkänna förfrågningar** | Visar en lista med begär Anden om att aktivera berättigade roller av användare i din katalog som du är behörig att godkänna. |
| **Granska åtkomst** | Visar en lista över aktiva åtkomst granskningar som du har tilldelats, oavsett om du granskar åtkomst åt dig själv eller någon annan. |
| **Azure AD-roller** | Visar en instrument panel och inställningar för privilegierade roll administratörer för att hantera roll tilldelningar för Azure AD. Instrumentpanelen är inaktiverad för alla som inte är en privilegierad rolladministratör. De här användarna har åtkomst till en särskild instrumentpanel som heter My view (Min vy). Instrumentpanelen My view (Min vy) visar endast information om användaren som har åtkomst till instrumentpanelen, inte hela klientorganisationen. |
| **Azure-resurser** | Visar en instrument panel och inställningar för privilegierade roll administratörer för hantering av roll tilldelningar för Azure-resurser. Instrumentpanelen är inaktiverad för alla som inte är en privilegierad rolladministratör. De här användarna har åtkomst till en särskild instrumentpanel som heter My view (Min vy). Instrumentpanelen My view (Min vy) visar endast information om användaren som har åtkomst till instrumentpanelen, inte hela klientorganisationen. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Lägg till en PIM-panel på instrument panelen

För att göra det lättare att öppna PIM bör du lägga till en PIM-panel i Azure Portal instrument panelen.

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Klicka på **alla tjänster** och hitta **Azure AD Privileged Identity Management** tjänsten.

    ![Azure AD Privileged Identity Management i alla tjänster](./media/pim-getting-started/pim-all-services-find.png)

1. Klicka för att öppna PIM-snabb starten.

1. Kontrol lera **Fäst bladet på instrument panelen** för att fästa PIM-snabb starts bladet på instrument panelen.

    ![Ikonen kartnål för att fästa PIM-bladet på instrument panelen](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    På Azure-instrumentpanelen ser du en panel så här:

    ![PIM snabb starts panel på instrument panelen](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure AD-roller i PIM](pim-how-to-add-role-to-user.md)
- [Identifiera Azure-resurser som ska hanteras i PIM](pim-resource-roles-discover-resources.md)
