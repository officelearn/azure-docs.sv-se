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
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 784d8c9280aeff7224f90ecee0b16c9c30381aeb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746920"
---
# <a name="managed-identities-for-azure-resources-with-event-hubs"></a>Hanterade identiteter för Azure-resurser med Event Hubs

[Hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) är en över flera Azure-funktion som låter dig skapa en säker identitet som är kopplad till distributionen som programkoden körs under. Därefter kan du associera den identiteten med åtkomstkontroll roller som ger anpassade behörigheter för åtkomst till specifika Azure-resurser som programmet behöver. 

Med hanterade identiteter hanterar den här identiteten för körning i Azure-plattformen. Du behöver inte att lagra och skydda åtkomstnycklar i din programkod eller konfiguration för identiteten själva, eller för de resurser du behöver för att få åtkomst till. Ett Event Hubs-klientapp som körs i ett program i Azure App Service eller på en virtuell dator med hanterade identiteter för aktiverat stöd för Azure-resurser behöver inte hantera SAS regler och nycklar eller andra åtkomsttoken. Klientappen behöver bara slutpunktsadress för Event Hubs-namnområdet. När appen ansluter Binder Händelsehubbar hanterad identitet kontext till klienten i en åtgärd som visas i ett exempel senare i den här artikeln.

När den är associerad med en hanterad identitet, kan en händelsehubbklient göra alla behöriga åtgärder. Auktorisering genom att associera en hanterad identitet med Event Hubs-roller. 

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs roller och behörigheter

Du kan bara lägga till en hanterad identitet ”ägare” eller ”deltagare” rollerna för ett namnområde för Event Hubs, vilket ger fullständig kontrollen identitet i alla entiteter i namnområdet. Hantering av åtgärder som ändrar namnområde topologin är från början stöds dock bara om Azure Resource Manager. Det är inte via det inbyggda Event Hubs REST-hanteringsgränssnittet. Det här stödet innebär också att du inte kan använda .NET Framework-klienten [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objekt i en hanterad identitet. 
 
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

Nästa [skapa ett namnområde för Event Hubs](event-hubs-create.md) i någon av de Azure-regioner som har förhandsversionsstöd för hanterade identiteter för Azure-resurser: **USA, Öst**, **USA, Öst 2**, eller **Västeuropa**. 

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
