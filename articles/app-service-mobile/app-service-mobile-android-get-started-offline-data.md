---
title: Aktivera offline-synkronisering (Android)
description: Lär dig hur du använder App Service Mobile Apps för att cachelagra och synkronisera offline-data i Android-programmet.
ms.assetid: 32a8a079-9b3c-4faf-8588-ccff02097224
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: c215105af5fe1ef8056b0d816cf2c2a6b96f2038
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461631"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Aktivera synkronisering offline för din Android-mobilapp
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Översikt
I den här självstudien beskrivs funktionen offline-synkronisering i Azure Mobile Apps för Android. Offline-synkronisering innebär att slutanvändare kan interagera med en mobilapp&mdash;att visa, lägga till eller ändra data&mdash;även om det inte finns någon nätverks anslutning. Ändringarna lagras i en lokal databas. När enheten är online igen synkroniseras de här ändringarna med fjärrservern.

Om det här är din första upplevelse med Azure Mobile Apps bör du först slutföra självstudien [skapa en Android-app]. Om du inte använder det hämtade projektet för snabb starts Server måste du lägga till paket för data åtkomst tillägg i projektet. Mer information om Server tilläggs paket finns i [arbeta med .NET-Server del Server SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Mer information om funktionen offline-synkronisering finns i avsnittet [Datasynkronisering offline i Azure Mobile Apps].

## <a name="update-the-app-to-support-offline-sync"></a>Uppdatera appen så att den stöder offline-synkronisering
Med offlinesynkronisering kan du läsa och skriva från en *routningstabell* (med *IMobileServiceSyncTable* -gränssnittet), som är en del av en **sqlite** -databas på enheten.

Om du vill skicka och ta emot ändringar mellan enheten och Azure Mobile Services använder du en *synkroniseringsanslutning* (*MobileServiceClient. SyncContext*), som du initierar med den lokala databasen för att lagra data lokalt.

1. Kommentera den befintliga definitionen av `mToDoTable` i `TodoActivity.java`och ta bort kommentaren till Sync-tabellens version:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. I metoden `onCreate` kommenterar du ut den befintliga initieringen av `mToDoTable` och avkommenterar den här definitionen:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. I `refreshItemsFromTable` kommenterar du definitionen av `results` och avkommenterar den här definitionen:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Kommentera ut definitionen av `refreshItemsFromMobileServiceTable`.
5. Ta bort kommentar till definitionen av `refreshItemsFromMobileServiceTableSyncTable`:
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. Ta bort kommentar till definitionen av `sync`:
   
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
I det här avsnittet testar du beteendet med WiFi på och stänger sedan av WiFi för att skapa ett offline-scenario.

När du lägger till data objekt lagras de i den lokala SQLite-butiken, men synkroniseras inte till mobil tjänsten förrän du klickar på **Uppdatera** -knappen. Andra appar kan ha olika krav när det gäller när data behöver synkroniseras, men i demonstrations syfte har den här självstudien som användaren uttryckligen begär.

När du trycker på den knappen startar en ny bakgrunds aktivitet. Den skickar först alla ändringar som gjorts i det lokala arkivet med hjälp av kontexten för synkronisering och hämtar sedan alla ändrade data från Azure till den lokala tabellen.

### <a name="offline-testing"></a>Offline-testning
1. Placera enheten eller simulatorn i *flyg Plans läge*. Detta skapar ett offline-scenario.
2. Lägg till några att göra *-objekt eller markera några objekt som* slutförda. Avsluta enheten eller simulatorn (eller framtvinga stängning av appen) och starta om. Kontrol lera att dina ändringar har sparats på enheten eftersom de lagras i det lokala SQLite-arkivet.
3. Visa innehållet i Azure *TodoItem* -tabellen antingen med ett SQL-verktyg som *SQL Server Management Studio*eller en rest-klient, till exempel *Fiddler* eller *Postman*. Kontrol lera att de nya objekten *inte* har synkroniserats till servern
   
       + För en Node. js-backend går du till [Azure Portal](https://portal.azure.com/)och i Server delen för mobilappen klickar du på **enkla tabeller** > **TodoItem** för att visa innehållet i `TodoItem` tabellen.
       + För en .NET-Server del visar du tabell innehållet antingen med ett SQL-verktyg som *SQL Server Management Studio*eller en rest-klient, till exempel *Fiddler* eller *Postman*.
4. Aktivera WiFi i enheten eller simulatorn. Klicka sedan på knappen **Uppdatera** .
5. Visa TodoItem-data igen i Azure Portal. Den nya och ändrade TodoItems bör nu visas.

## <a name="additional-resources"></a>Ytterligare resurser
* [Datasynkronisering offline i Azure Mobile Apps]
* [Cloud Cover: offline-synkronisering i azure Mobile Services] \(Obs! videon är på Mobile Services, men offline-synkronisering fungerar på ett liknande sätt i Azure Mobile Apps\)

<!-- URLs. -->

[Datasynkronisering offline i Azure Mobile Apps]: app-service-mobile-offline-data-sync.md

[Skapa en Android-app]: app-service-mobile-android-get-started.md

[Cloud Cover: offline-synkronisering i Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

