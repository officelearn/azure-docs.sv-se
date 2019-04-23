---
title: Registration Management
description: Det här avsnittet beskrivs hur du registrerar enheter med notification hubs för att kunna ta emot push-meddelanden.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.author: jowargo
ms.date: 04/08/2019
ms.openlocfilehash: 64c2cd0ed1572fdaaa42f4731519ba6d5c320f1c
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149137"
---
# <a name="registration-management"></a>Registreringshantering

## <a name="overview"></a>Översikt

Det här avsnittet beskrivs hur du registrerar enheter med notification hubs för att kunna ta emot push-meddelanden. Avsnittet beskriver registreringar på hög nivå och sedan introducerar två huvudsakliga mönster för att registrera enheter: registrering från enheten direkt till notification hub och registrera via en program-serverdel.

## <a name="what-is-device-registration"></a>Vad är enhetsregistrering

Enhetsregistrering med en Notification Hub åstadkoms med hjälp av en **registrering** eller **Installation**.

### <a name="registrations"></a>Registreringar

En registrering associerar Platform Notification Service (PNS) referensen för en enhet med taggar och eventuellt en mall. PNS-handtag kan vara en ChannelURI eller enhetstoken FCM registrerings-id. Taggar används för att dirigera meddelanden till rätt uppsättning enhetshandtag. Mer information finns i [Routning och Tagguttryck](notification-hubs-tags-segment-push-message.md). Mallar används för att implementera per registrering omvandling. Mer information finns i [Mallar](notification-hubs-templates-cross-platform-push-messages.md).

> [!NOTE]
> Azure Notification Hubs stöder högst 60 taggar per enhet.

### <a name="installations"></a>Installationer

En Installation är en förbättrad registrering som innehåller en mängd push relaterade egenskaper. Det är den senaste och bästa metoden för att registrera dina enheter. Men det inte stöds av klient-SDK för .NET ([Notification Hub-SDK för backend-åtgärder](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) ännu.  Det innebär att om du registrerar från själva klientenheten, skulle du behöva använda den [Notification Hubs – REST API](https://docs.microsoft.com/en-us/rest/api/notificationhubs/create-overwrite-installation) metod för att stödja installationer. Om du använder en backend-tjänst du ska kunna använda [Notification Hub-SDK för backend-åtgärder](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Här följer några viktiga fördelar med att använda installationer:

- Skapar eller uppdaterar en installation är helt idempotenta. Så du kan försöka igen utan frågor om duplicerade registreringar.
- Modellen installation har stöd för en särskild tagg-format (`$InstallationId:{INSTALLATION_ID}`) som gör att skicka ett meddelande direkt till enheten. Exempel: om appens koden anger ett installations-ID för `joe93developer` för just den här enheten, en utvecklare kan riktas mot den här enheten när du skickar ett meddelande till den `$InstallationId:{joe93developer}` tagg. På så sätt kan du rikta en specifik enhet utan att behöva göra ytterligare kodning.
- Med hjälp av installationer kan du också göra partiella registreringsuppdateringar. Deluppdatering av en installation begärs med en PATCH-metoden med den [JSON-Patch standard](https://tools.ietf.org/html/rfc6902). Detta är användbart när du vill uppdatera taggar på registreringen. Du behöver hämta hela registreringen och skicka sedan om alla tidigare taggar.

En installation kan innehålla följande egenskaper. En fullständig lista över installationsegenskaper Se [skapa eller skriva över en Installation med REST API](https://docs.microsoft.com/en-us/rest/api/notificationhubs/create-overwrite-installation) eller [installationsegenskaper](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.installation_properties.aspx).

```json
// Example installation format to show some supported properties
{
    installationId: "",
    expirationTime: "",
    tags: [],
    platform: "",
    pushChannel: "",
    ………
    templates: {
        "templateName1" : {
            body: "",
            tags: [] },
        "templateName2" : {
            body: "",
            // Headers are for Windows Store only
            headers: {
                "X-WNS-Type": "wns/tile" }
            tags: [] }
    },
    secondaryTiles: {
        "tileId1": {
            pushChannel: "",
            tags: [],
            templates: {
                "otherTemplate": {
                    bodyTemplate: "",
                    headers: {
                        ... }
                    tags: [] }
            }
        }
    }
}
```

> [!NOTE]
> Som standard, registreringar och installationer inte upphör att gälla.

Registreringar och installationer måste innehålla en giltig PNS-handtag för varje enhet/kanal. Eftersom PNS-handtag kan endast hämtas i en klientapp på enheten, är ett mönster att registrera direkt på enheten med klientappen. Å andra sidan kan säkerhetsaspekter och affärslogik som rör taggar kräva att du hanterar registrering av enheter i appens serverdel.

### <a name="templates"></a>Mallar

Om du vill använda [mallar](notification-hubs-templates-cross-platform-push-messages.md), Enhetsinstallationen innehåller även alla mallar som är associerade med enheten i en JSON-format (se exemplet ovan). Mallnamn hjälpa target olika mallar för samma enhet.

Varje mallnamn mappas till en mall-text och en valfri uppsättning taggar. Varje plattform kan dessutom ha ytterligare mallens egenskaper. För Windows Store (med WNS) och Windows Phone 8 (med MPNS), kan ytterligare en uppsättning huvuden ingå i mallen. När det gäller APNs, kan du ange en giltighetstid-egenskap till antingen en konstant eller till ett malluttryck. En fullständig lista över egenskaper finns i den installationen [skapa eller skriva över en Installation med REST](https://msdn.microsoft.com/library/azure/mt621153.aspx) avsnittet.

### <a name="secondary-tiles-for-windows-store-apps"></a>Sekundär paneler för Windows Store-appar

För Windows Store-klientprogram, skicka meddelanden till sekundär paneler är samma som skickas till den primära. Detta stöds även för installationer. Sekundär paneler har en annan ChannelUri då SDK i din klientapp hanterar transparent.

SecondaryTiles ordlista använder samma TileId som används för att skapa objektet SecondaryTiles i din Windows Store-app. Precis som med primära ChannelUri kan ChannelUris av sekundära paneler ändra när som helst. För att skydda installationerna i meddelandehubben uppdateras måste enheten uppdatera dem med den aktuella ChannelUris av de sekundära panelerna.

## <a name="registration-management-from-the-device"></a>Registreringshantering från enheten

När du hanterar registrering av enheten från klientappar ansvarar endast serverdelen för att skicka meddelanden. Klientappar hålla PNS-handtag uppdaterade och registrera taggar. Följande bild illustrerar det här mönstret.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Enheten kan du först hämtar PNS-handtag från pns-systemet och sedan registreras i meddelandehubben direkt. När registreringen är klar, kan app-serverdel skicka ett meddelande som riktar in sig på denna registrering. Mer information om hur du skickar meddelanden finns i [Routning och Tagguttryck](notification-hubs-tags-segment-push-message.md).

I det här fallet kan du använda endast behörighet att lyssna, få åtkomst till din meddelandehubbar från enheten. Mer information finns i [Security](notification-hubs-push-notification-security.md).

Det är det enklaste sättet att registrera från enheten, men den har vissa nackdelar:

- Ett klientprogram kan bara uppdatera dess taggar när appen är aktiv. Till exempel om en användare har två enheter att registreras taggar som är relaterade till grupper med sport, när den första enheten registreras för en ytterligare tagg (t.ex, Seahawks), får den andra enheten inte meddelanden om Seahawks tills appen på den andra är köra en andra gång. När taggar som påverkas av flera enheter, är hantera taggar från serverdelen, vanligtvis ett önskvärt alternativ.
- Eftersom appar kan vara hackad, kräver skydda registreringen att särskilda taggar extra försiktig, enligt beskrivningen i avsnittet ”säkerhet tagg på användarnivå”.

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Exempelkod för att registrera på en notification hub från en enhet med en installation

För tillfället detta stöds endast med hjälp av den [Notification Hubs – REST API](https://msdn.microsoft.com/library/mt621153.aspx).

Du kan också använda en PATCH metoden med hjälp av den [JSON-Patch standard](https://tools.ietf.org/html/rfc6902) för att uppdatera installationen.

```
class DeviceInstallation
{
    public string installationId { get; set; }
    public string platform { get; set; }
    public string pushChannel { get; set; }
    public string[] tags { get; set; }
}

private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
        string hubName, string listenConnectionString)
{
    if (deviceInstallation.installationId == null)
        return HttpStatusCode.BadRequest;

    // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
    ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
    string hubResource = "installations/" + deviceInstallation.installationId + "?";
    string apiVersion = "api-version=2015-04";

    // Determine the targetUri that we will sign
    string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

    //=== Generate SaS Security Token for Authorization header ===
    // See, https://msdn.microsoft.com/library/azure/dn495627.aspx
    string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

    using (var httpClient = new HttpClient())
    {
        string json = JsonConvert.SerializeObject(deviceInstallation);

        httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

        var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
        return response.StatusCode;
    }
}

var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

string installationId = null;
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored an installation id in application data, create and store as application data.
if (!settings.ContainsKey("__NHInstallationId"))
{
    installationId = Guid.NewGuid().ToString();
    settings.Add("__NHInstallationId", installationId);
}

installationId = (string)settings["__NHInstallationId"];

var deviceInstallation = new DeviceInstallation
{
    installationId = installationId,
    platform = "wns",
    pushChannel = channel.Uri,
    //tags = tags.ToArray<string>()
};

var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                    "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

if (statusCode != HttpStatusCode.Accepted)
{
    var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
    dialog.Commands.Add(new UICommand("OK"));
    await dialog.ShowAsync();
}
else
{
    var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
    dialog.Commands.Add(new UICommand("OK"));
    await dialog.ShowAsync();
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Exempelkod för att registrera på en notification hub från en enhet med en registrering

De här metoderna skapa eller uppdatera en registrering för enheten där de heter. Det innebär att om du vill uppdatera referensen eller taggarna, du måste skriva över hela registreringen. Kom ihåg att registreringar är tillfälligt, så du bör alltid ha en pålitlig store med de aktuella taggar som krävs för en specifik enhet.

```
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// The Device id from the PNS
var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

// If you are registering from the client itself, then store this registration id in device
// storage. Then when the app starts, you can check if a registration id already exists or not before
// creating.
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored a registration id in application data, store in application data.
if (!settings.ContainsKey("__NHRegistrationId"))
{
    // make sure there are no existing registrations for this push handle (used for iOS and Android)    
    string newRegistrationId = null;
    var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
    foreach (RegistrationDescription registration in registrations)
    {
        if (newRegistrationId == null)
        {
            newRegistrationId = registration.RegistrationId;
        }
        else
        {
            await hub.DeleteRegistrationAsync(registration);
        }
    }

    newRegistrationId = await hub.CreateRegistrationIdAsync();

    settings.Add("__NHRegistrationId", newRegistrationId);
}

string regId = (string)settings["__NHRegistrationId"];

RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
registration.RegistrationId = regId;
registration.Tags = new HashSet<string>(YourTags);

try
{
    await hub.CreateOrUpdateRegistrationAsync(registration);
}
catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
{
    settings.Remove("__NHRegistrationId");
}
```

## <a name="registration-management-from-a-backend"></a>Av registreringshantering av en serverdel

Hantera registreringar från serverdelen kräver skriva ytterligare kod. Appen från enheten måste ange de uppdaterade PNS hantera till serverdelen varje gång appen startas (tillsammans med taggar och mallar) och serverdelen måste uppdatera den här referensen i meddelandehubben. Följande bild illustrerar den här designen.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

Fördelar med att hantera registreringar från serverdelen är möjligheten att ändra taggar för registreringar även när motsvarande appen på enheten är inaktiv samt för att autentisera klientappen innan du lägger till en tagg registreringen.

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Exempelkod för att registrera på en notification hub från en serverdel som använder en installation

Klientenheten hämtar dess PNS-handtag och relevanta installationsegenskaper som innan fortfarande och anropar en anpassad API på serverdelen som kan utföra registreringen och auktorisera taggar osv. Serverdelen kan utnyttja den [Notification Hub-SDK för backend-åtgärder](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Du kan också använda en PATCH metoden med hjälp av den [JSON-Patch standard](https://tools.ietf.org/html/rfc6902) för att uppdatera installationen.

```
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// Custom API on the backend
public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
{

    Installation installation = new Installation();
    installation.InstallationId = deviceUpdate.InstallationId;
    installation.PushChannel = deviceUpdate.Handle;
    installation.Tags = deviceUpdate.Tags;

    switch (deviceUpdate.Platform)
    {
        case "mpns":
            installation.Platform = NotificationPlatform.Mpns;
            break;
        case "wns":
            installation.Platform = NotificationPlatform.Wns;
            break;
        case "apns":
            installation.Platform = NotificationPlatform.Apns;
            break;
        case "fcm":
            installation.Platform = NotificationPlatform.Fcm;
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }


    // In the backend we can control if a user is allowed to add tags
    //installation.Tags = new List<string>(deviceUpdate.Tags);
    //installation.Tags.Add("username:" + username);

    await hub.CreateOrUpdateInstallationAsync(installation);

    return Request.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Exempelkod för att registrera på en notification hub från en enhet med en registrerings-ID

Du kan utföra grundläggande CRUDS åtgärder på registreringar från din app-serverdel. Exempel:

```
var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");

// create a registration description object of the correct type, e.g.
var reg = new WindowsRegistrationDescription(channelUri, tags);

// Create
await hub.CreateRegistrationAsync(reg);

// Get by id
var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

// update
r.Tags.Add("myTag");

// update on hub
await hub.UpdateRegistrationAsync(r);

// delete
await hub.DeleteRegistrationAsync(r);
```

Serverdelen måste hantera samtidighet mellan registreringsuppdateringar. Service Bus erbjuder optimistisk samtidighetskontroll för registreringshantering av. Detta är implementerat med hjälp av ETag på registreringshanteringsåtgärder på HTTP-nivå. Den här funktionen används transparent av Microsoft-SDKs som utlöser ett undantag om en uppdatering avvisas samtidighet skäl. Appserverdelen ansvarar för att hantera de här undantagen och försöker igen uppdateringen om det behövs.
