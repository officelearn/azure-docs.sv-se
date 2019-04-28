---
title: Aktivera synkronisering offline för din Azure-Mobilapp (Android)
description: Lär dig hur du använder App Service Mobile Apps för att cache och synkronisering av offlinedata i din Android-App
documentationcenter: android
author: conceptdev
manager: crdun
services: app-service\mobile
ms.assetid: 32a8a079-9b3c-4faf-8588-ccff02097224
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: a20c79acce8c9dc9051651a0473fd07b8e62f5de
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126910"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Aktivera synkronisering offline för din Android-mobilapp
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Översikt
Den här självstudien beskriver funktionen offline-synkronisering i Azure Mobile Apps för Android. Offlinesynkronisering kan slutanvändarna kan interagera med en mobil app&mdash;visa, lägga till eller ändra data&mdash;även om det inte finns någon nätverksanslutning. Ändringarna sparas i en lokal databas. När enheten är online igen kan har de här ändringarna synkroniserats med fjärr-serverdel.

Om det här är din första erfarenhet med Azure Mobile Apps, bör du först genomföra kursen [skapa en Android-App]. Om du inte använder serverprojekt hämtade Snabbstart, måste du lägga till data access-tillägg-paket i projektet. Läs mer om server-tilläggspaket [arbeta med SDK för .NET-serverdelen för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Mer information om funktionen offline-synkronisering, finns i avsnittet [Datasynkronisering offline i Azure Mobile Apps].

## <a name="update-the-app-to-support-offline-sync"></a>Uppdatera appen för att stödja offlinesynkronisering
Med offlinesynkronisering kan du läsa till och skriva från en *synkronisering tabell* (med hjälp av den *IMobileServiceSyncTable* interface), som ingår i en **SQLite** databasen på din enhet.

Om du vill skicka och hämta ändringar mellan enheten och Azure Mobile Services, som du använder en *synkroniseringskontext* (*MobileServiceClient.SyncContext*), som du initiera med den lokala databasen att lagra data lokalt.

1. I `TodoActivity.java`, kommentera ut den befintliga definitionen av `mToDoTable` och ta bort kommentarerna synkroniserade tabell versionen:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. I den `onCreate` metod, kommentera ut befintliga initieringen av `mToDoTable` och ta bort den här definitionen:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. I `refreshItemsFromTable` kommentera ut definitionen av `results` och ta bort den här definitionen:
   
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
I det här avsnittet, testa beteende med Wi-Fi på och inaktiverar Wi-Fi att skapa ett offline-scenario.

När du lägger till dataobjekt de lagras i det lokala arkivet SQLite, men inte har synkroniserats till den mobila tjänsten tills du trycker på den **uppdatera** knappen. Andra appar kan ha olika krav angående när data ska synkroniseras, men för demonstration den här självstudien har du uttryckligen begära den.

När du trycker på knappen startar en ny bakgrundsuppgift. Först skickar den alla ändringar som gjorts på det lokala arkivet med synkroniseringskontext och sedan hämtar alla ändrats data från Azure till den lokala tabellen.

### <a name="offline-testing"></a>Offlinetestning
1. Placera enheten eller simulatorn i *Flygplansläge*. Detta skapar ett offline-scenario.
2. Lägg till några *ToDo* objekt eller markera vissa objekt som slutförd. Avsluta den enheten eller simulatorn (eller tvång stänga appen) och starta om. Kontrollera att dina ändringar har sparats på enheten eftersom de lagras i det lokala arkivet sqlite-felkod.
3. Visa innehållet i Azure *TodoItem* tabellen antingen med ett SQL-verktyg som *SQL Server Management Studio*, eller en REST-klient som *Fiddler* eller  *Postman*. Kontrollera att de nya objekt har *inte* har synkroniserats till servern
   
       + För Node.js-serverdel, går du till den [Azure-portalen](https://portal.azure.com/), och i Mobilappen på serverdelen **enkla tabeller** > **TodoItem** att visa innehållet i `TodoItem`tabell.
       + Visa innehåll antingen med en SQL-verktyget som för en .NET-serverdel *SQL Server Management Studio*, eller en REST-klient som *Fiddler* eller *Postman*.
4. Aktivera WiFi i enheten eller simulatorn. Tryck sedan på den **uppdatera** knappen.
5. Visa TodoItem igen i Azure-portalen. Nya och ändrade TodoItems bör nu visas.

## <a name="additional-resources"></a>Ytterligare resurser
* [Datasynkronisering offline i Azure Mobile Apps]
* [Cloud Cover: Offline-synkronisering i Azure mobila tjänster] \(Obs: videon är på mobiltjänster, men offlinesynkronisering fungerar på liknande sätt i Azure Mobile Apps\)

<!-- URLs. -->

[Datasynkronisering offline i Azure Mobile Apps]: app-service-mobile-offline-data-sync.md

[Skapa en Android-App]: app-service-mobile-android-get-started.md

[Cloud Cover: Offline-synkronisering i Azure mobila tjänster]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

