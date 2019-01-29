---
title: Börja använda PIM - Azure | Microsoft Docs
description: Lär dig hur du aktiverar och komma igång med Azure AD Privileged Identity Management (PIM) i Azure-portalen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 11/09/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: a670b6f1e8c2298214e28de6183064d6db424b33
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55191609"
---
# <a name="start-using-pim"></a>Börja använda PIM

Med Azure Active Directory (Azure AD) Privileged Identity Management (PIM), som du kan hantera, kontrollera och övervaka åtkomst inom din organisation. Det här omfånget inkluderar åtkomst till Azure-resurser, Azure AD och andra Microsoft onlinetjänster som Office 365 eller Microsoft Intune.

Den här artikeln beskriver hur du aktiverar och börja använda PIM.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda PIM måste du ha en av följande licenser:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Mer information finns i [licenskraven för att använda PIM](subscription-requirements.md).

## <a name="first-person-to-use-pim"></a>Första personen som använder PIM

Om du är den första personen som använder PIM i din katalog så tilldelas du automatiskt den [säkerhetsadministratör](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) och [privilegierad Rolladministratör](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) roller i katalogen. Endast privilegierade rolladministratörer kan hantera rolltilldelningar för användare i Azure AD-katalogen. Dessutom kan du välja att köra den [säkerhetsguiden](pim-security-wizard.md) som vägleder dig genom den första upplevelsen för identifiering och tilldelning.

## <a name="enable-pim"></a>Aktivera PIM

Om du vill börja använda PIM i din katalog, måste du först aktivera PIM.

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som Global administratör för din katalog.

    Du måste vara en Global administratör med ett organisationskonto (till exempel @yourdomain.com), inte ett Microsoft-konto (till exempel @outlook.com), för att aktivera PIM för en katalog.

1. Klicka på **alla tjänster** och hitta den **Azure AD Privileged Identity Management** service.

    ![Azure AD Privileged Identity Management i alla tjänster](./media/pim-getting-started/pim-all-services-find.png)

1. Klicka för att öppna PIM-Snabbstart.

1. I listan klickar du på **Samtyck till PIM**.

    ![Samtyck till PIM](./media/pim-getting-started/consent-pim.png)

1. Klicka på **verifiera min identitet** verifiera din identitet med Azure MFA. Du ombeds välja ett konto.

    ![Välj ett konto](./media/pim-getting-started/pick-account.png)

1. Om det krävs mer information för verifiering, får du guidas genom processen. Mer information finns i [få hjälp med tvåstegsverifiering](https://go.microsoft.com/fwlink/p/?LinkId=708614).

    ![Mer information krävs](./media/pim-getting-started/more-information-required.png)

    Du kan till exempel bli ombedd att tillhandahålla verifiering via telefon.

    ![Ytterligare säkerhetskontroll](./media/pim-getting-started/additional-security-verification.png)

1. När du har slutfört verifieringen, klickar du på den **godkänna** knappen.

1. I det meddelande som visas, klickar du på **Ja** samtycker till att PIM-tjänsten.

    ![Samtycka till PIM-meddelande](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>Registrera PIM för Azure AD-roller

När du har aktiverat PIM för din katalog, kommer du behöva registrera PIM att hantera Azure AD-roller.

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure AD-roller**.

    ![Registrera PIM för Azure AD-roller](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Klicka på **registrera**.

1. I det meddelande som visas, klickar du på **Ja** att registrera PIM att hantera Azure AD-roller.

    ![Registrera PIM för Azure AD-roller meddelande](./media/pim-getting-started/sign-up-pim-message.png)

    När registrering är klar aktiveras Azure AD-alternativ. Du kan behöva uppdatera portalen.

    Information om hur du identifierar och välj de Azure-resurserna att skydda med PIM finns [identifiera Azure-resurser för att hantera i PIM](pim-resource-roles-discover-resources.md).

## <a name="navigate-to-your-tasks"></a>Gå till dina uppgifter

Du kan utföra dina identitetshanteringsaktiviteter när PIM har ställts in.

![Toppnivåaktiviteter för PIM - skärmbild](./media/pim-getting-started/pim-quickstart-tasks.png)

| Uppgift + hantera | Beskrivning |
| --- | --- |
| **Mina roller**  | Visar en lista över tillgängliga och aktiva roller som är tilldelade till dig. Här kan du aktivera tilldelade berättigade roller. |
| **Mina begäranden** | Visar väntande förfrågningar om att aktivera kvalificerade rolltilldelningar. |
| **Programåtkomst** | Gör det möjligt för dig att minska potentiella fördröjningar och Använd en roll omedelbart efter aktiveringen. |
| **Godkänna förfrågningar** | Visar en lista över förfrågningar om att aktivera berättigade roller av användare i din katalog som du är gjorda för att godkänna. |
| **Granska åtkomst** | Visar en lista över aktiva åtkomstgranskningar som du har tilldelats slutföra, oavsett om du granskar åtkomst åt dig själv eller någon annan. |
| **Azure AD-roller** | Visar en instrumentpanel och inställningar för privilegierade rolladministratörer att hantera rolltilldelningar för Azure AD-katalog. Instrumentpanelen är inaktiverad för alla som inte är en privilegierad rolladministratör. De här användarna har åtkomst till en särskild instrumentpanel som heter My view (Min vy). Instrumentpanelen My view (Min vy) visar endast information om användaren som har åtkomst till instrumentpanelen, inte hela klientorganisationen. |
| **Azure-resurser** | Visar en instrumentpanel och inställningar för privilegierade rolladministratörer att hantera rolltilldelningar i Azure-resurs. Instrumentpanelen är inaktiverad för alla som inte är en privilegierad rolladministratör. De här användarna har åtkomst till en särskild instrumentpanel som heter My view (Min vy). Instrumentpanelen My view (Min vy) visar endast information om användaren som har åtkomst till instrumentpanelen, inte hela klientorganisationen. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Lägga till en PIM-panel på instrumentpanelen

Om du vill göra det enklare att öppna PIM, bör du lägga till en PIM-panel på instrumentpanelen för Azure-portalen.

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Klicka på **alla tjänster** och hitta den **Azure AD Privileged Identity Management** service.

    ![Azure AD Privileged Identity Management i alla tjänster](./media/pim-getting-started/pim-all-services-find.png)

1. Klicka för att öppna PIM-Snabbstart.

1. Kontrollera **Fäst bladet på instrumentpanelen** att fästa PIM Snabbstart-bladet på instrumentpanelen.

    ![Fäst bladet på instrumentpanelen](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    På Azure-instrumentpanelen visas en panel så här:

    ![Snabbstart för PIM-panel](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure AD-katalogroller i PIM](pim-how-to-add-role-to-user.md)
- [Identifiera Azure-resurser du hanterar i PIM](pim-resource-roles-discover-resources.md)
