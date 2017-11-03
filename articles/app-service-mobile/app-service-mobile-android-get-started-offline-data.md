---
title: "Aktivera offlinesynkronisering för Azure Mobile App (Android)"
description: "Lär dig mer om Apptjänst Mobilappar cache och synkronisering offline data i din Android-App"
documentationcenter: android
author: ggailey777
manager: syntaxc4
services: app-service\mobile
ms.assetid: 32a8a079-9b3c-4faf-8588-ccff02097224
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 304323c1816302e8c1f68f36a029aee55e02c54e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Aktivera offlinesynkronisering av din Android-mobilapp
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Översikt
Den här kursen ingår funktionen offlinesynkronisering i Azure Mobilappar för Android. Offlinesynkronisering kan slutanvändarna interagera med en mobil app&mdash;visa, lägga till eller ändra data&mdash;även om det inte finns någon nätverksanslutning. Ändringarna sparas i en lokal databas. När enheten är online igen, har ändringarna synkroniserats med fjärr-serverdelen.

Om det här är din första upplevelse med Azure Mobile Apps bör du först slutföra kursen [skapa en Android-App]. Om du inte använder serverprojekt hämtade Snabbstart, måste du lägga till data access-tilläggspaket projektet. Mer information om server tilläggspaket finns [arbeta med serverdelen .NET SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Mer information om funktionen offlinesynkronisering finns i avsnittet [offlinesynkronisering Data i Azure Mobile Apps].

## <a name="update-the-app-to-support-offline-sync"></a>Uppdatera program för att stödja offlinesynkronisering
Med offlinesynkronisering, för att läsa och skriva från en *sync tabell* (med hjälp av den *IMobileServiceSyncTable* interface), som ingår i en **SQLite** databasen på din enhet.

För att push och pull-ändringar mellan enheten och Azure Mobile Services måste du använda en *synkroniseringskontext* (*MobileServiceClient.SyncContext*), som du initiera med lokal databas för lagring data lokalt.

1. I `TodoActivity.java`, kommentera ut den befintliga definitionen av `mToDoTable` och Avkommentera synkroniserade tabell versionen:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. I den `onCreate` metod, kommentera befintliga initieringen av `mToDoTable` och Avkommentera den här definitionen:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. I `refreshItemsFromTable` kommentera ut definitionen av `results` och Avkommentera den här definitionen:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Kommentera ut definitionen av `refreshItemsFromMobileServiceTable`.
5. Ta bort kommentarerna definitionen av `refreshItemsFromMobileServiceTableSyncTable`:
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. Ta bort kommentarerna definitionen av `sync`:
   
        private AsyncTask<Void, Void, Void> sync() {
            AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        MobileServiceSyncContext syncContext = mClient.getSyncContext();
                        syncContext.push().get();
                        mToDoTable.pull(null).get();
                    } catch (final Exception e) {
                        createAndShowDialogFromTask(e, "Error");
                    }
                    return null;
                }
            };
            return runAsyncTask(task);
        }

## <a name="test-the-app"></a>Testa appen
I det här avsnittet testa fungerar med WiFi på och stäng sedan av WiFi att skapa ett offline-scenario.

När du lägger till data som de lagras i det lokala arkivet SQLite, men inte synkroniserats med den mobila tjänsten tills du trycker på **uppdatera** knappen. Andra appar kan ha olika krav angående när data måste synkroniseras, men för demonstration den här självstudiekursen har användaren explicit begära den.

När du trycker på knappen startar en ny bakgrundsuppgift. Den första skickar alla ändringar som gjorts i det lokala arkivet med hjälp av synkroniseringskontext hämtar alla ändrade data från Azure till lokala tabellen.

### <a name="offline-testing"></a>Testa offline
1. Placera enheten eller simulator i *Flygplansläge*. Detta skapar ett offline-scenario.
2. Lägga till några *ToDo* funktioner eller markera vissa objekt som slutförd. Avsluta enheten eller simulator (eller framtvingar stänga appen) och starta om. Kontrollera att dina ändringar har sparats på enheten eftersom de hade i det lokala arkivet SQLite.
3. Visa innehållet i Azure *TodoItem* tabell antingen med ett SQL-verktyg som *SQL Server Management Studio*, eller en REST-klient som *Fiddler* eller  *Postman*. Kontrollera att de nya objekt har *inte* har synkroniserats till servern
   
       + För Node.js-serverdel, gå till den [Azure-portalen](https://portal.azure.com/), och i din Mobilapp backend på **enkelt tabeller** > **TodoItem** att visa innehållet i `TodoItem`tabell.
       + Visa innehåll antingen med en SQL-verktyget som för en .NET-serverdel *SQL Server Management Studio*, eller en REST-klient som *Fiddler* eller *Postman*.
4. Aktivera WiFi i enheten eller simulatorn. Tryck sedan på den **uppdatera** knappen.
5. Visa TodoItem igen i Azure-portalen. Nya och ändrade TodoItems bör nu visas.

## <a name="additional-resources"></a>Ytterligare resurser
* [offlinesynkronisering Data i Azure Mobile Apps]
* [Molnet omfattar: Offlinesynkronisering i Azure Mobile Services] \(Obs: videon är på Mobile Services, men offlinesynkronisering fungerar på liknande sätt i Azure Mobile Apps\)

<!-- URLs. -->

[offlinesynkronisering Data i Azure Mobile Apps]: app-service-mobile-offline-data-sync.md

[skapa en Android-App]: app-service-mobile-android-get-started.md

[Molnet omfattar: Offlinesynkronisering i Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

