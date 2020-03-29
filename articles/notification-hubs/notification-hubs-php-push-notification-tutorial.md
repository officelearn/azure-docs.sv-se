---
title: Så här använder du Azure Notification Hubs med PHP
description: Läs om hur du använder Azure Notification Hubs från en PHP-backend.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0156f994-96d0-4878-b07b-49b7be4fd856
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 9a77a9d9c8b2d71197089f66d81e07d56c780e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263854"
---
# <a name="how-to-use-notification-hubs-from-php"></a>Så här använder du meddelandehubbar från PHP

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Du kan komma åt alla Notification Hubs-funktioner från en Java/PHP/Ruby-backend med hjälp av GRÄNSSNITTET Notification Hub REST enligt beskrivningen i [MSDN-avsnittet Notification Hubs REST API:er](https://msdn.microsoft.com/library/dn223264.aspx).

I det här avsnittet visar vi hur du:

* Skapa en REST-klient för Notification Hubs-funktioner i PHP;
* Följ [guiden Kom igång](notification-hubs-ios-apple-push-notification-apns-get-started.md) för din mobila plattform val, genomföra backend del i PHP.

## <a name="client-interface"></a>Klientgränssnitt

Huvudklientgränssnittet kan tillhandahålla samma metoder som finns i [.NET Notification Hubs SDK](https://msdn.microsoft.com/library/jj933431.aspx), som gör att du direkt kan översätta alla självstudier och prover som för närvarande finns tillgängliga på denna webbplats, och bidragit med communityn på Internet.

Du kan hitta all kod som finns i [PHP REST omslag provet].

Så här skapar du till exempel en klient:

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

Så här skickar du ett inbyggt iOS-meddelande:

    ```php
    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);
    ```

## <a name="implementation"></a>Implementering

Om du inte redan har gjort det följer du [självstudien Kom igång] fram till det sista avsnittet där du måste implementera backend.
Dessutom, om du vill kan du använda koden från [PHP REST omslag provet] och gå direkt till [Komplettera handledningen](#complete-tutorial) avsnittet.

Alla detaljer för att genomföra en fullständig REST omslag finns på [MSDN](https://msdn.microsoft.com/library/dn530746.aspx). I det här avsnittet beskriver vi PHP-implementeringen av de viktigaste stegen som krävs för att komma åt E-slutpunkterna För meddelandehubbar REST:

1. Parsa anslutningssträngen
2. Generera auktoriseringstoken
3. Utföra HTTP-anropet

### <a name="parse-the-connection-string"></a>Parsa anslutningssträngen

Här är huvudklassen som implementerar klienten, vars konstruktor som tolkar anslutningssträngen:

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

Mer information om hur du [skapar en SAS-säkerhetstoken](https://docs.microsoft.com/previous-versions/azure/reference/dn495627(v=azure.100)#create-sas-security-token)finns i Azure-dokumentationen .

Lägg `generateSasToken` till metoden `NotificationHub` i klassen för att skapa token baserat på URI för den aktuella begäran och autentiseringsuppgifterna som extraherats från anslutningssträngen.

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

Den här klassen är en behållare för ett inbyggt meddelandeorgan, eller en uppsättning egenskaper i ett mallmeddelande och en uppsättning rubriker, som innehåller format (inbyggd plattform eller mall) och plattformsspecifika egenskaper (som Apples förfallodeegenskap och WNS rubriker).

Se [dokumentationen för REST-API:er för meddelandehubbar](https://msdn.microsoft.com/library/dn495827.aspx) och de specifika meddelandeplattformsformaten för alla tillgängliga alternativ.

Beväpnad med denna klass, kan vi nu skriva `NotificationHub` skicka anmälan metoder inne i klassen:

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

Ovanstående metoder skickar en HTTP `/messages` POST-begäran till slutpunkten för meddelandehubben, med rätt brödtext och rubriker för att skicka meddelandet.

## <a name="complete-the-tutorial"></a><a name="complete-tutorial"></a>Slutför självstudien

Nu kan du slutföra guiden Kom igång genom att skicka meddelandet från en PHP-backend.

Initiera din Notification Hubs-klient (ersätt anslutningssträngen och hubbnamnet enligt anvisningarna i [självstudien Kom igång):]

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

Lägg sedan till skickakoden beroende på din målmobila plattform.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store och Windows Phone 8.1 (ej Silverlight)

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

### <a name="kindle-fire"></a>Kindle Brand

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);
    ```

Köra din PHP-kod bör producera nu ett meddelande som visas på din målenhet.

## <a name="next-steps"></a>Efterföljande moment

I det här avsnittet visade vi hur du skapar en enkel Java REST-klient för Notification Hubs. Här kan göra du följande:

* Ladda ner hela [PHP REST wrapper provet], som innehåller all kod ovan.
* Fortsätt att lära dig om taggningsfunktionen för notification hubs i [Breaking News tutorial]
* Läs mer om hur du pushar aviseringar till enskilda användare i [Meddela användarnas självstudiekurs]

Mer information finns i [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[PHP REST omslag prov]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Självstudier för att komma igång]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
