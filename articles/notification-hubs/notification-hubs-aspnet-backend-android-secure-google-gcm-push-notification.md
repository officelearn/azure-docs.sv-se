---
title: Skicka säkra push-meddelanden med Azure Notification Hubs
description: Läs om hur du skickar säkra push-meddelanden till en Android-app från Azure. Kodexempel skrivna i Java och C#.
documentationcenter: android
keywords: push-meddelande,push-meddelanden,push-meddelanden,android push-meddelanden
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: daf3de1c-f6a9-43c4-8165-a76bfaa70893
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 419a9f9b5ce698c7516edd55856cbea9891ba029
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212180"
---
# <a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Skicka säkra push-meddelanden med Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Översikt

> [!IMPORTANT]
> Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Med push-meddelandestöd i Microsoft Azure kan du komma åt en lättanvänd, infrastruktur för utskalade push-meddelanden med flera plattformar, vilket förenklar implementeringen av push-meddelanden för både konsument- och företagsprogram för mobila plattformar.

På grund av regel- eller säkerhetsbegränsningar kan ibland ett program vilja inkludera något i meddelandet som inte kan överföras via standardinfrastrukturen för push-meddelanden. Den här självstudien beskriver hur du uppnår samma upplevelse genom att skicka känslig information via en säker, autentiserat anslutning mellan klientens Android-enhet och appens backend.

På en hög nivå är flödet följande:

1. Appens baksida:
   * Lagrar säker nyttolast i backend-databas.
   * Skickar ID:t för detta meddelande till Android-enheten (ingen säker information skickas).
2. Appen på enheten, när du tar emot meddelandet:
   * Android-enheten kontaktar backend-begäran om säker nyttolast.
   * Appen kan visa nyttolasten som ett meddelande på enheten.

Det är viktigt att notera att i föregående flöde (och i den här självstudien) antas det att enheten lagrar en autentiseringstoken i den lokala lagringen, när användaren loggar in. Den här metoden garanterar en sömlös upplevelse, eftersom enheten kan hämta meddelandets säkra nyttolast med den här token. Om ditt program inte lagrar autentiseringstoken på enheten, eller om dessa token kan komma att gälla, bör enhetsappen när du tar emot push-meddelandet visa ett allmänt meddelande som uppmanar användaren att starta appen. Appen autentiserar sedan användaren och visar aviseringsnyttolasten.

Den här självstudien visar hur du skickar säkra push-meddelanden. Den bygger på [notify users](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) handledning, så du bör slutföra stegen i den handledningen först om du inte redan har.

> [!NOTE]
> Den här självstudien förutsätter att du har skapat och konfigurerat meddelandehubben enligt beskrivningen i [Komma igång med Meddelandehubbar (Android).](notification-hubs-android-push-notification-google-gcm-get-started.md)

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Ändra Android-projektet

Nu när du har ändrat appens serverdel för att bara skicka *ID:t* för ett push-meddelande måste du ändra din Android-app för att hantera meddelandet och ringa tillbaka din serverdel för att hämta det säkra meddelandet som ska visas.
För att uppnå detta mål måste du se till att din Android-app vet hur du autentiserar sig med din backend när den tar emot push-meddelandena.

Ändra nu *inloggningsflödet* för att spara värdet för autentiseringshuvudet i de delade inställningarna för din app. Liknande mekanismer kan användas för att lagra alla autentiseringstoken (till exempel OAuth-token) som appen måste använda utan att användaren behöver autentiseringsuppgifter.

1. I ditt Android-appprojekt lägger du till `MainActivity` följande konstanter högst upp i klassen:

    ```java
    public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
    public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
    ```
2. Uppdatera metoden `MainActivity` så att `getAuthorizationHeader()` den innehåller följande kod fortfarande:

    ```java
    private String getAuthorizationHeader() throws UnsupportedEncodingException {
        EditText username = (EditText) findViewById(R.id.usernameText);
        EditText password = (EditText) findViewById(R.id.passwordText);
        String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
        basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

        SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

        return basicAuthHeader;
    }
    ```
3. Lägg till `import` följande satser `MainActivity` högst upp i filen:

    ```java
    import android.content.SharedPreferences;
    ```

Byt nu hanteraren som anropas när meddelandet tas emot.

1. I `MyHandler` klassändringen `OnReceive()` ändras metoden så att den innehåller:

    ```java
    public void onReceive(Context context, Bundle bundle) {
        ctx = context;
        String secureMessageId = bundle.getString("secureId");
        retrieveNotification(secureMessageId);
    }
    ```
2. Lägg sedan `retrieveNotification()` till metoden och `{back-end endpoint}` ersätt platshållaren med den slutpunkt som erhålls när du distribuerar backend:Then add the method, replacing the placeholder with the back-endpoint obtained while deploying your back-end:

    ```java
    private void retrieveNotification(final String secureMessageId) {
        SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {
                    HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                    request.addHeader("Authorization", "Basic "+authorizationHeader);
                    HttpResponse response = new DefaultHttpClient().execute(request);
                    if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                        Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                        throw new RuntimeException("Error retrieving secure notification");
                    }
                    String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                    JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                    sendNotification(secureNotification.getString("Payload"));
                } catch (Exception e) {
                    Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                    return e;
                }
                return null;
            }
        }.execute(null, null, null);
    }
    ```

Den här metoden anropar appens backend för att hämta meddelandeinnehållet med hjälp av de autentiseringsuppgifter som lagras i de delade inställningarna och visar det som ett vanligt meddelande. Meddelandet ser ut till appanvändaren precis som alla andra push-meddelanden.

Det är bättre att hantera fall av saknade autentisering header egendom eller avslag av backend. Den specifika hanteringen av dessa fall beror främst på din målgruppsupplevelse. Ett alternativ är att visa ett meddelande med en allmän uppmaning för användaren att autentisera för att hämta det faktiska meddelandet.

## <a name="run-the-application"></a>Kör programmet

Så här kör du programmet:

1. Kontrollera att **AppBackend** distribueras i Azure. Om du använder Visual Studio kör du **AppBackend** Web API-programmet. En ASP.NET webbsida visas.
2. I Eclipse kör du appen på en fysisk Android-enhet eller emulatorn.
3. Ange ett användarnamn och lösenord i Android-appens användargränssnitt. Dessa kan vara vilken sträng som helst, men de måste vara samma värde.
4. Klicka på Logga in **i**Android-appgränssnittet . Klicka sedan på **Skicka push**.
