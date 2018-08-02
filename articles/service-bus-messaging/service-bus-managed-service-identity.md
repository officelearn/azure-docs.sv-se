---
title: Hanterad tjänstidentitet med förhandsversionen av Azure Service Bus | Microsoft Docs
description: Använda hanterade tjänstidentiteter med Azure Service Bus
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
ms.date: 08/01/2018
ms.author: sethm
ms.openlocfilehash: 30df312e349bd6f6ebd1f38141075382be2522a2
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397992"
---
# <a name="managed-service-identity-preview"></a>Hanterad tjänstidentitet (förhandsgranskning)

En hanterad tjänstidentitet (MSI) är en över flera Azure-funktion som gör det möjligt för dig att skapa en säker identitet som är kopplad till distributionen som programkoden körs under. Därefter kan du associera den identiteten med åtkomstkontroll roller som ger anpassade behörigheter för åtkomst till specifika Azure-resurser som programmet behöver.

Med MSI hanterar den här identiteten för körning i Azure-plattformen. Du behöver inte att lagra och skydda åtkomstnycklar i din programkod eller konfiguration för identiteten själva, eller för de resurser du behöver för att få åtkomst till. En Service Bus-klientapp som körs i ett program i Azure App Service eller i en virtuell dator med aktiverad MSI-stöd behöver inte hantera SAS regler och nycklar eller andra åtkomsttoken. Klientappen behöver bara slutpunktsadress av namnområde för Service Bus-meddelanden. När appen ansluter Binder Service Bus MSI-kontexten till klienten i en åtgärd som visas i ett exempel senare i den här artikeln. 

När den är associerad med hanterade tjänstidentiteter, kan ett klientprogram utföra alla behöriga åtgärder. Auktorisering genom att associera en MSI med Service Bus-roller. 

## <a name="service-bus-roles-and-permissions"></a>Service Bus-roller och behörigheter

Den första offentliga förhandsversionen kan du bara lägga till en hanterad tjänstidentitet till ”ägare” eller ”bidragsgivare” roller av en Service Bus-namnområde som ger den identitet fullständig kontrollen på alla entiteter i namnområdet. Hantering av åtgärder som ändrar namnområde topologin är från början stöds dock bara om Azure Resource Manager och inte via gränssnittet för hantering av interna Service Bus REST. Det här stödet innebär också att du inte kan använda .NET Framework-klienten [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objekt i en hanterad tjänstidentitet.

## <a name="use-service-bus-with-a-managed-service-identity"></a>Använda Service Bus med hanterade tjänstidentiteter

I följande avsnitt beskrivs de steg som krävs för att skapa och distribuera ett exempelprogram som körs under en hanterad tjänstidentitet, hur du bevilja åtkomst till ett namnområde för Service Bus-meddelanden identitet och hur programmet interagerar med Service Bus entiteter som den identiteten.

Den här introduktionen beskriver ett webbprogram i [Azure App Service](https://azure.microsoft.com/services/app-service/). De steg som krävs för ett VM-värdbaserade program liknar varandra.

### <a name="create-an-app-service-web-application"></a>Skapa en App Service-webbapp

Det första steget är att skapa en App Service ASP.NET-program. Om du inte är bekant med hur du gör detta i Azure följer [i den här guiden](../app-service/app-service-web-get-started-dotnet-framework.md). Men istället för att skapa ett MVC-program som visas i självstudien skapar du en Web Forms-program.

### <a name="set-up-the-managed-service-identity"></a>Ställ in den hanterade tjänstidentiteten

När du har skapat programmet går du till den nya webbappen i Azure portal (visas också i anvisningar) och gå sedan till den **hanterad tjänstidentitet** sidan och aktivera funktionen: 

![](./media/service-bus-managed-service-identity/msi1.png)

När du har aktiverat funktionen, en ny tjänstidentitet skapas i Azure Active Directory och som konfigurerats i App Service-värden.

### <a name="create-a-new-service-bus-messaging-namespace"></a>Skapa ett nytt namnområde för Service Bus-meddelanden

Nästa [skapa ett namnområde för Service Bus-meddelanden](service-bus-create-namespace-portal.md) i någon av de Azure-regioner som har förhandsversionsstöd för RBAC: **östra USA**, **USA, Öst 2**, eller **Europa, västra** . 

Gå till namnområdet **åtkomstkontroll (IAM)** på portalen och klicka sedan på **Lägg till** att lägga till den hanterade tjänstidentiteten för den **ägare** roll. Du gör detta genom att söka efter namnet på webbprogrammet i den **Lägg till behörigheter** panelen **Välj** fältet och sedan klickar du på posten. Klicka sedan på **Spara**.

![](./media/service-bus-managed-service-identity/msi2.png)
 
Webbprogrammets hanterad tjänstidentitet har nu tillgång till Service Bus-namnrymd och i kön du skapade tidigare. 

### <a name="run-the-app"></a>Kör appen

Anpassa standardsidan för ASP.NET-programmet som du skapade. Du kan använda web programkoden från [den här GitHub-lagringsplatsen](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

Default.aspx-sidan är din landningssida. Koden finns i filen Default.aspx.cs. Resultatet är ett minimalt webbprogram med några fält och **skicka** och **får** knappar som ansluter till Service Bus antingen skicka eller ta emot meddelanden.

Observera hur [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) -objektet initieras. Istället för att använda den för delad åtkomst-Token (SAS)-Tokenleverantören koden skapar en tokenleverantör för den hanterade tjänstidentiteten med den `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.ServiceBusAudience)` anropa. Det finns därför inga hemligheter för att spara och använda. Flödet av hanterad tjänst identitetskontexten till Service Bus och auktorisering med handskakningen hanteras automatiskt av den tokenleverantör, vilket är en modell som är enklare än med hjälp av SAS.

När du har gjort dessa ändringar, publicera och köra programmet. Ett enkelt sätt att få rätt publicerar data är att hämta och importera en publiceringsprofil i Visual Studio:

![](./media/service-bus-managed-service-identity/msi3.png)
 
Ange namnet på namnområdet och namnet på den entitet som du skapade för att skicka eller ta emot meddelanden, och klicka sedan på antingen **skicka** eller **får**.


> [!NOTE]
> - Den hanterade tjänstidentiteten som fungerar endast i Azure-miljön, på App services, virtuella Azure-datorer och skalningsuppsättningar. För .NET-program, Microsoft.Azure.Services.AppAuthentication-biblioteket, som används av Service Bus NuGet-paketet, tillhandahåller en abstraktion över det här protokollet och har stöd för en lokal utvecklingsmiljö. Det här biblioteket kan du testa din kod lokalt på utvecklingsdatorn, använder användarkontot från Visual Studio, Azure CLI 2.0 eller Active Directory-integrerad autentisering. Mer information om alternativ för lokal utveckling med det här biblioteket finns i [tjänst-till-tjänst-autentisering till Azure Key Vault med hjälp av .NET](../key-vault/service-to-service-authentication.md).  
> 
> - Hanterade tjänstidentiteter fungerar för närvarande inte med App Service-distributionsplatser.

## <a name="next-steps"></a>Nästa steg

I följande ämnen kan du lära dig mer om Service Bus-meddelanden.

* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)