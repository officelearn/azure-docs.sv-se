---
title: Hur hanterar användarkonton i Azure API Management | Microsoft Docs
description: Lär dig att skapa eller bjuda in användare i Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 6a4ad827c39ec106acdc7b52a5b769ab6e7febf8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59783819"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Hantera användarkonton i Azure API Management

I API Management är utvecklare användare av API: er som du exponerar med hjälp av API Management. Den här guiden visar hur du skapar och bjuda in utvecklare att använda API: er och produkter att du gör tillgängliga för dem med API Management-instans. Information om hur du hanterar användarkonton programmässigt finns i den [användarentiteten](https://docs.microsoft.com/en-us/rest/api/apimanagement/user) dokumentationen i den [API Management REST](/rest/api/apimanagement/) referens.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Utför uppgifterna i den här artikeln: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-developer"> </a>Skapa en ny utvecklare

Om du vill lägga till en ny användare, följer du stegen i det här avsnittet:

1. Välj den **användare** fliken till vänster på skärmen.
2. Tryck på **+ Lägg till**.
3. Ange rätt information för användaren.
4. Tryck på **Lägg till**.

    ![Lägg till en ny användare](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Nyligen skapade utvecklarkonton är som standard **Active**, och som är associerade med den **utvecklare** grupp. Konton som tillhör en **active** tillstånd kan användas för åtkomst till alla API: er som de har prenumerationer. Om du vill associera den nyligen skapade utvecklaren med ytterligare grupper, se [associera grupper med utvecklare][How to associate groups with developers].

## <a name="invite-developer"> </a>Bjuda in utvecklare
Om du vill bjuda in utvecklare, följer du stegen i det här avsnittet:

1. Välj den **användare** fliken till vänster på skärmen.
2. Tryck på **+ bjuda in**.

Ett bekräftelsemeddelande visas, men nyligen inbjudna utvecklaren visas inte i listan tills när de har accepterat inbjudan. 

När en utvecklare bjuds in, skickas ett e-postmeddelande till utvecklaren. Den här e-post skapas med en mall och kan anpassas. Mer information finns i [Konfigurera e-postmallar][Configure email templates].

När inbjudan har accepterats blir kontot aktiv.

## <a name="block-developer"> </a> Inaktivera eller återaktivera ett utvecklarkonto

Som standard är nyskapade eller inbjudna utvecklarkonton **Active**. Om du vill inaktivera ett developer-konto, klickar du på **blockera**. Om du vill återaktivera ett blockerade developer-konto, klickar du på **aktivera**. Ett blockerat developer-konto kan inte komma åt developer-portalen eller anropa alla API: er. Om du vill ta bort ett användarkonto, klickar du på **ta bort**.

Följ stegen nedan om du vill blockera en användare.

1. Välj den **användare** fliken till vänster på skärmen.
2. Klicka på den användare som du vill blockera.
3. Tryck på **blockera**.

## <a name="reset-a-user-password"></a>Återställa en användarlösenord

Om du vill arbeta programmeringsbaserat med konton, se den [användarentiteten](https://docs.microsoft.com/en-us/rest/api/apimanagement/user) dokumentationen i den [API Management REST](/rest/api/apimanagement/) referens. Om du vill återställa lösenordet för ett användarkonto till ett specifikt värde, kan du använda den [uppdatera en användare](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity#UpdateUser) igen och ange önskat lösenord.

## <a name="next-steps"> </a>Nästa steg
När ett developer-konto har skapats kan du koppla den till roller och den prenumerera på produkter och API: er. Mer information finns i [hur du skapar och använda grupper för][How to create and use groups].

[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png


[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates
