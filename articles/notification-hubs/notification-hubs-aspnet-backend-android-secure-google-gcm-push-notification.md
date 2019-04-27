---
title: Skicka säkra Push-meddelanden med Azure Notification Hubs
description: Lär dig hur du skickar säker push-meddelanden till en Android-app från Azure. Kodexempel som skrivits i Java och C#.
documentationcenter: android
keywords: push-meddelande, push-meddelanden, push-meddelanden, android-push-meddelanden
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
ms.assetid: daf3de1c-f6a9-43c4-8165-a76bfaa70893
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 27536b0a3d7e0858a5660b4c7b33cb6679b5fbf1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60874572"
---
# <a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Skicka säkra Push-meddelanden med Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Översikt

> [!IMPORTANT]
> Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Stöd för push-meddelande i Microsoft Azure kan du komma åt en enkel att använda och infrastruktur för utskalad push-meddelande, vilket förenklar implementeringen av push-meddelanden för konsument- och enterprise-program för mobila plattformar.

På grund av föreskrifter säkerhetsbegränsningar, ibland ett program kan också vilja inkludera något i meddelandet som inte kan tillhandahållas via standard push-meddelandeinfrastruktur. Den här självstudien beskrivs hur du uppnår samma upplevelse genom att skicka känslig information via en säker och autentiserad anslutning mellan Android klientenheten och appserverdelen.

På en hög nivå är flödet på följande sätt:

1. App backend-server:
   * Butiker säker nyttolast i backend-databasen.
   * Skickar ID för det här meddelandet till Android-enhet (ingen säker information skickas).
2. Appen på enheten när du tar emot meddelandet:
   * Android-enheten kontaktar serverdelen begär säker nyttolasten.
   * Appen kan visa nyttolasten som ett meddelande på enheten.

Det är viktigt att Observera att i föregående flödet (och i den här självstudien), förutsätts det att enheten lagrar en autentiseringstoken i den lokala lagringen när användaren loggar in. Den här metoden garanterar en sömlös upplevelse som enheten kan hämta den meddelandets säker nyttolast använder denna token. Om ditt program lagrar inte autentiseringstoken på enheten eller om dessa token kan ha upphört att gälla, enhetsapp vid mottagning av push-meddelanden ska visa ett allmänt meddelande där användaren uppmanas att starta appen. Appen sedan autentiserar användaren och visar meddelandets nyttolast.

Den här självstudiekursen visar hur du skickar säker push-meddelanden. Den bygger på den [meddela användare](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) kursen, så bör du genomföra stegen i självstudien först om du inte redan har gjort.

> [!NOTE]
> Den här självstudien förutsätter att du har skapat och konfigurerat din meddelandehubb, enligt beskrivningen i [komma igång med Notification Hubs (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Ändra Android-projekt

Nu när du har ändrat din appens serverdel att skicka bara *ID* av ett push-meddelande, du måste ändra din Android-app för att hantera som meddelanden och Ring tillbaka din serverdel för att hämta det säkra meddelandet som ska visas.
För att åstadkomma detta måste du kontrollera att din Android-app vet hur du autentiserar sig själv med din serverdel när den tar emot push-meddelanden.

Anpassa den *inloggning* flöde för att kunna spara autentisering huvudets värde i de delade inställningarna för din app. Liknande metoder kan användas för att lagra alla autentiseringstoken (till exempel OAuth-token) som appen har du använder utan att användarens autentiseringsuppgifter.

1. I Android-app-projektet lägger du till följande konstanter överst i den `MainActivity` klass:

    ```java
    public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
    public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
    ```
2. Fortfarande är i den `MainActivity` klass, uppdaterar den `getAuthorizationHeader()` metoden innehåller följande kod:

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
3. Lägg till följande `import` uttryck överst i den `MainActivity` fil:

    ```java
    import android.content.SharedPreferences;
    ```

Nu ska du ändra hanteraren som anropas när meddelandet tas emot.

1. I den `MyHandler` klassen ändra den `OnReceive()` metod för att innehålla:

    ```java
    public void onReceive(Context context, Bundle bundle) {
        ctx = context;
        String secureMessageId = bundle.getString("secureId");
        retrieveNotification(secureMessageId);
    }
    ```
2. Lägg sedan till den `retrieveNotification()` metod, Ersätt platshållarvärdet `{back-end endpoint}` med backend-slutpunkten fick när du distribuerar din serverdel:

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

Den här metoden anropar din appens serverdel för att hämta meddelandeinnehållet med de autentiseringsuppgifter som lagras i delade inställningar och visar det som en normal meddelande. Meddelandet som ser ut för app-användaren precis som andra push-meddelande.

Det är bättre att hantera fall av saknas huvud-autentiseringsegenskapen eller nekande av backend-server. Särskild hantering av dessa fall beror huvudsakligen på din användarupplevelse för målet. Ett alternativ är att visa ett meddelande med en allmän uppmaning att autentisera användaren att hämta anmälan.

## <a name="run-the-application"></a>Kör programmet

Om du vill köra programmet måste du utföra följande åtgärder:

1. Se till att **AppBackend** distribueras i Azure. Om du använder Visual Studio kör den **AppBackend** webb-API-program. En ASP.NET-webbsida visas.
2. Kör appen på en fysisk Android-enhet eller emulatorn i Eclipse.
3. I Android-appen Användargränssnittet, anger du ett användarnamn och lösenord. Det kan vara valfri sträng, men de måste vara samma värde.
4. I Android-appen Användargränssnittet, klickar du på **logga in**. Klicka sedan på **skicka push**.
