---
title: Hanterade identiteter för Azure-resurser – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om hur du använder hanterade identiteter för Azure-resurser med Azure Event Hubs
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 05/20/2019
ms.author: shvija
ms.openlocfilehash: 4e6f16a15547583baab63f452504d36eb2e43b85
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978485"
---
# <a name="managed-identities-for-azure-resources-with-event-hubs"></a>Hanterade identiteter för Azure-resurser med Event Hubs

[Hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) är en över flera Azure-funktion som låter dig skapa en säker identitet som är kopplad till distributionen som programkoden körs under. Därefter kan du associera den identiteten med åtkomstkontroll roller som ger anpassade behörigheter för åtkomst till specifika Azure-resurser som programmet behöver. 

Med hanterade identiteter hanterar den här identiteten för körning i Azure-plattformen. Du behöver inte att lagra och skydda åtkomstnycklar i din programkod eller konfiguration för identiteten själva, eller för de resurser du behöver för att få åtkomst till. Ett Event Hubs-klientapp som körs i ett program i Azure App Service eller på en virtuell dator med hanterade identiteter för aktiverat stöd för Azure-resurser behöver inte hantera SAS regler och nycklar eller andra åtkomsttoken. Klientappen behöver bara slutpunktsadress för Event Hubs-namnområdet. När appen ansluter Binder Händelsehubbar hanterad identitet kontext till klienten i en åtgärd som visas i ett exempel senare i den här artikeln.

När den är associerad med en hanterad identitet, kan en händelsehubbklient göra alla behöriga åtgärder. Auktorisering genom att associera en hanterad identitet med Event Hubs-roller. 

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs roller och behörigheter
Du kan lägga till en hanterad identitet till den **Dataägaren för Event Hubs** rollen för ett namnområde för Event Hubs. Den här rollen ger identitet, fullständig behörighet (för hantering och åtgärder för) alla entiteter i namnområdet.

>[!IMPORTANT]
> Vi tidigare stöd för att lägga till hanterad identitet till den **ägare** eller **deltagare** roll. Men data behörighet för **ägare** och **deltagare** rollen respekteras inte längre. Om du använder den **ägare** eller **deltagare** roll, växla till med hjälp av den **Dataägaren för Event Hubs** roll.

Följ dessa steg om du vill använda den nya inbyggda rollen: 

1. Navigera till [Azure Portal](https://portal.azure.com)
2. Gå till Event Hubs-namnområdet.
3. På den **Event Hubs Namespace** väljer **åtkomst Control(IAM)** menyn till vänster.
4. På den **åtkomstkontroll (IAM)** väljer **Lägg till** i den **Lägg till en rolltilldelning** avsnittet. 

    ![Lägg till en knapp för tilldelning av roll](./media/event-hubs-managed-service-identity/add-role-assignment-button.png)
5. På den **Lägg till rolltilldelning** gör du följande steg: 
    1. För **rollen**väljer **Dataägaren för Azure Event Hubs**. 
    2. Välj den **identitet** som ska läggas till rollen.
    3. Välj **Spara**. 

        ![Event Hubs Data ägarrollen](./media/event-hubs-managed-service-identity/add-role-assignment-dialog.png)
6. Växla till den **rolltilldelningar** sidan och bekräfta att användaren har lagts till i **Dataägaren för Azure Event Hubs** roll. 

    ![Bekräfta användare läggs till i rollen](./media/event-hubs-managed-service-identity/role-assignments.png)
 
## <a name="use-event-hubs-with-managed-identities-for-azure-resources"></a>Använda Event Hubs med hanterade identiteter för Azure-resurser

I följande avsnitt beskrivs följande steg:

1. Skapa och distribuera ett exempelprogram som körs under en hanterad identitet.
2. Bevilja åtkomst identitet till en Event Hubs-namnområdet.
3. Hur programmet interagerar med händelsehubbar med hjälp av den identiteten.

Den här introduktionen beskriver ett webbprogram i [Azure App Service](https://azure.microsoft.com/services/app-service/). De steg som krävs för ett VM-värdbaserade program liknar varandra.

### <a name="create-an-app-service-web-application"></a>Skapa en App Service-webbapp

Det första steget är att skapa en App Service ASP.NET-program. Om du inte är bekant med hur du gör detta i Azure följer [i den här guiden](../app-service/app-service-web-get-started-dotnet-framework.md). Men istället för att skapa ett MVC-program som visas i självstudien skapar du en Web Forms-program.

### <a name="set-up-the-managed-identity"></a>Ställ in den hanterade identitet

När du har skapat programmet går du till den nya webbappen i Azure portal (visas också i anvisningar) och gå sedan till den **hanterad tjänstidentitet** sidan och aktivera funktionen: 

![Hanterad tjänstidentitet sidan](./media/event-hubs-managed-service-identity/msi1.png)
 
När du har aktiverat funktionen, en ny tjänstidentitet skapas i Azure Active Directory och som konfigurerats i App Service-värden.

### <a name="create-a-new-event-hubs-namespace"></a>Skapa ett nytt namnområde för Event Hubs

Nästa [skapa ett namnområde för Event Hubs](event-hubs-create.md). 

Gå till namnområdet **åtkomstkontroll (IAM)** på portalen och klicka sedan på **Lägg till rolltilldelning** att lägga till den hantera identitet som den **ägare** roll. Du gör detta genom att söka efter namnet på webbprogrammet i den **Lägg till behörigheter** panelen **Välj** fältet och sedan klickar du på posten. Klicka sedan på **Spara**. Den hanterade identitet för webbprogrammet nu har åtkomst till Event Hubs-namnområdet och till händelsehubben du skapade tidigare. 

### <a name="run-the-app"></a>Kör appen

Nu ändra standardsidan för ASP.NET-programmet som du skapade. Du kan också använda web programkoden från [den här GitHub-lagringsplatsen](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp). 

När du startar appen, pekar EventHubsMSIDemo.aspx din webbläsare. Du kan också ange den som din startsida. Koden finns i filen EventHubsMSIDemo.aspx.cs. Resultatet är ett minimalt webbprogram med några fält och **skicka** och **får** knappar som ansluter till Event Hubs antingen skicka eller ta emot händelser. 

Observera hur [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) -objektet initieras. Istället för att använda den för delad åtkomst-Token (SAS)-Tokenleverantören koden skapar en tokenleverantör för den hanterade identitet med den `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)` anropa. Det finns därför inga hemligheter för att spara och använda. Flödet av hanterade identitetskontexten Event Hubs och auktorisering med handskakningen hanteras automatiskt av den tokenleverantör, vilket är en modell som är enklare än med hjälp av SAS.

När du har gjort dessa ändringar, publicera och köra programmet. Du kan få rätt publicera data genom att hämta och importera en publiceringsprofil i Visual Studio:

![Importera publiceringsprofilen](./media/event-hubs-managed-service-identity/msi3.png)
 
Ange namnet på namnområdet och namnet på den entitet som du skapade för att skicka eller ta emot meddelanden, och klicka sedan på antingen **skicka** eller **får**. 
 
Den hanterade identitet fungerar endast i Azure-miljön och endast i App Service-distributionen som du har konfigurerat den. Hanterade identiteter fungerar inte med App Service-distributionsplatser just nu.

## <a name="next-steps"></a>Nästa steg

Besök följande länkar för mer utförlig information om Event Hubs:

* Kom igång med en [kurs om händelsehubbar](event-hubs-dotnet-standard-getstarted-send.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Prisinformation om Händelsehubbar](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Exempelprogram som använder Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
