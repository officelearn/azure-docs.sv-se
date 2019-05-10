---
title: Hanterade identiteter för Azure-resurser med Azure Service Bus-förhandsgranskning | Microsoft Docs
description: Använda hanterade identiteter för Azure-resurser med Azure Service Bus
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/01/2018
ms.author: aschhab
ms.openlocfilehash: abba0e15314387aed09e39f05d9127f346f9c799
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228392"
---
# <a name="managed-identities-for-azure-resources-with-service-bus"></a>Hanterade identiteter för Azure-resurser med Service Bus 

[Hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) är en över flera Azure-funktion som låter dig skapa en säker identitet som är kopplad till distributionen som programkoden körs under. Därefter kan du associera den identiteten med åtkomstkontroll roller som ger anpassade behörigheter för åtkomst till specifika Azure-resurser som programmet behöver.

Med hanterade identiteter hanterar den här identiteten för körning i Azure-plattformen. Du behöver inte att lagra och skydda åtkomstnycklar i din programkod eller konfiguration för identiteten själva, eller för de resurser du behöver för att få åtkomst till. En Service Bus klientapp som körs i ett program i Azure App Service eller i en virtuell dator med aktiverad hanterade entiteter för Azure-resurser stöd inte behöver hantera SAS regler och nycklar eller andra åtkomsttoken. Klientappen behöver bara slutpunktsadress av namnområde för Service Bus-meddelanden. När appen ansluter Binder Service Bus hanterad entitet kontext till klienten i en åtgärd som visas i ett exempel senare i den här artikeln. När den är associerad med en hanterad identitet, kan Service Bus-klient göra alla behöriga åtgärder. Auktorisering genom att associera en hanterad entitet med Service Bus-roller. 

## <a name="service-bus-roles-and-permissions"></a>Service Bus-roller och behörigheter

Du kan bara lägga till en hanterad identitet till ”ägare” eller ”bidragsgivare” roller av en Service Bus-namnrymd. Den ger fullständig kontrollen identitet i alla entiteter i namnområdet. Hantering av åtgärder som ändrar namnområde topologin är från början stöds dock bara om Azure Resource Manager. Det är inte via gränssnittet för hantering av interna Service Bus REST. Det här stödet innebär också att du inte kan använda .NET Framework-klienten [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller .NET Standard-klienten [ManagementClient](/dotnet/api/microsoft.azure.servicebus.management.managementclient) objekt inom en hanterad identitet.

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Använda Service Bus med hanterade identiteter för Azure-resurser

I följande avsnitt beskrivs de steg som krävs för att skapa och distribuera ett exempelprogram som körs under en hanterad identitet, hur du bevilja åtkomst till ett namnområde för Service Bus-meddelanden identitet och hur programmet interagerar med Service Bus-entiteter med hjälp av den identiteten.

Den här introduktionen beskriver ett webbprogram i [Azure App Service](https://azure.microsoft.com/services/app-service/). De steg som krävs för ett VM-värdbaserade program liknar varandra.

### <a name="create-an-app-service-web-application"></a>Skapa en App Service-webbapp

Det första steget är att skapa en App Service ASP.NET-program. Om du inte vet hur du gör i Azure kan du följa [i den här guiden](../app-service/app-service-web-get-started-dotnet-framework.md). Men istället för att skapa ett MVC-program som visas i självstudien skapar du en Web Forms-program.

### <a name="set-up-the-managed-identity"></a>Ställ in den hanterade identitet

När du har skapat programmet går du till den nya webbappen i Azure portal (visas också i anvisningar) och gå sedan till den **hanterad tjänstidentitet** sidan och aktivera funktionen: 

![](./media/service-bus-managed-service-identity/msi1.png)

När du har aktiverat funktionen, en ny tjänstidentitet skapas i Azure Active Directory och som konfigurerats i App Service-värden.

### <a name="create-a-new-service-bus-messaging-namespace"></a>Skapa ett nytt namnområde för Service Bus-meddelanden

Nästa [skapa ett namnområde för Service Bus-meddelanden](service-bus-create-namespace-portal.md) i någon av de Azure-regioner som har förhandsversionsstöd för RBAC: **USA, Öst**, **USA, Öst 2**, eller **Västeuropa**. 

Gå till namnområdet **åtkomstkontroll (IAM)** på portalen och klicka sedan på **Lägg till rolltilldelning** att lägga till den hantera identitet som den **ägare** roll. Du gör detta genom att söka efter namnet på webbprogrammet i den **Lägg till behörigheter** panelen **Välj** fältet och sedan klickar du på posten. Klicka sedan på **Spara**.

Hanterad identitet för webbprogrammets har nu tillgång till Service Bus-namnrymd och i kön du skapade tidigare. 

### <a name="run-the-app"></a>Kör appen

Anpassa standardsidan för ASP.NET-programmet som du skapade. Du kan använda web programkoden från [den här GitHub-lagringsplatsen](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

Default.aspx-sidan är din landningssida. Koden finns i filen Default.aspx.cs. Resultatet är ett minimalt webbprogram med några fält och **skicka** och **får** knappar som ansluter till Service Bus antingen skicka eller ta emot meddelanden.

Observera hur [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) -objektet initieras. Istället för att använda den för delad åtkomst-Token (SAS)-Tokenleverantören koden skapar en tokenleverantör för den hanterade identitet med den `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.ServiceBusAudience)` anropa. Det finns därför inga hemligheter för att spara och använda. Flödet av hanterad identitetskontexten till Service Bus och auktorisering med handskakningen hanteras automatiskt av Tokenleverantören. Det är en enklare än med hjälp av SAS.

När du har gjort dessa ändringar, publicera och köra programmet. Du kan hämta rätt publicera data enkelt genom att hämta och importera en publiceringsprofil i Visual Studio:

![](./media/service-bus-managed-service-identity/msi3.png)
 
Ange namnet på namnområdet och namnet på den entitet som du skapade för att skicka eller ta emot meddelanden. Klicka sedan på antingen **skicka** eller **får**.


> [!NOTE]
> - Den hanterade identitet som fungerar endast i Azure-miljön, på App services virtuella Azure-datorer och skalningsuppsättningar. För .NET-program, Microsoft.Azure.Services.AppAuthentication-biblioteket, som används av Service Bus NuGet-paketet, tillhandahåller en abstraktion över det här protokollet och har stöd för en lokal utvecklingsmiljö. Det här biblioteket kan du testa din kod lokalt på utvecklingsdatorn, använder användarkontot från Visual Studio, Azure CLI 2.0 eller Active Directory-integrerad autentisering. Mer information om alternativ för lokal utveckling med det här biblioteket finns i [tjänst-till-tjänst-autentisering till Azure Key Vault med hjälp av .NET](../key-vault/service-to-service-authentication.md).  
> 
> - Hanterade identiteter fungerar för närvarande inte med App Service-distributionsplatser.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Service Bus-meddelanden, finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
