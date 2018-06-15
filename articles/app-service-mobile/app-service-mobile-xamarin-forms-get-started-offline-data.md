---
title: Aktivera offlinesynkronisering för Azure Mobile App (Xamarin.Forms) | Microsoft Docs
description: Lär dig mer om Apptjänst Mobile App till cache och synkronisera offlinedata i Xamarin.Forms-program
documentationcenter: xamarin
author: conceptdev
manager: yochayk
editor: ''
services: app-service\mobile
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2016
ms.author: crdun
ms.openlocfilehash: f88e6a4037bcca54982359742cdc6021f020882d
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2018
ms.locfileid: "27594725"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Aktivera synkronisering offline för din Xamarin.Forms-mobilapp
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Översikt
Den här kursen introducerar funktionen offlinesynkronisering i Azure Mobilappar för Xamarin.Forms. Offlinesynkronisering kan slutanvändarna interagera med en mobil app – visa, lägga till eller ändra data, även om det inte finns någon nätverksanslutning. Ändringarna sparas i en lokal databas. När enheten är online igen kan har de här ändringarna synkroniserats med tjänsten remote.

Den här kursen är baserad på quickstart Xamarin.Forms-lösningen för Mobilappar som du skapar när du har slutfört vägledningen [skapa en Xamarin iOS-app]. Snabbstart-lösning för Xamarin.Forms innehåller koden för att stödja offlinesynkronisering som behöver aktiveras. I den här kursen kan du uppdatera quickstart-lösningen för att aktivera Azure Mobile Apps offline funktioner. Vi kan också markera offline-specifika koden i appen. Om du inte använder den hämta quickstart lösningen måste du lägga till data access-tilläggspaket projektet. Mer information om server tilläggspaket finns [arbeta med serverdelen .NET SDK för Azure Mobile Apps][1].

Mer information om funktionen offlinesynkronisering finns i avsnittet [offlinesynkronisering Data i Azure Mobile Apps][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Aktivera offlinesynkronisering funktionerna i Snabbstart-lösningen
Koden offlinesynkronisering ingår i projektet med hjälp av C# preprocessor-direktiv. När den **OFFLINE\_SYNC\_AKTIVERAD** symbol har definierats, dessa kodsökvägar ingår i versionen. För Windows-appar måste du också installera SQLite-plattformen.

1. Högerklicka på lösningen i Visual Studio > **hantera NuGet-paket för lösningen...** , och Sök efter och installera den **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-paket för alla projekt i lösningen.
2. Öppna filen TodoItemManager.cs i Solution Explorer från projektet med **bärbara** i namnet, som bärbara klassbiblioteket projekt, ta sedan bort kommentarerna följande preprocessor direktiv:

        #define OFFLINE_SYNC_ENABLED
3. (Valfritt) Installera en av följande SQLite runtime paket för att stödja Windows-enheter:

   * **Windows 8.1 Runtime:** installera [SQLite för Windows 8.1][3].
   * **Windows Phone 8.1:** installera [SQLite för Windows Phone 8.1][4].
   * **Universella Windows-plattformen** installera [SQLite för universell Windows Universal][5].

     Även om Snabbstart inte innehåller en universell Windows-projektet, stöds universella Windows-plattformen med Xamarin Forms.
4. (Valfritt) Högerklicka i varje Windows-approjekt **referenser** > **Lägg till referens...** , expandera den **Windows** mappen > **tillägg**.
    Aktivera lämpliga **SQLite för Windows** SDK tillsammans med den **Visual C++ 2013 Runtime för Windows** SDK.
    SQLite SDK namnen varierar något med varje Windows-plattformen.

## <a name="review-the-client-sync-code"></a>Granska klientkod för synkronisering
Här är en kort översikt över vad ingår redan i självstudiekursen kod i den `#if OFFLINE_SYNC_ENABLED` direktiven. Synkronisering offline-funktionen är i projektfilen TodoItemManager.cs i bärbara klassbiblioteket projektet. En översikt över funktionen finns [Offline datasynkronisering i Azure Mobile Apps][2].

* Det lokala arkivet måste initieras innan alla tabellåtgärder kan utföras. Lokalt Arkiv databasen initieras i den **TodoItemManager** klasskonstruktor med hjälp av följande kod:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Den här koden skapar en ny lokal SQLite databasen med hjälp av **MobileServiceSQLiteStore** klass.

    Den **DefineTable** metoden skapar en tabell i det lokala arkivet som matchar fälten i den angivna typen.  Typen måste inte innehålla alla kolumner som tillhör fjärrdatabasen. Det är möjligt att lagra en delmängd med kolumner.
* Den **todoTable** i **TodoItemManager** är en **IMobileServiceSyncTable** Skriv i stället för **IMobileServiceTable**. Den här klassen använder den lokala databasen för alla skapa, läsa, uppdatera och ta bort CRUD-tabellåtgärder. Du bestämmer dig för när ändringarna pushas till serverdelen för Mobilappen genom att anropa **PushAsync** på den **IMobileServiceSyncContext**. Kontexten sync bevaras relationerna mellan tabellerna genom att spåra och överföra ändringar i alla tabeller som ett klientprogram har ändrat när **PushAsync** anropas.

    Följande **SyncAsync** metoden anropas ska synkroniseras med serverdelen för Mobilappen:

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

    Det här exemplet använder enkla felhantering med standardprogram för synkronisering. Ett riktigt program skulle hantera olika fel som nätverksförhållanden och servern är i konflikt med hjälp av en anpassad **IMobileServiceSyncHandler** implementering.

## <a name="offline-sync-considerations"></a>Offlinesynkronisering överväganden
I det här exemplet i **SyncAsync** är bara att anropa metoden på Start- och när en synkronisering har begärts.  Om du vill initiera en synkronisering i en Android eller iOS-app dra nedåt i listan objekt; Windows, Använd den **Sync** knappen. I ett riktigt program kan du också göra sync utlösaren när nätverket tillstånd ändras.

När en pull körs mot en tabell som har väntande spåras lokala uppdateringar av sammanhanget, som hämtar åtgärden automatiskt utlösare en föregående kontexten push. När du uppdaterar, lägga till och slutföra objekt i det här exemplet kan du utelämna en explicit **PushAsync** anropa.

I den angivna koden, alla poster i fjärrtabellen TodoItem frågas, men det är också möjligt att filtrera poster genom att skicka en fråge-id och fråga till **PushAsync**. Mer information finns i avsnittet *inkrementell synkronisering* i [Offline datasynkronisering i Azure Mobile Apps][2].

## <a name="run-the-client-app"></a>Kör klientapp
Med offlinesynkronisering har nu aktiverats, kör du klientprogrammet minst en gång på varje plattform för att fylla i databasen för lokal lagring. Senare, simulera ett offline scenario och ändra data i det lokala arkivet medan appen är offline.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Uppdatera sync beteendet för klientappen
Ändra klientprojektet för att simulera ett offline-scenario med en ogiltig programmets URL för din serverdel i det här avsnittet. Du kan också kan du stänga av Nätverksanslutningar genom att flytta din enhet till ”Flygplansläge”.  När du lägger till eller ändra dataobjekt ändringarna lagras i det lokala arkivet, men har inte synkroniserats till datalagret backend tills anslutningen har återupprättats.

1. I Solution Explorer, Öppna projektfilen Constants.cs från den **bärbar** projektet och ändra värdet för `ApplicationURL` att peka till en ogiltig URL:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Öppna filen TodoItemManager.cs från den **bärbar** projektet och sedan lägga till en **fånga** för basen **undantag** undergrupp till den **försök... catch** blockera i **SyncAsync**. Detta **fånga** block skriver Undantagsmeddelandet till konsolen på följande sätt:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Skapa och köra klientappen.  Lägga till några nya objekt. Observera att ett undantag loggas i konsolen för varje försök att synkronisera med serverdelen. Dessa nya objekt finns bara i det lokala arkivet tills de flyttas till den mobila serverdelen. Klientappen fungerar som om den är ansluten till backend, stöder alla skapa, läsa, uppdatera, borttagningsåtgärder (CRUD).
4. Stänga appen och starta om den för att kontrollera att de nya objekt som du skapade sparas på det lokala arkivet.
5. (Valfritt) Du kan använda Visual Studio för att visa din Azure SQL Database-tabellen för att se data i databasen inte har ändrats.

    Öppna i Visual Studio **Server Explorer**. Navigera till din databas i **Azure**->**SQL-databaser**. Högerklicka på databasen och välj **öppna i SQL Server Object Explorer**. Nu kan du bläddra till SQL-databastabell och dess innehåll.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Uppdatera klientappen för att återansluta din mobila serverdel
Anslut appen till mobila serverdelen som simulerar appen komma tillbaka till onlineläge i det här avsnittet. När du utför en uppdatering gest, synkroniseras data till din mobila serverdel.

1. Öppna Constants.cs. Korrigera det `applicationURL` att peka till rätt URL.
2. Bygg och kör klientapp. Appen försöker synkronisera med serverdelen för mobilappen när du startar. Kontrollera att inga undantag loggas i Felsökningskonsolen.
3. (Valfritt) Visa den uppdaterade data med hjälp av SQL Server Object Explorer eller ett REST-verktyg som Fiddler eller [Postman][6]. Observera att data har synkroniserats mellan backend-databas och det lokala arkivet.

    Observera att data har synkroniserats mellan databasen och det lokala arkivet och innehåller objekt som du har lagt till medan kopplades från din app.

## <a name="additional-resources"></a>Ytterligare resurser
* [Datasynkronisering offline i Azure Mobile Apps][2]
* [Azure Mobile Apps .NET SDK ta][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: http://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: http://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: http://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: http://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
