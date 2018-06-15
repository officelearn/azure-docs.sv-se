---
title: Hantering av registrering
description: Det här avsnittet beskrivs hur du registrerar enheter med notification hubs för att kunna ta emot push-meddelanden.
services: notification-hubs
documentationcenter: .net
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 7f9052da066fcc0021151bf3b547484859cf216d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776931"
---
# <a name="registration-management"></a>Registreringshantering
## <a name="overview"></a>Översikt
Det här avsnittet beskrivs hur du registrerar enheter med notification hubs för att kunna ta emot push-meddelanden. Avsnittet beskriver registreringar på en hög nivå och beskriver de två huvudsakliga mönster för att registrera enheter: registrering från enheten direkt till meddelandehubben och registrera via en serverdel för programmet. 

## <a name="what-is-device-registration"></a>Vad är registrering av enheten
Registrering av enheten med en Meddelandehubb åstadkoms med hjälp av en **registrering** eller **Installation**.

#### <a name="registrations"></a>Registreringar
En registrering associerar referensen Platform Notification Service (PNS) för en enhet med taggar och eventuellt en mall. PNS-handtag kan vara en ChannelURI eller enhetstoken GCM registrerings-id. Taggar används för att vidarebefordra meddelanden till rätt uppsättning enhetshandtag. Mer information finns i [Routning och Tagguttryck](notification-hubs-tags-segment-push-message.md). Mallar används för att implementera per registrering omvandling. Mer information finns i [mallar](notification-hubs-templates-cross-platform-push-messages.md).

#### <a name="installations"></a>Installationer
En Installation är en förbättrad registrering som innehåller en egenskapsuppsättning för push-relaterade egenskaper. Det är den senaste och bästa metoden för att registrera dina enheter. Men det inte stöds av klientsidan .NET SDK ([Notification Hub SDK för backend-åtgärder](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) ännu.  Det innebär att om du registrerar från själva klientenheten, skulle du behöva använda de [Notification Hub REST API](https://msdn.microsoft.com/library/mt621153.aspx) metod för att stödja installationer. Om du använder en serverdelstjänst du ska kunna använda [Notification Hub SDK för backend-åtgärder](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Här följer några viktiga fördelar med att använda installationer:

* Skapar eller uppdaterar en installation är helt idempotent. Så du kan köra det igen utan frågor om duplicerade registreringar.
* Installationen modellen gör det enkelt att göra enskilda push - specifik enhet som mål. En system-tagg **”$InstallationId: [installationId]”** läggs till automatiskt med varje installation baserad registrering. Så kan du anropa en Skicka till den här taggen ska gälla en viss enhet utan att behöva göra ytterligare kodning.
* Med hjälp av installationer kan du också göra en partiell registrering uppdateringar. Begärs deluppdatering av en installation med en korrigering metoden den [JSON-korrigering standard](https://tools.ietf.org/html/rfc6902). Detta är användbart när du vill uppdatera taggarna i registreringen. Du behöver inte hämtar hela registreringen och skicka om alla tidigare taggar.

En installation kan innehålla följande egenskaper. En fullständig lista över installationsegenskaperna finns [skapa eller skriva över en Installation med REST API](https://msdn.microsoft.com/library/azure/mt621153.aspx) eller [installationsegenskaper](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.installation_properties.aspx).

    // Example installation format to show some supported properties
    {,
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



Det är viktigt att notera att registreringar och installationer som standard inte längre går ut.

Registreringar och installationer måste innehålla en giltig PNS-referens för varje enhet/kanal. Eftersom PNS-handtag kan endast hämtas i ett klientprogram på enheten, är ett mönster för att registrera direkt på enheten med klientappen. Å andra sidan kan säkerhetsaspekter och affärslogik som rör taggar kräva att du vill hantera enhetsregistrering i appens serverdel. 

#### <a name="templates"></a>Mallar
Om du vill använda [mallar](notification-hubs-templates-cross-platform-push-messages.md), Enhetsinstallationen innehåller även alla mallar som är associerade med enheten i en JSON-format (se exemplet ovan). Mallnamn hjälpa mål olika mallar för samma enhet.

Varje mallnamn mappar till en mall text och ett valfritt antal taggar. Dessutom kan varje plattform har ytterligare egenskaper. Ytterligare en uppsättning huvuden kan vara en del av mallen för Windows Store (med WNS) och Windows Phone 8 (med MPNS). Du kan ange en utgången egenskap antingen en konstant eller till ett malluttryck som för APNs. En fullständig lista över egenskaper finns i den installation [skapa eller skriva över en Installation med övriga](https://msdn.microsoft.com/library/azure/mt621153.aspx) avsnittet.

#### <a name="secondary-tiles-for-windows-store-apps"></a>Sekundär paneler för Windows Store-appar
För Windows Store-klientprogram, skicka meddelanden till sekundär paneler är samma som skickas till den primära. Detta stöds även för installationer. Sekundär brickor har en annan ChannelUri som SDK på din klientapp hanterar transparent.

Ordlistan SecondaryTiles använder samma TileId som används för att skapa SecondaryTiles-objektet i Windows Store-app.
Precis som med den primära ChannelUri kan ChannelUris sekundära brickor ändra när som helst. För att skydda installationer i meddelandehubben uppdateras måste enheten uppdatera dem med den aktuella ChannelUris sekundära brickor.

## <a name="registration-management-from-the-device"></a>Hantering av registrering från enheten
När du hanterar registrering av enheten från klientprogram ansvarar endast serverdelen för att skicka meddelanden. Klientprogram hålla PNS-handtag uppdaterade och registrera taggar. Följande bild illustrerar detta mönster.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Enheten först hämtar PNS-handtag från pns-systemet och registreras i meddelandehubben direkt. När registreringen är klar, kan appens serverdel skicka ett meddelande som mål att registreringen. Mer information om hur du skickar meddelanden finns [Routning och Tagguttryck](notification-hubs-tags-segment-push-message.md).
I det här fallet kan du använda endast lyssna rättigheter att få åtkomst till din meddelandehubbar från enheten. Mer information finns i [säkerhet](notification-hubs-push-notification-security.md).

Det är det enklaste sättet att registrera från enheten, men den har vissa nackdelar.
Den första Nackdelen är att ett klientprogram kan uppdatera taggarna endast när appen är aktiv. Till exempel om en användare har två enheter som registreras taggar som rör sport team när den första enheten registreras för en ytterligare tagg (till exempel Seahawks), får den andra enheten meddelanden om Seahawks förrän appen på den andra körs en gång. När taggar som påverkas av flera enheter, är hantera taggar från serverdelen generellt önskvärt alternativet.
Registrering-hantering från klientappen andra Nackdelen är att, eftersom kan vara över appar, skydda registrering till särskilda taggar kräver extra noggrann enligt beskrivningen i avsnittet ”säkerhet på användarnivå taggen”.

#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Exempelkod registreras med en meddelandehubb från en enhet med en installation
Just nu är detta stöds endast med hjälp av den [Notification Hub REST API](https://msdn.microsoft.com/library/mt621153.aspx).

Du kan också använda en korrigering metoden med hjälp av den [JSON-korrigering standard](https://tools.ietf.org/html/rfc6902) för att uppdatera installationen.

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

    // If we have not stored a installation id in application data, create and store as application data.
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



#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Exempelkod registreras med en meddelandehubb från en enhet med en registrering
Dessa metoder skapa eller uppdatera en registrering för enheten som de anropas. Det innebär att för att kunna uppdatera referensen eller taggarna, du måste skriva över hela registreringen. Kom ihåg att registreringar är tillfälligt, så du bör alltid ha en tillförlitlig lagring med aktuella taggar som behöver en specifik enhet.

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


## <a name="registration-management-from-a-backend"></a>Registrering-hantering från en serverdel
Hantera registreringar från serverdelen måste du skriva ytterligare kod. Appen från enheten måste ange hantera uppdaterade pns-systemet till serverdelen för varje gång appen startas (tillsammans med taggar och mallar) och serverdelen måste uppdatera det här handtaget på meddelandehubben. Följande bild illustrerar den här designen.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

Fördelarna med att hantera registreringar från serverdelen omfattar möjligheten att ändra taggar till registreringar även om motsvarande appen på enheten är inaktiv och att autentisera klientappen innan du lägger till en tagg registreringen.

#### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Exempelkod registreras med en meddelandehubb från en serverdel som använder en installation
Klientenheten hämtar dess PNS-handtag och relevanta installationsegenskaper som innan fortfarande och en anpassad API-anrop på serverdelen som kan utföra registreringen och auktorisera taggar osv. Serverdelen kan utnyttja den [Notification Hub SDK för backend-åtgärder](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Du kan också använda en korrigering metoden med hjälp av den [JSON-korrigering standard](https://tools.ietf.org/html/rfc6902) för att uppdatera installationen.

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
            case "gcm":
                installation.Platform = NotificationPlatform.Gcm;
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


#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Exempelkod registreras med en meddelandehubb från en enhet med ett registrerings-ID
Du kan utföra grundläggande åtgärder för CRUDS för registreringar från din Apps serverdel. Exempel:

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


Serverdelen måste hantera samtidighet mellan registrering uppdateringar. Service Bus erbjuder optimistisk samtidighetskontroll för hantering av registreringen. Detta har implementerats med hjälp av ETag på registrering hanteringsåtgärder på HTTP-nivå. Den här funktionen används transparent av Microsoft SDKs som utlöser ett undantag om en uppdatering avvisas samtidighet skäl. Appens serverdel ansvarar för att hantera dessa undantag och försöker igen uppdateringen om det behövs.

