---
title: Om Mobile Apps i Azure App Service
description: "Läs om fördelarna med App Service i dina företagsmobilappar."
services: app-service\mobile
documentationcenter: 
author: ggailey777
manager: yochayk
editor: 
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: f0731237c9376d5f762eea1c7559bc6a70a2025d
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="getting-started"> </a>Om Mobile Apps i Azure App Service
Azure App Service är ett helt hanterat [PaaS](https://azure.microsoft.com/overview/what-is-paas/)-erbjudande (plattform som en tjänst) för professionella utvecklare. Tjänsten ger en omfattande uppsättning funktioner för webb-, mobil- och integrationsscenarier. 

Funktionen Mobile Apps i Azure App Service ger företagsutvecklare och systemintegratörer en plattform för utveckling av mobilappar med global tillgänglighet och hög skalbarhet.

![Visuell översikt över Mobile Apps-funktioner](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>Varför Mobile Apps?
Med Mobile Apps-funktionen kan du:

* **Bygga plattformsspecifika och plattformsoberoende appar**: Oavsett om du bygger appar särskilt för iOS, Android eller Windows eller plattformsoberoende Xamarin- eller Cordova-appar (PhoneGap) kan du använda App Service med plattformsspecifika SDK:er.
* **Koppla ihop dina affärssystem**: Med Mobile Apps kan du lägga till företagsövergripande inloggning på några minuter och ansluta till resurser som finns lagrade lokalt eller i molnet.
* **Bygga appar som kan användas offline med datasynkronisering**: Du kan öka personalens produktivitet genom att bygga appar som fungerar offline och synkronisera data i bakgrunden med Mobile Apps när det finns anslutning till någon källa med företagsdata eller Saas-API:er (programvara som en tjänst).
* **Skicka push-meddelanden till miljontals användare på några sekunder**: Du kan engagera kunderna genom att, oavsett vilken enhet de använder, skicka direkta push-meddelanden anpassade efter deras behov precis vid precis rätt tidpunkt.

## <a name="mobile-apps-features"></a>Funktioner i Mobile Apps
Följande funktioner är viktiga för molnkompatibel mobilutveckling:

* **Autentisering och auktorisering**: Du kan välja mellan ett ständigt växande antal identitetsprovidrar, till exempel Azure Active Directory för företagsautentisering, och även leverantörer via sociala nätverk som Facebook, Google, Twitter eller Microsoft-konton. Mobile Apps erbjuder en OAuth 2.0-tjänst för varje provider. Du kan även integrera SDK:n för identitetsprovidern för leverantörsspecifika funktioner.

    Läs mer om våra [autentiseringsfunktioner].

* **Dataåtkomst**: Mobile Apps erbjuder en mobilvänlig OData v3-datakälla som är kopplad till Azure SQL Database eller en lokal SQL Server. Eftersom här tjänsten kan byggas på Entity Framework kan du lätt kan integrera med andra NoSQL- och SQL-dataleverantörer, till exempel [Azure Table Storage], MongoDB, [Azure Cosmos DB] och SaaS-API-leverantörer som Office 365 och Salesforce.com.

* **Offlinesynkronisering**: Med våra klient-SDK:er är det enkelt att bygga robusta mobilappar med kort svarstid som fungerar med en offlinedatamängd. Du kan synkronisera datamängden automatiskt med data på serverdelen, inklusive konfliktlösningsstöd.

  Läs mer om våra [datafunktioner].

* **Push-meddelanden**: Våra klient-SDK:er integreras sömlöst med registreringsfunktionerna i Azure Notification Hubs, vilket innebär att du kan skicka push-meddelanden till miljontals mottagare samtidigt.

  Läs mer om våra [push-meddelandefunktioner].

* **Klient-SDK:er**: Vi erbjuder en komplett uppsättning klient-SDK:er för plattformsspecifik utveckling ([iOS], [Android] och [Windows]), plattformsoberoende utveckling ([Xamarin.iOS och Xamarin.Android], [Xamarin Forms]) och hybridapputveckling ([Apache Cordova]). Alla klient-SDK:er fås med MIT-licens och har öppen källkod.

## <a name="azure-app-service-features"></a>Funktioner i Azure App Service
Följande plattformsfunktioner är användbara i produktionsmiljöer för mobilappar:

* **Automatisk skalning**: Med App Service kan du snabbt skala upp eller ut för att hantera inkommande kundbelastning. Du kan manuellt välja antal och storleken på virtuella datorer eller ställa in automatisk skalning av mobilappsservern utifrån belastning eller schema.

  Läs mer om [automatisk skalning].

* **Mellanlagringsmiljöer** – Med App Service kan du köra flera olika versioner av webbplatsen så att du kan genomföra A- och B-testning, testa i produktionsmiljö som en del i en större DevOps-plan och mellanlagra en ny serverdel direkt på plats.

  Läs mer om [mellanlagringsmiljöer].

* **Kontinuerlig distribution**: App Service kan integreras med vanliga SCM-system, så att du kan distribuera en ny version av en serverdel automatiskt genom att skicka ut den till en gren i SCM-systemet.

  Läs mer om [distributionsalternativ](../app-service/app-service-deploy-local-git.md).

* **Virtuella nätverk**: App Service kan ansluta till lokala resurser genom virtuella nätverk, Azure ExpressRoute eller hybridanslutningar.

  Läs mer om [hybridanslutningar], [virtuella nätverk] och [ExpressRoute].

* **Isolerade/dedikerade miljöer**: Du kan köra App Service i en helt isolerad och dedikerad miljö för säker körning av storskaliga Azure App Service-appar. Miljön är perfekt för programarbetsbelastningar som kräver storskalighet, isolering eller säker nätverksåtkomst.

  Läs mer om [App Service-miljöer].

## <a name="next-steps"></a>Nästa steg

Kom igång med Mobile Apps i Azure App Service genom att slutföra den här [självstudiekursen]. I kursen lär du dig grunderna i hur du skapar en valfri mobil serverdelstjänst och klient. Kursen omfattar också integrering av autentisering, offlinesynkronisering och push-meddelanden. Du kan slutföra kursen flera gånger, en gång för varje klientprogram.

Mer information om Mobile Apps finns i vår [utbildningsväg].
Mer information om Azure App Service-plattformen finns på [Azure App Service].

<!-- URLs. -->
[Migrate your mobile service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[självstudiekursen]: app-service-mobile-ios-get-started.md
[Azure Table Storage]:../cosmos-db/table-storage-how-to-use-dotnet.md
[Azure Cosmos DB]: ../cosmos-db/sql-api-get-started.md
[autentiseringsfunktioner]: ./app-service-mobile-auth.md
[datafunktioner]: ./app-service-mobile-offline-data-sync.md
[push-meddelandefunktioner]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.iOS och Xamarin.Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[automatisk skalning]: ../app-service/web-sites-scale.md
[mellanlagringsmiljöer]: ../app-service/web-sites-staged-publishing.md
[hybridanslutningar]: ../biztalk-services/integration-hybrid-connection-overview.md
[virtuella nätverk]: ../app-service/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service/environment/app-service-app-service-environment-network-configuration-expressroute.md
[App Service-miljöer]: ../app-service/environment/intro.md
[utbildningsväg]: https://azure.microsoft.com/en-us/documentation/learning-paths/appservice-mobileapps/
