---
title: Hanterad tjänstidentitet med Händelsehubbar i Azure preview | Microsoft Docs
description: Använda hanterade tjänstidentiteter med Azure Event Hubs
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: sethm
ms.openlocfilehash: abff3f715a1fccba172147f02b83f7209f87cf9e
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902524"
---
# <a name="managed-service-identity-preview"></a>Hanterad tjänstidentitet (förhandsgranskning)

En hanterad tjänstidentitet (MSI) är en över flera Azure-funktion som gör det möjligt för dig att skapa en säker identitet som är kopplad till distributionen som programkoden körs under. Därefter kan du associera den identiteten med åtkomstkontroll roller som ger anpassade behörigheter för åtkomst till specifika Azure-resurser som programmet behöver. 

Med MSI hanterar den här identiteten för körning i Azure-plattformen. Du behöver inte att lagra och skydda åtkomstnycklar i din programkod eller konfiguration för identiteten själva, eller för de resurser du behöver för att få åtkomst till. Ett Event Hubs-klientapp som körs i ett program i Azure App Service eller i en virtuell dator med aktiverad MSI-stöd behöver inte hantera SAS regler och nycklar eller andra åtkomsttoken. Klientappen behöver bara slutpunktsadress för Event Hubs-namnområdet. När appen ansluter Binder Händelsehubbar MSI-kontexten till klienten i en åtgärd som visas i ett exempel senare i den här artikeln.

När den är associerad med hanterade tjänstidentiteter, kan en händelsehubbklient utföra alla behöriga åtgärder. Auktorisering genom att associera en MSI med Event Hubs-roller. 

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs roller och behörigheter

Den första offentliga förhandsversionen kan lägga du bara till en hanterad tjänstidentitet ”ägare” eller ”deltagare” rollerna för ett namnområde för Event Hubs, vilket ger fullständig kontrollen identitet i alla entiteter i namnområdet. Hantering av åtgärder som ändrar namnområde topologin är från början stöds dock bara om Azure Resource Manager och inte via gränssnittet för hantering av inbyggda Event Hubs REST. Det här stödet innebär också att du inte kan använda .NET Framework-klienten [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objekt i en hanterad tjänstidentitet. 
 
## <a name="use-event-hubs-with-a-managed-service-identity"></a>Använda Event Hubs med en hanterad tjänstidentitet

I följande avsnitt beskrivs de steg som krävs för att skapa och distribuera ett exempelprogram som körs under en hanterad tjänstidentitet, hur du bevilja åtkomst till ett Event Hubs-namnområdet identitet och hur programmet interagerar med event hubs med som identitet.

Den här introduktionen beskriver ett webbprogram i [Azure App Service](https://azure.microsoft.com/services/app-service/). De steg som krävs för ett VM-värdbaserade program liknar varandra.

### <a name="create-an-app-service-web-application"></a>Skapa en App Service-webbapp

Det första steget är att skapa en App Service ASP.NET-program. Om du inte är bekant med hur du gör detta i Azure följer [i den här guiden](../app-service/app-service-web-get-started-dotnet-framework.md). Men istället för att skapa ett MVC-program som visas i självstudien skapar du en Web Forms-program.

### <a name="set-up-the-managed-service-identity"></a>Ställ in den hanterade tjänstidentiteten

När du har skapat programmet går du till den nya webbappen i Azure portal (visas också i anvisningar) och gå sedan till den **hanterad tjänstidentitet** sidan och aktivera funktionen: 

![](./media/event-hubs-managed-service-identity/msi1.png)
 
När du har aktiverat funktionen, en ny tjänstidentitet skapas i Azure Active Directory och som konfigurerats i App Service-värden.

### <a name="create-a-new-event-hubs-namespace"></a>Skapa ett nytt namnområde för Event Hubs

Nästa [skapa ett namnområde för Event Hubs](event-hubs-create.md) i någon av de Azure-regioner som har förhandsversionsstöd för MSI: **östra USA**, **USA, Öst 2**, eller **Västeuropa**. 

Gå till namnområdet **åtkomstkontroll (IAM)** på portalen och klicka sedan på **Lägg till** att lägga till den hanterade tjänstidentiteten för den **ägare** roll. Du gör detta genom att söka efter namnet på webbprogrammet i den **Lägg till behörigheter** panelen **Välj** fältet och sedan klickar du på posten. Klicka sedan på **Spara**.

![](./media/event-hubs-managed-service-identity/msi2.png)
 
Den hanterade tjänstidentiteten för webbprogrammet nu har åtkomst till Event Hubs-namnområdet och till händelsehubben du skapade tidigare. 

### <a name="run-the-app"></a>Kör appen

Nu ändra standardsidan för ASP.NET-programmet som du skapade. Du kan också använda web programkoden från [den här GitHub-lagringsplatsen](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp). 

>[!NOTE] 
> MSI-funktionen är i förhandsversion, måste du använda den [förhandsversionen av Service Bus-bibliotek](https://www.nuget.org/packages/WindowsAzure.ServiceBus/4.2.2-preview) för att komma åt den nya API: er. 

När du startar appen, pekar EventHubsMSIDemo.aspx din webbläsare. Du kan också ange den som din startsida. Koden finns i filen EventHubsMSIDemo.aspx.cs. Resultatet är ett minimalt webbprogram med några fält och **skicka** och **får** knappar som ansluter till Event Hubs antingen skicka eller ta emot händelser. 

Observera hur [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) -objektet initieras. Istället för att använda den för delad åtkomst-Token (SAS)-Tokenleverantören koden skapar en tokenleverantör för den hanterade tjänstidentiteten med den `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)` anropa. Det finns därför inga hemligheter för att spara och använda. Flödet av hanterad tjänst identitetskontexten Event Hubs och auktorisering med handskakningen hanteras automatiskt av den tokenleverantör, vilket är en modell som är enklare än med hjälp av SAS.

När du har gjort dessa ändringar, publicera och köra programmet. Ett enkelt sätt att få rätt publicerar data är att hämta och importera en publiceringsprofil i Visual Studio:

![](./media/event-hubs-managed-service-identity/msi3.png)
 
Ange namnet på namnområdet och namnet på den entitet som du skapade för att skicka eller ta emot meddelanden, och klicka sedan på antingen **skicka** eller **får**. 
 
Observera att den hanterade tjänstidentiteten fungerar bara i Azure-miljön och endast i App Service-distributionen som du har konfigurerat den. Observera också att hanterade tjänstidentiteter inte fungerar med App Service-distributionsplatser just nu.

## <a name="next-steps"></a>Nästa steg

Besök följande länkar för mer utförlig information om Event Hubs:

* Kom igång med en [kurs om händelsehubbar](event-hubs-dotnet-standard-getstarted-send.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Prisinformation om Händelsehubbar](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Exempelprogram som använder Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)