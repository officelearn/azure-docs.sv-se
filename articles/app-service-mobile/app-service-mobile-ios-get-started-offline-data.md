---
title: Aktivera offline synkroniseras med iOS-mobilappar | Microsoft Docs
description: Lär dig hur du använder Azure Apptjänst mobile apps till cache och synkronisering av offlinedata i iOS-program.
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 1283f812799fe71ef6987dbc7fab092aed4d3417
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112658"
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Aktivera offline synkroniseras med iOS-appar
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Översikt
Den här självstudien tar upp offline synkroniseras med funktionen Mobile Apps i Azure App Service för iOS. Med offline synkroniserar slutanvändare kan interagera med en mobil app att visa, lägga till eller ändra data, även när de har någon nätverksanslutning. Ändringarna sparas i en lokal databas. När enheten är online igen, har ändringarna synkroniserats med remote backend-servern.

Om det här är din första erfarenhet med Mobile Apps, bör du först genomföra kursen [skapa en iOS-App]. Om du inte använder hämtade Snabbstart serverprojektet måste du lägga till tilläggspaket åtkomst till data i projektet. Läs mer om server-tilläggspaket [arbeta med SDK för .NET-serverdelen för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Mer information om funktionen offline-synkronisering finns [Synkronisering av offlinedata i mobila appar].

## <a name="review-sync"></a>Granska klientkod för synkronisering
Klientprojektet som du hämtade för den [skapa en iOS-App] självstudien innehåller redan kod som har stöd för offlinesynkronisering med hjälp av en lokal grundläggande information-baserad databas. Det här avsnittet sammanfattas vad som redan ingår i självstudiekursen koden. Läs en översikt över funktionen [Synkronisering av offlinedata i mobila appar].

Med funktionen offline med datasynkronisering med Mobile Apps kan kan slutanvändarna interagera med en lokal databas även när nätverket inte är tillgänglig. Om du vill använda dessa funktioner i din app måste du initiera synkronisering kontexten för `MSClient` och referera till ett lokalt Arkiv. Sedan du referera till tabellen via den **MSSyncTable** gränssnitt.

I **QSTodoService.m** (Objective-C) eller **ToDoTableViewController.swift** (Swift), Observera att typen av medlem **syncTable** är **MSSyncTable** . Offlinesynkronisering använder det här gränssnittet med sync tabell i stället för **MSTable**. När en sync-tabellen används alla åtgärder går du till det lokala arkivet och synkroniseras endast med remote serverdelen med explicita push och pull-åtgärder.

 Hämta en referens till en tabell för synkronisering med den **syncTableWithName** metoden på `MSClient`. Ta bort funktioner för offline-synkronisering med **tableWithName** i stället.

Innan tabellåtgärder kan utföras, måste det lokala arkivet initieras. Här är den relevanta koden:

* **Objective-C**. I den **QSTodoService.init** metod:

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **SWIFT**. I den **ToDoTableViewController.viewDidLoad** metod:

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Den här metoden skapar ett lokalt Arkiv med hjälp av den `MSCoreDataStore` gränssnitt, vilket ger SDK för Mobile Apps. Du kan även ange ett annat lokalt Arkiv genom att implementera den `MSSyncContextDataSource` protokoll. Dessutom den första parametern i **MSSyncContext** används för att ange en konflikt hanterare. Eftersom vi har klarat `nil`, vi får standard konflikt hanteraren misslyckas vid eventuella konflikter.

Nu ska vi utföra den faktiska synkroniseringsåtgärden och hämta data från fjärranslutna backend-servern:

* **Objective-C**. `syncData` först skickar nya ändringar och anropar sedan **pullData** att hämta data från fjärranslutna backend-servern. I sin tur den **pullData** metoden hämtar nya data som matchar en fråga:

   ```objc
   -(void)syncData:(QSCompletionBlock)completion
   {
       // Push all changes in the sync context, and then pull new data.
       [self.client.syncContext pushWithCompletion:^(NSError *error) {
           [self logErrorIfNotNil:error];
           [self pullData:completion];
       }];
   }

   -(void)pullData:(QSCompletionBlock)completion
   {
       MSQuery *query = [self.syncTable query];

       // Pulls data from the remote server into the local table.
       // We're pulling all items and filtering in the view.
       // Query ID is used for incremental sync.
       [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
           [self logErrorIfNotNil:error];

           // Lets the caller know that we have finished.
           if (completion != nil) {
               dispatch_async(dispatch_get_main_queue(), completion);
           }
       }];
   }
   ```
* **Swift**:
   ```swift
   func onRefresh(sender: UIRefreshControl!) {
      UIApplication.sharedApplication().networkActivityIndicatorVisible = true

      self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
          (error) -> Void in

          UIApplication.sharedApplication().networkActivityIndicatorVisible = false

          if error != nil {
              // A real application would handle various errors like network conditions,
              // server conflicts, etc. via the MSSyncContextDelegate
              print("Error: \(error!.description)")

              // We will discard our changes and keep the server's copy for simplicity
              if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                  for opError in opErrors {
                      print("Attempted operation to item \(opError.itemId)")
                      if (opError.operation == .Insert || opError.operation == .Delete) {
                          print("Insert/Delete, failed discarding changes")
                          opError.cancelOperationAndDiscardItemWithCompletion(nil)
                      } else {
                          print("Update failed, reverting to server's copy")
                          opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
                      }
                  }
              }
          }
          self.refreshControl?.endRefreshing()
      }
   }
   ```

I Objective-C-versionen i `syncData`, vi först anropa **pushWithCompletion** på sync-kontext. Den här metoden är medlem i `MSSyncContext` (och inte själva tabellen sync) eftersom det skickar ändringar över alla tabeller. Endast de poster som har ändrats på något sätt lokalt (via CUD åtgärder) skickas till servern. Sedan helper **pullData** anropas, som anropar **MSSyncTable.pullWithQuery** till hämta fjärrdata och lagra den i den lokala databasen.

I Swift version, eftersom den utgivarinitierade åtgärden inte var absolut nödvändigt finns inga anrop till **pushWithCompletion**. Om det finns alla väntande ändringar i synkronisering kontexten för den tabell som gör en push-åtgärd, skickar pull alltid en push först. Om du har mer än en synkronisering tabell, är det dock bäst att explicit anropa push så att allt är konsekvent mellan relaterade tabeller.

I både Objective-C och Swift versioner kan du använda den **pullWithQuery** metod för att ange en fråga för att filtrera poster som ska hämtas. I det här exemplet returneras alla poster i fjärransluten `TodoItem` tabell.

Den andra parametern för **pullWithQuery** är en fråge-ID som används för *inkrementell synkronisering*. Inkrementell synkronisering hämtar endast de poster som har ändrats sedan den senaste synkroniseringen, från posten `UpdatedAt` tidsstämpel (kallas `updatedAt` i det lokala arkivet.) Fråge-ID ska vara en beskrivande sträng som är unik för varje logisk fråga i din app. Om du vill välja bort inkrementell synkronisering, skicka `nil` som frågan-ID. Den här metoden kan vara potentiellt ineffektiv eftersom den hämtar alla poster på varje pull-åtgärd.

Appen Objective-C synkroniserar när du ändrar eller lägger till data, när en användare utför gest för uppdatering och vid start.

Swift appen synkroniseras när användaren utför en uppdatering gest och vid start.

Eftersom appen synkroniseringar när data har ändrats (Objective-C), eller när appen startar (Objective-C och Swift), förutsätter appen att användaren är online. I ett senare avsnitt kommer du att uppdatera appen så att användare kan redigera även när de är offline.

## <a name="review-core-data"></a>Granska Core datamodellen
När du använder grundläggande Data offline store måste du definiera specifika tabeller och fält i datamodellen. Exempelappen innehåller redan en datamodell med rätt format. I det här avsnittet ska går vi igenom dessa tabeller för att visa hur de används.

Öppna **QSDataModel.xcdatamodeld**. Fyra tabeller definieras--tre som används av SDK och en som används för att göra-uppgiften objekt själva:
  * MS_TableOperations: Spårar de objekt som ska synkroniseras med servern.
  * MS_TableOperationErrors: Spårar eventuella fel som inträffar under offlinesynkronisering.
  * MS_TableConfig: Spårar senast uppdaterat tid för senaste synkroniseringsåtgärden för alla pull-åtgärder.
  * TodoItem: Lagrar att göra-objekt. Systemkolumner **createdAt**, **updatedAt**, och **version** är valfritt Systemegenskaper.

> [!NOTE]
> SDK för Mobile Apps reserverar kolumnnamn som börjar med ”**``**”. Använd inte det här prefixet med något annat än Systemkolumner. I annat fall ändras kolumnnamn som när du använder fjärranslutna backend-servern.
>
>

När du använder funktionen offline-synkronisering kan du definiera tre systemtabellerna och datatabellen.

### <a name="system-tables"></a>Systemtabeller

**MS_TableOperations**  

![MS_TableOperations tabellattribut][defining-core-data-tableoperations-entity]

| Attribut | Typ |
| --- | --- |
| id | Heltal 64 |
| itemId | String |
| properties | Binära Data |
| tabell | String |
| tableKind | Heltal 16 |


**MS_TableOperationErrors**

 ![MS_TableOperationErrors tabellattribut][defining-core-data-tableoperationerrors-entity]

| Attribut | Typ |
| --- | --- |
| id |String |
| operationId |Heltal 64 |
| properties |Binära Data |
| tableKind |Heltal 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Attribut | Typ |
| --- | --- |
| id |String |
| key |String |
| KeyType |Heltal 64 |
| tabell |String |
| value |String |

### <a name="data-table"></a>Datatabell

**TodoItem**

| Attribut | Typ | Obs! |
| --- | --- | --- |
| id | Sträng, markeras krävs |primärnyckeln i fjärrlager |
| Slutför | Boolean | Fältet för att göra-objekt |
| text |String |Fältet för att göra-objekt |
| createdAt | Date | (valfritt) Mappar till **createdAt** Systemegenskapen |
| updatedAt | Date | (valfritt) Mappar till **updatedAt** Systemegenskapen |
| version | String | (valfritt) Används för att identifiera konflikter, mappas till version |

## <a name="setup-sync"></a>Ändra beteendet för synkronisering av appen
I det här avsnittet ska ändra du appen, så att den inte synkroniserar på app-start eller när du infogar och uppdatera objekt. Det synkroniserar bara när knappen Uppdatera gest utförs.

**Objective-C**:

1. I **QSTodoListViewController.m**, ändra den **viewDidLoad** metod för att ta bort anropet till `[self refresh]` i slutet av metoden. Data är nu inte synkroniserat med servern på app-start. I stället synkroniseras den med innehållet i det lokala arkivet.
2. I **QSTodoService.m**, ändra definitionen av `addItem` så att den inte synkroniseras när objektet infogas. Ta bort den `self syncData` blockera och Ersätt den med följande:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Ändra definitionen av `completeItem` som tidigare nämnts. Ta bort blockeringen för `self syncData` och Ersätt den med följande:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Swift**:

I `viewDidLoad`i **ToDoTableViewController.swift**, kommentera ut de två raderna som visas här, för att stoppa synkroniseringen på app-start. När detta skrivs uppdateras Swift Todo-appen inte tjänsten när någon lägger till eller Slutför en artikel. Uppdaterar tjänsten endast på app-start.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>Testa appen
I det här avsnittet kan du ansluta till en ogiltig URL för att simulera ett offline-scenario. När du lägger till dataobjekt, är de som lagras i lokalt Core datalager, men de är inte synkroniserat med serverdelen för mobilappar.

1. Ändra URL-Adressen för mobilappar i **QSTodoService.m** till en ogiltig URL och kör app igen:

   **Objective-C**. I QSTodoService.m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **SWIFT**. In ToDoTableViewController.swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Lägg till några att-göra-objekt. Avsluta simulatorn (eller tvång stänga appen) och sedan starta om den. Kontrollera att spara dina ändringar.

3. Visa innehållet i fjärransluten **TodoItem** tabell:
   * För en Node.js-serverdelen går du till den [Azure-portalen](https://portal.azure.com/) i din serverdel för mobilappar, klickar du på **enkla tabeller** > **TodoItem**.  
   * För en .NET tillbaka sluta, Använd en SQL-verktyg, till exempel SQL Server Management Studio eller en REST-klient, till exempel Fiddler eller Postman.  

4. Kontrollera att de nya objekt har *inte* har synkroniserats med servern.

5. Ändra URL-Adressen till rätta i **QSTodoService.m**, och kör appen.

6. Utföra en uppdatering gest genom att hämta listan över objekt.  
En rotationsruta förloppet visas.

7. Visa den **TodoItem** data igen. Nya och ändrade att göra-objekt ska nu visas.

## <a name="summary"></a>Sammanfattning
För att stödja funktionen offline-synkronisering, vi använde den `MSSyncTable` gränssnitt och initiera `MSClient.syncContext` med ett lokalt Arkiv. I det här fallet har det lokala arkivet en grundläggande information-baserad databas.

När du använder ett lokalt Arkiv grundläggande information, måste du definiera flera tabeller med den [korrigera Systemegenskaper](#review-core-data).

Normal skapa, läsa, uppdatera och ta bort CRUD-åtgärder för mobilappar fungerar som om appen är fortfarande ansluten, men alla åtgärder som inträffar mot det lokala arkivet.

När vi synkroniseras det lokala arkivet med servern, som vi använde den **MSSyncTable.pullWithQuery** metod.

## <a name="additional-resources"></a>Ytterligare resurser
* [Synkronisering av offlinedata i mobila appar]
* [Cloud Cover: Offline-synkronisering i Azure mobila tjänster] \(videon handlar om mobiltjänster, men fungerar på liknande sätt som synkronisering med Mobile Apps offline.\)

<!-- URLs. -->


[Skapa en iOS-App]: app-service-mobile-ios-get-started.md
[Synkronisering av offlinedata i mobila appar]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloud Cover: Offline-synkronisering i Azure mobila tjänster]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
