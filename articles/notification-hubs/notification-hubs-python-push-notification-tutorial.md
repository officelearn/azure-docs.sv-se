---
title: Så här använder du meddelandehubbar med Python
description: Lär dig hur du använder Azure Notification Hubs från ett Python-program.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 5640dd4a-a91e-4aa0-a833-93615bde49b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: python
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 1ff8c382813654b1dee38a99bf2cc0ca67afbedd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76313835"
---
# <a name="how-to-use-notification-hubs-from-python"></a>Så här använder du meddelandehubbar från Python

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Du kan komma åt alla Notification Hubs-funktioner från en Java/PHP/Python/Ruby-backend med hjälp av REST-gränssnittet Notification Hub enligt beskrivningen i [MSDN-artikeln Notification Hubs REST API:er](https://msdn.microsoft.com/library/dn223264.aspx).

> [!NOTE]
> Detta är ett exempel referensimplementering för att implementera meddelandet skickas i Python och är inte den officiellt stöds Notifications Hub Python SDK. Exemplet skapades med Python 3.4.

Den här artikeln visar hur du:

- Skapa en REST-klient för Notification Hubs-funktioner i Python.
- Skicka meddelanden med Python-gränssnittet till API:erna för REST-API:er för meddelandehubben.
- Få en dump av HTTP REST-begäran/svar för felsökning/utbildningssyfte.

Du kan följa [självstudien Kom igång](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) för din mobila plattform som du väljer och implementera backend-delen i Python.

> [!NOTE]
> Exemplets omfattning är endast begränsad till att skicka meddelanden och det gör ingen registreringshantering.

## <a name="client-interface"></a>Klientgränssnitt

Huvudklientgränssnittet kan tillhandahålla samma metoder som är tillgängliga i [.NET Notification Hubs SDK](https://msdn.microsoft.com/library/jj933431.aspx). Detta gränssnitt kan du direkt översätta alla tutorials och prover som för närvarande finns på denna webbplats, och bidragit med samhället på Internet.

Du hittar all kod som finns i [python-omslaget.]

Så här skapar du till exempel en klient:

```python
isDebug = True
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Så här skickar du ett popup-meddelande från Windows:

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

## <a name="implementation"></a>Implementering

Om du inte redan gjorde det följer [du självstudien Kom igång] fram till det sista avsnittet där du måste implementera backend-avsnittet.

Alla detaljer för att genomföra en fullständig REST omslag finns på [MSDN](https://msdn.microsoft.com/library/dn530746.aspx). I det här avsnittet beskrivs Python-implementeringen av de viktigaste stegen som krävs för att komma åt E-slutpunkter för Notification Hubs REST och skicka meddelanden

1. Parsa anslutningssträngen
2. Generera auktoriseringstoken
3. Skicka ett meddelande med HTTP REST API

### <a name="parse-the-connection-string"></a>Parsa anslutningssträngen

Här är huvudklassen som implementerar klienten, vars konstruktor tolkar anslutningssträngen:

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

Informationen om skapandet av säkerhetstoken finns [här](https://msdn.microsoft.com/library/dn495627.aspx).
Lägg till följande `NotificationHub` metoder i klassen för att skapa token baserat på URI för den aktuella begäran och autentiseringsuppgifterna som extraherats från anslutningssträngen.

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

### <a name="send-a-notification-using-http-rest-api"></a>Skicka ett meddelande med HTTP REST API

Låt först använda definiera en klass som representerar ett meddelande.

```python
class Notification:
    def __init__(self, notification_format=None, payload=None, debug=0):
        valid_formats = ['template', 'apple', 'fcm',
                         'windows', 'windowsphone', "adm", "baidu"]
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

Den här klassen är en behållare för ett inbyggt meddelandetext eller en uppsättning egenskaper för ett mallmeddelande, en uppsättning rubriker som innehåller format (inbyggd plattform eller mall) och plattformsspecifika egenskaper (till exempel Apples förfallodeegenskap och WNS-huvuden).

Se [dokumentationen för REST-API:er för meddelandehubbar](https://msdn.microsoft.com/library/dn495827.aspx) och de specifika meddelandeplattformsformaten för alla tillgängliga alternativ.

Nu med den här klassen skriver du `NotificationHub` skicka meddelandemetoderna inuti klassen.

```python
def make_http_request(self, url, payload, headers):
    parsed_url = urllib.parse.urlparse(url)
    connection = http.client.HTTPSConnection(
        parsed_url.hostname, parsed_url.port)

    if self.Debug > 0:
        connection.set_debuglevel(self.Debug)
        # adding this querystring parameter gets detailed information about the PNS send notification outcome
        url += self.DEBUG_SEND
        print("--- REQUEST ---")
        print("URI: " + url)
        print("Headers: " + json.dumps(headers, sort_keys=True,
                                       indent=4, separators=(' ', ': ')))
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
        nh.headers = {'X-WindowsPhone-Target': 'toast',
                      'X-NotificationClass': '2'}
    elif "<wp:Tile>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'tile',
                      'X-NotificationClass': '1'}

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

Dessa metoder skickar en HTTP POST-begäran till /messages-slutpunkten för meddelandehubben, med rätt brödtext och rubriker för att skicka meddelandet.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Använda felsökningsegenskapen för att aktivera detaljerad loggning

Aktivera felsökningsegenskapen när meddelandehubben initieras skrivs detaljerad loggningsinformation om HTTP-begäran och svarsdump samt detaljerade meddelandemeddelandemeddelandemeddelande.
Egenskapen [Notification Hubs TestSend returnerar](https://docs.microsoft.com/previous-versions/azure/reference/dn495827(v=azure.100)) detaljerad information om resultatet av meddelandesändningen.
Så här använder du den - initiera med följande kod:

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

HTTP-URL:en för meddelandehã¥ssã¥r i uppdrag att lägga till en "testfrågesträng" som följd.

## <a name="complete-the-tutorial"></a><a name="complete-tutorial"></a>Slutför självstudien

Nu kan du slutföra självstudien Kom igång genom att skicka meddelandet från en Python-backend.

Initiera din Notification Hubs-klient (ersätt anslutningssträngen och hubbnamnet enligt anvisningarna i [självstudien Kom igång):]

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName")
```

Lägg sedan till skickakoden beroende på din målmobila plattform. Det här exemplet lägger också till metoder på högre nivå för att möjliggöra att skicka meddelanden baserat på plattformen, till exempel send_windows_notification för fönster. send_apple_notification (för äpple) etc.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store och Windows Phone 8.1 (ej Silverlight)

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

### <a name="kindle-fire"></a>Kindle Brand

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

Om du kör Python-koden ska det skapas ett meddelande som visas på målenheten.

## <a name="examples"></a>Exempel

### <a name="enabling-the-debug-property"></a>Aktivera egenskapen `debug`

När du aktiverar felsökningsflaggan när du initierar NotificationHub visas detaljerad HTTP-begäran och svarsdump samt NotificationOutcome som du kan förstå vad HTTP-huvuden skickas i begäran och vad HTTP-svar var tas emot från meddelandehubben:

![][1]

Du ser detaljerade Notification Hub resultat till exempel.

- meddelandet har skickats till push-meddelandetjänsten.
    ```xml
    <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
    ```
- Om det inte fanns några mål hittades för någon push anmälan, då är det troligt att du kommer att se följande resultat som svar (vilket tyder på att det inte fanns några registreringar hittades för att leverera anmälan förmodligen eftersom registreringarna hade några inkompatibla taggar)
    ```xml
    '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="https://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'
    ```

### <a name="broadcast-toast-notification-to-windows"></a>Sända popup-meddelande till Windows

Lägg märke till de rubriker som skickas ut när du skickar ett popup-meddelande till Windows-klienten.

```python
hub.send_windows_notification(wns_payload)
```

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Skicka meddelande om att ange en tagg (eller tagguttryck)

Lägg märke till HTTP-huvudet för taggar, som läggs till i HTTP-begäran (i exemplet nedan skickas meddelandet endast till registreringar med nyttolast för sporter)

```python
hub.send_windows_notification(wns_payload, "sports")
```

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>Skicka meddelande om att ange flera taggar

Lägg märke till hur HTTP-huvudet taggar ändras när flera taggar skickas.

```python
tags = {'sports', 'politics'}
hub.send_windows_notification(wns_payload, tags)
```

![][4]

### <a name="templated-notification"></a>Meddelande om mall

Observera att formatet HTTP-huvudet ändras och att nyttolasterns brödtext skickas som en del av http-begäranden:

**Klientsidan - registrerad mall:**

```python
var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
```

**Serversidan - skicka nyttolasten:**

```python
template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
hub.send_template_notification(template_payload)
```

![][5]

## <a name="next-steps"></a>Efterföljande moment

Den här artikeln visade hur du skapar en Python REST-klient för Notification Hubs. Här kan göra du följande:

- Hämta [hela Python REST-omslaget,]som innehåller all kod i den här artikeln.
- Fortsätt lära dig mer om taggningsfunktionen för meddelandehubbar i [självstudien Breaking News]
- Fortsätt lära dig mer om funktionen Mallar för meddelandehubbar i [självstudien Lokalisering av nyheter]

<!-- URLs -->
[Exempel på PYTHON REST-omslag]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Självstudier för att komma igång]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Breaking News handledning]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Lokalisera nyheter handledning]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
