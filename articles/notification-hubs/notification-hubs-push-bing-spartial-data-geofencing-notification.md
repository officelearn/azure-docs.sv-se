---
title: "Push-meddelanden till geografiskt avgränsade områden med Azure Notification Hubs och Bing Spatial Data | Microsoft Docs"
description: "I den här självstudiekurskursen får lära du dig att leverera platsbaserade push-meddelanden med Azure Notification Hub och Bing Spatial Data."
services: notification-hubs
documentationcenter: windows
keywords: push-meddelande, pushmeddelande
author: dend
manager: yuaxu
editor: dend
ms.assetid: f41beea1-0d62-4418-9ffc-c9d70607a1b7
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/13/2017
ms.author: dendeli
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 8db82ae9f37a89b6b7049208133949a7f49e9d92
ms.contentlocale: sv-se
ms.lasthandoff: 09/14/2017

---
# <a name="geo-fenced-push-notifications-with-azure-notification-hubs-and-bing-spatial-data"></a>Push-meddelanden till geografiskt avgränsade områden med Azure Notification Hubs och Bing Spatial Data
> [!NOTE]
> Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02).
> 
> 

I den här självstudiekursen får lära du dig att leverera platsbaserade push-meddelanden med Azure Notification Hub och Bing Spatial Data. Användningen underlättas av att allt genomförs från en UWP-app (Universal Windows Platform).

## <a name="prerequisites"></a>Krav
Först och främst ser du till att ha alla program och tjänster som krävs:

* [Visual Studio 2015 Update 1](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) eller senare ([Community Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409) går också bra). 
* Senaste versionen av [Azure SDK](https://azure.microsoft.com/downloads/). 
* [Konto på Bing Maps Dev Center](https://www.bingmapsportal.com/) (du kan skapa ett gratis och associera det med ditt Microsoft-konto). 

## <a name="getting-started"></a>Komma igång
Låt oss börja med att skapa projektet. Starta ett nytt projekt av typen **tom app (Universal Windows)** i Visual Studio.

![](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

När projektet har skapats har du en stomme för själva appen. Nu är det dags att göra alla justeringar för infrastrukturen för geografisk avgränsning (geo-fencing). Eftersom vi ska använda Bing-tjänster för att göra detta, finns det en offentlig REST-API-slutpunkt med vilken vi kan skicka förfrågningar till specifika platsramar: 

    http://spatial.virtualearth.net/REST/v1/data/

Du måste ange följande parametrar för att detta ska fungera:

* **Datakällans ID** och **Datakällnamn** – I Bing Maps API innehåller datakällorna flera bucketgrupperade metadata, som platser och öppettider. Du kan läsa mer om dessa här. 
* **Entitetsnamn** – Den entitet som du vill använda som referenspunkt för meddelandet. 
* **Bing Maps API-nyckel** – Det här är den nyckel som du fick tidigare, när du skapade Bing Dev Center-kontot.

Låt oss göra en djupdykning i inställningarna för vart och ett av elementen ovan.

## <a name="setting-up-the-data-source"></a>Ställa in datakällan
Du kan konfigurera datakällan i Bing Maps Dev Center. I det övre navigeringsfältet väljer du **Datakällor** > **Hantera datakällor**.

![](./media/notification-hubs-geofence/bing-maps-manage-data.png)

Om du inte har arbetat med Bing Maps API tidigare så kommer det troligen inte att finnas några datakällor där. Då kan du helt enkelt skapa en ny genom att välja **Datakällor** > **Ladda upp data**. Kontrollera att du fyller i alla obligatoriska fält:

![](./media/notification-hubs-geofence/bing-maps-create-data.png)

Du kanske undrar: Vad är datafilen och vad är det du ska föra över? I syfte att genomföra det här testet kan vi helt enkelt använda det pipe-baserade exempel som omfattar en del av San Franciscos hamnområde. 

    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))

Ovanstående representerar den här entiteten:

![](./media/notification-hubs-geofence/bing-maps-geofence.png)

Kopiera och klistra in ovanstående sträng i en ny fil, spara den som **NotificationHubsGeofence.pipe**. och ladda sedan upp den till Bing Dev Center.

> [!NOTE]
> Du kan uppmanas att ange en ny nyckel för **huvudnyckel**n. Denna skiljer sig från **frågenyckeln**. Du behöver bara skapa en ny nyckel via instrumentpanelen och uppdatera överföringssidan för datakällan.
> 
> 

När du har laddat upp datafilen, måste du publicera datakällan. 

Gå till **Hantera datakällor**, precis som du gjorde ovan, leta rätt på datakällan i listan och välj sedan **Publicera** i kolumnen **Åtgärder**. Efter en stund bör du se din datakälla på fliken **Publicerade datakällor**:

![](./media/notification-hubs-geofence/bing-maps-published-data.png)

Om du väljer **Redigera**, kommer du att få en snabb överblick över vilka platser du har fört in i den:

![](./media/notification-hubs-geofence/bing-maps-data-details.png)

I det här läget visar inte portalen gränserna för det geofence som du skapade. Det enda du behöver är en bekräftelse på att den angivna platsen ligger ungefär i rätt område.

Nu är alla krav uppfyllda för datakällan. Om du vill ha information om API-anropets URL-adress för begäran i Bing Maps Dev Center, väljer du **Datakällor** och väljer **Information om datakällan**.

![](./media/notification-hubs-geofence/bing-maps-data-info.png)

Det vi letar efter här är **Fråge-URL**. Det här är den slutpunkt mot vilken vi kan köra frågor för att kontrollera om enheten för närvarande befinner sig inom platsgränserna eller inte. För att utföra den här kontrollen kör vi bara ett GET-anrop mot fråge-URL:en, där följande parametrar läggs till:

    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY

På så sätt anger du en målpunkt som vi får från enheten och Bing Maps kommer automatiskt att utföra beräkningar för att kontrollera om den befinner sig inom vårt geofence eller inte. När du kör begäran via en webbläsare (eller cURL), kommer du att få ett JSON-standardsvar:

![](./media/notification-hubs-geofence/bing-maps-json.png)

Du får bara tillbaka detta svar när punkten verkligen ligger inom de angivna gränserna. Om detta inte är fallet, får du upp en tom **resultat**-bucket:

![](./media/notification-hubs-geofence/bing-maps-nores.png)

## <a name="setting-up-the-uwp-application"></a>Konfigurera UWP-appen
Nu när datakällan är redo kan vi börja jobba med UWP-appen som vi startade om tidigare.

Först och främst måste vi aktivera platstjänster för vår app. Det gör du genom att öppna `Package.appxmanifest`-filen i **Solution Explorer**.

![](./media/notification-hubs-geofence/vs-package-manifest.png)

I den flik för paketegenskaper som just öppnades väljer du **Funktioner** och väljer sedan **plats**:

![](./media/notification-hubs-geofence/vs-package-location.png)

Eftersom platsegenskapen har angetts ska du skapa en ny mapp i din lösning som kallas för `Core`. Sedan lägger du till en ny fil i denna med namnet `LocationHelper.cs`:

![](./media/notification-hubs-geofence/vs-location-helper.png)

Själva `LocationHelper`-klassen är ganska grundläggande just nu: allt den gör är att låta oss hämta användarens plats via system-API:et:

    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }

Du kan läsa mer om att hämta användarens plats med UWP-appar i det officiella [MSDN-dokumentet](https://msdn.microsoft.com/library/windows/apps/mt219698.aspx).

Om du vill kontrollera att platsinsamlingen faktiskt fungerar, kan du öppna din huvudsidas kodsida (`MainPage.xaml.cs`). Skapa en ny händelsehanterare för `Loaded`-händelsen i `MainPage`-konstruktorn:

    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }

Implementeringen av händelsehanteraren fungerar så här:

    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }

Observera att vi har deklarerat hanteraren som asynkron eftersom `GetCurrentLocation` är awaitable och därför måste köras i en asynkron kontext. Och eftersom vi under vissa omständigheter kan få en null-plats (t.ex. att platstjänsterna är inaktiverade eller att appen har nekats åtkomst till platsen på grund av fel behörighet), måste vi försäkra oss om att den hanteras korrekt med en null-kontroll.

Kör appen. Kontrollera att du tillåter platsåtkomst:

![](./media/notification-hubs-geofence/notification-hubs-location-access.png)

När appen startas ska du kunna se koordinaterna i fönstret **Utmatning**:

![](./media/notification-hubs-geofence/notification-hubs-location-output.png)

Nu vet du att platsförvärvet fungerar som det ska. Nu kan du även passa på att ta bort händelsehanteraren för test eftersom vi inte behöver använda den längre.

Nästa steg är att samla in platsförändringar. För att göra detta går vi tillbaka till klassen `LocationHelper` och lägger till händelsehanteraren för `PositionChanged`:

    geolocator.PositionChanged += Geolocator_PositionChanged;

Implementeringen visar platskoordinaterna i fönstret **Utmatning**:

    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }

## <a name="setting-up-the-backend"></a>Ställa in serverdelen
Hämta [Exempel för .NET-serverdel från GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers). När hämtningen är klar, öppnar du mappen `NotifyUsers` och därefter filen `NotifyUsers.sln`.

Ange projektet `AppBackend` som **startprojektet** och starta det.

![](./media/notification-hubs-geofence/vs-startup-project.png)

Projektet har redan konfigurerats för att skicka push-meddelanden till målenheterna så du behöver bara göra två saker: byta ut rätt anslutningssträng för meddelandehubben och lägga till gränsidentifieringar för att endast skicka meddelanden när användaren befinner sig inom geofence-området.

Öppna `Notifications.cs` i mappen `Models` för att konfigurera anslutningssträngen. Funktionen `NotificationHubClient.CreateClientFromConnectionString` bör innehålla den information om meddelandehubben som du kan hämta i [Azure-portalen](https://portal.azure.com) (titta på bladet **Åtkomstprinciper** under **Inställningar**). Spara den uppdaterade konfigurationsfilen.

Nu måste vi skapa en modell för Bing Maps API-resultatet. Det enklaste sättet att göra det är att öppna `Models`-mappen och välja **Lägg till** > **klass**. Ge den namnet `GeofenceBoundary.cs`. När du har gjort detta ska du kopiera JSON:et från det API-svar som vi pratade om i det första avsnittet. Och i Visual Studio använder du sedan **Redigera** > **Klistra in special** > **Klistra in JSON som klass**. 

På så sätt ser vi till att objektet avserialiseras exakt på det sätt som vi vill att det ska. Du bör nu ha en klassuppsättning som liknar följande:

    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }

Därefter öppnar du `Controllers` > `NotificationsController.cs`. Vi behöver finjustera efteranropet till kontot för att ta med mållongituden och mållatituden i beräkningen. För att göra detta behöver vi bara lägga till två strängar i funktionssignaturen: `latitude` och `longitude`.

    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)

Skapa en ny klass i projektet med namnet `ApiHelper.cs`. Vi kommer att använda den för att ansluta till Bing och kontrollera skärningspunkterna för punktgränser. Gör så här för att implementera en `IsPointWithinBounds`-funktion:

    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }

> [!NOTE]
> Ersätt API-slutpunkten med fråge-URL:en som du tidigare hämtade från Bing Dev Center (det samma gäller för API-nyckeln). 
> 
> 

Om du får resultat från begäran så innebär det att den angivna punkten befinner sig inom gränserna för geofence-området. Därför returnerar vi `true`. Om du inte får några resultat, kommer Bing att tala om för oss att punkten ligger utanför uppslagsramen. Därför returnerar vi `false`.

När du har återgått till `NotificationsController.cs` skapar du en markering precis före switch-uttrycket:

    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }

Om du gör detta kommer meddelandet endast att skickas när punkten befinner sig inom gränserna.

## <a name="testing-push-notifications-in-the-uwp-app"></a>Testa push-meddelanden i UWP-appen
När vi går tillbaka till UWP-appen, bör vi nu kunna testa meddelandefunktionen. Skapa en ny funktion – `SendLocationToBackend` –  i klassen `LocationHelper`.

    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }

> [!NOTE]
> Byt ut `POST_URL` till platsen för din distribuerade webbapp, den som vi skapade i det förra avsnittet. Just nu är det ok att köra appen lokalt men när du arbetar med att distribuera en offentlig version, måste du lägga upp den hos en extern leverantör.
> 
> 

Nu ska vi registrera UWP-appen för push-meddelanden. I Visual Studio väljer du **Project** (projekt)  > **Store** > **Associate App with the Store** (associera app med Store).

![](./media/notification-hubs-geofence/vs-associate-with-store.png)

Kontrollera att du väljer en befintlig app, eller skapa en ny, och associera paketet med den när du loggar in på ditt utvecklarkonto. 

Gå till Dev Center och öppna den app som du nyss skapade. Välj **Tjänster** > **Push-meddelanden** > **Webbplatsen för Live-tjänster** .

![](./media/notification-hubs-geofence/ms-live-services.png)

När du är på webbplatsen skriver du ned **apphemligheten** och **paket-SID:et**. Du kommer att behöva båda i Azure-portalen. Öppna din meddelandehubb, välj **Inställningar** > **Notification Services** > **Windows (WNS)** och ange uppgifterna i de obligatoriska fälten.

![](./media/notification-hubs-geofence/notification-hubs-wns.png)

Välj **Spara**.

Öppna **Referenser** i **Solution Explorer** och välj **Hantera NuGet-paket**. Vi måste lägga till en referens till **hanteringsbiblioteket för Microsoft Azure Service Bus**. Du behöver bara söka efter `WindowsAzure.Messaging.Managed` och lägga till den i projektet.

![](./media/notification-hubs-geofence/vs-nuget.png)

I testsyfte kan vi skapa händelsehanteraren `MainPage_Loaded` igen och lägga till det här kodstycket i den:

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }

Ovanstående innebär att appen registreras på meddelandehubben. Du är nu redo att sätta igång! 

I `LocationHelper`, inuti hanteraren `Geolocator_PositionChanged`, kan du lägga till en del av en testkod som med tvång placerar platsen inom geofence-området:

    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");

Eftersom vi inte skickar de faktiska koordinaterna (som kanske inte ligger inom gränserna för tillfället) och använder fördefinierade testvärden, kommer vi att se en avisering som visas i uppdateringen:

![](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

## <a name="whats-next"></a>Nästa steg
Det finns några steg som du kan behöva följa, förutom det som nämns ovan, för att försäkra dig om att lösningen är klar för produktion.

Först och främst kan du behöva se till att geofence-områdena är dynamiska. Detta kräver lite extraarbete med Bing-API:iet för att kunna ladda upp nya gränser inom den befintliga datakällan. Läs [API-dokumentationen för Bing Spatial Data Services](https://msdn.microsoft.com/library/ff701734.aspx) för mer information om ämnet.

Och för det andra: När du jobbar med att säkerställa att leveransen görs till rätt deltagare kanske du vill rikta in dig på dem med hjälp av [taggning](notification-hubs-tags-segment-push-message.md).

Lösningen ovan beskriver ett scenario där du kan ha en stor mängd olika målplattformar. Därför begränsar vi inte denna geofencing till systemspecifika funktioner. Men det bör understrykas att UWP har inbyggda, [kraftfulla funktioner för att detektera geofence-områden](https://msdn.microsoft.com/windows/uwp/maps-and-location/set-up-a-geofence).

För mer information om funktioner i Notification Hubs, se vår [dokumentationsportal](https://azure.microsoft.com/documentation/services/notification-hubs/).


