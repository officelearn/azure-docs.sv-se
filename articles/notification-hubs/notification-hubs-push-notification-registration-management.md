---
title: Registreringshantering
description: I det här avsnittet beskrivs hur du registrerar enheter med meddelandehubbar för att ta emot push-meddelanden.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/08/2019
ms.openlocfilehash: 00de9c803ef796eda8da609a4009e0a8cfcb3664
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455375"
---
# <a name="registration-management"></a>Registreringshantering

I det här avsnittet beskrivs hur du registrerar enheter med meddelandehubbar för att ta emot push-meddelanden. Avsnittet beskriver registreringar på hög nivå, introducerar sedan de två huvudmönstren för registrering av enheter: registrering från enheten direkt till meddelandehubben och registrering via en programservering.

## <a name="what-is-device-registration"></a>Vad är enhetsregistrering

Enhetsregistrering med en meddelandehubb utförs med hjälp av en **registrering** eller **installation**.

### <a name="registrations"></a>Registreringar

En registrering associerar PNS-handtaget (Platform Notification Service) för en enhet med taggar och eventuellt en mall. PNS-referensen kan vara ett ChannelURI-, enhetstoken eller FCM-registrerings-ID. Taggar används för att dirigera meddelanden till rätt uppsättning enhetshandtag. Mer information finns i [Routning och tagguttryck](notification-hubs-tags-segment-push-message.md). Mallar används för att implementera omvandling per registrering. Mer information finns i [Mallar](notification-hubs-templates-cross-platform-push-messages.md).

> [!NOTE]
> Azure Notification Hubs stöder högst 60 taggar per enhet.

### <a name="installations"></a>Installationer

En installation är en förbättrad registrering som innehåller en påse med push-relaterade egenskaper. Det är den senaste och bästa metoden för att registrera dina enheter. Det stöds dock inte av klientsidan .NET SDK ([Notification Hub SDK för backend-åtgärder](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) i nuläget.  Det innebär att om du registrerar dig från själva klientenheten måste du använda metoden [Notification Hubs REST API](/rest/api/notificationhubs/create-overwrite-installation) för att stödja installationer. Om du använder en backend-tjänst bör du kunna använda [Notification Hub SDK för backend-åtgärder](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Följande är några viktiga fördelar med att använda installationer:

- Att skapa eller uppdatera en installation är helt idempotent. Så du kan försöka igen utan några frågor om dubbla registreringar.
- Installationsmodellen stöder ett speciellt`$InstallationId:{INSTALLATION_ID}`taggformat ( ) som gör det möjligt att skicka ett meddelande direkt till den specifika enheten. Om appens kod till exempel anger ett `joe93developer` installations-ID för den här enheten kan en `$InstallationId:{joe93developer}` utvecklare rikta in sig på den här enheten när du skickar ett meddelande till taggen. På så sätt kan du rikta in dig på en viss enhet utan att behöva göra någon ytterligare kodning.
- Med hjälp av installationer kan du också göra partiella registreringsuppdateringar. Den partiella uppdateringen av en installation begärs med en [PATCH-metod med JSON-Patch-standarden](https://tools.ietf.org/html/rfc6902). Detta är användbart när du vill uppdatera taggar på registreringen. Du behöver inte dra ner hela registreringen och sedan skicka alla tidigare taggar igen.

En installation kan innehålla följande egenskaper. En fullständig lista över installationsegenskaperna finns i [Skapa eller skriva över en installation med REST API](/rest/api/notificationhubs/create-overwrite-installation) eller [installationsegenskaper](/dotnet/api/microsoft.azure.notificationhubs.installation).

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
> Som standard upphör inte registreringar och installationer att gälla.

Registreringar och installationer måste innehålla ett giltigt PNS-handtag för varje enhet/kanal. Eftersom PNS-referenser endast kan erhållas i en klientapp på enheten är ett mönster att registrera sig direkt på den enheten med klientappen. Å andra sidan kan säkerhetsöverväganden och affärslogik som är relaterade till taggar kräva att du hanterar enhetsregistrering i appens backend.

> [!NOTE]
> Installations-API:et stöder inte Baidu-tjänsten (även om API:et för registreringar gör det). 

### <a name="templates"></a>Mallar

Om du vill använda Mallar innehåller [enhetsinstallationen](notification-hubs-templates-cross-platform-push-messages.md)även alla mallar som är associerade med enheten i ett JSON-format (se exempel ovan). Mallnamnen hjälper till att rikta in sig på olika mallar för samma enhet.

Varje mallnamn mappas till en malltext och en valfri uppsättning taggar. Dessutom kan varje plattform ha ytterligare mallegenskaper. För Windows Store (med WNS) och Windows Phone 8 (med MPNS) kan ytterligare en uppsättning rubriker ingå i mallen. När det gäller APN kan du ange en förfallodeegenskap till antingen en konstant eller till ett malluttryck. En fullständig lista över installationsegenskaperna finns i [Skapa eller skriva över ett installations- med REST-ämne.](/rest/api/notificationhubs/create-overwrite-installation)

### <a name="secondary-tiles-for-windows-store-apps"></a>Sekundära paneler för Windows Store-appar

För Windows Store-klientprogram är det samma sätt att skicka meddelanden till sekundära paneler som att skicka dem till den primära. Detta stöds också i installationer. Sekundära paneler har en annan ChannelUri, som SDK på klientappen hanterar transparent.

Ordlistan SecondaryTiles använder samma TileId som används för att skapa SecondaryTiles-objektet i Din Windows Store-app. Precis som med den primära ChannelUri kan ChannelUris av sekundära paneler ändras när som helst. För att hålla installationerna i meddelandehubben uppdaterade måste enheten uppdatera dem med de aktuella ChannelUris-hubben för de sekundära panelerna.

## <a name="registration-management-from-the-device"></a>Registreringshantering från enheten

När du hanterar enhetsregistrering från klientappar är backend endast ansvarig för att skicka meddelanden. Klientappar håller PNS-hanterar uppdaterade och registertaggar. Följande bild illustrerar detta mönster.

![Registrering från enhet](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Enheten hämtar först PNS-handtaget från PNS och registrerar sedan med meddelandehubben direkt. När registreringen har slutförts kan appens medberedskap skicka ett meddelande som är inriktat på registreringen. Mer information om hur du skickar meddelanden finns i [Routning och tagguttryck](notification-hubs-tags-segment-push-message.md).

I det här fallet använder du bara lyssningsrättigheter för att komma åt dina meddelandehubbar från enheten. Mer information finns i [Säkerhet](notification-hubs-push-notification-security.md).

Registrering från enheten är den enklaste metoden, men det har några nackdelar:

- En klientapp kan bara uppdatera sina taggar när appen är aktiv. Om en användare till exempel har två enheter som registrerar taggar relaterade till idrottslag, när den första enheten registrerar sig för en ytterligare tagg (till exempel Seahawks), kommer den andra enheten inte att få meddelanden om Seahawks förrän appen på den andra enheten är en andra gång. Mer allmänt, när taggar påverkas av flera enheter, hantera taggar från backend är ett önskvärt alternativ.
- Eftersom appar kan hackas krävs extra försiktighet för att säkra registreringen till specifika taggar, vilket förklaras i artikeln [Säkerhet](notification-hubs-push-notification-security.md).

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Exempel på kod för att registrera med en meddelandehubb från en enhet med hjälp av en installation

För närvarande stöds detta endast med hjälp av [REST API FÖR Notification Hubs.](/rest/api/notificationhubs/create-overwrite-installation)

Du kan också använda [PATCH-metoden med JSON-Patch-standarden](https://tools.ietf.org/html/rfc6902) för att uppdatera installationen.

```csharp
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

// If we have not stored an installation ID in application data, create and store as application data.
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

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Exempel på kod för att registrera med en meddelandehubb från en enhet med hjälp av en registrering

Dessa metoder skapar eller uppdaterar en registrering för den enhet som de anropas på. Det innebär att du måste skriva över hela registreringen för att kunna uppdatera handtaget eller taggarna. Kom ihåg att registreringar är övergående, så du bör alltid ha en tillförlitlig butik med de aktuella taggarna som en viss enhet behöver.

```csharp
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// The Device ID from the PNS
var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

// If you are registering from the client itself, then store this registration ID in device
// storage. Then when the app starts, you can check if a registration ID already exists or not before
// creating.
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored a registration ID in application data, store in application data.
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

## <a name="registration-management-from-a-backend"></a>Registreringshantering från en backend

För att hantera registreringar från backend krävs ytterligare kod. Appen från enheten måste tillhandahålla det uppdaterade PNS-handtaget till backend varje gång appen startar (tillsammans med taggar och mallar), och backend måste uppdatera den här referensen på meddelandehubben. Följande bild illustrerar den här designen.

![Registreringshantering](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

Fördelarna med att hantera registreringar från backend inkluderar möjligheten att ändra taggar till registreringar även när motsvarande app på enheten är inaktiv och att autentisera klientappen innan du lägger till en tagg i registreringen.

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Exempel på kod för att registrera med en meddelandehubb från en serverd med hjälp av en installation

Klientenheten får fortfarande sin PNS-handtag och relevanta installationsegenskaper som tidigare och anropar ett anpassat API på serversida som kan utföra registrerings- och auktorisera taggar etc. Den bakre sidan kan utnyttja [Meddelande Hub SDK för backend-åtgärder](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Du kan också använda [PATCH-metoden med JSON-Patch-standarden](https://tools.ietf.org/html/rfc6902) för att uppdatera installationen.

```csharp
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

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Exempel på kod för att registrera med en meddelandehubb från en enhet med hjälp av ett registrerings-ID

Från din app backend kan du utföra grundläggande CRUDS-åtgärder på registreringar. Ett exempel:

```csharp
var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");

// create a registration description object of the correct type, e.g.
var reg = new WindowsRegistrationDescription(channelUri, tags);

// Create
await hub.CreateRegistrationAsync(reg);

// Get by ID
var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

// update
r.Tags.Add("myTag");

// update on hub
await hub.UpdateRegistrationAsync(r);

// delete
await hub.DeleteRegistrationAsync(r);
```

Backend måste hantera samtidighet mellan registreringsuppdateringar. Service Bus erbjuder optimistisk samtidighetskontroll för registreringshantering. På HTTP-nivå implementeras detta med hjälp av ETag på registreringshanteringsåtgärder. Den här funktionen används transparent av Microsoft SDK:er, som genererar ett undantag om en uppdatering avvisas av samtidiga skäl. Appens backend ansvarar för att hantera dessa undantag och försöka igen uppdateringen om det behövs.
