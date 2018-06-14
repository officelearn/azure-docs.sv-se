---
title: Hanterade tjänstidentiteten med förhandsversionen av Azure Service Bus | Microsoft Docs
description: Använda hanteringstjänster identiteter med Azure Service Bus
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 7b9901ee3478cb193c808b65d2dbbcf8b596a3c1
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
ms.locfileid: "29874660"
---
# <a name="managed-service-identity-preview"></a>Hanterade tjänstidentiteten (förhandsgranskning)

En hanterad tjänst identitet (MSI) är en mellan Azure-funktion som gör att du kan skapa en säker identitet som associeras med distributionen som programkoden körs under. Därefter kan du associera identiteten med åtkomstkontroll roller som ger anpassade behörigheter för åtkomst till specifika Azure-resurser som behövs i programmet.

Med MSI hanterar Azure-plattformen runtime identiteten. Du behöver inte att lagra och skydda snabbtangenter i din programkod eller konfiguration, antingen för identiteten sig själv eller för de resurser du behöver för att få åtkomst till. Ett klientprogram för Service Bus körs i Azure App Service-program eller i en virtuell dator med aktiverat stöd för MSI behöver inte hantera SAS-regler och nycklar eller andra åtkomsttoken. Klientappen behöver bara slutpunktsadress av namnområdet för Service Bus-meddelanden. När appen ansluter Binder Service Bus MSI-kontext till klienten i en åtgärd som visas i ett exempel senare i den här artikeln. 

När den är kopplad till en hanterad tjänstidentitet kan en Service Bus-klient utföra alla behöriga åtgärder. Auktorisering genom att associera en MSI med Service Bus-roller. 

## <a name="service-bus-roles-and-permissions"></a>Service Bus-roller och behörigheter

Inledande offentliga förhandsversionen kan lägga du bara till en hanterad tjänstidentitet ”ägare” eller ”bidragsgivare” rollerna i en Service Bus-namnområde, vilket ger identitet fullständig kontroll på alla entiteter i namnområdet. Dock hantering av åtgärder som ändrar namnområde topologin är från början stöds endast om Azure Resource Manager och inte via gränssnittet för hantering av interna Service Bus REST. Det här stödet innebär också att du inte kan använda .NET Framework-klienten [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objekt i en hanterad tjänst-identitet.

## <a name="use-service-bus-with-a-managed-service-identity"></a>Använda Service Bus med en hanterad tjänst-identitet

I följande avsnitt beskrivs de steg som krävs för att skapa och distribuera ett exempelprogram som körs under en hanterad tjänstidentitet, hur du ger den identity åtkomsten till ett namnområde för Service Bus-meddelanden och hur programmet samverkar med Service Bus enheter med hjälp av identitet.

Den här introduktionen beskriver ett webbprogram som finns i [Azure App Service](https://azure.microsoft.com/services/app-service/). De steg som krävs för ett Virtuellt program är liknande.

### <a name="create-an-app-service-web-application"></a>Skapa en Apptjänst-webbprogram

Det första steget är att skapa en App Service ASP.NET-program. Om du inte känner till hur du gör detta i Azure, Följ [denna instruktioner](../app-service/app-service-web-get-started-dotnet-framework.md). Skapa ett webbformulär program i stället för att skapa ett MVC-program som visas i kursen.

### <a name="set-up-the-managed-service-identity"></a>Konfigurera hanterade tjänstidentiteten

När du har skapat programmet navigera till den nya webbappen i Azure-portalen (visas också i anvisningar) och navigera till den **hanterade tjänstidentiteten** sidan och aktivera funktionen: 

![](./media/service-bus-managed-service-identity/msi1.png)

När du har aktiverat funktionen för en ny tjänstidentitet skapas i Azure Active Directory, och som konfigurerats i App Service-värden.

### <a name="create-a-new-service-bus-messaging-namespace"></a>Skapa ett nytt namnområde för Service Bus-meddelanden

Nästa [skapa ett namnområde för Service Bus-meddelanden](service-bus-create-namespace-portal.md) i ett Azure-regioner som har stöd för förhandsgranskning för RBAC: **oss Öst**, **oss östra 2**, eller **västra Europa** . 

Navigera till namnområdet **Access Control (IAM)** på portalen, och klickar sedan på **Lägg till** att lägga till hanterade tjänstidentiteten till den **ägare** roll. Gör du genom att söka efter namnet på webbprogrammet på **lägga till behörigheter** panelen **Välj** fält och klicka på posten. Klicka sedan på **Spara**.

![](./media/service-bus-managed-service-identity/msi2.png)
 
Webbprogrammets Hanterade tjänstidentiteten har nu tillgång till Service Bus-namnrymd och till kön du skapade tidigare. 

### <a name="run-the-app"></a>Kör appen

Nu ändra standardsida för ASP.NET-programmet som du skapade. Du kan också använda web application-kod från [GitHub-lagringsplatsen](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ManagedServiceIdentity).

Sidan Default.aspx är denna sida. Koden kan hittas i filen Default.aspx.cs. Resultatet är ett minimalt webbprogram med några fält och **skicka** och **får** knappar som ansluter till Service Bus antingen skicka eller ta emot meddelanden.

Observera hur [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) objektet har initierats. Istället för att använda tokenleverantör för delad åtkomst-Token (SAS), kod som skapar en tokenleverantör för hanterade tjänstidentiteten med den `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.ServiceBusAudience)` anropa. Det finns därför inga hemligheter ska behålla och använda. Flödet av hanteringstjänster identitetskontexten till Service Bus och auktorisering handskakningen hanteras automatiskt av tokenleverantör, vilket är en enklare än med hjälp av SAS.

När du har gjort dessa ändringar, publicera och köra programmet. Ett enkelt sätt att få rätt publicera data är att hämta och sedan importera publiceringsprofilen i Visual Studio:

![](./media/service-bus-managed-service-identity/msi3.png)
 
Ange namnet på namnområdet och namnet på den enhet som du skapade för att skicka eller ta emot meddelanden, och klicka sedan på antingen **skicka** eller **får**.
 
Observera att hanterade tjänstidentiteten fungerar bara i Azure-miljön och endast i App Service-distributionen som du har konfigurerat detta. Observera också att hanteringstjänster identiteter inte fungerar med distributionsplatser för Apptjänst just nu.

## <a name="next-steps"></a>Nästa steg

I följande ämnen kan du lära dig mer om Service Bus-meddelanden.

* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)