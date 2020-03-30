---
title: Aktivera offlinesynkronisering (Xamarin.Forms) | Microsoft-dokument
description: Lär dig hur du använder App Service Mobile App för att cachelagra och synkronisera offlinedata i ditt Xamarin.Forms-program.
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 57776073ef7e2760b308df22280faf1d65b8d104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458859"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Aktivera offlinesynkronisering för mobilappen Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Översikt
Den här självstudien introducerar offlinesynkroniseringsfunktionen i Azure Mobile Apps för Xamarin.Forms. Offlinesynkronisering gör det möjligt för slutanvändare att interagera med en mobilapp – visa, lägga till eller ändra data – även när det inte finns någon nätverksanslutning. Ändringar lagras i en lokal databas. När enheten är online igen synkroniseras dessa ändringar med fjärrtjänsten.

Den här självstudien baseras på snabbstartslösningen Xamarin.Forms för mobilappar som du skapar när du slutför självstudien [Skapa en Xamarin iOS-app]. Snabbstartslösningen för Xamarin.Forms innehåller koden för att stödja offlinesynkronisering, som bara behöver aktiveras. I den här självstudien uppdaterar du snabbstartslösningen för att aktivera offlinefunktionerna i Azure Mobile Apps. Vi lyfter också fram den offlinespecifika koden i appen. Om du inte använder den hämtade snabbstartslösningen måste du lägga till paketen för dataåtkomsttillägg i projektet. Mer information om servertilläggspaket finns i [Arbeta med .NET-server för server för server-server SDK för Azure Mobile Apps][1].

Mer information om synkroniseringsfunktionen offline finns i avsnittet [Offlinedatasynkning i Azure Mobile Apps][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Aktivera offlinesynkroniseringsfunktioner i snabbstartslösningen
Offlinesynkroniseringskoden ingår i projektet med hjälp av C# preprocessor-direktiv. När **offlinesynkroniseringssymbolen\_\_har definierats** inkluderas dessa kodsökvägar i versionen. För Windows-appar måste du också installera SQLite-plattformen.

1. Högerklicka på lösningen > **Hantera NuGet-paket för lösning i**Visual Studio och sök sedan efter och installera **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-paketet för alla projekt i lösningen.
2. Öppna filen TodoItemManager.cs från projektet med **Portable** i namnet, som är projektet Portable Class Library, och avkommentera sedan följande förprocessordirektiv:

        #define OFFLINE_SYNC_ENABLED
3. (Valfritt) Installera något av följande SQLite-runtime-paket för att stödja Windows-enheter:

   * **Windows 8.1 Runtime:** Installera [SQLite för Windows 8.1][3].
   * **Windows Phone 8.1:** Installera [SQLite för Windows Phone 8.1][4].
   * **Universell Windows-plattform** Installera [SQLite för Universal Windows Universal][5].

     Även om snabbstarten inte innehåller ett Universellt Windows-projekt stöds den universella Windows-plattformen med Xamarin-formulär.
4. (Valfritt) Högerklicka på **Referenser** > i varje Windows-appprojekt... och expandera **Windows-mappen** > **tillägg**.**Add Reference...**
    Aktivera lämplig **SQLite för Windows** SDK tillsammans med Visual **C++ 2013 Runtime för Windows** SDK.
    SQLite SDK-namnen varierar något med varje Windows-plattform.

## <a name="review-the-client-sync-code"></a>Granska klientsynkroniseringskoden
Här är en kort översikt över vad som `#if OFFLINE_SYNC_ENABLED` redan ingår i handledningen koden i direktiven. Offlinesynkroniseringsfunktionen finns i TodoItemManager.cs projektfilen i projektet Portable Class Library. En begreppsmässig översikt över funktionen finns [i Offlinedatasynkronisering i Azure Mobile Apps][2].

* Innan några tabellåtgärder kan utföras måste det lokala arkivet initieras. Den lokala arkivdatabasen initieras i **todoitemmanager-klasskonstruktorn** med hjälp av följande kod:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Den här koden skapar en ny lokal SQLite-databas med klassen **MobileServiceSQLiteStore.**

    Metoden **DefineTable** skapar en tabell i det lokala arkivet som matchar fälten i den angivna typen.  Typen behöver inte innehålla alla kolumner som finns i fjärrdatabasen. Det är möjligt att lagra en delmängd av kolumner.
* **Fältet todoTable** i **TodoItemManager** är en **IMobileServiceSyncTable-typ** i stället för **IMobileServiceTable**. Den här klassen använder den lokala databasen för alla tabellåtgärder för att skapa, läsa, uppdatera och ta bort .. Du bestämmer när ändringarna skjuts till mobile app-backend genom att anropa **PushAsync** på **IMobileServiceSyncContext**. Synkroniseringskontexten hjälper till att bevara tabellrelationer genom att spåra och trycka på ändringar i alla tabeller som en klientapp har ändrat när **PushAsync** anropas.

    Följande **SyncAsync-metod** anropas för synkronisering med mobile app-backend:

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

    Det här exemplet använder enkel felhantering med standardsynkroniseringshanteraren. Ett verkligt program skulle hantera de olika fel som nätverksvillkor och serverkonflikter med hjälp av en anpassad **IMobileServiceSyncHandler-implementering.**

## <a name="offline-sync-considerations"></a>Överväganden för offlinesynkronisering
I exemplet anropas **metoden SyncAsync** endast vid start och när en synkronisering begärs.  Om du vill initiera en synkronisering i en Android- eller iOS-app drar du ned objektlistan. för Windows använder du knappen **Synkronisera.** I ett verkligt program kan du också göra synkroniseringsutlösaren när nätverkstillståndet ändras.

När ett handtag körs mot en tabell som har väntande lokala uppdateringar spåras av kontexten, utlöser den pull-åtgärden automatiskt en föregående kontext push. När du uppdaterar, lägger till och slutför objekt i det här exemplet kan du utelämna det explicita **PushAsync-anropet.**

I den angivna koden efterfrågas alla poster i tabellen för fjärr-TodoItem, men det är också möjligt att filtrera poster genom att skicka ett fråge-ID och en fråga till **PushAsync**. Mer information finns i avsnittet *Inkrementell synkronisering* i [offlinedatasynkronisering i Azure Mobile Apps][2].

## <a name="run-the-client-app"></a>Kör klientappen
När offlinesynkronisering nu är aktiverat kör du klientprogrammet minst en gång på varje plattform för att fylla i den lokala butiksdatabasen. Senare simulerar du ett offlinescenario och ändrar data i den lokala butiken medan appen är offline.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Uppdatera synkroniseringsbeteendet för klientappen
I det här avsnittet ändrar du klientprojektet för att simulera ett offlinescenario med hjälp av en ogiltig program-URL för din backend. Du kan också stänga av nätverksanslutningar genom att flytta enheten till "Flygplansläge".  När du lägger till eller ändrar dataobjekt lagras dessa ändringar i det lokala arkivet, men synkroniseras inte med backend-dataarkivet förrän anslutningen återupprättas.

1. Öppna projektfilen för Constants.cs från det **bärbara** projektet i Solution Explorer `ApplicationURL` och ändra värdet på så att värdet pekar på en ogiltig URL:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Öppna den TodoItemManager.cs filen från det **bärbara** projektet och lägg sedan till en **hake** för klassen **base Exception** i **försöket... catch** block i **SyncAsync**. Det här **catch-blocket** skriver undantagsmeddelandet till konsolen enligt följande:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Skapa och kör klientappen.  Lägg till några nya objekt. Observera att ett undantag är inloggat i konsolen för varje försök att synkronisera med serverda. Dessa nya objekt finns bara i den lokala butiken tills de kan skickas till den mobila backend. Klientappen fungerar som om den är ansluten till backend, som stöder alla åtgärder för att skapa, läsa, uppdatera, ta bort (CRUD).
4. Stäng appen och starta om den för att kontrollera att de nya objekten som du har skapat sparas i den lokala arkivet.
5. (Valfritt) Använd Visual Studio för att visa din Azure SQL Database-tabell för att se att data i serveringsdatabasen inte har ändrats.

    Öppna **Server Explorer**i Visual Studio . Navigera till databasen i **Azure**->**SQL Databases**. Högerklicka på databasen och välj **Öppna i SQL Server Object Explorer**. Nu kan du bläddra till din SQL-databastabell och dess innehåll.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Uppdatera klientappen för att återansluta din mobila backend
I det här avsnittet återansluter du appen till den mobila backend, som simulerar att appen kommer tillbaka till ett onlinetillstånd. När du utför uppdateringsgesten synkroniseras data med din mobila backend.

1. Öppna Constants.cs igen. Korrigera `applicationURL` för att peka på rätt webbadress.
2. Återskapa och kör klientappen. Appen försöker synkronisera med mobilappens backend efter lanseringen. Kontrollera att inga undantag är inloggade i felsökningskonsolen.
3. (Valfritt) Visa uppdaterade data med antingen SQL Server Object Explorer eller ett REST-verktyg som Fiddler eller [Postman][6]. Observera att data har synkroniserats mellan serverdelsdatabasen och det lokala arkivet.

    Observera att data har synkroniserats mellan databasen och den lokala butiken och innehåller de objekt som du lade till medan appen kopplades från.

## <a name="additional-resources"></a>Ytterligare resurser
* [Datasynkronisering offline i Azure Mobile Apps][2]
* [Azure Mobile Apps .NET SDK HOWTO][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: https://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: https://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: https://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: https://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
