---
title: Hantera organisationens åtkomst till appar & grupper – Azure AD
description: Lär dig hur du utför en åtkomstgranskning för att hantera säkerhetsåtkomst för organisationens appar och grupper från portalen Mina appar.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: c71195b247af6d5046d88d3e6918a660eddf09b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062394"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Utföra en åtkomstgranskning från portalen Mina appar

Du kan använda ditt arbets- eller skolkonto med den webbaserade **Portalen Mina Appar** för att utföra åtkomstgranskningar för dina appar och grupper. Åtkomstgranskningar hjälper dig att hantera inaktuell åtkomst eller ändrade åtkomstkrav och ser till att de granskas och uppdateras.

Om du inte har tillgång till portalen **Mina appar** kontaktar du helpdesk för att få tillstånd.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Det här innehållet är avsett för **Användare av Mina appar.** Om du är administratör kan du hitta mer information om hur du konfigurerar och hanterar dina molnbaserade appar i [dokumentationen för programhantering](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="manage-access-reviews"></a>Hantera åtkomstgranskningar

Om administratören har gett dig behörighet att utföra dina egna åtkomstgranskningar kan du hantera dina grupper eller appars åtkomst från panelen **Access-granskningar** på portalsidan **Mina appar.**

>[!Note]
>Om du inte ser panelen **Access-recensioner** betyder det antingen att du inte har behörighet att utföra åtkomstgranskningar eller att du inte har några väntande granskningar som väntar på ditt godkännande. Om du tycker att du ska ha tillgång till panelen kontaktar du helpdesk för att få hjälp.

## <a name="to-perform-your-access-reviews"></a>Så här gör du dina åtkomstgranskningar

1. Logga in på ditt arbets- eller skolkonto.

2. Öppna webbläsaren och gå https://myapps.microsoft.comtill , eller använd länken som tillhandahålls av din organisation. Du kan till exempel dirigeras till en anpassad https://myapps.microsoft.com/contoso.comsida för din organisation, till exempel .

    Sidan **Appar** visas som visar alla molnbaserade appar som ägs av din organisation och är tillgängliga för dig att använda.

    ![Sidan Appar i portalen Mina appar](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Välj panelen **Access-granskningar** om du vill se en lista över åtkomstgranskningar som väntar på ditt godkännande.

    ![Sidan Åtkomstgranskningar med väntande åtkomstgranskningar för organisationen](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Välj **Börja granska** för att starta din åtkomstgranskning.

5. Granska din åtkomst och ta reda på om det fortfarande är nödvändigt.

    ![Sidan Access granskning, som visar granskningsinformation](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Om du är administratör och får granska organisationens åtkomst till grupper och appar visas en annan sida. Mer information om hur du granskar grupper eller appar för din organisation finns i [Granska åtkomst till grupper eller program i Azure AD Access Reviews](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review).

6. Välj **Ja** om du vill behålla åtkomsten eller **Nej** om du vill ta bort åtkomsten.

    Om du väljer **Ja**kan du behöva ange en justering i rutan **Orsak.**

    ![Sidan Access-granskning, som visar rutan Orsak med exempeltext](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Välj **Skicka**.

    Din åtkomstgranskning är klar och du återvänder till portalen **Mina appar.**

    >[!Note]
    >Du kan ändra din åtkomst när som helst tills åtkomstgranskningsperioden är. Om du tar bort åtkomsten till en app eller grupp tas den inte bort omedelbart. Borttagningen sker när åtkomstgranskningsperioden avslutas eller när en administratör stänger granskningen.

## <a name="next-steps"></a>Nästa steg

- [Komma åt och använda appar på portalen Mina appar](my-apps-portal-end-user-access.md)
- [Ändra din profilinformation](my-apps-portal-end-user-update-profile.md)
- [Visa och uppdatera grupprelaterad information](my-apps-portal-end-user-groups.md)
