---
title: Godkänna eller neka begäranden om Azure AD-roller i PIM - Azure AD | Microsoft-dokument
description: Lär dig hur du godkänner eller nekar begäranden för Azure AD-roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73ccfd1ad270072989e9b575fda538b94fd8927c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049023"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Godkänna eller neka begäranden om Azure AD-roller i privilegierad identitetshantering

Med Azure Active Directory (Azure AD) Privilegierad identitetshantering (PIM) kan du konfigurera roller för godkännande för aktivering och välja en eller flera användare eller grupper som delegerade godkännare. Delegerade godkännare har 24 timmar på sig att godkänna begäranden. Om en begäran inte godkänns inom 24 timmar måste den berättigade användaren skicka en ny begäran på nytt. Tidsfönstret för 24 timmar godkännande kan inte konfigureras.

## <a name="determine-your-version-of-pim"></a>Bestäm din version av PIM

Från och med november 2019 uppdateras Azure AD-rollerna i Privileged Identity Management till en ny version som matchar upplevelserna för Azure-roller. Detta skapar ytterligare funktioner samt [ändringar i det befintliga API:et](azure-ad-roles-features.md#api-changes). Medan den nya versionen distribueras, vilka procedurer som du följer i den här artikeln beror på vilken version av privilegierad identitetshantering du har för närvarande. Följ stegen i det här avsnittet för att avgöra vilken version av privilegierad identitetshantering du har. När du känner till din version av Privilegierad identitetshantering kan du välja de procedurer i den här artikeln som matchar den versionen.

1. Logga in på [Azure-portalen](https://portal.azure.com/) med en användare som är i [rollen Privilegierad rolladministratör.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Öppna **Azure AD-privilegierad identitetshantering**. Om du har en banderoll högst upp på översiktssidan följer du instruktionerna på fliken **Ny version** i den här artikeln. Annars följer du instruktionerna på fliken **Föregående version.**

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Följ stegen i den här artikeln för att godkänna eller neka begäranden om Azure AD-roller.

# <a name="new-version"></a>[Ny version](#tab/new)

## <a name="view-pending-requests"></a>Visa väntande begäranden

Som delegerad godkännare får du ett e-postmeddelande när en Azure AD-rollbegäran väntar på ditt godkännande. Du kan visa dessa väntande begäranden i Privilegierad identitetshantering.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Välj **Godkänn begäranden**.

    ![Godkänna begäranden – sidan som visar begäran om att granska Azure AD-roller](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    I avsnittet **Begäran om rollaktiveringar** visas en lista över begäranden som väntar på ditt godkännande.

## <a name="approve-requests"></a>Godkänn förfrågningar

1. Sök efter och välj den begäran som du vill godkänna. En godkänn- eller neka-sida visas.

    ![Godkänn begäranden - godkänn eller neka rutan med information och rutan Motivering](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. Ange affärsmotiveringen i rutan **Motivering.**

1. Välj **Godkänn**. Du får ett Azure-meddelande om ditt godkännande.

    ![Godkänn meddelande som visar att begäran har godkänts](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

## <a name="deny-requests"></a>Neka begäranden

1. Sök efter och välj den begäran som du vill neka. En godkänn- eller neka-sida visas.

    ![Godkänn begäranden - godkänn eller neka rutan med information och rutan Motivering](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Ange affärsmotiveringen i rutan **Motivering.**

1. Välj **Neka**. Ett meddelande visas med ditt förnekande.

## <a name="workflow-notifications"></a>Meddelanden om arbetsflöde

Här är lite information om arbetsflödesaviseringar:

- Godkännare meddelas via e-post när en begäran om en roll väntar på granskning. E-postmeddelanden innehåller en direkt länk till begäran, där godkännaren kan godkänna eller neka.
- Begäranden löses av den första godkännaren som godkänner eller nekar.
- När en godkännare svarar på begäran meddelas alla godkännare åtgärden.
- Globala administratörer och privilegierade rolladministratörer meddelas när en godkänd användare blir aktiv i sin roll.

>[!NOTE]
>En global administratör eller privilegierad rolladministratör som anser att en godkänd användare inte ska vara aktiv kan ta bort den aktiva rolltilldelningen i Privilegierad identitetshantering. Även om administratörer inte meddelas om väntande begäranden om de inte är en godkännare, kan de visa och avbryta alla väntande begäranden för alla användare genom att visa väntande begäranden i Privilegierad identitetshantering.

# <a name="previous-version"></a>[Tidigare version](#tab/previous)

## <a name="view-pending-requests"></a>Visa väntande begäranden

Som delegerad godkännare får du ett e-postmeddelande när en Azure AD-rollbegäran väntar på ditt godkännande. Du kan visa dessa väntande begäranden i Privilegierad identitetshantering.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Klicka på **Azure AD-roller**.

1. Klicka på **Godkänn begäranden**.

    ![Azure AD-roller - Godkänn begäranden](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    Du ser en lista över begäranden som väntar på ditt godkännande.

## <a name="approve-requests"></a>Godkänn förfrågningar

1. Markera de begäranden som du vill godkänna och klicka sedan på **Godkänn** för att öppna fönstret Godkänn valda begäranden.

    ![Lista över godkänna begäranden med alternativet Godkänn markerat](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. Skriv en orsak i rutan **Godkänn orsak.**

    ![Fönstret Godkänn markerade begäranden med en godkännandeorsak](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Klicka på **Godkänn**.

    Statussymbolen uppdateras med ditt godkännande.

    ![Fönstret Godkänn markerade begäranden efter klickande på knappen Godkänn](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Neka begäranden

1. Markera de begäranden som du vill neka och klicka sedan på **Neka** för att öppna fönstret Neka markerade begäranden.

    ![Lista över godkänna begäranden med alternativet Neka markerat](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Skriv en orsak i rutan **Neka orsak.**

    ![Neka markerat begärandefönster med en nekande orsak](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Välj **Neka**.

    Statussymbolen uppdateras med ditt förnekande.

---

## <a name="next-steps"></a>Nästa steg

- [E-postmeddelanden i privilegierad identitetshantering](pim-email-notifications.md)
- [Godkänna eller neka begäranden om Azure-resursroller i Privilegierad identitetshantering](pim-resource-roles-approval-workflow.md)
