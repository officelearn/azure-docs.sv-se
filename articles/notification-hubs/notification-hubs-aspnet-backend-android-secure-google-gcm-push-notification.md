---
title: "Skicka säkra Push-meddelanden med Azure Notification Hubs"
description: "Lär dig mer om att skicka säkra push-meddelanden till en Android-app från Azure. Kodexempel som skrivits i Java- och C#."
documentationcenter: android
keywords: push-meddelande, push-meddelanden, push-meddelanden, android push-meddelanden
author: ysxu
manager: erikre
editor: 
services: notification-hubs
ms.assetid: daf3de1c-f6a9-43c4-8165-a76bfaa70893
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 29f8c516e611c13fb73c7edc15e7c52708c75bb0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Skicka säkra Push-meddelanden med Azure Notification Hubs
> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>Översikt
> [!IMPORTANT]
> Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).
> 
> 

Stöd för push-meddelanden i Microsoft Azure kan du komma åt en lätt att använda, multiplatform, skalats ut push-meddelande-infrastruktur, vilket förenklar implementeringen av push-meddelanden för konsument- och enterprise-program för mobila plattformar.

På grund av reglerande säkerhetsbegränsningar, ibland ett program kan också innehålla något i meddelandet inte kan överföras via de standard push-infrastrukturen. Den här självstudiekursen beskrivs hur du kan uppnå samma upplevelse genom att skicka känslig information via en säker och autentiserad anslutning mellan klienten Android-enhet och appens serverdel.

På en hög nivå är flödet:

1. På appens serverdel:
   * Lagrar säker nyttolast i backend-databas.
   * Skickar ID för det här meddelandet till Android-enhet (ingen säker information skickas).
2. Appen på enheten när du tar emot meddelandet:
   * Android-enhet kontaktar serverdelen begär säker nyttolasten.
   * Appen kan du visa nyttolasten som ett meddelande på enheten.

Det är viktigt att Observera att i det föregående flödet (och i den här självstudiekursen) antar vi att enheten lagrar en autentiseringstoken i lokal lagring när en användare loggar in. Detta garanterar en helt integrerad upplevelse som enheten kan hämta den anmälan säker nyttolast med denna token. Om ditt program inte kan lagra autentiseringstoken på enheten, eller om dessa token kan ha gått, visas vid mottagning av push-meddelanden i appen enhet ett allmänt meddelande där användaren uppmanas att starta appen. Appen sedan autentiserar användaren och visar nyttolasten för meddelanden.

Den här kursen visar hur du skickar säker push-meddelanden. Den bygger på den [meddela användare](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) självstudier, så bör du genomföra stegen i den här självstudiekursen först om du inte redan har gjort.

> [!NOTE]
> Den här kursen förutsätter att du har skapat och konfigurerat din meddelandehubb enligt beskrivningen i [komma igång med Notification Hubs (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Ändra Android-projekt
Nu när du har ändrat din appens serverdel att skicka bara den *id* av ett push-meddelande måste du ändra din Android-app för att hantera detta meddelande och ringa upp din serverdel för att hämta det säkra meddelandet som ska visas.
Du måste se till att din Android-app användas att autentisera sig med din serverdel när den tar emot push-meddelanden för att åstadkomma detta.

Vi kommer nu att ändra den *inloggning* flödet för att kunna spara huvudvärde autentisering i delade inställningar för din app. Liknande metoder kan användas för att lagra alla autentiseringstoken (t.ex. OAuth-token) som appen kommer att använda utan att användarens autentiseringsuppgifter.

1. Lägg till följande konstanter i projektet Android-app överst i den **MainActivity** klass:
   
        public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
        public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
2. Fortfarande i den **MainActivity** klass, uppdaterar den `getAuthorizationHeader()` metoden innehåller följande kod:
   
        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
   
            SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();
   
            return basicAuthHeader;
        }
3. Lägg till följande `import` instruktioner överst i den **MainActivity** fil:
   
        import android.content.SharedPreferences;

Nu ska vi ändra hanteraren som anropas när meddelandet tas emot.

1. I den **MyHandler** klassen ändra den `OnReceive()` metoden innehålla:
   
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String secureMessageId = bundle.getString("secureId");
            retrieveNotification(secureMessageId);
        }
2. Lägg sedan till den `retrieveNotification()` metod, ersätter platshållaren `{back-end endpoint}` med backend-slutpunkten som erhålls vid distribution av din serverdel:
   
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

Den här metoden anropar din appens serverdel för att hämta det meddelandeinnehåll med de autentiseringsuppgifter som lagras i delade inställningar och visas som ett vanligt meddelande. Meddelandet verkar app användaren exakt samma sätt som andra push-meddelande.

Observera att det är bättre att hantera de egenskap som saknas autentisering sidhuvud eller underkännande av serverdelen. Särskild hantering av dessa fall beror huvudsakligen på användarupplevelsen mål. Ett alternativ är att visa ett meddelande med en allmän fråga att autentisera användaren att hämta anmälan.

## <a name="run-the-application"></a>Köra programmet
Om du vill köra programmet, gör du följande:

1. Kontrollera att **AppBackend** distribueras i Azure. Om du använder Visual Studio, köra den **AppBackend** Web API-program. En ASP.NET-webbsida visas.
2. Kör appen på en fysisk Android-enhet eller emulatorn i Eclipse.
3. Ange ett användarnamn och lösenord i Användargränssnittet Android app. Det kan vara valfri sträng, men de måste ha samma värde.
4. I Android-appen UI, klickar du på **logga in**. Klicka på **skicka push**.

