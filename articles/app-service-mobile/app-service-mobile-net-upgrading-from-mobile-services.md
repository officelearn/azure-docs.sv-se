---
title: Uppgradera från Mobile Services till Azure App Service
description: Lär dig hur du lätt att uppgradera ditt Mobile Services-program till en Apptjänst Mobile App
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 9c0ac353-afb6-462b-ab94-d91b8247322f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 05041637aa5cbb044e6731208825f75edec83352
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32157052"
---
# <a name="upgrade-your-existing-net-azure-mobile-service-to-app-service"></a>Uppgradera din befintliga Mobiltjänst för .NET-Azure till App Service
Apptjänst Mobile är ett nytt sätt att skapa mobila program med Microsoft Azure. Läs mer i [vad är Mobilappar?].

Det här avsnittet beskriver hur du uppgraderar ett befintligt program i .NET-serverdel från Azure Mobile Services till en ny Apptjänst Mobilappar. Medan du utför uppgraderingen fortsätter befintliga mobiltjänster programmet att fungera.   Om du behöver uppgradera ett Node.js-program för serverdelen finns [uppgraderar din Mobile Services för Node.js](app-service-mobile-node-backend-upgrading-from-mobile-services.md).

När en mobilserverdel uppgraderas till Azure App Service, den har åtkomst till alla funktioner i Apptjänst och debiteras enligt [priser för Apptjänst], inte Mobile Services priser.

## <a name="migrate-vs-upgrade"></a>Migrera kontra uppgradering
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> Vi rekommenderar att du [utför en migrering](app-service-mobile-migrating-from-mobile-services.md) innan du fortsätter med uppgraderingen. På så sätt kan du spärra båda versionerna av programmet på samma App Service-Plan och innebära utan extra kostnad.
>
>

### <a name="improvements-in-mobile-apps-net-server-sdk"></a>Förbättringar i Mobile Apps .NET server SDK
Uppgradera till den nya [Mobile Apps-SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) ger följande fördelar:

* Större flexibilitet på NuGet-beroenden. Värdmiljön ger längre sina egna versioner av NuGet-paket, så du kan använda alternativa kompatibla versioner. Om det finns nya bugfixes för kritiska uppdateringar eller säkerhetsuppdateringar till Mobile Server SDK eller beroenden, måste du uppdatera tjänsten manuellt.
* Större flexibilitet i mobila SDK. Du kan styra vilka funktioner som uttryckligen och vägar har ställts in, till exempel autentisering, tabell API: er och slutpunkten för push-registrering. Läs mer i [hur du använder SDK för .NET-server för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).
* Stöd för andra typer av ASP.NET-projekt och flöden. Du kan nu vara värd för MVC- och webb-API domänkontrollanter i samma projekt som projektet mobila serverdel.
* Stöd för nya App Service autentiseringsfunktioner, som gör det möjligt att använda en vanlig Autentiseringskonfiguration i ditt webb- och mobilappar.

## <a name="overview"></a>Uppgradera översikt
I många fall blir uppgraderar bara växla till den nya servern för Mobile Apps SDK och publicera din kod till en ny instans för Mobilapp. Det finns dock några scenarier som kräver ytterligare konfigurering, till exempel scenarier med avancerad autentisering och arbeta med schemalagda jobb. Var och en av dessa beskrivs i senare avsnitt.

> [!TIP]
> Det rekommenderas att du läser och förstå resten av det här avsnittet helt innan du påbörjar en uppgradering. Anteckna alla funktioner som du använder som kallas nedan.
>
>

Klienten för Mobile Services SDK: er **inte** kompatibel med den nya servern för Mobile Apps SDK. Du bör inte publicera ändringar till en plats som betjänar publicerade klienter för att kunna erbjuda kontinuitet i tjänsten för din app. I stället bör du skapa en ny mobil app som fungerar som en dubblett. Du kan publicera det här programmet på samma App Service-plan för att undvika ytterligare kostnader.

Du måste sedan två versioner av programmet: en som förblir densamma och fungerar publicerade appar i jokertecken och andra som du kan sedan uppgradera och mål med en ny klientversion. Du kan flytta och testa din kod i din takt, men bör du se till att alla felkorrigeringar som du gör gäller för båda. När du anser att ett önskat antal klientdatorer appar i jokertecken har uppdaterat till den senaste versionen, du kan ta bort den ursprungliga migrerade appen om du vill ha.

Fullständig dispositionen för uppgraderingen är följande:

1. Skapa en ny Mobile App
2. Uppdatera projektet om du vill använda den nya Server-SDK
3. Använda en ny version av klientprogrammet
4. (Valfritt) Ta bort din ursprungliga migrerade instans

## <a name="mobile-app-version"></a>Skapa en andra instans av programmet
Det första steget i uppgraderingen är att skapa resursen Mobilapp som ska vara värd för den nya versionen av programmet. Om du redan har migrerat en befintlig mobila tjänst kommer du vill skapa den här versionen på samma värd plan. Öppna den [Azure-portalen] och navigera till migrerade programmet. Anteckna Apptjänstplan den körs på.

Skapa sedan den andra förekomsten av programmet genom att följa den [.NET-serverdel skapa instruktioner](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app). När du uppmanas att välja du väljer App Service-Plan eller ”värd plan” du planen för migrerade programmet.

Du förmodligen vill använda samma databas och Meddelandehubben som du gjorde i Mobile Services. Du kan kopiera värdena genom att öppna [Azure-portalen] och navigera till det ursprungliga programmet Klicka **inställningar** > **programinställningar**. Under **anslutningssträngar**, kopiera `MS_NotificationHubConnectionString` och `MS_TableConnectionString`. Navigera till den nya platsen för uppgradering och klistra in dem i att skriva över befintliga värden. Upprepa processen för alla andra programinställningar app-behov. Om du inte använder en migrerad tjänst kan du läsa anslutningssträngar och app-inställningar från den **konfigurera** fliken för Mobile Services-avsnitt i den [klassiska Azure-portalen].

Skapa en kopia av ASP.NET-projekt för ditt program och publicera det till den nya platsen. Med en kopia av klientprogrammet uppdateras med den nya URL, verifiera att allt fungerar som förväntat.

## <a name="updating-the-server-project"></a>Uppdatera serverprojektet
Mobilappar erbjuder en ny [Mobile App-serverns SDK] som innehåller en stor del av samma funktioner som Mobile Services runtime. Först bör du ta bort alla referenser till Mobile Services-paket. I NuGet-Pakethanteraren, söker du efter `WindowsAzure.MobileServices.Backend`. De flesta appar kommer att se flera paket här, inklusive `WindowsAzure.MobileServices.Backend.Tables` och `WindowsAzure.MobileServices.Backend.Entity`. I sådana fall börjar med det lägsta paketet i beroendeträdet t ex `Entity`, och ta bort den. När du uppmanas, ta inte bort alla beroende paket. Upprepa processen tills du har tagit bort `WindowsAzure.MobileServices.Backend` sig själv.

Nu har du ett projekt som inte längre refererar till Mobile Services SDK.

Nästa lägger du till referenser Mobile Apps-SDK. För den här uppgraderingen de flesta utvecklare ska du hämta och installera den `Microsoft.Azure.Mobile.Server.Quickstart` paketet, eftersom detta hämtar i uppsättningen av hela krävs.

Det blir ett ganska stort antal kompileringsfel som uppstår till följd av skillnaderna mellan SDK: erna, men dessa är lätta att adressen och beskrivs i resten av det här avsnittet.

### <a name="base-configuration"></a>Grundläggande konfiguration
Sedan, i WebApiConfig.cs, du kan ersätta:

        // Use this class to set configuration options for your mobile service
        ConfigOptions options = new ConfigOptions();

        // Use this class to set WebAPI configuration options
        HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

med

        HttpConfiguration config = new HttpConfiguration();
        new MobileAppConfiguration()
            .UseDefaultConfiguration()
        .ApplyTo(config);

> [!NOTE]
> Om du vill lära dig mer om den nya servern .NET SDK och Lägg till/ta bort funktioner från din app kan du se den [hur du använder SDK för .NET-server] avsnittet.
>
>

Om din app använder autentiseringsfunktioner måste du också registrera en OWIN-mellanprogram. I det här fallet bör du flytta konfigurationskoden ovan till en ny OWIN-startklass.

1. Lägg till NuGet-paketet `Microsoft.Owin.Host.SystemWeb` om det inte redan ingår i projektet.
2. I Visual Studio högerklickar du på projektet och välj **Lägg till** -> **nytt objekt**. Välj **Web** -> **allmänna** -> **OWIN-startklass**.
3. Flytta koden ovan för MobileAppConfiguration från `WebApiConfig.Register()` till den `Configuration()` metod för den nya Start-klassen.

Kontrollera att den `Configuration()` metoden slutar med:

        app.UseWebApi(config)
        app.UseAppServiceAuthentication(config);

Det finns ytterligare ändringar som är relaterade till autentisering som beskrivs i avsnittet fullständiga autentiseringen.

### <a name="working-with-data"></a>Arbeta med Data
I Mobile Services mobila appnamnet visas som standard schemanamn i Entity Framework-installationsprogrammet.

Så att du har samma schema refereras som tidigare, Använd följande för att ställa in schemat i DbContext för programmet:

        string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");

Kontrollera att du har MS_MobileServiceName ange om du gör det som nämns ovan. Du kan också ange en annan schemanamnet om tillämpningsprogrammet anpassat detta tidigare.

### <a name="system-properties"></a>Systemegenskaper
#### <a name="naming"></a>Namngivning
I Azure Mobile Services-servern SDK Systemegenskaper alltid innehålla ett dubbelt understreck (`__`)-prefixet för egenskaperna:

* __createdAt
* __updatedAt
* __deleted
* __version

SDK: er för Mobile Services-klienten har särskilda logik för parsning av Systemegenskaper i det här formatet.

I Azure Mobile Apps Systemegenskaper inte längre har en särskild format och har följande namn:

* CreatedAt
* updatedAt
* borttaget
* version

Mobile Apps klienten SDK: er använda de nya egenskaper för namn, så inga ändringar krävs för att klientkod. Men om du direkt gör REST-anrop till din tjänst bör du ändra dina frågor i enlighet med detta.

#### <a name="local-store"></a>Lokalt Arkiv
Ändringar till namnen på Systemegenskaper innebär att en lokal databas offlinesynkronisering för Mobile Services inte är kompatibel med Mobile Apps. Om möjligt bör du undvika uppgradering av klienten appar från Mobile Services för Mobilappar förrän efter väntande ändringar har skickats till servern. Sedan kan använda appen uppgraderade ett nytt databasnamn.

Om ett klientprogram uppgraderas från Mobile Services till Mobilappar när det finns väntande offline ändringar i kö för åtgärden, måste systemdatabasen uppdateras för att använda nya kolumnnamn. På iOS, kan detta uppnås med hjälp av lightweight migreringar ändra kolumnnamnen. På Android- och .NET hanterad klient ska du skriva anpassade SQL Byt namn på kolumner för din datatabeller för objektet.

På iOS, bör du ändra schemat för dina data entiteter till följande grundläggande information. Observera att egenskaperna `createdAt`, `updatedAt` och `version` har inte längre ett `ms_` prefix:

| Attribut | Typ | Obs! |
| --- | --- | --- |
| id |Strängen som markerats krävs |primärnyckeln i fjärranslutna store |
| CreatedAt |Date |(valfritt) mappas till createdAt Systemegenskapen |
| updatedAt |Date |(valfritt) mappas till updatedAt Systemegenskapen |
| version |Sträng |(valfritt) används för att identifiera konflikter, mappas till version |

#### <a name="querying-system-properties"></a>Frågar Systemegenskaper
I Azure Mobile Services Systemegenskaper skickas inte som standard, men endast när de begär med hjälp av frågesträngen `__systemProperties`. Däremot i Azure Mobile Apps system egenskaper är **alltid valt** eftersom de är en del av objektmodellen server SDK.

Den här ändringen påverkar huvudsakligen anpassade implementeringar av domän chefer, till exempel tillägg av `MappedEntityDomainManager`. I Mobile Services, om en klient begär aldrig alla Systemegenskaper är det möjligt att använda en `MappedEntityDomainManager` som faktiskt mappar inte alla egenskaper. Men i Azure Mobile Apps genereras egenskaperna omappade ett fel i GET-frågor.

Det enklaste sättet att lösa problemet är att ändra din DTOs så att de ärver från `ITableData` i stället för `EntityData`. Lägg sedan till den `[NotMapped]` attribut till fälten som ska uteslutas.

Exempelvis definierar följande `TodoItem` utan system-egenskaper:

    using System.ComponentModel.DataAnnotations.Schema;

    public class TodoItem : ITableData
    {
        public string Text { get; set; }

        public bool Complete { get; set; }

        public string Id { get; set; }

        [NotMapped]
        public DateTimeOffset? CreatedAt { get; set; }

        [NotMapped]
        public DateTimeOffset? UpdatedAt { get; set; }

        [NotMapped]
        public bool Deleted { get; set; }

        [NotMapped]
        public byte[] Version { get; set; }
    }

Obs: om det uppstår fel `NotMapped`, lägga till en referens till sammansättningen `System.ComponentModel.DataAnnotations`.

### <a name="cors"></a>CORS
Mobile Services med vissa stöd för CORS genom att omsluta ASP.NET CORS-lösning. Detta lager med radbrytning har tagits bort för att ge utvecklare mer kontroll så att du direkt kan utnyttja [stöd för ASP.NET-CORS](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

De huvudsakliga problemområden om du använder CORS är som den `eTag` och `Location` huvuden måste tillåtas för klient-SDK: er ska fungera korrekt.

### <a name="push-notifications"></a>Push-meddelanden
För distribution är objektet kanske saknas från Server SDK PushRegistrationHandler-klass. Registreringar hanteras lite annorlunda i Mobile Apps och tagless registreringar är aktiverade som standard. Hantera taggar kan åstadkommas med hjälp av anpassade API: er. Finns det [registrering för taggar](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) instruktioner för mer information.

### <a name="scheduled-jobs"></a>Schemalagda jobb
Schemalagda jobb är inte inbyggda i Mobilappar, så alla befintliga jobb som du har i .NET-serverdel måste uppgraderas individuellt. Ett alternativ är att skapa en schemalagd [Web jobbet] på webbplatsen Mobilapp kod. Du kan också konfigurera en domänkontrollant som innehåller jobbkoden och konfigurera den [Azure Scheduler] att träffa den slutpunkten på det förväntade schemat.

### <a name="miscellaneous-changes"></a>Funktionsförändringar
Alla ApiControllers som används av en mobil klient måste nu har den `[MobileAppController]` attribut. Det är inte längre ingår som standard så att andra ApiControllers gå påverkas inte av mobila-formaterare.

Den `ApiServices` objektet är inte längre ingår i SDK. Du kan använda följande för att komma åt Mobile App-inställningar:

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

På liknande sätt, utförs nu loggning använda standard ASP.NET spårning:

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

## <a name="authentication"></a>Överväganden för autentisering
Autentiseringskomponenter i Mobile Services har nu flyttats till funktionen autentisering/auktorisering i Apptjänst. Du kan lära dig om hur du aktiverar detta för webbplatsen genom att läsa den [Lägg till autentisering i appen mobila](app-service-mobile-ios-get-started-users.md) avsnittet.

För vissa leverantörer, till exempel AAD, Facebook och Google, bör du kunna utnyttja den befintliga registreringen från tillämpningsprogrammet kopia. Du behöver bara gå till portalen för den identitetsleverantör och lägga till en ny omdirigerings-URL i registreringen. Konfigurera autentisering/auktorisering i Apptjänst med klient-ID och hemlighet.

### <a name="controller-action-authorization"></a>Domänkontrollanten åtgärd auktorisering
Alla instanser av den `[AuthorizeLevel(AuthorizationLevel.User)]` attributet nu måste ändras för att använda standard ASP.NET `[Authorize]` attribut. Dessutom är domänkontrollanter nu anonym som standard, som i andra ASP.NET-program.
Om du använder något av de andra märke AuthorizeLevel alternativ, till exempel Admin eller ett program, Lägg till att de är borta. Du kan i stället konfigurera AuthorizationFilters för delade hemligheter eller konfigurera ett AAD-tjänstens huvudnamn för att aktivera service-to-service-anrop på ett säkert sätt.

### <a name="getting-additional-user-information"></a>Hämtar användarinformation om ytterligare
Du kan få ytterligare information, inklusive åtkomsttoken via den `GetAppServiceIdentityAsync()` metoden:

        FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();

Dessutom är det viktigt att notera att användar-ID mellan Mobile Services och Apptjänst Mobilappar skiljer sig om ditt program tar beroenden på användar-ID, till exempel lagra dem i en databas. Du kan fortfarande få Mobile Services användar-ID, även om. Alla ProviderCredentials underklasser har en användar-ID-egenskap. Så fortsätter från exempel innan:

        string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

Om din app tar några beroenden på användar-ID, är det viktigt att du använder samma registrering med en identitetsprovider om möjligt. Användar-ID är vanligtvis begränsade till registreringen program som har använts, så Vi presenterar en ny registrering kan skapa problem med matchande användare till deras data.

### <a name="custom-authentication"></a>Anpassad autentisering
Om din app använder en lösning för anpassad autentisering, kommer du vill kontrollera att den uppgraderade platsen har åtkomst till systemet. Följ instruktionerna för anpassad autentisering i nya den [översikt över SDK för .NET server] att integrera din lösning. Observera att komponenter för anpassad autentisering är fortfarande under förhandsgranskning.

## <a name="updating-clients"></a>Uppdatera klienter
När du har en operativa mobilappsserverdel kan arbeta du med en ny version av ditt klientprogram som använder den. Mobile Apps innehåller också en ny version av klienten SDK: er och liknar serveruppgraderingen ovan, behöver du ta bort alla referenser till SDK: er för Mobile Services innan du installerar Mobile Apps-versioner.

En av de viktigaste ändringarna mellan versionerna är att konstruktorer längre kräver en nyckel för programmet. Du nu överföra bara URL-adressen till din Mobilapp. Till exempel på .NET-klienter på `MobileServiceClient` konstruktorn är nu:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Du kan läsa om hur du installerar nya SDK: er och använda den nya strukturen via länkarna nedan:

* [iOS version 3.0.0 eller senare](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (Windows/Xamarin) version 2.0.0 eller senare](app-service-mobile-dotnet-how-to-use-client-library.md)

Om ditt program gör användning av push-meddelanden, anteckna registreringen instruktioner för varje plattform, har eftersom det ändringar det också.

När du har den nya klientversionen redo prova mot projektet uppgraderade servern. När du verifierar att det fungerar, kan du släppa en ny version av programmet till kunder. Slutligen, när dina kunder har haft möjlighet att få dessa uppdateringar, du kan ta bort Mobile Services-versionen av din app. Nu är har du uppgraderat till en App Service-Mobilapp med hjälp av den senaste Mobile Apps servern SDK.

<!-- URLs. -->

[Azure-portalen]: https://portal.azure.com/
[klassiska Azure-portalen]: https://manage.windowsazure.com/
[vad är Mobilappar?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App-serverns SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[Web jobbet]: https://github.com/Azure/azure-webjobs-sdk/wiki
[hur du använder SDK för .NET-server]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[priser för Apptjänst]: https://azure.microsoft.com/pricing/details/app-service/
[översikt över SDK för .NET server]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
