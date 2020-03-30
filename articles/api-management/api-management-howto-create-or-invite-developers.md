---
title: Så här hanterar du användarkonton i Azure API Management | Microsoft-dokument
description: Lär dig hur du skapar eller bjuder in användare i Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 9de75b5537b3e267116882e7f151d893de575071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70072471"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Hantera användarkonton i Azure API Management

I API Management är utvecklare användare av API:er som du exponerar med API Management. Den här guiden visar hur du skapar och bjuder in utvecklare att använda API:er och produkter som du gör tillgängliga för dem med API Management-instansen. Information om hur du hanterar användarkonton programmässigt finns i dokumentationen [för användarentitet](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user) i API [Management REST-referensen.](/rest/api/apimanagement/)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Krav

Slutför uppgifter i den här artikeln: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-new-developer"></a><a name="create-developer"> </a>Skapa en ny utvecklare

Så här lägger du till en ny användare:

1. Välj fliken **Användare** till vänster om skärmen.
2. Tryck på **+Lägg till**.
3. Ange lämplig information för användaren.
4. Tryck på **Lägg till**.

    ![Lägga till en ny användare](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Som standard är nyligen skapade utvecklarkonton **aktiva**och associerade med gruppen **Utvecklare.** Utvecklarkonton som är i ett **aktivt** tillstånd kan användas för att komma åt alla API:er som de har prenumerationer på. Information om hur du associerar den nyskapade utvecklaren med ytterligare grupper finns i [Så här associerar du grupper med utvecklare][How to associate groups with developers].

## <a name="invite-a-developer"></a><a name="invite-developer"> </a>Bjud in en utvecklare
Så här bjuder du in en utvecklare:

1. Välj fliken **Användare** till vänster om skärmen.
2. Tryck på **+Bjud in**.

Ett bekräftelsemeddelande visas, men den nyinbjudna utvecklaren visas inte i listan förrän de har accepterat inbjudan. 

När en utvecklare bjuds in skickas ett e-postmeddelande till utvecklaren. Detta e-postmeddelande genereras med hjälp av en mall och är anpassningsbar. Mer information finns i [Konfigurera e-postmallar][Configure email templates].

När inbjudan har accepterats blir kontot aktivt.

## <a name="deactivate-or-reactivate-a-developer-account"></a><a name="block-developer"> </a> Inaktivera eller återaktivera ett utvecklarkonto

Som standard är nyligen skapade eller inbjudna utvecklarkonton **aktiva**. Om du vill inaktivera ett utvecklarkonto klickar du på **Blockera**. Om du vill återaktivera ett blockerat utvecklarkonto klickar du på **Aktivera**. Ett blockerat utvecklarkonto kan inte komma åt utvecklarportalen eller anropa api:er. Om du vill ta bort ett användarkonto klickar du på **Ta bort**.

Så här blockerar du en användare:

1. Välj fliken **Användare** till vänster om skärmen.
2. Klicka på den användare som du vill blockera.
3. Tryck på **Blockera**.

## <a name="reset-a-user-password"></a>Återställa ett användarlösenord

Programmässigt arbete med användarkonton läser du dokumentationen för användarentiteter i API [Management REST API-referensen.](/rest/api/apimanagement/) Om du vill återställa ett lösenord för användarkontot till ett visst värde kan du använda åtgärden [Uppdatera en användare](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity#UpdateUser) och ange önskat lösenord.

## <a name="next-steps"></a><a name="next-steps"> </a>Nästa steg
När ett utvecklarkonto har skapats kan du associera det med roller och prenumerera på produkter och API:er. Mer information finns i [Så här skapar och använder du grupper][How to create and use groups].

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
