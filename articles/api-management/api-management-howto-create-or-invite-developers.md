---
title: "Hur hanterar användarkonton i Azure API Management | Microsoft Docs"
description: "Lär dig hur du skapar eller bjuda in användare i Azure API Management"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 078abfa5-1e4f-4c9d-b9c7-a172bd19c1a2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 34343194cb9896916cb02cb8acf61af24f0784c9
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Hantera användarkonton i Azure API Management
Utvecklare finns i API-hantering, användare av API: er som du exponera med hjälp av API-hantering. Den här guiden visar hur du skapar och bjuda in utvecklare att använda API: er och produkter som du gör tillgängliga för dem med din API Management-instans. Information om hur du hanterar användarkonton programmässigt finns i [användarentiteten](https://msdn.microsoft.com/library/azure/dn776330.aspx) dokumentation i den [API Management REST](https://msdn.microsoft.com/library/azure/dn776326.aspx) referens.

## <a name="create-developer"></a>Skapa en ny utvecklare
Klicka för att skapa en ny utvecklare **Publisher portal** i Azure Portal för API Management-tjänsten. När du gör det kommer du till utgivarportalen för API Management. Om du inte har skapat en API Management-tjänstinstans än läser du [Skapa en API Management-tjänstinstans][Create an API Management service instance] i självstudiekursen [Komma igång med Azure API Management][Get started with Azure API Management].

![Utgivarportalen][api-management-management-console]

Klicka på **användare** från den **API Management** menyn till vänster och klicka sedan på **lägga till användare**.

![Skapa utvecklare][api-management-create-developer]

Ange den **e-post**, **lösenord**, och **namn** för nya utvecklare och klickar på **spara**.

![Skapa utvecklare][api-management-add-new-user]

Nyligen skapade developer konton är som standard **Active**, och som är associerade med den **utvecklare** grupp.

![Nya utvecklare][api-management-new-developer]

Utvecklare konton som är i ett **active** tillstånd kan användas för att få tillgång till alla de API: er som de har prenumerationer. Om du vill associera den nyligen skapade utvecklaren med ytterligare grupper, se [hur du associerar grupper med utvecklare][How to associate groups with developers].

## <a name="invite-developer"></a>Bjuda in en utvecklare
Om du vill bjuda in en utvecklare, klickar du på **användare** från den **API Management** menyn till vänster och klicka sedan på **bjuda in användare**.

![Bjud in utvecklare][api-management-invite-developer]

Ange namn och e-postadress som utvecklare och på **bjuda in**.

![Bjud in utvecklare][api-management-invite-developer-window]

Ett meddelande visas, men nyligen inbjudna utvecklaren visas inte i listan förrän efter att de har accepterat inbjudan. 

![Bjud in bekräftelse][api-management-invite-developer-confirmation]

När du uppmanas att utvecklare skickas ett e-postmeddelande till utvecklaren. Den här e-post skapas med en mall och anpassas. Mer information finns i [Konfigurera e-postmallar][Configure email templates].

När du har accepterat inbjudan blir kontot aktiv.

## <a name="block-developer"></a> Inaktivera eller återaktivera ett utvecklarkonto
Som standard nyskapade eller inbjudna developer konton är **Active**. Om du vill inaktivera ett utvecklarkonto klickar du på **Block**. Om du vill återaktivera blockerade utvecklarkonto klickar du på **aktivera**. Blockerade utvecklarkonto kan inte komma åt developer-portalen eller anropa alla API: er. Om du vill ta bort ett användarkonto, klickar du på **ta bort**.

![Block-utvecklare][api-management-new-developer]

## <a name="reset-a-user-password"></a>Återställa en användarlösenord
Om du vill återställa lösenordet för ett användarkonto, klickar du på namnet på kontot.

![Återställa lösenord][api-management-view-developer]

Klicka på **Återställ lösenord** att skicka en länk till användaren att återställa sina lösenord.

![Återställa lösenord][api-management-reset-password]

Du kan via programmering med användarkonton, finns i [användarentiteten](https://msdn.microsoft.com/library/azure/dn776330.aspx) dokumentation i den [API Management REST](https://msdn.microsoft.com/library/azure/dn776326.aspx) referens. Om du vill återställa lösenordet för ett användarkonto till ett visst värde, kan du använda den [uppdatera en användare](https://msdn.microsoft.com/library/azure/dn776330.aspx#UpdateUser) igen och ange önskade lösenordet.

## <a name="pending-verification"></a>Väntande verifiering
![Väntande verifiering][api-management-pending-verification]

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

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates
