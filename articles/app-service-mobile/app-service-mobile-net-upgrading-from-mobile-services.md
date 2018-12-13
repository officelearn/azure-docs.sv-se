---
title: Uppgradera från mobila tjänster till Azure App Service
description: Lär dig hur du lätt att uppgradera ditt program för mobiltjänster till en App Service Mobile App
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
ms.openlocfilehash: f5ffc795e6469971d1eaf335d6683f94d05f0807
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53278632"
---
# <a name="upgrade-your-existing-net-azure-mobile-service-to-app-service"></a>Uppgradera din befintliga .NET Azure-Mobiltjänst till App Service
App Service Mobile är ett nytt sätt att bygga mobilappar med Microsoft Azure. Mer information finns i [vad är Mobile Apps?].

Det här avsnittet beskriver hur du uppgraderar ett befintligt .NET-serverdel program från Azure Mobile Services till en ny App Service Mobile Apps. Medan du utför den här uppgraderingen kan ditt befintliga program för mobiltjänster fortsätta att fungera.   Om du vill uppgradera ett Node.js-program för serverdelen kan se [uppgradera din Node.js-mobiltjänster](app-service-mobile-node-backend-upgrading-from-mobile-services.md).

När en mobil serverdel uppgraderas till Azure App Service, den har åtkomst till alla App Service-funktioner och debiteras enligt [prissättning för App Service], inte Mobile Services priser.

## <a name="migrate-vs-upgrade"></a>Migrera jämfört med uppgraderingen
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> Vi rekommenderar att du [utför en migrering](app-service-mobile-migrating-from-mobile-services.md) innan du går igenom en uppgradering. På så sätt kan du placera båda versioner av ditt program på samma App Service-planen och medföra utan extra kostnad.
>
>

### <a name="improvements-in-mobile-apps-net-server-sdk"></a>Förbättringar i Mobile Apps .NET server SDK
Uppgradera till den nya [SDK för Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) ger följande fördelar:

* Mer flexibilitet i NuGet-beroenden. Den faktiska driftsmiljön ger längre sina egna versioner av NuGet-paket, så du kan använda alternativa kompatibla versioner. Men om det finns nya bugfixes för kritiska uppdateringar eller säkerhetsuppdateringar till Mobile Server SDK eller beroenden, måste du uppdatera din tjänst manuellt.
* Mer flexibilitet i mobila SDK. Du kan uttryckligen styra vilka funktioner och vägar har ställts in, till exempel autentisering, tabell-API: er och push-profilslutpunkt. Mer information finns i [hur du använder .NET server SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).
* Stöd för andra typer av ASP.NET-projekt och flöden. Du kan nu ha MVC och webb-API-domänkontrollanter i samma projekt som projektet mobila serverdelstjänst.
* Stöd för nya App Service-autentisering, där du kan använda en vanlig Autentiseringskonfiguration i dina webb- och mobilappar.

## <a name="overview"></a>Grundläggande översikt över Programuppgradering
I många fall blir uppgraderar lika enkelt som att växla till den nya Mobile Apps-servern SDK och publicera din kod till en ny Mobilapp-instans. Det finns dock några scenarier som kräver ytterligare konfigurering, till exempel avancerad autentiseringsscenarier och arbeta med schemalagda jobb. Var och en av dessa beskrivs i senare avsnitt.

> [!TIP]
> Det rekommenderas att du läser och förstår resten av det här avsnittet helt innan du påbörjar en uppgradering. Skriv ner alla funktioner du använder som framhävs nedan.
>
>

Mobile Services-klient SDK: erna är **inte** kompatibel med den nya Mobile Apps-servern SDK. För att tillhandahålla replikeringsmodellen för din app, bör du inte publicera ändringar till en plats som betjänar publicerade klienter. I stället bör du skapa en ny mobilapp som fungerar som en dubblett. Du kan publicera det här programmet på samma App Service-planen för att undvika extra kostnader.

Du kommer då ha två versioner av programmet: en som förblir oförändrat och hanterar appar som har publicerats i naturen, och den andra som du kan sedan uppgradera och målet med en ny klientversion. Du kan flytta och testa din kod i din takt, men du bör se till att alla felkorrigeringar som du gör gäller för båda. När du anser att ett önskat antal klient apps i naturen har uppdaterat till den senaste versionen, du kan ta bort den ursprungliga migrerade appen om du vill ha.

Fullständig kantlinje för uppgraderingen är följande:

1. Skapa en ny Mobilapp
2. Uppdatera projektet om du vill använda den nya Server-SDK: er
3. Lansera en ny version av ditt klientprogram
4. (Valfritt) Ta bort din ursprungliga migrerade instans

## <a name="mobile-app-version"></a>Skapa en andra instans av programmet
Uppgradera det första steget är att skapa Mobile App-resurs som ska vara värd för den nya versionen av ditt program. Om du redan har migrerat en befintlig mobil tjänst kommer du att skapa den här versionen på samma värdplan. Öppna den [Azure Portal] och navigera till programmets migrerade. Anteckna App Service-planen den körs på.

Skapa sedan instansen för andra programmet genom att följa den [.NET-serverdel skapa instruktioner](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app). När du uppmanas att välja du väljer App Service-Plan eller ”värdplan” plan programmets migrerade.

Du kommer förmodligen vill använda samma databas och Notification Hub som du gjorde i mobiltjänster. Du kan kopiera dessa värden genom att öppna [Azure Portal] och navigera till det ursprungliga programmet, klickar **inställningar** > **programinställningar**. Under **anslutningssträngar**, kopiera `MS_NotificationHubConnectionString` och `MS_TableConnectionString`. Navigera till den nya uppgradera platsen och klistra in dem i skriver över alla befintliga värden. Upprepa proceduren för alla andra programinställningar behoven för dina appar.

Skapa en kopia av ASP.NET-projekt för ditt program och publicera den till den nya platsen. Använd en kopia av ditt klientprogram som uppdateras med den nya URL: en, verifiera att allt fungerar som förväntat.

## <a name="updating-the-server-project"></a>Uppdaterar serverprojektet
Mobile Apps erbjuder en ny [SDK för Mobile App-servern] som ger stor del samma funktioner som Mobile Services runtime. Först bör du ta bort alla referenser till Mobile Services-paket. I NuGet-Pakethanteraren, söker du efter `WindowsAzure.MobileServices.Backend`. De flesta appar visas flera paket här, inklusive `WindowsAzure.MobileServices.Backend.Tables` och `WindowsAzure.MobileServices.Backend.Entity`. I detta fall är börjar med det lägsta paketet i beroendeträd, t ex `Entity`, och ta bort den. När du uppmanas, ta inte bort alla beroende paket. Upprepa processen tills du har tagit bort `WindowsAzure.MobileServices.Backend` själva.

Nu har du ett projekt som inte längre refererar till Mobile Services SDK: er.

Därefter lägger du till referenser SDK för Mobile Apps. Den här uppgraderingen de flesta utvecklare ska du hämta och installera den `Microsoft.Azure.Mobile.Server.Quickstart` paketet som hämtas i samtliga krävs.

Det blir ett ganska stort antal Kompilatorfel som följd av skillnaderna mellan de SDK: erna, men de är enkla att åtgärda och beskrivs i resten av det här avsnittet.

### <a name="base-configuration"></a>Grundläggande konfiguration
Sedan, i WebApiConfig.cs, kan du ersätta:

```csharp
// Use this class to set configuration options for your mobile service
ConfigOptions options = new ConfigOptions();

// Use this class to set WebAPI configuration options
HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));
```

med 

```csharp
HttpConfiguration config = new HttpConfiguration();
new MobileAppConfiguration()
    .UseDefaultConfiguration()
.ApplyTo(config);

```

> [!NOTE]
> Om du vill lära dig mer om den nya .NET servern SDK och Lägg till/ta bort funktioner från din app kan du se den [Hur du använder SDK för .NET-server] avsnittet.
>
>

Om din app gör användning av autentiseringsfunktioner, måste du också att registrera en OWIN-mellanprogrammet. I det här fallet bör du flytta konfigurationskoden ovan till en ny OWIN-startklass.

1. Lägg till NuGet-paketet `Microsoft.Owin.Host.SystemWeb` om den inte redan ingår i projektet.
2. I Visual Studio högerklickar du på projektet och välj **Lägg till** -> **nytt objekt**. Välj **Web** -> **Allmänt** -> **OWIN-startklass**.
3. Flytta koden ovan för MobileAppConfiguration från `WebApiConfig.Register()` till den `Configuration()` -metoden för din nya Start-klassen.

Kontrollera att den `Configuration()` metoden slutar med:

```csharp
app.UseWebApi(config)
app.UseAppServiceAuthentication(config);
```

Det finns ytterligare ändringar för autentisering som beskrivs i avsnittet fullständiga autentiseringen nedan.

### <a name="working-with-data"></a>Arbeta med Data
I mobiltjänster hanteras mobila appnamnet som standard schemanamn i Entity Framework-installationsprogrammet.

Så att du har samma schema som refereras som tidigare, Använd följande för att ange schemat i DbContext för ditt program:

```csharp
string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");
```

Kontrollera att du har MS_MobileServiceName ange i så fall ovan. Du kan också ange en annan schemanamn om ditt program anpassat detta tidigare.

### <a name="system-properties"></a>Systemegenskaper
#### <a name="naming"></a>Namngivning
I Azure Mobile Services-server SDK Systemegenskaper alltid innehålla en dubbla understreck (`__`) prefix för egenskaperna:

* __createdAt
* __updatedAt
* __deleted
* __version

Mobile Services-klient SDK: er har särskilda logik för parsning Systemegenskaper i det här formatet.

I Azure Mobile Apps Systemegenskaper inte längre har en särskild format och har följande namn:

* createdAt
* updatedAt
* borttaget
* version

SDK: er för Mobile Apps-klienten använder de nya egenskapsnamn, så inga ändringar krävs för att klientkod. Men om du direkt gör REST-anrop till din tjänst ska du ändra dina frågor i enlighet med detta.

#### <a name="local-store"></a>Lokalt Arkiv
Ändringar i namnen på Systemegenskaper innebär att en lokal databas offlinesynkronisering för mobiltjänster inte är kompatibel med Mobile Apps. Om möjligt bör du undvika att uppgradera klienten appar från Mobile Services till Mobile Apps förrän efter väntande ändringar har skickats till servern. Sedan kan använda appen uppgraderade ett nytt databasnamn.

Om en klientapp uppgraderas från Mobile Services till Mobile Apps när det finns väntande offline ändringar i kön för åtgärden, måste systemdatabasen uppdateras om du vill använda de nya kolumnnamnen. Detta kan uppnås med hjälp av enkel migreringar ändra kolumnnamnen i iOS. På Android och den hanterade klienten för .NET, ska du skriva anpassade SQL för att byta namn på kolumner för din datatabeller för objektet.

På iOS-, bör du ändra schemat för dina dataentiteter till följande grundläggande information. Observera att egenskaperna `createdAt`, `updatedAt` och `version` inte längre att ha en `ms_` prefix:

| Attribut | Typ | Obs! |
| --- | --- | --- |
| id |Sträng, markeras krävs |primärnyckeln i fjärrlager |
| createdAt |Date |(valfritt) mappas till createdAt Systemegenskapen |
| updatedAt |Date |(valfritt) mappas till updatedAt Systemegenskapen |
| version |Sträng |(valfritt) används för att identifiera konflikter, mappas till version |

#### <a name="querying-system-properties"></a>Fråga Systemegenskaper
I Azure mobiltjänster, Systemegenskaper skickas inte som standard, men endast om det behövs med hjälp av frågesträngen `__systemProperties`. Däremot i Azure Mobile Apps system egenskaper är **alltid valt** eftersom de ingår i objektmodellen för server SDK.

Den här ändringen påverkar huvudsakligen anpassade implementeringar av domän-chefer, till exempel tillägg av `MappedEntityDomainManager`. I mobiltjänster, om en klient begär aldrig alla Systemegenskaper är det möjligt att använda en `MappedEntityDomainManager` som faktiskt mappar inte alla egenskaper. Men i Azure Mobile Apps genereras egenskaperna omappade ett fel i GET-frågor.

Det enklaste sättet att lösa problemet är att ändra din Dto så att de ärver från `ITableData` i stället för `EntityData`. Lägg sedan till den `[NotMapped]` attributet de fält som ska utelämnas.

Till exempel följande definierar `TodoItem` utan system-egenskaper:

```csharp
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
```

Obs: om det uppstår fel på `NotMapped`, lägga till en referens till sammansättningen `System.ComponentModel.DataAnnotations`.

### <a name="cors"></a>CORS
Mobiltjänster med vissa stöd för CORS genom att omsluta ASP.NET CORS-lösning. Det här radbrytning-lagret har tagits bort för att ge utvecklare mer kontroll så att du kan använda direkt [ASP.NET CORS-stöd](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

De viktigaste delarna av problem om du använder CORS är som den `eTag` och `Location` rubriker måste tillåtas för klient-SDK: er ska fungera korrekt.

### <a name="push-notifications"></a>Push-meddelanden
För push-installation är objektet kanske saknas från Server SDK PushRegistrationHandler-klassen. Registreringar hanteras lite annorlunda i Mobile Apps och tagless registreringar är aktiverade som standard. Hantera taggar kan åstadkommas med hjälp av anpassade API: er. Finns det [registrerar sig för taggar](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) instruktioner för mer information.

### <a name="scheduled-jobs"></a>Schemalagda jobb
Schemalagda uppgifter är inte inbyggda i Mobile Apps, så måste alla befintliga jobb som du har i din .NET-serverdel som ska uppgraderas individuellt. Ett alternativ är att skapa en schemalagd [Webbjobb] på webbplatsen kod för Mobilapp. Du kan också konfigurera en kontroll som innehåller jobbkoden för och konfigurera den [Azure Scheduler] att träffa att slutpunkten på förväntade schemat.

### <a name="miscellaneous-changes"></a>Övriga ändringar
Alla ApiControllers som ska användas av en mobil klient måste nu har den `[MobileAppController]` attribut. Det är inte längre ingår som standard så att andra ApiControllers gå påverkas inte av mobila-formaterare.

Den `ApiServices` objektet är inte längre ingår i SDK. Du kan använda följande för att komma åt Mobile App-inställningar:

```csharp
MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
```

På samma sätt sker nu loggning med hjälp av standard ASP.NET-spårning skrivning:

```csharp
ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
traceWriter.Info("Hello, World");  
```

## <a name="authentication"></a>Överväganden för autentisering
Autentisering-komponenterna i mobiltjänster har nu flyttats till funktionen App Service autentisering/auktorisering. Du kan lära dig om hur du aktiverar det för din webbplats genom att läsa den [Lägg till autentisering till din mobilapp](app-service-mobile-ios-get-started-users.md) avsnittet.

För vissa leverantörer, till exempel AAD, Facebook och Google, bör du kunna utnyttja den befintliga registreringen från programmets kopia. Du behöver bara gå till portalen den identitetsprovider och lägga till en ny omdirigerings-URL i registreringen. Konfigurera App Service autentisering/auktorisering med klient-ID och hemlighet.

### <a name="controller-action-authorization"></a>Controller åtgärd auktorisering
Alla instanser av den `[AuthorizeLevel(AuthorizationLevel.User)]` attributet måste nu ändras om du vill använda standard ASP.NET `[Authorize]` attribut. Dessutom är nu anonym som standard, som i andra ASP.NET-program.
Om du använde något av de andra märke AuthorizeLevel alternativ, till exempel Admin eller ett program, Lägg till att de är borta. Du kan i stället konfigurera AuthorizationFilters för delade hemligheter eller konfigurera ett AAD-tjänstens huvudnamn för att aktivera tjänst-till-tjänst-anrop på ett säkert sätt.

### <a name="getting-additional-user-information"></a>Ytterligare hämtades
Du kan få ytterligare information, inklusive åtkomst-token via den `GetAppServiceIdentityAsync()` metoden:

```csharp
FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();
```

Om ditt program tar beroenden på användar-ID, till exempel lagra dem i en databas, är det dessutom viktigt att notera att användar-ID mellan mobiltjänster och App Service Mobile Apps är olika. Du kan fortfarande komma Mobile Services användar-ID, men. Alla ProviderCredentials underklasser har en användar-ID-egenskap. Du fortsätter så i exemplet innan:

```csharp
string mobileServicesUserId = creds.Provider + ":" + creds.UserId;
```

Om din app tar eventuella beroenden på användar-ID, är det viktigt att du utnyttja samma registrering med en identitetsprovider om möjligt. Användar-ID är vanligtvis begränsade till programregistrering som användes, så Vi presenterar en ny registrering kan skapa problem med matchande användare till sina data.

### <a name="custom-authentication"></a>Anpassad autentisering
Om din app använder en lösning för anpassad autentisering, kommer du att se till att den uppgraderade platsen har åtkomst till systemet. I nya anvisningarna för anpassad autentisering i den [översikt över SDK för .NET server] att integrera din lösning. Observera att komponenter för anpassad autentisering är fortfarande i förhandsversion.

## <a name="updating-clients"></a>Uppdatera klienter
När du har en operational mobilappsserverdel kan arbeta du med en ny version av ditt klientprogram som använder den. Mobile Apps innehåller också en ny version av klienten SDK: er och liknar serveruppgradering ovan, behöver du ta bort alla referenser till SDK: er för Mobile Services innan du installerar Mobile Apps-versioner.

En av de viktigaste ändringarna mellan versionerna är att konstruktorer inte längre behöver en programnyckel. Du nu skicka bara URL-adressen till din Mobilapp. Till exempel på .NET-klienter den `MobileServiceClient` konstruktorn är nu:

```csharp
public static MobileServiceClient MobileService = new MobileServiceClient(
    "https://contoso.azurewebsites.net", // URL of the Mobile App
);
```

Du kan läsa om att installera de nya SDK: er och använder den nya strukturen via länkarna nedan:

* [iOS-version 3.0.0 eller senare](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (Windows/Xamarin) version 2.0.0 eller senare](app-service-mobile-dotnet-how-to-use-client-library.md)

Om din tillämpning utför använder push-meddelanden kan anteckna de specifika instruktionerna för varje plattform, har eftersom det ändringar det också.

När du har den nya klientversionen som är redo, prova mot projektet uppgraderade servern. När du har validerat att det fungerar, kan du släpper en ny version av programmet till kunder. Så småningom när kunderna har haft möjlighet att få dessa uppdateringar, du kan ta bort den mobiltjänster-versionen av din app. Nu kan har du uppgraderat till en App Service Mobile App som använder den senaste Mobile Apps-servern SDK.

<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[Vad är Mobile Apps?]: app-service-mobile-value-prop.md
[SDK för Mobile App-servern]: https://www.nuget.org/packages/microsoft.azure.mobile.server
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /azure/scheduler/
[Webbjobb]: https://github.com/Azure/azure-webjobs-sdk/wiki
[Hur du använder SDK för .NET-server]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Prissättning för App Service]: https://azure.microsoft.com/pricing/details/app-service/
[Översikt över SDK för .NET server]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
