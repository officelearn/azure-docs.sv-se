---
title: Aktivera synkronisering offline för din Azure-Mobilapp (Xamarin.Forms) | Microsoft Docs
description: Lär dig hur du använder App Service Mobile App till cache och synkronisering av offlinedata i din Xamarin.Forms-App
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
ms.openlocfilehash: 506c59ca24aeafbac59b1508bb78142051302765
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127887"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Aktivera synkronisering offline för din Xamarin.Forms-mobilapp
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Översikt
Den här självstudien innehåller funktionen offline-synkronisering i Azure Mobile Apps för Xamarin.Forms. Offlinesynkronisering kan slutanvändarna kan interagera med en mobil app – visa, lägga till eller ändra data – även när det inte finns någon nätverksanslutning. Ändringarna sparas i en lokal databas. När enheten är online igen kan har de här ändringarna synkroniserats med fjärrtjänsten.

Den här självstudien är baserad på Xamarin.Forms-lösningen för Snabbstart för Mobile Apps som du skapar när du slutför självstudien [skapa en Xamarin iOS-app]. Snabbstart för lösningen för Xamarin.Forms innehåller kod för att stödja offline sync, vilket ska vara aktiverat. I den här självstudien får uppdatera du quickstart-lösning för att aktivera Offlinefunktioner för Azure Mobile Apps. Vi kan också markera offline-specifik kod i appen. Om du inte använder den hämta quickstart-lösningen kan du lägga till data access-tillägg-paket i projektet. Läs mer om server-tilläggspaket [arbeta med SDK för .NET-serverdelen för Azure Mobile Apps][1].

Mer information om funktionen offline-synkronisering, finns i avsnittet [offlinesynkronisering Data i Azure Mobile Apps][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Aktivera offlinesynkronisering funktionerna i Snabbstart-lösningen
Offlinesynkronisering koden ingår i projektet genom att använda C# preprocessor direktiv. När den **OFFLINE\_SYNKRONISERING\_AKTIVERAD** symbol definieras, sökvägarna kod som ingår i versionen. För Windows-appar, måste du också installera SQLite-plattformen.

1. Högerklicka på lösningen i Visual Studio > **hantera NuGet-paket för lösningen...** , Sök sedan efter och installera den **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-paketet för alla projekt i lösningen.
2. Öppna filen TodoItemManager.cs i Solution Explorer från projektet med **bärbar** i namnet, som är portabelt klassbibliotek projekt, ta sedan bort kommentarerna följande preprocessor direktiv:

        #define OFFLINE_SYNC_ENABLED
3. (Valfritt) Installera ett av följande SQLite runtime-paket för Windows-enheter måste:

   * **Windows 8.1 Runtime:** Installera [SQLite för Windows 8.1][3].
   * **Windows Phone 8.1:** Installera [SQLite för Windows Phone 8.1][4].
   * **Universal Windows Platform** installera [SQLite för Universal Windows Universal][5].

     Även om snabbstarten inte innehåller en universell Windows-projektet, stöds universell Windows-plattformen med Xamarin Forms.
4. (Valfritt) Högerklicka i varje Windows-approjekt **referenser** > **Lägg till referens...** , expandera den **Windows** mapp > **tillägg**.
    Aktivera lämplig **SQLite för Windows** SDK tillsammans med den **Visual C++ 2013 Runtime för Windows** SDK.
    SQLite SDK-namnen kan variera med varje Windows-plattform.

## <a name="review-the-client-sync-code"></a>Granska klientkod för synkronisering
Här är en kort översikt över vad som redan ingår i självstudiekursen koden i den `#if OFFLINE_SYNC_ENABLED` direktiv. Funktionen offline-synkronisering är i projektfilen TodoItemManager.cs i portabelt klassbibliotek projektet. Läs en översikt över funktionen [Offline datasynkronisering i Azure Mobile Apps][2].

* Innan tabellåtgärder kan utföras, måste det lokala arkivet initieras. Databasen för lokal lagring initieras i den **TodoItemManager** klasskonstruktor med hjälp av följande kod:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Den här koden skapar en ny lokal SQLite databas med den **MobileServiceSQLiteStore** klass.

    Den **DefineTable** metoden skapar en tabell i det lokala arkivet som matchar fälten i den angivna typen.  Typen har inte att inkludera alla kolumner som finns i fjärrdatabasen. Det är möjligt att lagra en delmängd med kolumner.
* Den **todoTable** i **TodoItemManager** är en **IMobileServiceSyncTable** Skriv i stället för **IMobileServiceTable**. Denna klass använder den lokala databasen för alla skapa, läsa, uppdatera och ta bort (CRUD) tabellåtgärder. Du bestämmer dig för när ändringarna skickas till serverdelen för Mobilappen genom att anropa **PushAsync** på den **IMobileServiceSyncContext**. Synkronisera kontext bevaras tabellrelationer genom att spåra och push-överföra ändringarna i alla tabeller som en klientapp har ändrat när **PushAsync** anropas.

    Följande **SyncAsync** metoden anropas för synkronisering med serverdelen för Mobilappen:

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

    Det här exemplet används enkla felhantering med standard sync-hanteraren. Ett riktigt program skulle hantera olika fel som nätverksförhållanden och servern är i konflikt med hjälp av en anpassad **IMobileServiceSyncHandler** implementering.

## <a name="offline-sync-considerations"></a>Överväganden för offline-synkronisering
I det här exemplet på **SyncAsync** metoden anropas endast på Start- och när en synkronisering har begärts.  Om du vill initiera en synkronisering i en Android eller iOS-app dra nedåt i objektlistan; för Windows, använder den **synkronisering** knappen. I ett verkligt program kan du också göra synkronisering utlösaren när nätverket tillstånd ändras.

När en pull körs mot en tabell som har väntande spåras lokala uppdateringar av den kontext som hämtar åtgärden automatiskt utlösare en föregående kontext-push. När du uppdaterar, att lägga till och slutföra objekt i det här exemplet kan du utelämna den explicita **PushAsync** anropa.

I den angivna koden alla poster i fjärrtabellen TodoItem tillfrågas, men det är också möjligt att filtrera poster genom att skicka en fråge-id och skicka frågor till **PushAsync**. Mer information finns i avsnittet *inkrementell synkronisering* i [Offline datasynkronisering i Azure Mobile Apps][2].

## <a name="run-the-client-app"></a>Kör klientappen
Med offlinesynkronisering nu aktiverade, kör du klientprogrammet minst en gång på varje plattform för att fylla i lokalt Arkiv-databasen. Senare kan simulera en offlinescenario och ändra data i det lokala arkivet medan appen är offline.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Uppdatera beteendet för synkronisering för klientappen
Ändra klientprojektet för att simulera en offlinescenario med hjälp av en ogiltig programmets URL för din serverdel i det här avsnittet. Du kan också kan du stänga av Nätverksanslutningar genom att flytta din enhet till ”flygplansläget”.  När du lägger till eller ändra dataobjekt dessa ändringar finns i det lokala arkivet, men inte har synkroniserats till datalagret serverdelen tills anslutningen har återupprättats.

1. I Solution Explorer öppnar du Constants.cs projektfilen från den **bärbar** projekt och ändra värdet för `ApplicationURL` så att den pekar till en ogiltig URL:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Öppna filen TodoItemManager.cs från den **bärbar** projektet och sedan lägga till en **fånga** för grundläggande **undantag** klassen till den **försök... catch**blockera i **SyncAsync**. Detta **fånga** block skriver Undantagsmeddelandet till konsolen på följande sätt:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Skapa och kör klientappen.  Lägga till några nya objekt. Observera att ett undantag loggas i konsolen för varje försök att synkronisera med serverdelen. Dessa nya objekt finns bara i det lokala arkivet tills de kan skickas till den mobila serverdelstjänst. Klientappen fungerar som om den är ansluten till serverdelen, stöder alla skapa, läsa, uppdatera, ta bort (CRUD) åtgärder.
4. Stäng appen och starta om den för att kontrollera att de nya objekt som du har skapat sparas till det lokala arkivet.
5. (Valfritt) Använda Visual Studio för att visa din Azure SQL Database-tabell för att se att data i databasen i serverdelen inte har ändrats.

    Öppna i Visual Studio **Server Explorer**. Gå till din databas i **Azure**->**SQL-databaser**. Högerklicka på databasen och välj **öppna i SQL Server Object Explorer**. Nu kan du bläddra till din SQL-databastabell och dess innehåll.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Uppdatera klientappen för att återansluta din mobila serverdelstjänst
Återanslut till appen till mobila serverdel, vilket simulerar appen kommer tillbaka till ett onlinetillstånd i det här avsnittet. När du utför en uppdatering gest synkroniseras data till din mobila serverdelstjänst.

1. Öppna Constants.cs. Korrigera det `applicationURL` så att den pekar till rätt URL.
2. Bygg och kör klientappen. Appen försöker synkronisera med serverdelen för mobilappen när du startar. Kontrollera att inga undantag loggas i Felsökningskonsolen.
3. (Valfritt) Visa uppdaterade data med hjälp av SQL Server Object Explorer eller ett REST-verktyg som Fiddler eller [Postman][6]. Observera att dessa data har synkroniserats mellan databasen och det lokala arkivet.

    Observera att dessa data har synkroniserats mellan databasen och det lokala arkivet och innehåller de objekt som du har lagt till medan kopplades från din app.

## <a name="additional-resources"></a>Ytterligare resurser
* [Synkronisering av offlinedata i Azure Mobile Apps][2]
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
