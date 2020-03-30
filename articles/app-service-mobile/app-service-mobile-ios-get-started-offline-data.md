---
title: Aktivera offlinesynkronisering (iOS)
description: Lär dig hur du använder Azure App Service-mobilappar för att cachelagra och synkronisera offlinedata i iOS-program.
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: d943213814b999f101a541abb0195a9fdd5a7423
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459182"
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Aktivera offlinesynkronisering med iOS-mobilappar
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Översikt
Den här självstudien omfattar offlinesynkronisering med funktionen Mobilappar i Azure App Service för iOS. Med offlinesynkronisering kan slutanvändare interagera med en mobilapp för att visa, lägga till eller ändra data, även när de inte har någon nätverksanslutning. Ändringar lagras i en lokal databas. När enheten är online igen synkroniseras ändringarna med fjärrryggen.

Om detta är din första upplevelse med mobilappar bör du först slutföra självstudien [Skapa en iOS-app]. Om du inte använder det hämtade snabbstartsserverprojektet måste du lägga till tilläggspaketen för dataåtkomst i projektet. Mer information om servertilläggspaket finns i [Arbeta med .NET-server för server för server-server SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Mer information om synkroniseringsfunktionen offline finns [i Offlinedatasynkronisering i mobilappar].

## <a name="review-the-client-sync-code"></a><a name="review-sync"></a>Granska klientsynkroniseringskoden
Klientprojektet som du hämtade för självstudien [Skapa en iOS-app] innehåller redan kod som stöder offlinesynkronisering med hjälp av en lokal Core-databaserad databas. Det här avsnittet sammanfattar vad som redan ingår i självstudiekoden. En begreppsmässig översikt över funktionen finns [i Offlinedatasynkronisering i mobilappar].

Med hjälp av funktionen för offlinedatasynkronisering i mobilappar kan slutanvändare interagera med en lokal databas även när nätverket är otillgängligt. Om du vill använda dessa funktioner i appen `MSClient` initierar du synkroniseringskontexten för och refererar till en lokal butik. Sedan refererar du till tabellen via **MSSyncTable-gränssnittet.**

I **QSTodoService.m** (Objective-C) eller **ToDoTableViewController.swift** (Swift) observerar du att typen av **medlemssynkronisering** är **MSSyncTable**. Offlinesynkronisering använder det här synkroniseringstabellgränssnittet i stället för **MSTable**. När en synkroniseringstabell används går alla åtgärder till det lokala arkivet och synkroniseras endast med fjärrbakdelen med explicita push- och pull-åtgärder.

 Om du vill hämta en referens till en synkroniseringstabell använder du metoden **syncTableWithName** på `MSClient`. Om du vill ta bort offlinesynkroniseringsfunktioner använder du **tableWithName** i stället.

Innan några tabellåtgärder kan utföras måste det lokala arkivet initieras. Här är den relevanta koden:

* **Mål C**. I **QSTodoService.init-metoden:**

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **Snabbt.** I metoden **ToDoTableViewController.viewDidLoad:**

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Den här metoden skapar en `MSCoreDataStore` lokal butik med hjälp av gränssnittet, som Mobile Apps SDK tillhandahåller. Du kan också tillhandahålla ett annat lokalt `MSSyncContextDataSource` arkiv genom att implementera protokollet. Dessutom används den första parametern i **MSSyncContext** för att ange en konflikthanterare. Eftersom vi `nil`har passerat får vi standardkonflikthanteraren, som misslyckas på någon konflikt.

Nu ska vi utföra den faktiska synkroniseringen och hämta data från fjärrryggen:

* **Mål C**. `syncData`först skjuter nya ändringar och sedan samtal **pullData** för att få data från fjärrkontrollen back end. PullData-metoden **pullData** får i sin tur nya data som matchar en fråga:

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
* **Swift:**
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

I Objective-C-versionen, `syncData`i, kallar vi först **pushWithCompletion** på synkroniseringskontexten. Den här metoden `MSSyncContext` är medlem i (och inte själva synkroniseringstabellen) eftersom den skickar ändringar över alla tabeller. Endast poster som har ändrats på något sätt lokalt (via CUD-åtgärder) skickas till servern. Då den hjälpare **pullData** kallas, som kallar **MSSyncTable.pullWithQuery** att hämta fjärrdata och lagra den i den lokala databasen.

I Swift-versionen, eftersom push-åtgärden inte var absolut nödvändig, finns det ingen uppmaning att **pushWithCompletion**. Om det finns några ändringar som väntar i synkroniseringskontexten för tabellen som gör en push-åtgärd, drar du alltid problem med en push först. Men om du har mer än en synkroniseringstabell är det bäst att uttryckligen anropa push för att säkerställa att allt är konsekvent över relaterade tabeller.

I både Objective-C- och Swift-versionerna kan du använda metoden **pullWithQuery** för att ange en fråga för att filtrera de poster som du vill hämta. I det här exemplet hämtar frågan alla `TodoItem` poster i fjärrtabellen.

Den andra parametern **för pullWithQuery** är ett fråge-ID som används för *inkrementell synkronisering*. Inkrementell synkronisering hämtar endast poster som har ändrats `UpdatedAt` sedan den `updatedAt` senaste synkroniseringen med postens tidsstämpel (anropad i den lokala arkivet.) Fråge-ID:et ska vara en beskrivande sträng som är unik för varje logisk fråga i appen. Om du vill välja bort `nil` inkrementell synkronisering skickar du som fråge-ID. Den här metoden kan vara potentiellt ineffektiv, eftersom den hämtar alla poster på varje pull-åtgärd.

Objective-C-appen synkroniseras när du ändrar eller lägger till data när en användare utför uppdateringsgesten och vid start.

Swift-appen synkroniseras när användaren utför uppdateringsgesten och vid start.

Eftersom appen synkroniseras när data ändras (Objective-C) eller när appen startar (Objective-C och Swift) förutsätter appen att användaren är online. I ett senare avsnitt uppdaterar du appen så att användarna kan redigera även när de är offline.

## <a name="review-the-core-data-model"></a><a name="review-core-data"></a>Granska kärndatamodellen
När du använder offlinearkivet Core Data måste du definiera särskilda tabeller och fält i datamodellen. Exempelappen innehåller redan en datamodell med rätt format. I det här avsnittet går vi igenom dessa tabeller för att visa hur de används.

Öppna **QSDataModel.xcdatamodeld**. Fyra tabeller definieras - tre som används av SDK och en som används för att göra-objekt själva:
  * MS_TableOperations: Spårar de objekt som måste synkroniseras med servern.
  * MS_TableOperationErrors: Spårar alla fel som inträffar under offlinesynkronisering.
  * MS_TableConfig: Spårar den senast uppdaterade tiden för den senaste synkroniseringsåtgärden för alla pull-åtgärder.
  * TodoItem: Lagrar att göra-objekt. De systemkolumner **som skapasAt**, **updatedAt**och **version** är valfria systemegenskaper.

> [!NOTE]
> Mobile Apps SDK reserverar kolumnnamn**``** som börjar med " ". Använd inte det här prefixet med något annat än systemkolumner. Annars ändras kolumnnamnen när du använder fjärrserverdelen.
>
>

När du använder offlinesynkroniseringsfunktionen definierar du de tre systemtabellerna och datatabellen.

### <a name="system-tables"></a>Systemtabeller

**MS_TableOperations**  

![MS_TableOperations tabellattribut][defining-core-data-tableoperations-entity]

| Attribut | Typ |
| --- | --- |
| id | Heltal 64 |
| Itemid | String |
| properties | Binära data |
| tabell | String |
| tabellKind | Heltal 16 |


**MS_TableOperationErrors**

 ![MS_TableOperationErrors tabellattribut][defining-core-data-tableoperationerrors-entity]

| Attribut | Typ |
| --- | --- |
| id |String |
| operationId |Heltal 64 |
| properties |Binära data |
| tabellKind |Heltal 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Attribut | Typ |
| --- | --- |
| id |String |
| key |String |
| Keytype |Heltal 64 |
| tabell |String |
| värde |String |

### <a name="data-table"></a>Datatabell

**TodoItem (todoitem)**

| Attribut | Typ | Obs! |
| --- | --- | --- |
| id | Sträng, markerad krävs |Primärnyckel i fjärrarkivet |
| slutföra | Boolean | Att göra-artikelfält |
| text |String |Att göra-artikelfält |
| createdAt | Datum | (valfritt) Kartor till **skapadAt** systemegenskap |
| updatedAt | Datum | (valfritt) Kartor till **uppdateradAt** systemegenskap |
| version | String | (valfritt) Används för att upptäcka konflikter, kartor till version |

## <a name="change-the-sync-behavior-of-the-app"></a><a name="setup-sync"></a>Ändra appens synkroniseringsbeteende
I det här avsnittet ändrar du appen så att den inte synkroniseras vid appstart eller när du infogar och uppdaterar objekt. Den synkroniseras bara när knappen för uppdateringsgest utförs.

**Mål C**:

1. I **QSTodoListViewController.m**ändrar du **metoden viewDidLoad** för att ta bort anropet till `[self refresh]` i slutet av metoden. Nu synkroniseras inte data med servern vid appstart. I stället synkroniseras den med innehållet i den lokala butiken.
2. I **QSTodoService.m**ändrar `addItem` du definitionen av så att den inte synkroniseras när objektet har infogats. Ta `self syncData` bort blocket och byt ut det mot följande:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Ändra definitionen `completeItem` av som tidigare nämnts. Ta bort `self syncData` blocket för och ersätt det med följande:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Swift:**

I `viewDidLoad`, i **ToDoTableViewController.swift**, kommentera ut de två rader som visas här, för att sluta synkronisera på app start. I skrivande stund uppdaterar swift Todo-appen inte tjänsten när någon lägger till eller slutför ett objekt. Den uppdaterar tjänsten endast vid appstart.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-the-app"></a><a name="test-app"></a>Testa appen
I det här avsnittet ansluter du till en ogiltig URL för att simulera ett offlinescenario. När du lägger till dataobjekt lagras de i det lokala kärndataarkivet, men de synkroniseras inte med sluten start för mobilappen.

1. Ändra webbadressen till mobilappen i **QSTodoService.m** till en ogiltig URL och kör appen igen:

   **Mål C**. I QSTodoService.m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Snabbt.** I ToDoTableViewController.swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Lägg till några att göra-objekt. Avsluta simulatorn (eller stäng appen med våld) och starta sedan om den. Kontrollera att ändringarna kvarstår.

3. Visa innehållet i fjärrtabellen **TodoItem:**
   * För en Node.js-backdel går du till [Azure-portalen](https://portal.azure.com/) och klickar på **Enkla tabeller** > **TodoItem**i din start i mobilappens baksida.  
   * För en .NET-serverdel använder du antingen ett SQL-verktyg, till exempel SQL Server Management Studio eller en REST-klient, till exempel Fiddler eller Postman.  

4. Kontrollera att de nya objekten *inte* har synkroniserats med servern.

5. Ändra tillbaka webbadressen till rätt i **QSTodoService.m**och kör appen igen.

6. Utför uppdateringsgesten genom att dra ned listan med objekt.  
En förloppspinnare visas.

7. Visa **TodoItem-data** igen. De nya och ändrade att göra-objekten ska nu visas.

## <a name="summary"></a>Sammanfattning
För att stödja offlinesynkroniseringsfunktionen använde vi `MSSyncTable` gränssnittet och initierade `MSClient.syncContext` med en lokal butik. I det här fallet var det lokala arkivet en Core Data-baserad databas.

När du använder ett lokalt core data-arkiv måste du definiera flera tabeller med [rätt systemegenskaper](#review-core-data).

De normala åtgärderna skapa, läsa, uppdatera och ta bort (CRUD) för mobilappar fungerar som om appen fortfarande är ansluten, men alla åtgärder sker mot den lokala butiken.

När vi synkroniserade den lokala butiken med servern använde vi **metoden MSSyncTable.pullWithQuery.**

## <a name="additional-resources"></a>Ytterligare resurser
* [Synkronisering av offlinedata i mobilappar]
* [Cloud Cover: Offline-synkronisering i Azure Mobile Services] \(Videon handlar om mobila tjänster, men offlinesynkronisering för mobilappar fungerar på ett liknande sätt.\)

<!-- URLs. -->


[Skapa en iOS-app]: app-service-mobile-ios-get-started.md
[Synkronisering av offlinedata i mobilappar]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloud Cover: Offline-synkronisering i Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
