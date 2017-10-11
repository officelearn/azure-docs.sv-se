---
title: "Hur du använder Notification Hubs med PHP"
description: "Lär dig hur du använder Azure Notification Hubs från PHP backend."
services: notification-hubs
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 0156f994-96d0-4878-b07b-49b7be4fd856
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 06/07/2016
ms.author: yuaxu
ms.openlocfilehash: c27b6308ff528224a0398e0ff40537db05417bb0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-use-notification-hubs-from-php"></a>Hur du använder Notification Hubs från PHP
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Du kan använda alla funktioner i Notification Hubs från en Java/PHP/Ruby serverdel med Notification Hub REST-gränssnitt som beskrivs i avsnittet MSDN [Notification Hub REST API: er](http://msdn.microsoft.com/library/dn223264.aspx).

I det här avsnittet visar vi hur du:

* Skapa ett REST-klient för Notification Hubs funktionerna i PHP;
* Följ den [Get igång-kursen](notification-hubs-ios-apple-push-notification-apns-get-started.md) för din mobila plattform väljer implementera backend-delen i PHP.

## <a name="client-interface"></a>Klientgränssnitt
Det huvudsakliga klientgränssnittet kan ge samma metoder som är tillgängliga i den [.NET Notification Hubs SDK](http://msdn.microsoft.com/library/jj933431.aspx), detta kan du direkt översätta alla självstudier och exempel som är tillgängliga på den här platsen och tillhandahålls av gemenskapen på internet.

Du hittar kod som är tillgängliga i den [PHP REST wrapper exempel].

Till exempel att skapa en klient:

    $hub = new NotificationHub("connection string", "hubname");    

Att skicka en iOS interna avisering:

    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);

## <a name="implementation"></a>Implementering
Om du inte redan gjort det, Följ våra [Get igång-kursen] upp till den senaste avsnitt där du måste implementera serverdel.
Om du vill att du kan också använda koden från den [PHP REST wrapper exempel] och gå direkt till den [slutföra kursen](#complete-tutorial) avsnitt.

All information du implementerar en fullständig REST-omslutning finns på [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). I det här avsnittet beskriver vi PHP-implementeringen av huvudsakliga steg för att komma åt Notification Hub REST-slutpunkter:

1. Parsa anslutningssträngen
2. Generera autentiseringstoken
3. Utföra HTTP-anrop

### <a name="parse-the-connection-string"></a>Parsa anslutningssträngen
Här är den viktigaste klass som implementerar klienten, vars konstruktor som Parsar anslutningssträngen:

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


### <a name="create-security-token"></a>Skapa säkerhetstoken
Information om säkerhet token skapa finns [här](http://msdn.microsoft.com/library/dn495627.aspx).
Följande metod måste läggas till i **NotificationHub** klassen för att skapa token baserat på URI: N för den aktuella begäranden och de autentiseringsuppgifter som har extraherats från anslutningssträngen.

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

### <a name="send-a-notification"></a>Skicka ett meddelande
Låt oss först definiera en klass som representerar ett meddelande.

    class Notification {
        public $format;
        public $payload;

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;

        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "gcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }

            $this->format = $format;
            $this->payload = $payload;
        }
    }

Den här klassen är en behållare för en intern notification text eller en uppsättning egenskaperna i fall av ett meddelande om mallen och en uppsättning huvuden som innehåller plattformsspecifika egenskaper (till exempel Apple giltighetstid egenskap och WNS och format (intern plattform eller mall) rubriker).

Mer information finns i [Notification Hub REST API: er dokumentationen](http://msdn.microsoft.com/library/dn495827.aspx) och specifika meddelanden plattformar format för alla tillgängliga alternativ.

Tillsammans med den här klassen, vi kan nu skriva skicka Meddelandemetoder inuti den **NotificationHub** klass.

    public function sendNotification($notification, $tagsOrTagExpression="") {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "gcm"])) {
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
            throw new Exception('Error sending notificaiton: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 

Ovanstående metoder skicka en HTTP POST-begäran till slutpunkten /messages för meddelandehubben med rätt brödtext och rubriker för att skicka meddelandet.

## <a name="complete-tutorial"></a>Slutför guiden
Du kan nu slutföra kursen Kom igång genom att skicka meddelandet från PHP backend.

Initiera Notification Hubs-klienten (Ersätt strängen och hubb anslutningsnamn som finns beskrivet i den [Get igång-kursen]):

    $hub = new NotificationHub("connection string", "hubname");    

Lägg sedan till skicka kod beroende på din mobila målplattform.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store och Windows Phone 8.1 (utan Silverlight)
    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);

### <a name="ios"></a>iOS
    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);

### <a name="android"></a>Android
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("gcm", $message);
    $hub->sendNotification($notification, null);

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 och 8.1 Silverlight
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


### <a name="kindle-fire"></a>Kindle Fire
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);

Köra koden PHP ska producera nu ett meddelande som visas på målenheten.

## <a name="next-steps"></a>Nästa steg
Vi visar hur du skapar en enkel RESTEN av Java-klient för Meddelandehubbar i det här avsnittet. Härifrån kan du:

* Hämta fullständigt [PHP REST wrapper exempel], som innehåller alla koden ovan.
* Fortsätta lära dig mer om Notification Hubs taggning funktionen i [bryta nyheter självstudiekursen]
* Lär dig mer om push-meddelanden till enskilda användare i [meddela användare självstudiekursen]

Mer information finns också i [PHP Developer Center](/develop/php/).

[PHP REST wrapper exempel]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Get igång-kursen]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/

