---
title: Hantera organisationens åtkomst till appar & grupper – Azure AD
description: Lär dig hur du utför en åtkomst granskning för att hantera säkerhets åtkomst för din organisations appar och grupper från portalen Mina appar.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 232f4b6fde84b7e1cd706b62e0ba9a0998b4171e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231841"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Utföra en åtkomst granskning från portalen Mina appar

Du kan använda ditt arbets-eller skol konto med den webbaserade **min Apps** -portalen för att visa och starta många av organisationens molnbaserade appar för att uppdatera viss profil-och konto information, för att se din **grupp** information och utföra  **åtkomst granskningar** för dina appar och grupper. Om du inte har åtkomst till portalen för **Mina appar** måste du kontakta supportavdelningen för att få behörighet.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Innehållet är avsett för användare. Om du är administratör kan du hitta mer information om hur du konfigurerar och hanterar dina molnbaserade appar i [program hanterings dokumentationen](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="manage-access-reviews"></a>Hantera åtkomst granskningar

Om din administratör har gett dig behörighet att utföra dina egna åtkomst granskningar kan du hantera dina grupper eller appar åtkomst från panelen **åtkomst granskningar** på Portal sidan **Mina appar** .

>[!Note]
>Om du inte ser panelen **åtkomst granskningar** innebär det antingen att du inte har behörighet att utföra åtkomst granskningar eller att du inte har några väntande granskningar som väntar på ditt godkännande. Om du tror att du borde ha åtkomst till panelen kan du kontakta supportavdelningen om du behöver hjälp.

### <a name="to-perform-your-access-reviews"></a>Så här utför du åtkomst granskningar

1. Logga in på ditt arbets-eller skol konto.

2. Öppna webbläsaren och gå till https://myapps.microsoft.com, eller Använd länken som tillhandahålls av din organisation. Du kan till exempel dirigeras till en anpassad sida för din organisation, till exempel https://myapps.microsoft.com/contoso.com.

    Sidan **appar** visas och visar alla molnbaserade appar som ägs av din organisation och som du kan använda.

    ![Sidan appar i portalen Mina appar](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Välj panelen **åtkomst granskningar** för att se en lista över åtkomst granskningar som väntar på ditt godkännande.

    ![Sidan åtkomst granskningar med väntande åtkomst granskningar för organisationen](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Välj **Starta granskning** för att starta åtkomst granskningen.

5. Granska din åtkomst och ta reda på om det fortfarande är nödvändigt.

    ![Sidan åtkomst granskning, som visar gransknings informationen](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Om du är administratör och har behörighet att granska organisationens åtkomst till grupper och appar, ser du en annan sida. Mer information om hur du granskar grupper eller appar för din organisation finns i [Granska åtkomst till grupper eller program i åtkomst granskningar i Azure AD](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review).

6. Välj **Ja** om du vill behålla åtkomsten eller **Nej** om du vill ta bort åtkomsten.

    Om du väljer **Ja**kan du behöva ange en motivering i rutan **orsak** .

    ![Sidan åtkomst granskning, som visar rutan orsak med exempel text](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Välj **Skicka**.

    Åtkomst granskningen är klar och du kommer tillbaka till portalen för **Mina appar** .

    >[!Note]
    >Du kan när som helst ändra din åtkomst tills din åtkomst gransknings period är slut. Om du tar bort din åtkomst till en app eller grupp tas den inte bort omedelbart. Borttagningen sker när åtkomst gransknings perioden är slut eller när en administratör stänger granskningen.

## <a name="next-steps"></a>Nästa steg

- [Få åtkomst till och använda appar på Mina appar-portalen](my-apps-portal-end-user-access.md).

- [Ändra din profil information](my-apps-portal-end-user-update-profile.md).

- [Visa och uppdatera dina grupper – relaterad information](my-apps-portal-end-user-groups.md).
