---
title: Hanterade tjänstidentiteten med Händelsehubbar i Azure preview | Microsoft Docs
description: Använda hanteringstjänster identiteter med Azure Event Hubs
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2017
ms.author: sethm
ms.openlocfilehash: dd50e4f6ebc5fdf5496a5127fde20bd052087b59
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2017
ms.locfileid: "26783475"
---
# <a name="managed-service-identity-preview"></a>Hanterade tjänstidentiteten (förhandsgranskning)

En hanterad tjänst identitet (MSI) är en mellan Azure-funktion som gör att du kan skapa en säker identitet som associeras med distributionen som programkoden körs under. Därefter kan du associera identiteten med åtkomstkontroll roller som ger anpassade behörigheter för åtkomst till specifika Azure-resurser som behövs i programmet. 

Med MSI hanterar Azure-plattformen runtime identiteten. Du behöver inte att lagra och skydda snabbtangenter i din programkod eller konfiguration, antingen för identiteten sig själv eller för de resurser du behöver för att få åtkomst till. En Händelsehubbar klientapp som körs i Azure App Service-program eller i en virtuell dator med aktiverat stöd för MSI behöver inte hantera SAS-regler och nycklar eller andra åtkomsttoken. Klientappen behöver bara slutpunktsadress av Händelsehubbar namnområdet. När appen ansluter Binder Händelsehubbar MSI-kontext till klienten i en åtgärd som visas i ett exempel senare i den här artikeln.

När den är kopplad till en hanterad tjänstidentitet kan en händelsehubbklient utföra alla behöriga åtgärder. Auktorisering genom att associera en MSI med Händelsehubbar roller. 

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs roller och behörigheter

Inledande offentliga förhandsversionen kan lägga du bara till en hanterad tjänstidentitet ”ägare” eller ”bidragsgivare” rollerna i ett namnområde för Event Hubs, vilket ger identitet fullständig kontroll på alla entiteter i namnområdet. Dock hantering av åtgärder som ändrar namnområde topologin är från början stöds endast om Azure Resource Manager och inte via gränssnittet för hantering av inbyggda Event Hub REST. Det här stödet innebär också att du inte kan använda .NET Framework-klienten [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objekt i en hanterad tjänst-identitet. 
 
## <a name="use-event-hubs-with-a-managed-service-identity"></a>Använda Händelsehubbar med en hanterad tjänst-identitet

I följande avsnitt beskrivs de steg som krävs för att skapa och distribuera ett exempelprogram som kan köras under en hanterade tjänstidentiteten, hur du bevilja den identity åtkomsten till ett Händelsehubbar namnområde och hur programmet samverkar med händelsehubbar med som identitet.

Den här introduktionen beskriver ett webbprogram som finns i [Azure App Service](https://azure.microsoft.com/services/app-service/). De steg som krävs för ett Virtuellt program är liknande.

### <a name="create-an-app-service-web-application"></a>Skapa en Apptjänst-webbprogram

Det första steget är att skapa en App Service ASP.NET-program. Om du inte känner till hur du gör detta i Azure, Följ [denna instruktioner](../app-service/app-service-web-get-started-dotnet-framework.md). Skapa ett webbformulär program i stället för att skapa ett MVC-program som visas i kursen.

### <a name="set-up-the-managed-service-identity"></a>Konfigurera hanterade tjänstidentiteten

När du har skapat programmet navigera till den nya webbappen i Azure-portalen (visas också i anvisningar) och navigera till den **hanterade tjänstidentiteten** sidan och aktivera funktionen: 

![](./media/event-hubs-managed-service-identity/msi1.png)
 
När du har aktiverat funktionen för en ny tjänstidentitet skapas i Azure Active Directory, och som konfigurerats i App Service-värden.

### <a name="create-a-new-event-hubs-namespace"></a>Skapa ett nytt namnområde för Händelsehubbar

Nästa [skapa ett namnområde för Händelsehubbar](event-hubs-create.md) i ett Azure-regioner som har stöd för förhandsgranskning för MSI: **oss Öst**, **oss östra 2**, eller **Västeuropa**. 

Navigera till namnområdet **Access Control (IAM)** på portalen, och klickar sedan på **Lägg till** att lägga till hanterade tjänstidentiteten till den **ägare** roll. Gör du genom att söka efter namnet på webbprogrammet på **lägga till behörigheter** panelen **Välj** fält och klicka på posten. Klicka sedan på **Spara**.

![](./media/event-hubs-managed-service-identity/msi2.png)
 
Webbprogrammets Hanterade tjänstidentiteten har åtkomst till namnområdet Händelsehubbar och till händelsehubben du skapade tidigare. 

### <a name="run-the-app"></a>Kör appen

Nu ändra standardsida för ASP.NET-programmet som du skapade. Du kan också använda web application-kod från [GitHub-lagringsplatsen](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp). 

När du startar appen peka webbläsaren EventHubsMSIDemo.aspx. Du kan också ange den som din startsida. Koden kan hittas i filen EventHubsMSIDemo.aspx.cs. Resultatet är ett minimalt webbprogram med några fält och **skicka** och **får** knappar som ansluter till Händelsehubbar antingen skicka eller ta emot meddelanden. 

Observera hur [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) objektet har initierats. Istället för att använda tokenleverantör för delad åtkomst-Token (SAS), kod som skapar en tokenleverantör för hanterade tjänstidentiteten med den `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)` anropa. Det finns därför inga hemligheter ska behålla och använda. Flödet av hanteringstjänster identitetskontexten Händelsehubbar och auktorisering handskakningen hanteras automatiskt av tokenleverantör, vilket är en enklare än med hjälp av SAS.

När du har gjort dessa ändringar, publicera och köra programmet. Ett enkelt sätt att få rätt publicera data är att hämta och sedan importera publiceringsprofilen i Visual Studio:

![](./media/event-hubs-managed-service-identity/msi3.png)
 
Ange namnet på namnområdet och namnet på den enhet som du skapade för att skicka eller ta emot meddelanden, och klicka sedan på antingen **skicka** eller **får**. 
 
Observera att hanterade tjänstidentiteten fungerar bara i Azure-miljön och endast i App Service-distributionen som du har konfigurerat detta. Observera också att hanteringstjänster identiteter inte fungerar med distributionsplatser för Apptjänst just nu.

## <a name="next-steps"></a>Nästa steg

Besök följande länkar för mer utförlig information om Event Hubs:

* Kom igång med en [kurs om händelsehubbar](event-hubs-dotnet-standard-getstarted-send.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Priser för Händelsehubbar](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Exempelprogram som använder Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)