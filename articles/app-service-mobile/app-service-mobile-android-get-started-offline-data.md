---
title: Aktivera offlinesynkronisering (Android)
description: Lär dig hur du använder Mobilappar för apptjänst för att cachelagra och synkronisera offlinedata i ditt Android-program.
ms.assetid: 32a8a079-9b3c-4faf-8588-ccff02097224
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: c215105af5fe1ef8056b0d816cf2c2a6b96f2038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461631"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Aktivera offlinesynkronisering för din Android-mobilapp
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Översikt
Den här självstudien täcker synkroniseringsfunktionen offline i Azure Mobile Apps för Android. Offlinesynkronisering gör det möjligt&mdash;för slutanvändare att interagera&mdash;med en mobilapp som visar, lägger till eller ändrar data även när det inte finns någon nätverksanslutning. Ändringar lagras i en lokal databas. När enheten är online igen synkroniseras dessa ändringar med fjärrserveringen.

Om det här är din första upplevelse med Azure Mobile Apps bör du först slutföra självstudien [Skapa en Android-app]. Om du inte använder det hämtade snabbstartsserverprojektet måste du lägga till paketen för dataåtkomsttillägg i projektet. Mer information om servertilläggspaket finns i [Arbeta med .NET-server för server för server-server SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Mer information om synkroniseringsfunktionen offline finns i avsnittet [Offlinedatasynkning i Azure Mobile Apps].

## <a name="update-the-app-to-support-offline-sync"></a>Uppdatera appen så att den stöder offlinesynkronisering
Med offlinesynkronisering läser du till och skriver från en *synkroniseringstabell* (med *IMobileServiceSyncTable-gränssnittet),* som är en del av en **SQLite-databas** på enheten.

Om du vill skicka och hämta ändringar mellan enheten och Azure Mobile Services använder du en *synkroniseringskontext* *(MobileServiceClient.SyncContext*), som du initierar med den lokala databasen för att lagra data lokalt.

1. I `TodoActivity.java`kommenterar du den `mToDoTable` befintliga definitionen av och avkommentar synkroniseringstabellversionen:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. I `onCreate` metoden, kommentera ut den `mToDoTable` befintliga initieringen av och avkommenta denna definition:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. I `refreshItemsFromTable` kommentera ut `results` definitionen av och ta bortkommentar denna definition:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Kommentera definitionen av `refreshItemsFromMobileServiceTable`.
5. Avkommenta definitionen `refreshItemsFromMobileServiceTableSyncTable`av:
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. Avkommenta definitionen `sync`av:
   
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
I det här avsnittet testar du beteendet med WiFi aktiverat och inaktiverar sedan WiFi för att skapa ett offlinescenario.

När du lägger till dataobjekt lagras de i det lokala SQLite-arkivet, men synkroniseras inte med mobiltjänsten förrän du trycker på **knappen Uppdatera.** Andra appar kan ha olika krav på när data måste synkroniseras, men för demoändamål har den här självstudien användaren uttryckligen begära det.

När du trycker på den knappen startar en ny bakgrundsaktivitet. Den skickar först alla ändringar som gjorts till det lokala arkivet med hjälp av synkroniseringskontexten och hämtar sedan alla ändrade data från Azure till den lokala tabellen.

### <a name="offline-testing"></a>Offlinetestning
1. Placera enheten eller simulatorn i *flygplansläge*. Detta skapar ett offlinescenario.
2. Lägg till några *ToDo-objekt* eller markera vissa objekt som slutförd. Avsluta enheten eller simulatorn (eller stäng appen med tvång) och starta om. Kontrollera att ändringarna har sparats på enheten eftersom de finns i den lokala SQLite-butiken.
3. Visa innehållet i tabellen Azure *TodoItem,* antingen med ett SQL-verktyg som *SQL Server Management Studio*eller en REST-klient som *Fiddler* eller *Postman*. Kontrollera att de nya objekten *inte* har synkroniserats till servern
   
       + För en Node.js-backend går du till [Azure-portalen](https://portal.azure.com/)och klickar på **Enkla tabeller** > **TodoItem** i din mobile app för att visa innehållet i `TodoItem` tabellen.
       + För en .NET-serverdel visar du tabellinnehållet antingen med ett SQL-verktyg som *SQL Server Management Studio*eller en REST-klient som *Fiddler* eller *Postman*.
4. Slå på WiFi i enheten eller simulatorn. Tryck sedan på **knappen Uppdatera.**
5. Visa TodoItem-data igen i Azure-portalen. De nya och ändrade TodoItems ska nu visas.

## <a name="additional-resources"></a>Ytterligare resurser
* [Datasynkronisering offline i Azure Mobile Apps]
* [Cloud Cover: Offline-synkronisering i Azure Mobile Services] \(obs: videon är på mobila tjänster, men offlinesynkronisering fungerar på ett liknande sätt i Azure Mobile Apps\)

<!-- URLs. -->

[Datasynkronisering offline i Azure Mobile Apps]: app-service-mobile-offline-data-sync.md

[Skapa en Android-app]: app-service-mobile-android-get-started.md

[Cloud Cover: Offline-synkronisering i Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

