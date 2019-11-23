---
title: Aktivera synkronisering offline för din Azure-mobilapp (Xamarin. Forms) | Microsoft Docs
description: Lär dig hur du använder App Service mobilapp för att cachelagra och Synkronisera offlinedata i Xamarin. Forms-programmet
documentationcenter: xamarin
author: elamalani
manager: yochayk
editor: ''
services: app-service\mobile
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 65bd619a95b102c4ad4be57f1661a89fbae43e22
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388431"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Aktivera synkronisering offline för din Xamarin.Forms-mobilapp
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av slutpunkt till slutpunkt-tjänster och integrerade tjänster som är centrala för utveckling av mobilappar. Utvecklare kan använda tjänsterna för att **bygga**, **testa** och **distribuera** för att skapa en pipeline för kontinuerlig integrering och leverans. När appen har distribuerats kan utvecklarna övervaka status och användning av appen med hjälp av tjänsterna **Analys** och **Diagnostik**, och kommunicera med användarna via **Push**-tjänsten. Utvecklare kan också dra nytta av **Auth** för att autentisera sina användare och tjänsten **Data** för att spara och synkronisera appdata i molnet.
>
> Om du vill integrera molntjänster i ditt mobilprogram kan du registrera dig med [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) i dag.

## <a name="overview"></a>Översikt
I den här självstudien beskrivs funktionen offline-synkronisering i Azure Mobile Apps för Xamarin. Forms. Offline-synkronisering ger slutanvändare möjlighet att interagera med en mobilapp – Visa, lägga till eller ändra data, även när det inte finns någon nätverks anslutning. Ändringarna lagras i en lokal databas. När enheten är online igen synkroniseras dessa ändringar med fjärrtjänsten.

Den här självstudien baseras på Xamarin. Forms snabb starts lösning för Mobile Apps som du skapar när du är klar med självstudien [skapa en Xamarin iOS-app]. Snabb starts lösningen för Xamarin. Forms innehåller koden för att stödja offlinesynkronisering, som bara behöver aktive ras. I den här självstudien uppdaterar du snabb starts lösningen för att aktivera offline-funktionerna i Azure Mobile Apps. Vi markerar också den offline-/regionsspecifika koden i appen. Om du inte använder den nedladdade snabb start lösningen måste du lägga till paket för data åtkomst tillägg i projektet. Mer information om Server tilläggs paket finns i [arbeta med .NET-Server del Server SDK för Azure Mobile Apps][1].

Mer information om funktionen offline-synkronisering finns i avsnittet [data synkronisering offline i Azure Mobile Apps][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Aktivera funktionen offline-synkronisering i snabb starts lösningen
Koden för offline-synkronisering ingår i projektet med hjälp av C# Preprocessor-direktiv. Om den **\_aktive rad symbolen OFFLINE\_Sync** är definierad, ingår dessa kod Sök vägar i versionen. För Windows-appar måste du även installera SQLite-plattformen.

1. I Visual Studio högerklickar du på lösningen > **Hantera NuGet-paket för lösningen...** och söker efter och installerar paketet **Microsoft. Azure. Mobile. client. SQLiteStore** NuGet för alla projekt i lösningen.
2. Öppna TodoItemManager.cs-filen från projektet med **portabel** i namnet, som är ett biblioteks projekt för portabel klass, och ta sedan bort kommentaren till följande Preprocessor-direktiv: Solution Explorer

        #define OFFLINE_SYNC_ENABLED
3. Valfritt Om du vill ha stöd för Windows-enheter installerar du ett av följande SQLite runtime-paket:

   * **Windows 8,1-körning:** Installera [sqlite för Windows 8,1][3].
   * **Windows Phone 8,1:** Installera [sqlite för Windows Phone 8,1][4].
   * **Universell Windows-plattform** Installera [sqlite för Universal Windows Universal][5].

     Även om snabb starten inte innehåller ett universellt Windows-projekt, stöds den universella Windows-plattformen med Xamarin-formulär.
4. Valfritt I varje Windows-app-projekt högerklickar du på **referenser** > **Lägg till referens...** , expandera **Windows** -mappen > **tillägg**.
    Aktivera rätt **sqlite för Windows** SDK tillsammans med  **C++ Visual 2013-körningen för Windows** SDK.
    SQLite SDK-namnen skiljer sig något med varje Windows-plattform.

## <a name="review-the-client-sync-code"></a>Granska koden för klientens synkronisering
Här är en kort översikt över vad som redan ingår i själv studie kursen i `#if OFFLINE_SYNC_ENABLED` direktiv. Funktionen offline-synkronisering finns i TodoItemManager.cs-projekt filen i biblioteks projekt för portabel klass. En översikt över funktionen finns i [data synkronisering offline i Azure Mobile Apps][2].

* Innan alla tabell åtgärder kan utföras måste det lokala arkivet initieras. Den lokala lagrings databasen initieras i **TodoItemManager** -klassens konstruktor med hjälp av följande kod:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Den här koden skapar en ny lokal SQLite-databas med klassen **MobileServiceSQLiteStore** .

    Metoden **DefineTable** skapar en tabell i det lokala arkivet som matchar fälten i den angivna typen.  Typen behöver inte innehålla alla kolumner som finns i fjärrdatabasen. Det går att lagra en delmängd av kolumner.
* Fältet **todoTable** i **TodoItemManager** är en **IMobileServiceSyncTable** -typ i stället för **IMobileServiceTable**. Den här klassen använder den lokala databasen för tabell åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD). Du bestämmer när ändringarna ska skickas till Server delen för mobilappar genom att anropa **PushAsync** på **IMobileServiceSyncContext**. Den synkroniserade kontexten hjälper till att bevara tabell relationer genom att spåra och överföra ändringar i alla tabeller som en klient app har ändrat när **PushAsync** anropas.

    Följande **SyncAsync** -metod anropas för synkronisering med den mobila appens Server del:

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling.
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    I det här exemplet används enkel fel hantering med standard hanteraren för synkronisering. Ett verkligt program hanterar de olika felen som nätverks förhållanden och Server konflikter genom att använda en anpassad **IMobileServiceSyncHandler** -implementering.

## <a name="offline-sync-considerations"></a>Överväganden offline-synkronisering
I exemplet anropas **SyncAsync** -metoden endast vid start och när en synkronisering begärs.  Om du vill initiera en synkronisering i en Android-eller iOS-app går du till listan objekt. för Windows använder du knappen **Synkronisera** . I ett verkligt program kan du också göra en synkroniseringskoppling när nätverks tillstånd ändras.

När en pull körs mot en tabell som har väntande lokala uppdateringar som spåras av kontexten utlöser pull-åtgärden automatiskt en föregående kontext push. När du uppdaterar, lägger till och fyller i objekt i det här exemplet kan du utelämna det explicita **PushAsync** -anropet.

I den angivna koden frågas alla poster i den fjärranslutna TodoItem-tabellen, men det går också att filtrera poster genom att skicka ett fråge-ID och fråga till **PushAsync**. Mer information finns i avsnittet *stegvis synkronisering* i [data synkronisering Offline i Azure Mobile Apps][2].

## <a name="run-the-client-app"></a>Kör klient programmet
När offlinesynkronisering är aktive rad kan du köra klient programmet minst en gång på varje plattform för att fylla i den lokala lagrings databasen. Du kan senare simulera ett offline-scenario och ändra data i det lokala arkivet när appen är offline.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Uppdatera klient appens synkronisering
I det här avsnittet ändrar du klient projektet för att simulera ett offline-scenario genom att använda en ogiltig program-URL för Server delen. Du kan också inaktivera nätverks anslutningar genom att flytta enheten till "flyg Plans läge".  När du lägger till eller ändrar data objekt lagras dessa ändringar i det lokala arkivet, men synkroniseras inte till Server dels data lagret förrän anslutningen har återupprättats.

1. I Solution Explorer öppnar du projekt filen Constants.cs från det **bärbara** projektet och ändrar värdet för `ApplicationURL` så att det pekar på en ogiltig URL:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Öppna TodoItemManager.cs-filen från det **bärbara** projektet och Lägg sedan till en **fångst** för bas **undantags** klassen i kommandot **try... Catch** -block i **SyncAsync**. Detta **Catch** -block skriver undantags meddelandet till-konsolen på följande sätt:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Skapa och kör klient programmet.  Lägg till några nya objekt. Observera att ett undantag loggas i-konsolen för varje försök att synkronisera med Server delen. Dessa nya objekt finns bara i den lokala lagrings platsen förrän de kan flyttas till den mobila Server delen. Klient programmet fungerar som om det är anslutet till Server delen och stöder alla åtgärder för att skapa, läsa, uppdatera, ta bort (CRUD).
4. Stäng appen och starta om den för att kontrol lera att de nya objekt som du har skapat är sparade i det lokala arkivet.
5. Valfritt Använd Visual Studio för att visa din Azure SQL Database tabell för att se att data i backend-databasen inte har ändrats.

    Öppna **Server Explorer**i Visual Studio. Navigera till din databas i **Azure**->**SQL-databaser**. Högerklicka på databasen och välj **Öppna i SQL Server Object Explorer**. Nu kan du bläddra till SQL Database-tabellen och dess innehåll.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Uppdatera klient programmet för att återansluta din mobil Server del
I det här avsnittet ansluter du appen till mobil Server delen, som simulerar att appen kommer tillbaka till ett online-tillstånd. När du utför uppdaterings gesten synkroniseras data till din mobil Server del.

1. Öppna Constants.cs igen. Korrigera `applicationURL` så att den pekar på rätt URL.
2. Återskapa och kör klient programmet. Appen försöker synkronisera med Server delen för mobilappar när den har startats. Kontrol lera att inga undantag loggas i fel söknings konsolen.
3. Valfritt Visa uppdaterade data med antingen SQL Server Object Explorer eller ett REST-verktyg som Fiddler eller [Postman][6]. Observera att data har synkroniserats mellan server dels databasen och det lokala arkivet.

    Observera att data har synkroniserats mellan databasen och den lokala lagrings platsen och innehåller de objekt som du lade till när din app kopplades från.

## <a name="additional-resources"></a>Ytterligare resurser
* [Datasynkronisering offline i Azure Mobile Apps][2]
* [Azure Mobile Apps .NET SDK-HOWTO][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: https://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: https://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: https://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: https://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
