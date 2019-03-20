---
title: Hur du använder Meddelandehubbar med PHP
description: Lär dig hur du använder Azure Notification Hubs från PHP-serverdel.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 0156f994-96d0-4878-b07b-49b7be4fd856
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 054edaf321d90015840fd84e1697fca742fd7e1e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57838603"
---
# <a name="how-to-use-notification-hubs-from-php"></a>Hur du använder Notification Hubs från PHP

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Du kan komma åt alla funktioner i Notification Hubs från Java/PHP/Ruby serverdel med Notification Hub REST-gränssnittet enligt beskrivningen i avsnittet om MSDN [Notification Hubs REST API: er](https://msdn.microsoft.com/library/dn223264.aspx).

I det här avsnittet visar vi hur du:

* Skapa en REST-klient för Notification Hubs-funktioner i PHP;
* Följ den [komma igång-självstudiekurs](notification-hubs-ios-apple-push-notification-apns-get-started.md) för din mobil plattform, implementera backend-delen i PHP.

## <a name="client-interface"></a>Klientgränssnitt

Viktigaste användargränssnittet kan tillhandahålla samma metoder som är tillgängliga i den [.NET Notification Hubs SDK](https://msdn.microsoft.com/library/jj933431.aspx), kan du direkt översätta alla självstudier och exempel som är för närvarande tillgängligt på den här webbplatsen vilket som tillförts av den Community på internet.

Du kan hitta all kod som är tillgängliga i den [Exempel för PHP-REST-omslutning].

Till exempel vill skapa en klient:

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

Så här skickar en iOS systemspecifika meddelanden:

    ```php
    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);
    ```

## <a name="implementation"></a>Implementering

Om du inte redan gjort det, följ den [Självstudier för att komma igång] upp till den senaste avsnitt där du måste implementera serverdelen.
Om du vill att du kan också använda koden från den [Exempel för PHP-REST-omslutning] och gå direkt till den [i självstudiekursen](#complete-tutorial) avsnittet.

All information du implementerar en fullständig REST-omslutning kan hittas på [MSDN](https://msdn.microsoft.com/library/dn530746.aspx). I det här avsnittet beskriver vi PHP-implementeringen av de huvudsakliga steg som krävs för att få åtkomst till Notification Hubs REST-slutpunkter:

1. Parsa anslutningssträngen
2. Generera autentiseringstoken
3. Utföra HTTP-anrop

### <a name="parse-the-connection-string"></a>Parsa anslutningssträngen

Här är den viktigaste klass som implementerar klienten, vars konstruktorn som Parsar anslutningssträngen:

    ```php
    class NotificationHub {
        const API_VERSION = "?api-version=2013-10";

        private $endpoint;
        private $hubPath;
        private $sasKeyName;
        private $sasKeyValue;

        function __construct($connectionString, $hubPath) {
            $this->hubPath = $hubPath;

            $this->parseConnectionString($connectionString);
        }

        private function parseConnectionString($connectionString) {
            $parts = explode(";", $connectionString);
            if (sizeof($parts) != 3) {
                throw new Exception("Error parsing connection string: " . $connectionString);
            }

            foreach ($parts as $part) {
                if (strpos($part, "Endpoint") === 0) {
                    $this->endpoint = "https" . substr($part, 11);
                } else if (strpos($part, "SharedAccessKeyName") === 0) {
                    $this->sasKeyName = substr($part, 20);
                } else if (strpos($part, "SharedAccessKey") === 0) {
                    $this->sasKeyValue = substr($part, 16);
                }
            }
        }
    }
    ```

### <a name="create-a-security-token"></a>Skapa en säkerhetstoken

I Azure-dokumentationen för information om hur du [skapa en SAS Security Token](https://docs.microsoft.com/previous-versions/azure/reference/dn495627(v=azure.100)#create-sas-security-token).

Lägg till den `generateSasToken` metod för att den `NotificationHub` klassen för att skapa en token baserat på URI: N för den aktuella begäran och de autentiseringsuppgifter som extraheras från anslutningssträngen.

    ```php
    private function generateSasToken($uri) {
        $targetUri = strtolower(rawurlencode(strtolower($uri)));

        $expires = time();
        $expiresInMins = 60;
        $expires = $expires + $expiresInMins * 60;
        $toSign = $targetUri . "\n" . $expires;

        $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

        $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                    . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

        return $token;
    }
    ```

### <a name="send-a-notification"></a>Skicka ett meddelande

Låt oss först definiera en klass som representerar ett meddelande.

    ```php
    class Notification {
        public $format;
        public $payload;

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;

        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "fcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }

            $this->format = $format;
            $this->payload = $payload;
        }
    }
    ```

Den här klassen är en behållare för en systemspecifika meddelanden text eller en uppsättning egenskaper i fallet med ett mall-meddelande och en uppsättning rubriker, som innehåller format (interna plattform eller mall) och plattformsspecifika egenskaper (till exempel Apple förfalloegenskapen och WNS rubriker).

Referera till den [dokumentation för Notification Hubs REST API: er](https://msdn.microsoft.com/library/dn495827.aspx) och specifika meddelande plattformar format för alla tillgängliga alternativ.

Som enda verktyg med den här klassen, vi skriva skicka Meddelandemetoder inuti den `NotificationHub` klass:

    ```php
    public function sendNotification($notification, $tagsOrTagExpression="") {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "fcm"])) {
            $contentType = "application/json";
        } else {
            $contentType = "application/xml";
        }

        $token = $this->generateSasToken($uri);

        $headers = [
            'Authorization: '.$token,
            'Content-Type: '.$contentType,
            'ServiceBusNotification-Format: '.$notification->format
        ];

        if ("" !== $tagExpression) {
            $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
        }

        # add headers for other platforms
        if (is_array($notification->headers)) {
            $headers = array_merge($headers, $notification->headers);
        }

        curl_setopt_array($ch, array(
            CURLOPT_POST => TRUE,
            CURLOPT_RETURNTRANSFER => TRUE,
            CURLOPT_SSL_VERIFYPEER => FALSE,
            CURLOPT_HTTPHEADER => $headers,
            CURLOPT_POSTFIELDS => $notification->payload
        ));

        // Send the request
        $response = curl_exec($ch);

        // Check for errors
        if($response === FALSE){
            throw new Exception(curl_error($ch));
        }

        $info = curl_getinfo($ch);

        if ($info['http_code'] <> 201) {
            throw new Exception('Error sending notification: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 
    ```

Metoderna ovan skicka en HTTP POST-begäran till den `/messages` slutpunkten för din meddelandehubb, med rätt brödtext och rubriker för att skicka meddelandet.

## <a name="complete-tutorial"></a>I självstudiekursen

Du kan nu slutföra Kom igång-självstudien genom att skicka meddelandet från en PHP-serverdel.

Initiera Notification Hubs-klienten (Ersätt strängen och hub anslutningsnamn som finns beskrivet i de [Självstudier för att komma igång]):

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

Lägg sedan till skicka koden beroende på din mobila målplattform.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store och Windows Phone 8.1 (utan Silverlight)

    ```php
    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);
    ```

### <a name="ios"></a>iOS

    ```php
    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);
    ```

### <a name="android"></a>Android

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("fcm", $message);
    $hub->sendNotification($notification, null);
    ```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 och 8.1 Silverlight

    ```php
    $toast = '<?xml version="1.0" encoding="utf-8"?>' .
                '<wp:Notification xmlns:wp="WPNotification">' .
                   '<wp:Toast>' .
                        '<wp:Text1>Hello from PHP!</wp:Text1>' .
                   '</wp:Toast> ' .
                '</wp:Notification>';
    $notification = new Notification("windowsphone", $toast);
    $notification->headers[] = 'X-WindowsPhone-Target : toast';
    $notification->headers[] = 'X-NotificationClass : 2';
    $hub->sendNotification($notification, null);
    ```

### <a name="kindle-fire"></a>Kindle Fire

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);
    ```

Kör PHP-kod ska generera nu ett meddelande som visas på din målenhet.

## <a name="next-steps"></a>Nästa steg

I det här avsnittet visade vi hur du skapar en enkel Java REST-klient för Meddelandehubbar. Härifrån kan du:

* Ladda ned hela [Exempel för PHP-REST-omslutning], som innehåller all kod som ovan.
* Lära dig mer om Notification Hubs taggningsfunktion i [större nyheter självstudien]
* Lär dig mer om push-meddelanden till enskilda användare i [meddela användare självstudien]

Mer information finns också i [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[Exempel för PHP-REST-omslutning]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Självstudier för att komma igång]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
