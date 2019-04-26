---
title: Utföra en åtkomstgranskning från portalen Mina appar - Azure Active Directory | Microsoft Docs
description: Lär dig mer om att visa och hantera säkerhetsåtkomsten för din organisations appar och grupper.
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
ms.openlocfilehash: 551bbe4e994e15688c59fd395ab762e14927dfda
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60482888"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Utföra en åtkomstgranskning från portalen Mina appar
Du kan använda ditt arbets- eller skolkonto med den webbaserade **Mina appar** portalen för att visa och starta många av organisationens molnbaserade appar, att uppdatera en del av din profil och konto information att se din **grupper** information och utföra **åtkomstgranskningar** för dina appar och grupper. Om du inte har åtkomst till den **Mina appar** portalen, måste du kontakta din supportavdelning för behörighet.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Innehållet är avsett för användare. Om du är administratör kan du hittar mer information om hur du konfigurerar och hanterar dina molnbaserade appar i den [dokumentation om Application Management](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="manage-access-reviews"></a>Hantera åtkomstgranskningar
Om din administratör har gett dig behörighet att utföra egna åtkomstgranskningar, kan du hantera dina grupper eller appar åtkomst från den **Åtkomstgranskningar** panel på den **Mina appar** portalsidan.

>[!Note]
>Om du inte ser den **Åtkomstgranskningar** skriver ut den antingen innebär att du inte har behörighet att utföra åtkomstgranskningar eller att du inte har några väntande granskningar som väntar på ditt godkännande. Om du tror att du ska ha åtkomst till panelen, kontakta supportavdelningen för hjälp.

### <a name="to-perform-your-access-reviews"></a>Att utföra dina åtkomstgranskningar

1.  Logga in på ditt arbets- eller skolkonto konto.

2.  Öppna webbläsaren och gå till https://myapps.microsoft.com, eller Använd länken som tillhandahålls av din organisation. Exempelvis kan du dirigeras till en anpassad sida för din organisation, till exempel https://myapps.microsoft.com/contoso.com.

    Den **appar** visas, som visar alla molnbaserade appar som ägs av din organisation och som du kan använda.

    ![Sidan appar i portalen Mina appar](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Välj den **Åtkomstgranskningar** ikonen för att visa en lista över åtkomst går igenom väntar på ditt godkännande.

    ![Sidan med väntande åtkomstgranskningar för organisationen med åtkomstgranskningar](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Välj **påbörja granskning** startar din åtkomstgranskning.

5. Granska din åtkomst och kontrollera om det är ändå nödvändigt.

    ![Åtkomst granskningssidan som visar information för granskning](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Om du är administratör och kunna granska din organisation åtkomst till grupper och appar, visas en annan sida. Läs mer om att granska grupper eller appar för din organisation, [granska åtkomst till grupper eller program i Azure AD-Åtkomstgranskningar](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review).

6. Välj **Ja** att hålla din åtkomst eller **nr** att ta bort din åtkomst.

    Om du väljer **Ja**, du kan behöva ange en motivering i den **orsak** box.

    ![Åtkomst Granska sidan som visar rutan orsak med exempeltext](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Välj **Skicka**.

    Din åtkomstgranskning är klar och du kommer tillbaka till den **Mina appar** portal.

    >[!Note]
    >Du kan ändra din åtkomst när som helst tills din åtkomst granska har upphört att gälla. Om du tar bort din åtkomst till en app eller en grupp kan tas den inte bort direkt. Borttagningen sker när åtkomst granska har upphört att gälla eller när en administratör stängs granskningen. 

## <a name="next-steps"></a>Nästa steg

- [Komma åt och använda appar på portalen Mina appar](my-apps-portal-end-user-access.md).

- [Ändra profilinformationen](my-apps-portal-end-user-update-profile.md).

- [Visa och uppdatera dina grupper-relaterad information](my-apps-portal-end-user-groups.md).