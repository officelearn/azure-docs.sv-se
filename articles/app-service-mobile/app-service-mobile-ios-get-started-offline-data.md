---
title: Aktivera offline synkroniserar med iOS-appar | Microsoft Docs
description: "Lär dig hur du använder Azure Apptjänst mobilappar till cache och synkronisera offlinedata i iOS-program."
documentationcenter: ios
author: conceptdev
manager: crdun
editor: 
services: app-service\mobile
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: b676b51241e4883fb1b4c40caba8e281bfa68a4c
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2018
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Aktivera offline synkroniserar med iOS-appar
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Översikt
Den här kursen ingår offline synkroniserar med funktionen Mobilappar i Azure App Service för iOS. Med offline synkroniserar slutanvändare kan interagera med en mobil app att visa, lägga till eller ändra data, även om de har någon nätverksanslutning. Ändringarna sparas i en lokal databas. Ändringarna har synkroniserats med fjärråtkomst serverdelen när enheten är online igen.

Om det här är din första upplevelse med Mobilappar, bör du först slutföra kursen [skapa en iOS-App]. Om du inte använder hämtade Snabbkurs serverprojekt, måste du lägga till tilläggspaket dataåtkomst projektet. Mer information om server tilläggspaket finns [arbeta med serverdelen .NET SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Mer information om funktionen offlinesynkronisering finns [offlinesynkronisering Data i Mobile Apps].

## <a name="review-sync"></a>Granska klientkod för synkronisering
Klientprojektet som du hämtade för den [skapa en iOS-App] kursen redan innehåller kod som har stöd för offlinesynkronisering med hjälp av en lokal databas för kärnor databaserad. Det här avsnittet sammanfattas ingår redan i självstudiekursen koden. En översikt över funktionen finns [offlinesynkronisering Data i Mobile Apps].

Funktionen datasynkronisering offline för Mobile Apps kan interagera slutanvändarna med en lokal databas även om nätverket inte är tillgänglig. För att använda funktionerna i din app måste du initiera synkronisering kontexten för `MSClient` och referera till ett lokalt Arkiv. Sedan du refererar till tabellen via den **MSSyncTable** gränssnitt.

I **QSTodoService.m** (Objective-C) eller **ToDoTableViewController.swift** (Swift), Lägg märke till att typen av medlem **syncTable** är **MSSyncTable** . Offlinesynkronisering använder sync tabell gränssnittet i stället för **MSTable**. När en tabell med synkronisering används alla åtgärder går du till det lokala arkivet och synkroniseras med fjärråtkomst serverdelen med explicit åtgärder för sändning och mottagning.

 Om du vill hämta en referens till en tabell för synkronisering, Använd den **syncTableWithName** metod på `MSClient`. Ta bort offlinesynkronisering funktioner med **tableWithName** i stället.

Det lokala arkivet måste initieras innan alla tabellåtgärder kan utföras. Här är den relevanta koden:

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
   Den här metoden skapar ett lokalt Arkiv med hjälp av den `MSCoreDataStore` gränssnitt, som ger Mobile Apps-SDK. Du kan också ange ett annat lokalt Arkiv genom att implementera den `MSSyncContextDataSource` protokoll. Dessutom den första parametern för **MSSyncContext** används för att ange en konflikt-hanterare. Eftersom vi har klarat `nil`, vi hämta konflikt hanteraren, som inte på någon konflikt.

Nu ska vi utföra den faktiska synkroniseringsåtgärden och hämta data från fjärranslutna serverdelen:

* **Objective-C**. `syncData`först skickar nya ändringar och anropar sedan **pullData** att hämta data från fjärranslutna serverdelen. I sin tur den **pullData** metoden hämtar nya data som matchar en fråga:

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
* **SWIFT**:
   ```swift
   func onRefresh(sender: UIRefreshControl!) {
      UIApplication.sharedApplication().networkActivityIndicatorVisible = true

      self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
          (error) -> Void in

          UIApplication.sharedApplication().networkActivityIndicatorVisible = false

          if error != nil {
              // A real application would handle various errors like network conditions,
              // server conflicts, etc via the MSSyncContextDelegate
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

I Objective-C-versionen i `syncData`, vi först anropa **pushWithCompletion** på sync-kontext. Den här metoden är medlem i `MSSyncContext` (och inte själva tabellen sync) eftersom den skickar ändringarna över alla tabeller. Endast de poster som har ändrats på något sätt lokalt (via CUD operations) skickas till servern. Sedan helper **pullData** anropas, som anropar **MSSyncTable.pullWithQuery** till hämta fjärrdata och lagra den i den lokala databasen.

I Swift version, eftersom den utgivarinitierade åtgärden inte var absolut nödvändigt finns inga anrop till **pushWithCompletion**. Om det finns några väntande ändringar i sync-kontexten för den tabell som gör en push-åtgärd, utfärdar pull alltid en push först. Om du har mer än en tabell för synkronisering, är det dock bäst att explicit anropa push så att allt är konsekvent på relaterade tabeller.

I både Objective-C och Swift versioner kan du använda den **pullWithQuery** metod för att ange en fråga för att filtrera poster som ska hämtas. I det här exemplet frågan hämtar alla poster i fjärransluten `TodoItem` tabell.

Den andra parametern för **pullWithQuery** är en fråge-ID som används för *inkrementell synkronisering*. Inkrementell synkronisering hämtar poster som har ändrats sedan den senaste synkroniseringen från posten `UpdatedAt` tidsstämpeln (kallas `updatedAt` i det lokala arkivet.) Fråge-ID ska vara en beskrivande sträng som är unik för varje logisk fråga i din app. Om du vill välja bort inkrementell synkronisering, skicka `nil` som frågan-ID. Den här metoden kan vara potentiellt ineffektiv eftersom den hämtar alla poster på varje pull-åtgärd.

Objective-C-app som ska synkroniseras när du ändrar eller lägger till data, när användaren utför en uppdatering gest och startas.

Swift appen synkroniseras när användaren utför en uppdatering gest och startas.

Eftersom app synkroniseringar när data har ändrats (Objective-C) eller när appen startar (Objective-C och Swift) förutsätter appen att användaren är online. I ett senare avsnitt ska du uppdatera appen så att användare kan redigera även när de är offline.

## <a name="review-core-data"></a>Granska Core datamodellen
När du använder Core-offline datalagret, måste du definiera specifika tabeller och fält i datamodellen. Exempelappen innehåller redan en datamodell med rätt format. I det här avsnittet går vi igenom dessa tabeller för att visa hur de används.

Öppna **QSDataModel.xcdatamodeld**. Fyra tabeller definieras--tre som används av SDK och en som används för uppgiften objekt själva:
  * MS_TableOperations: Spårar objekt som ska synkroniseras med servern.
  * MS_TableOperationErrors: Spårar alla fel som inträffar under offlinesynkronisering.
  * MS_TableConfig: Spårar senaste uppdaterade tid för den senaste synkroniseringen för alla pull-åtgärder.
  * TodoItem: Lagrar att göra-objekt. Systemkolumner **createdAt**, **updatedAt**, och **version** är valfria Systemegenskaper.

> [!NOTE]
> Mobile Apps-SDK reserverar kolumnnamn som börjar med ”**``**”. Använd inte det här prefixet med något annat än Systemkolumner. Annars ändras kolumnnamn som när du använder fjärråtkomst serverdelen.
>
>

När du använder funktionen offlinesynkronisering definiera tre systemtabellerna och tabellen.

### <a name="system-tables"></a>Systemtabeller

**MS_TableOperations**  

![MS_TableOperations attribut][defining-core-data-tableoperations-entity]

| Attribut | Typ |
| --- | --- |
| id | Heltal 64 |
| itemId | Sträng |
| properties | Binära Data |
| tabell | Sträng |
| tableKind | Heltal 16 |


**MS_TableOperationErrors**

 ![MS_TableOperationErrors attribut][defining-core-data-tableoperationerrors-entity]

| Attribut | Typ |
| --- | --- |
| id |Sträng |
| Åtgärds-ID |Heltal 64 |
| properties |Binära Data |
| tableKind |Heltal 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Attribut | Typ |
| --- | --- |
| id |Sträng |
| key |Sträng |
| KeyType |Heltal 64 |
| tabell |Sträng |
| värde |Sträng |

### <a name="data-table"></a>Datatabell

**TodoItem**

| Attribut | Typ | Obs! |
| --- | --- | --- |
| id | Strängen som markerats krävs |primärnyckeln i fjärranslutna store |
| Slutför | Boolesk | Fältet för att göra-objekt |
| Text |Sträng |Fältet för att göra-objekt |
| CreatedAt | Date | (valfritt) Mappar till **createdAt** Systemegenskapen |
| updatedAt | Date | (valfritt) Mappar till **updatedAt** Systemegenskapen |
| version | Sträng | (valfritt) Används för att identifiera konflikter, mappas till version |

## <a name="setup-sync"></a>Ändra beteendet för synkronisering av appen
I det här avsnittet kan ändra du appen så att den inte synkroniseras på appen startas eller när du infogar och uppdatera objekt. Det synkroniserar bara när knappen Uppdatera gest utförs.

**Objective-C**:

1. I **QSTodoListViewController.m**, ändra den **viewDidLoad** metod för att ta bort anropet till `[self refresh]` i slutet av metoden. Nu data inte har synkroniserats med servern på appen startas. I stället är den synkroniserad med innehållet i det lokala arkivet.
2. I **QSTodoService.m**, ändra definitionen av `addItem` så att den inte synkronisera efter objektet infogas. Ta bort den `self syncData` blockera och Ersätt den med följande:

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

**SWIFT**:

I `viewDidLoad`i **ToDoTableViewController.swift**, kommentera ut två rader som visas här, för att stoppa synkroniseringen appen startas. När detta skrivs uppdaterar Swift Todo-appen inte tjänsten när någon lägger till eller ett objekt slutförs. Tjänsten endast på programstart uppdateras.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>Testa appen
I det här avsnittet kan du ansluta till en ogiltig URL för att simulera ett offline-scenario. När du lägger till data som de ska lagras i lokalt Core datalager, men de är inte synkroniserade med mobilapp serverdelen.

1. Ändra URL mobilapp i **QSTodoService.m** till en ogiltig URL och kör app igen:

   **Objective-C**. I QSTodoService.m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **SWIFT**. I ToDoTableViewController.swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Lägga till vissa arbetsuppgifter. Avsluta simulatorn (eller framtvingar stänga appen) och starta om den. Kontrollera att spara dina ändringar.

3. Visa innehållet i fjärransluten **TodoItem** tabell:
   * För en Node.js-serverdel går du till den [Azure-portalen](https://portal.azure.com/) och på din mobila app serverdel **enkelt tabeller** > **TodoItem**.  
   * Avsluta, använda ett SQL-verktyg, till exempel SQL Server Management Studio eller en REST-klient, till exempel Fiddler eller Postman för en .NET tillbaka.  

4. Kontrollera att de nya objekt har *inte* har synkroniserats med servern.

5. Ändra URL tillbaka till det rätta i **QSTodoService.m**, och kör appen.

6. Utföra en uppdatering gest genom att dra nedåt i listan över objekt.  
En rotationsruta förloppet visas.

7. Visa den **TodoItem** data igen. Nya och ändrade arbetsuppgifter ska nu visas.

## <a name="summary"></a>Sammanfattning
För att stödja funktionen offlinesynkronisering vi använde den `MSSyncTable` gränssnitt och initieras `MSClient.syncContext` med ett lokalt Arkiv. I detta fall var det lokala arkivet en Core databaserad databas.

När du använder ett lokalt Arkiv grundläggande information, måste du definiera flera tabeller med den [korrigera Systemegenskaper](#review-core-data).

Normal skapa, läsa, uppdatera och ta bort CRUD-åtgärder för mobilappar fungerar som om appen fortfarande är ansluten, men alla åtgärder mot det lokala arkivet.

När vi synkroniseras det lokala arkivet med servern som vi använde den **MSSyncTable.pullWithQuery** metod.

## <a name="additional-resources"></a>Ytterligare resurser
* [offlinesynkronisering Data i Mobile Apps]
* [Molnet omfattar: Offlinesynkronisering i Azure Mobile Services] \(videon är om Mobile Services, men Mobile Apps offline synkroniseras fungerar på liknande sätt.\)

<!-- URLs. -->


[skapa en iOS-App]: app-service-mobile-ios-get-started.md
[offlinesynkronisering Data i Mobile Apps]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Molnet omfattar: Offlinesynkronisering i Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/en-us/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
