---
title: Hur hanterar användarkonton i Azure API Management | Microsoft Docs
description: Lär dig hur du skapar eller bjuda in användare i Azure API Management
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
ms.openlocfilehash: 53ed914a3890aab1dd49536856feeb8975986e00
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "33934816"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Hantera användarkonton i Azure API Management
Utvecklare finns i API-hantering, användare av API: er som du exponera med hjälp av API-hantering. Den här guiden visar hur du skapar och bjuda in utvecklare att använda API: er och produkter som du gör tillgängliga för dem med din API Management-instans. Information om hur du hanterar användarkonton programmässigt finns i [användarentiteten](https://msdn.microsoft.com/library/azure/dn776330.aspx) dokumentation i den [API Management REST](https://msdn.microsoft.com/library/azure/dn776326.aspx) referens.

## <a name="prerequisites"></a>Förutsättningar

Utföra åtgärder i den här artikeln: [skapa en instans av Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-developer"> </a>Skapa en ny utvecklare

Följ stegen i det här avsnittet för att lägga till en ny användare:

1. Välj den **användare** fliken till vänster på skärmen.
2. Tryck på **+ Lägg till**.
3. Ange rätt information för användaren.
4. Tryck på **Lägg till**.

    ![Lägg till en ny användare](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Nyligen skapade developer konton är som standard **Active**, och som är associerade med den **utvecklare** grupp. Utvecklare konton som är i ett **active** tillstånd kan användas för att få tillgång till alla de API: er som de har prenumerationer. Om du vill associera den nyligen skapade utvecklaren med ytterligare grupper, se [hur du associerar grupper med utvecklare][How to associate groups with developers].

## <a name="invite-developer"> </a>Bjud in en utvecklare
Följ stegen i det här avsnittet för att bjuda in en utvecklare:

1. Välj den **användare** fliken till vänster på skärmen.
2. Tryck på **+ bjuda in**.

Ett meddelande visas, men nyligen inbjudna utvecklaren visas inte i listan förrän efter att de har accepterat inbjudan. 

När du uppmanas att utvecklare skickas ett e-postmeddelande till utvecklaren. Den här e-post skapas med en mall och anpassas. Mer information finns i [Konfigurera e-postmallar][Configure email templates].

När du har accepterat inbjudan blir kontot aktiv.

## <a name="block-developer"> </a> Inaktivera eller återaktivera ett utvecklarkonto

Som standard nyskapade eller inbjudna developer konton är **Active**. Om du vill inaktivera ett utvecklarkonto klickar du på **Block**. Om du vill återaktivera blockerade utvecklarkonto klickar du på **aktivera**. Blockerade utvecklarkonto kan inte komma åt developer-portalen eller anropa alla API: er. Om du vill ta bort ett användarkonto, klickar du på **ta bort**.

Följ anvisningarna nedan om du vill blockera en användare.

1. Välj den **användare** fliken till vänster på skärmen.
2. Klicka på den användare som du vill blockera.
3. Tryck på **Block**.

## <a name="reset-a-user-password"></a>Återställa en användarlösenord

Du kan via programmering med användarkonton, finns i [användarentiteten](https://msdn.microsoft.com/library/azure/dn776330.aspx) dokumentation i den [API Management REST](https://msdn.microsoft.com/library/azure/dn776326.aspx) referens. Om du vill återställa lösenordet för ett användarkonto till ett visst värde, kan du använda den [uppdatera en användare](https://msdn.microsoft.com/library/azure/dn776330.aspx#UpdateUser) igen och ange önskade lösenordet.

## <a name="next-steps"> </a>Nästa steg
När ett utvecklarkonto har skapats kan du koppla den till roller och prenumerera på produkter och API: er. Mer information finns i [hur du skapar och använda grupper för][How to create and use groups].

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
