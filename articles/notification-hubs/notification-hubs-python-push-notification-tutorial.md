---
title: Hur du använder Meddelandehubbar med Python
description: Lär dig hur du använder Azure Notification Hubs från en Python-serverdel.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 5640dd4a-a91e-4aa0-a833-93615bde49b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: python
ms.devlang: php
ms.topic: article
ms.author: jowargo
ms.date: 01/04/2019
ms.openlocfilehash: 43a691ff9025cdb39786f965be6a2fca1b33bd3d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57883584"
---
# <a name="how-to-use-notification-hubs-from-python"></a>Hur du använder Notification Hubs från Python

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Du kan komma åt alla funktioner i Meddelandehubbar från en Java/PHP/Python/Ruby-serverdel med Notification Hub REST-gränssnittet enligt beskrivningen i MSDN-artikeln [Notification Hubs REST API: er](https://msdn.microsoft.com/library/dn223264.aspx).

> [!NOTE]
> Detta är en referensimplementering för exemplet för att implementera meddelande skickar i Python och är inte stöds officiellt meddelanden Hub Python SDK. Exemplet har skapats med hjälp av Python 3.4.

Den här artikeln visar hur du:

- Skapa en REST-klient för Notification Hubs-funktioner i Python.
- Skicka meddelanden med hjälp av Python-gränssnitt för Notification Hub REST-API: er.
- Få en dump av HTTP-REST-begäran/svar för felsökning/educational ändamål.

Du kan följa den [komma igång-självstudiekurs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) för din mobil plattform, implementera backend-delen i Python.

> [!NOTE]
> Omfattningen av exemplet begränsas endast för att skicka meddelanden och gör den alla registreringshantering.

## <a name="client-interface"></a>Klientgränssnitt

Viktigaste användargränssnittet kan tillhandahålla samma metoder som är tillgängliga i den [.NET Notification Hubs SDK](https://msdn.microsoft.com/library/jj933431.aspx). Det här gränssnittet kan du direkt översätta alla självstudier och exempel som är för närvarande tillgängligt på den här platsen och som tillförts av communityn på internet.

Du kan hitta all kod som är tillgängliga i den [Python REST wrapper-exempel].

Till exempel vill skapa en klient:

```python
isDebug = True
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Skicka ett popup-meddelande för Windows:

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

## <a name="implementation"></a>Implementering

Om du inte redan gjort det, följ den [Självstudier för att komma igång] upp till den senaste avsnitt där du måste implementera backend-server.

All information du implementerar en fullständig REST-omslutning kan hittas på [MSDN](https://msdn.microsoft.com/library/dn530746.aspx). Det här avsnittet beskrivs Python-implementeringen av de huvudsakliga steg som krävs för att få åtkomst till Notification Hubs REST-slutpunkter och skicka meddelanden

1. Parsa anslutningssträngen
2. Generera autentiseringstoken
3. Skicka ett meddelande med hjälp av HTTP REST API

### <a name="parse-the-connection-string"></a>Parsa anslutningssträngen

Här är den viktigaste klass som implementerar klienten, vars konstruktor Parsar anslutningssträngen:

```python
class NotificationHub:
    API_VERSION = "?api-version=2013-10"
    DEBUG_SEND = "&test"

    def __init__(self, connection_string=None, hub_name=None, debug=0):
        self.HubName = hub_name
        self.Debug = debug

        # Parse connection string
        parts = connection_string.split(';')
        if len(parts) != 3:
            raise Exception("Invalid ConnectionString.")

        for part in parts:
            if part.startswith('Endpoint'):
                self.Endpoint = 'https' + part[11:]
            if part.startswith('SharedAccessKeyName'):
                self.SasKeyName = part[20:]
            if part.startswith('SharedAccessKey'):
                self.SasKeyValue = part[16:]
```

### <a name="create-security-token"></a>Skapa säkerhetstoken

Information om säkerhet token skapas finns [här](https://msdn.microsoft.com/library/dn495627.aspx).
Lägga till följande metoder för att den `NotificationHub` klassen för att skapa en token baserat på URI: N för den aktuella begäran och de autentiseringsuppgifter som extraheras från anslutningssträngen.

```python
@staticmethod
def get_expiry():
    # By default returns an expiration of 5 minutes (=300 seconds) from now
    return int(round(time.time() + 300))

@staticmethod
def encode_base64(data):
    return base64.b64encode(data)

def sign_string(self, to_sign):
    key = self.SasKeyValue.encode('utf-8')
    to_sign = to_sign.encode('utf-8')
    signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
    digest = signed_hmac_sha256.digest()
    encoded_digest = self.encode_base64(digest)
    return encoded_digest

def generate_sas_token(self):
    target_uri = self.Endpoint + self.HubName
    my_uri = urllib.parse.quote(target_uri, '').lower()
    expiry = str(self.get_expiry())
    to_sign = my_uri + '\n' + expiry
    signature = urllib.parse.quote(self.sign_string(to_sign))
    auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
    sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
    return sas_token
```

### <a name="send-a-notification-using-http-rest-api"></a>Skicka ett meddelande med hjälp av HTTP REST API

Första bör du låta Använd definierar en klass som representerar ett meddelande.

```python
class Notification:
    def __init__(self, notification_format=None, payload=None, debug=0):
        valid_formats = ['template', 'apple', 'fcm', 'windows', 'windowsphone', "adm", "baidu"]
        if not any(x in notification_format for x in valid_formats):
            raise Exception(
                "Invalid Notification format. " +
                "Must be one of the following - 'template', 'apple', 'fcm', 'windows', 'windowsphone', 'adm', 'baidu'")

        self.format = notification_format
        self.payload = payload

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        self.headers = None
```

Den här klassen är en behållare för en systemspecifika meddelanden text eller en uppsättning av egenskaperna hos ett mall-meddelande, en uppsättning rubriker, som innehåller format (interna plattform eller mall) och plattformsspecifika egenskaper (till exempel Apple förfalloegenskapen och WNS rubriker).

Referera till den [dokumentation för Notification Hubs REST API: er](https://msdn.microsoft.com/library/dn495827.aspx) och specifika meddelande plattformar format för alla tillgängliga alternativ.

Med den här klassen skriva skicka Meddelandemetoder inuti den `NotificationHub` klass.

```python
def make_http_request(self, url, payload, headers):
    parsed_url = urllib.parse.urlparse(url)
    connection = http.client.HTTPSConnection(parsed_url.hostname, parsed_url.port)

    if self.Debug > 0:
        connection.set_debuglevel(self.Debug)
        # adding this querystring parameter gets detailed information about the PNS send notification outcome
        url += self.DEBUG_SEND
        print("--- REQUEST ---")
        print("URI: " + url)
        print("Headers: " + json.dumps(headers, sort_keys=True, indent=4, separators=(' ', ': ')))
        print("--- END REQUEST ---\n")

    connection.request('POST', url, payload, headers)
    response = connection.getresponse()

    if self.Debug > 0:
        # print out detailed response information for debugging purpose
        print("\n\n--- RESPONSE ---")
        print(str(response.status) + " " + response.reason)
        print(response.msg)
        print(response.read())
        print("--- END RESPONSE ---")

    elif response.status != 201:
        # Successful outcome of send message is HTTP 201 - Created
        raise Exception(
            "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

    connection.close()

def send_notification(self, notification, tag_or_tag_expression=None):
    url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

    json_platforms = ['template', 'apple', 'fcm', 'adm', 'baidu']

    if any(x in notification.format for x in json_platforms):
        content_type = "application/json"
        payload_to_send = json.dumps(notification.payload)
    else:
        content_type = "application/xml"
        payload_to_send = notification.payload

    headers = {
        'Content-type': content_type,
        'Authorization': self.generate_sas_token(),
        'ServiceBusNotification-Format': notification.format
    }

    if isinstance(tag_or_tag_expression, set):
        tag_list = ' || '.join(tag_or_tag_expression)
    else:
        tag_list = tag_or_tag_expression

    # add the tags/tag expressions to the headers collection
    if tag_list != "":
        headers.update({'ServiceBusNotification-Tags': tag_list})

    # add any custom headers to the headers collection that the user may have added
    if notification.headers is not None:
        headers.update(notification.headers)

    self.make_http_request(url, payload_to_send, headers)

def send_apple_notification(self, payload, tags=""):
    nh = Notification("apple", payload)
    self.send_notification(nh, tags)

def send_fcm_notification(self, payload, tags=""):
    nh = Notification("fcm", payload)
    self.send_notification(nh, tags)

def send_adm_notification(self, payload, tags=""):
    nh = Notification("adm", payload)
    self.send_notification(nh, tags)

def send_baidu_notification(self, payload, tags=""):
    nh = Notification("baidu", payload)
    self.send_notification(nh, tags)

def send_mpns_notification(self, payload, tags=""):
    nh = Notification("windowsphone", payload)

    if "<wp:Toast>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'toast', 'X-NotificationClass': '2'}
    elif "<wp:Tile>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'tile', 'X-NotificationClass': '1'}

    self.send_notification(nh, tags)

def send_windows_notification(self, payload, tags=""):
    nh = Notification("windows", payload)

    if "<toast>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/toast'}
    elif "<tile>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/tile'}
    elif "<badge>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/badge'}

    self.send_notification(nh, tags)

def send_template_notification(self, properties, tags=""):
    nh = Notification("template", properties)
    self.send_notification(nh, tags)
```

Dessa metoder kan du skicka en HTTP POST-begäran till /messages slutpunkten för din meddelandehubb, med rätt brödtext och rubriker för att skicka meddelandet.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Använda debug-egenskapen för att aktivera detaljerad loggning

Aktivera felsökning egenskapen vid initiering av Meddelandehubben skriver ut detaljerad loggningsinformation om HTTP-begäran och svaret dump samt detaljerade meddelandet skicka resultatet.
Den [Notification Hubs TestSend egenskapen](https://docs.microsoft.com/previous-versions/azure/reference/dn495827(v=azure.100)) returnerar detaljerad information om skicka meddelanderesultat.
Använda det – initiera med följande kod:

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Begäran för Notification Hub skicka HTTP-URL: en hämtar läggas till med en frågesträng för ”test” som ett resultat.

## <a name="complete-tutorial"></a>I självstudiekursen

Du kan nu slutföra Kom igång-självstudien genom att skicka meddelandet från en Python-serverdel.

Initiera Notification Hubs-klienten (Ersätt strängen och hub anslutningsnamn som finns beskrivet i de [Självstudier för att komma igång]):

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName")
```

Lägg sedan till skicka koden beroende på din mobila målplattform. Det här exemplet lägger också till kategoriseringen metoder för att aktivera skicka meddelanden baserat på plattform, till exempel send_windows_notification för windows. send_apple_notification (för apple) osv.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store och Windows Phone 8.1 (utan Silverlight)

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 och 8.1 Silverlight

```python
hub.send_mpns_notification(toast)
```

### <a name="ios"></a>iOS

```python
alert_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_apple_notification(alert_payload)
```

### <a name="android"></a>Android

```python
fcm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_fcm_notification(fcm_payload)
```

### <a name="kindle-fire"></a>Kindle Fire

```python
adm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_adm_notification(adm_payload)
```

### <a name="baidu"></a>Baidu

```python
baidu_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_baidu_notification(baidu_payload)
```

Köra Python-kod ska generera ett meddelande som visas på din målenhet.

## <a name="examples"></a>Exempel

### <a name="enabling-the-debug-property"></a>Aktivera den `debug` egenskapen

När du aktiverar flaggan Felsökning vid initiering av NotificationHub kan se du detaljerade HTTP-begäran och svaret dump samt NotificationOutcome som liknar följande där du kan förstå vilka HTTP-huvuden har skickats i begäran och vilka HTTP-svaret var togs emot från Meddelandehubben:

![][1]

Du ser detaljerad Notification Hub resultatet till exempel.

- När meddelandet har skickas till Push-meddelandetjänst.
    ```xml
    <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
    ```
- Om det fanns inga mål hittades för alla push-meddelande, sedan kommer du sannolikt att se följande utdata som svaret (vilket betyder att det fanns registreringar hittades för att leverera meddelandet förmodligen eftersom registreringarna hade några fel taggar)
    ```xml
    '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="https://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'
    ```

### <a name="broadcast-toast-notification-to-windows"></a>Skicka popup-meddelande till Windows

Observera sidhuvuden som skickas ut när du skickar ett broadcast popup-meddelande till Windows-klienten.

```python
hub.send_windows_notification(wns_payload)
```

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Skicka meddelande att ange en tagg (eller etikettuttrycket)

Lägg märke till de taggar HTTP-huvud som läggs till i HTTP-begäran (i exemplet nedan visas meddelandet skickas endast för registreringar med nyttolast i ”sport”)

```python
hub.send_windows_notification(wns_payload, "sports")
```

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>Skicka meddelande som anger flera taggar

Observera hur taggar HTTP-huvudet ändras när flera taggar skickas.

```python
tags = {'sports', 'politics'}
hub.send_windows_notification(wns_payload, tags)
```

![][4]

### <a name="templated-notification"></a>Mallbaserad meddelande

Observera att ändras i formatet HTTP-huvud och nyttolast brödtext skickas som en del av HTTP-begärandetexten:

**Klientsidan - registrerade mall:**

```python
var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
```

**Serversidan - skicka nyttolasten:**

```python
template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
hub.send_template_notification(template_payload)
```

![][5]

## <a name="next-steps"></a>Nästa steg

Den här artikeln visade hur du skapar en Python-REST-klient för Meddelandehubbar. Härifrån kan du:

- Ladda ned hela [Python REST wrapper-exempel], som innehåller all kod i den här artikeln.
- Lära dig mer om Notification Hubs taggningsfunktion i den [Icke-bakåtkompatibel News-självstudien]
- Lära dig mer om Notification Hubs mallfunktionen i den [lokalisera nyheter självstudien]

<!-- URLs -->
[Python REST wrapper-exempel]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Självstudier för att komma igång]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Icke-bakåtkompatibel News-självstudien]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Lokalisera nyheter självstudien]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
