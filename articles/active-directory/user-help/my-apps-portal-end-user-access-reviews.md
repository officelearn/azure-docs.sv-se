---
title: Hantera organisationens åtkomst till appar & grupper – Azure AD
description: Lär dig hur du utför en åtkomst granskning för att hantera säkerhets åtkomst för din organisations appar och grupper från portalen Mina appar.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 10/19/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 8da9e97aaa2c6d99990df4221d8b6e99ab18ac83
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124820"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Utföra en åtkomst granskning från portalen Mina appar

Du kan använda ditt arbets-eller skol konto med den webbaserade **Mina Apps** -portalen för att utföra åtkomst granskningar för dina appar och grupper. Åtkomst granskningar hjälper dig att hantera föråldrad åtkomst eller ändra åtkomst kraven och se till att de granskas och uppdateras.

Om du inte har åtkomst till portalen för **Mina appar** , Kontakta supportavdelningen för att få behörighet.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Det här innehållet är avsett för **Mina Apps** -användare. Om du är administratör kan du hitta mer information om hur du konfigurerar och hanterar dina molnbaserade appar i [program hanterings dokumentationen](../manage-apps/index.yml).

## <a name="manage-access-reviews"></a>Hantera åtkomst granskningar

Om din administratör har gett dig behörighet att utföra dina egna åtkomst granskningar kan du hantera dina grupper eller appar åtkomst från panelen **åtkomst granskningar** på Portal sidan **Mina appar** .

>[!Note]
>Om du inte ser panelen **åtkomst granskningar** innebär det antingen att du inte har behörighet att utföra åtkomst granskningar eller att du inte har några väntande granskningar som väntar på ditt godkännande. Om du tror att du borde ha åtkomst till panelen kan du kontakta supportavdelningen om du behöver hjälp.

## <a name="to-perform-your-access-reviews"></a>Så här utför du åtkomst granskningar

1. Logga in på ditt arbets-eller skol konto.

2. Öppna webbläsaren och gå till https://myapps.microsoft.com eller Använd den länk som tillhandahålls av din organisation. Du kan till exempel dirigeras till en anpassad sida för din organisation, till exempel https://myapps.microsoft.com/contoso.com .

    Sidan **appar** visas och visar alla molnbaserade appar som ägs av din organisation och som du kan använda.

    ![Sidan appar i portalen Mina appar](media/my-apps-portal/my-apps-home.png)

3. Välj panelen **åtkomst granskningar** för att se en lista över åtkomst granskningar som väntar på ditt godkännande.

    ![Sidan åtkomst granskningar med väntande åtkomst granskningar för organisationen](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Välj **Starta granskning** för att starta åtkomst granskningen.

5. Granska din åtkomst och ta reda på om det fortfarande är nödvändigt.

    ![Sidan åtkomst granskning, som visar gransknings informationen](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Om du är administratör och har behörighet att granska organisationens åtkomst till grupper och appar, ser du en annan sida. Mer information om hur du granskar grupper eller appar för din organisation finns i [Granska åtkomst till grupper eller program i åtkomst granskningar i Azure AD](../governance/perform-access-review.md).

6. Välj **Ja** om du vill behålla åtkomsten eller **Nej** om du vill ta bort åtkomsten.

    Om du väljer **Ja** kan du behöva ange en motivering i rutan **orsak** .

    ![Sidan åtkomst granskning, som visar rutan orsak med exempel text](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Välj **Skicka** .

    Åtkomst granskningen är klar och du kommer tillbaka till portalen för **Mina appar** .

    >[!Note]
    >Du kan när som helst ändra din åtkomst tills din åtkomst gransknings period är slut. Om du tar bort din åtkomst till en app eller grupp tas den inte bort omedelbart. Borttagningen sker när åtkomst gransknings perioden är slut eller när en administratör stänger granskningen.

## <a name="next-steps"></a>Nästa steg

- [Komma åt och använda appar på portalen Mina appar](my-apps-portal-end-user-access.md)
- [Ändra din profilinformation](./my-account-portal-settings.md)
- [Visa och uppdatera dina grupper – relaterad information](my-apps-portal-end-user-groups.md)