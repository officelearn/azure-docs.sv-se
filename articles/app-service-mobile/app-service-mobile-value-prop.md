<properties
    pageTitle="Vad är Mobilappar?"
    description="Läs om vilken nytta du har av Apptjänst i dina företagsmobilappar."
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/03/2016"
    ms.author="krisagh"/>

# <a name="getting-started"> </a>Vad är Mobilappar?

Azure Apptjänst är en helt hanterad plattform som en tjänst (Paas) för professionella utvecklare med ett stort utbud funktioner för webb-, mobil- och integrationsscenarier. *Mobilappar* i *Azure Apptjänst* är en mycket skalbar, globalt tillgänglig plattform för mobilappsutveckling tänkt att användas av utvecklare av företagsprogram och systemintegrerare. Den ger mobilutvecklare tillgång till ett stort utbud av funktioner.

![Mobilappar](./media/app-service-mobile-value-prop/overview.png)

##Varför Mobilappar?
*Mobilappar* i *Azure Apptjänst* är en mycket skalbar, globalt tillgänglig plattform för mobilappsutveckling tänkt att användas av utvecklare av företagsprogram och systemintegrerare. Den ger mobilutvecklare tillgång till ett stort utbud av funktioner. Med Mobilappar kan du:

- **Bygga plattformsspecifika och plattformsoberoende appar** – Oavsett om du bygger appar särskilt för iOS, Android eller Windows eller plattformsoberoende Xamarin- eller Cordova-appar (Phonegap) kan du använda Apptjänst med plattformsspecifika SDK:er.
- **Koppla ihop dina affärssystem** – Med Mobilappar kan du lägga till företagsövergripande inloggning på några minuter och ansluta till resurser som finns lagrade lokalt eller i molnet.
- **Bygga appar som kan användas offline med datasynkronisering** –Du kan öka personalens produktivitet genom att bygga appar som fungerar offline och synkronisera data i bakgrunden med Mobilappar när det finns anslutning till någon källa med företagsdata eller Saas-API:er.
- **Skicka push-meddelanden till miljontals användare på några sekunder** – Du kan engagera kunderna genom att, oavsett vilken enhet de använder, skicka direkta push-meddelanden anpassade efter deras behov precis vid precis rätt tidpunkt.

## Funktioner i Mobilappar
Följande funktioner är viktiga för molnkompatibel mobilutveckling:

- **Autentisering och auktorisering** –  Du kan välja mellan ett ständigt växande antal identitetsleverantörer, inklusive Azure Active Directory för företagsautentisering, och även leverantörer via sociala nätverk såsom Facebook, Google, Twitter eller Microsoftkonto.  Med Azure Mobilappar möjliggörs en OAuth 2.0-tjänst för varje leverantör.  Du kan även integrera SDK:n för identitetsleverantören för leverantörsspecifika funktioner.

  Läs mer om våra [autentiseringsfunktioner].

- **Dataåtkomst** – Azure Mobilappar erbjuder en mobilvänlig OData v3-datakälla kopplad till SQL Azure eller en lokal SQL Server.  Den här tjänsten kan byggas på Entity Framework, så att du lätt kan integrera med andra NoSQL- och SQL-dataleverantörer, inklusive [Azure Table Storage], MongoDB, [DocumentDB] och SaaS-API-leverantörer såsom Office 365 och Salesforce.com.
- **Synkronisering offline** – Med våra klient-SDK:er är det lätt att bygga robusta och snabba mobilappar som fungerar med offlinedata som kan synkroniseras med serverdata automatiskt, även med stöd för konfliktlösning.

  Läs mer om våra [datafunktioner].

- **Push-meddelanden** – Våra klient-SDK:er integreras sömlöst med registreringsfunktionerna i Azure Notification Hubs, vilket innebär att du kan skicka push-meddelanden till miljontals mottagare samtidigt.

  Läs mer om våra [push-meddelandefunktioner].

- **Klient-SDK:er** – Vi erbjuder en komplett uppsättning klient-SDK:er för utveckling av plattformsspecifika appar ([iOS], [Android] och [Windows]), plattformsoberoende appar ([Xamarin för iOS och Android], [Xamarin Forms]) och hybridappar ([Apache Cordova]).  Alla klient-SDK:er fås med MIT-licens och har öppen källkod.

## Funktioner i Azure Apptjänst
Följande plattformsfunktioner brukar vara användbara i produktionsmiljöer för mobilappar:

- **Automatisk skalning** – Med Apptjänst kan du snabbt skala upp eller ut för att hantera inkommande kundbelastning. Du kan manuellt välja antal och storleken på virtuella datorer eller ställa in automatisk skalning av mobilappsservern utifrån belastning eller schema.

  Läs mer om [automatisk skalning].

- **Mellanlagringsmiljöer** – Med Apptjänst kan du köra flera olika versioner av webbplatsen så att du kan genomföra A- och B-testning, testa i produktionsmiljö som en del i en större DevOps-plan och mellanlagra en ny serverdel direkt på plats.

  Läs mer om [mellanlagringsmiljöer].

- **Kontinuerlig distribution** – Apptjänst kan integreras med vanliga SCM-system, så att du kan distribuera en ny version av en serverdel automatiskt genom att skicka ut den till en gren i SCM-systemet.

  Läs mer om [distributionsalternativ].

- **Virtuella nätverk** – Apptjänst kan ansluta till lokala resurser genom virtuella nätverk, ExpressRoute eller hybridanslutningar.

  Läs mer om [hybridanslutningar], [virtuella nätverk] och [ExpressRoute].

- **Isolerade/dedikerade miljöer** – Apptjänst kan köras i en helt isolerad och dedikerad miljö för säker körning av storskaliga Azure Apptjänst-appar.  Det är perfekt för programarbetsbelastningar som kräver storskalighet, isolering eller säker nätverksåtkomst.

  Läs mer om [Apptjänstmiljöer].

## Komma igång ##
Du kommer enkelt igång med Mobilappar genom att gå igenom kursen [Kom igång].  Här får du lära dig grunderna i hur du skapar en mobilserverdel och en klient av önskat slag och sedan integrerar autentisering, offlinesynkronisering och push-meddelanden.  Du kan gå igenom kursen [Kom igång] flera gånger – en gång för varje klientprogram.

Mer information om Azure Mobilappar finns i vår [utbildningsväg].
Mer information om Azure Apptjänst-plattformen finns på [Azure Apptjänst].

>[AZURE.NOTE] Om du vill komma igång med Azure Apptjänst innan du registrerar dig för ett Azure-konto kan du gå till [Prova Apptjänst](https://tryappservice.azure.com/?appServiceName=mobile). Där kan du direkt skapa en tillfällig startwebbapp i Apptjänst. Inget kreditkort krävs, och du gör inga åtaganden.

<!-- URLs. -->
[Migrera Mobile Services till Apptjänst]: app-service-mobile-migrating-from-mobile-services.md
[Azure Apptjänst]: ../app-service/app-service-value-prop-what-is.md
[Kom igång]: app-service-mobile-ios-get-started.md
[Azure Table Storage]: ../storage/storage-getting-started-guide.md
[DocumentDB]: ../documentdb/documentdb-get-started.md
[autentiseringsfunktioner]: ./app-service-mobile-auth.md
[datafunktioner]: ./app-service-mobile-offline-data-sync.md
[push-meddelandefunktioner]: ../notification-hubs/notification-hubs-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin för iOS och Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[automatisk skalning]: ../app-service-web/web-sites-scale.md
[mellanlagringsmiljöer]: ../app-service-web/web-sites-staged-publishing.md
[distributionsalternativ]: ../app-service-web/web-sites-deploy.md
[hybridanslutningar]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[virtuella nätverk]: ../app-service-web/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service/app-service-app-service-environment-network-configuration-expressroute.md
[Apptjänstmiljöer]: ../app-service-web/app-service-app-service-environment-intro.md
[utbildningsväg]: https://azure.microsoft.com/en-us/documentation/learning-paths/appservice-mobileapps/



<!--HONumber=Jun16_HO2-->


