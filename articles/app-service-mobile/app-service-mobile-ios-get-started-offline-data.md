---
title: Aktivera synkronisering offline med iOS-mobilappar | Microsoft Docs
description: Lär dig hur du använder Azure App Service Mobile Apps för att cachelagra och Synkronisera offlinedata i iOS-program.
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: f29a28f9a80b64ef0a6890fa8fc7ecd0ca205e66
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388761"
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Aktivera synkronisering offline med iOS-mobilappar
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av slutpunkt till slutpunkt-tjänster och integrerade tjänster som är centrala för utveckling av mobilappar. Utvecklare kan använda tjänsterna för att **bygga**, **testa** och **distribuera** för att skapa en pipeline för kontinuerlig integrering och leverans. När appen har distribuerats kan utvecklarna övervaka status och användning av appen med hjälp av tjänsterna **Analys** och **Diagnostik**, och kommunicera med användarna via **Push**-tjänsten. Utvecklare kan också dra nytta av **Auth** för att autentisera sina användare och tjänsten **Data** för att spara och synkronisera appdata i molnet.
>
> Om du vill integrera moln tjänster i ditt mobil program kan du registrera dig med [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) idag.

## <a name="overview"></a>Översikt
I den här självstudien beskrivs offline-synkronisering med Mobile Apps funktionen i Azure App Service för iOS. Med offlinesynkronisering kan slutanvändare interagera med en mobilapp för att visa, lägga till eller ändra data, även om de inte har någon nätverks anslutning. Ändringarna lagras i en lokal databas. När enheten är online igen synkroniseras ändringarna med fjärrservern.

Om det här är din första upplevelse med Mobile Apps bör du först slutföra självstudien [skapa en iOS-app]. Om du inte använder det nedladdade snabb starts Server projektet måste du lägga till paket för data åtkomst tillägg i projektet. Mer information om Server tilläggs paket finns i [arbeta med .NET-Server del Server SDK för Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Mer information om funktionen offline-synkronisering finns i [Datasynkronisering offline i Mobile Apps].

## <a name="review-sync"></a>Granska koden för klientens synkronisering
Det klient projekt som du laddade ned för själv studie kursen [skapa en iOS-app] innehåller redan kod som har stöd för offlinesynkronisering med hjälp av en lokal central data baserad databas. I det här avsnittet sammanfattas vad som redan finns med i självstudie koden. En översikt över funktionen finns i [Datasynkronisering offline i Mobile Apps].

Med funktionen offline-datasynkronisering i Mobile Apps kan slutanvändare interagera med en lokal databas även om nätverket inte är tillgängligt. Om du vill använda dessa funktioner i din app initierar du synkroniseringen av Sync-kontexten för `MSClient` och refererar till ett lokalt Arkiv. Sedan hänvisar du till tabellen via **MSSyncTable** -gränssnittet.

I **QSTodoService. m** (mål-C) eller **ToDoTableViewController. SWIFT** (SWIFT) ser du till att typen av medlems **syncTable** är **MSSyncTable**. Offline-synkronisering använder det här gränssnittet i Sync-tabellen i stället för **MSTable**. När en routningstabell används går alla åtgärder till det lokala arkivet och synkroniseras bara med fjärrservern med explicita push-och pull-åtgärder.

 Om du vill hämta en referens till en Sync-tabell använder du metoden **syncTableWithName** på `MSClient`. Använd **tableWithName** i stället om du vill ta bort funktionen offline-synkronisering.

Innan alla tabell åtgärder kan utföras måste det lokala arkivet initieras. Här är den relevanta koden:

* **Mål-C**. I metoden **QSTodoService. init** :

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **Swift**. I metoden **ToDoTableViewController. viewDidLoad** :

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Den här metoden skapar en lokal lagrings plats med hjälp av gränssnittet `MSCoreDataStore`, som Mobile Apps SDK tillhandahåller. Alternativt kan du ange ett annat lokalt Arkiv genom att implementera `MSSyncContextDataSource`-protokollet. Den första parametern i **MSSyncContext** används också för att ange en konflikt hanterare. Eftersom vi har passerat `nil`, får vi standard konflikt hanteraren, vilket Miss lyckas vid eventuella konflikter.

Nu ska vi utföra den faktiska synkroniseringsåtgärden och hämta data från fjärrservern:

* **Mål-C**. `syncData` skickar först nya ändringar och anropar sedan **pullData** för att hämta data från fjärrservern. I sin tur hämtar metoden **pullData** nya data som matchar en fråga:

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

I mål-C-versionen, i `syncData`, anropar vi först **pushWithCompletion** i Sync-kontexten. Den här metoden är medlem i `MSSyncContext` (och inte i själva tabellen) eftersom den push-överför ändringar i alla tabeller. Endast poster som har ändrats på något sätt lokalt (via CUD-åtgärder) skickas till servern. Sedan anropas **pullData** , som anropar **MSSyncTable. pullWithQuery** för att hämta fjärrdata och lagra dem i den lokala databasen.

Eftersom push-åtgärden inte var absolut nödvändig i Swift-versionen, finns det inget anrop till **pushWithCompletion**. Om det finns ändringar som väntar i synkroniserings kontexten för den tabell som utför en push-åtgärd, utfärdar pull alltid en push-åtgärd först. Men om du har fler än en Sync-tabell är det bäst att uttryckligen anropa push för att se till att allt är konsekvent i relaterade tabeller.

I både mål-C-och SWIFT-versionerna kan du använda metoden **pullWithQuery** för att ange en fråga för att filtrera de poster som du vill hämta. I det här exemplet hämtar frågan alla poster i den fjärranslutna `TodoItem`-tabellen.

Den andra parametern för **pullWithQuery** är ett fråge-ID som används för *stegvis synkronisering*. Med den stegvisa synkroniseringen hämtas endast poster som har ändrats sedan den senaste synkroniseringen, med postens `UpdatedAt`-tidstämpel (kallas `updatedAt` i det lokala arkivet). Frågans ID ska vara en beskrivande sträng som är unik för varje logisk fråga i din app. Om du vill välja bort stegvis synkronisering, pass `nil` som fråge-ID. Den här metoden kan vara ineffektiv, eftersom den hämtar alla poster för varje pull-åtgärd.

Mål-C-appen synkroniseras när du ändrar eller lägger till data, när en användare utför uppdaterings gesten och vid start.

SWIFT-appen synkroniseras när användaren utför uppdaterings-gesten och vid start.

Eftersom appen synkroniseras när data ändras (mål-C) eller när appen startar (mål-C och SWIFT) förutsätter appen att användaren är online. I ett senare avsnitt kommer du att uppdatera appen så att användare kan redigera även när de är offline.

## <a name="review-core-data"></a>Granska kärn data modellen
När du använder lagringen för kärn data offline måste du definiera särskilda tabeller och fält i din data modell. Exempel appen innehåller redan en data modell med rätt format. I det här avsnittet går vi igenom dessa tabeller för att visa hur de används.

Öppna **QSDataModel. xcdatamodeld**. Fyra tabeller definieras – tre som används av SDK och en som används för att göra-objekten själva:
  * MS_TableOperations: spårar de objekt som måste synkroniseras med-servern.
  * MS_TableOperationErrors: spårar eventuella fel som inträffar under offlinesynkronisering.
  * MS_TableConfig: spårar den senast uppdaterade tiden för den senaste synkroniseringen för alla hämtnings åtgärder.
  * TodoItem: lagrar att göra-objekt. System kolumnerna **createdAt**, **updatedAt**och **version** är valfria system egenskaper.

> [!NOTE]
> I Mobile Apps SDK reserveras kolumn namnen som börjar med " **``** ". Använd inte det här prefixet med något annat än system kolumner. Annars ändras kolumn namnen när du använder fjärrservern.
>
>

När du använder funktionen offline-synkronisering definierar du de tre system tabellerna och data tabellen.

### <a name="system-tables"></a>Systemtabeller

**MS_TableOperations**  

![MS_TableOperations-tabellattribut][defining-core-data-tableoperations-entity]

| Attribut | Typ |
| --- | --- |
| id | Heltal 64 |
| itemId | Sträng |
| properties | Binära data |
| Partitionstabell | Sträng |
| tableKind | Heltal 16 |


**MS_TableOperationErrors**

 ![MS_TableOperationErrors-tabellattribut][defining-core-data-tableoperationerrors-entity]

| Attribut | Typ |
| --- | --- |
| id |Sträng |
| operationId |Heltal 64 |
| properties |Binära data |
| tableKind |Heltal 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Attribut | Typ |
| --- | --- |
| id |Sträng |
| key |Sträng |
| keyType |Heltal 64 |
| Partitionstabell |Sträng |
| värde |Sträng |

### <a name="data-table"></a>Data tabell

**TodoItem**

| Attribut | Typ | Obs! |
| --- | --- | --- |
| id | Sträng, markerad som krävs |Primär nyckel i fjärrarkiv |
| full | Boolesk | Fältet att göra-objekt |
| text |Sträng |Fältet att göra-objekt |
| CreatedAt | Datum | valfritt Mappar till **createdAt** system egenskap |
| updatedAt | Datum | valfritt Mappar till **updatedAt** system egenskap |
| version | Sträng | valfritt Används för att identifiera konflikter, mappar till version |

## <a name="setup-sync"></a>Ändra appens synkroniserings beteende
I det här avsnittet ändrar du appen så att den inte synkroniseras när appen startas eller när du infogar och uppdaterar objekt. Den synkroniseras bara när knappen Uppdatera gest utförs.

**Mål-C**:

1. I **QSTodoListViewController. m**ändrar du **viewDidLoad** -metoden för att ta bort anropet till `[self refresh]` i slutet av-metoden. Nu synkroniseras inte data med servern när appen startades. I stället synkroniseras den med innehållet i det lokala arkivet.
2. I **QSTodoService. m**ändrar du definitionen för `addItem` så att den inte synkroniseras när objektet har infogats. Ta bort `self syncData`-blocket och Ersätt det med följande:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Ändra definitionen för `completeItem` som tidigare nämnts. Ta bort blocket för `self syncData` och Ersätt det med följande:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Swift**:

I `viewDidLoad`, i **ToDoTableViewController. SWIFT**, kommentera ut de två raderna som visas här, för att stoppa synkroniseringen när appen startas. När detta skrivs uppdaterar inte tjänsten Swift när någon lägger till eller Slutför ett objekt. Den uppdaterar bara tjänsten i appens start.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>Testa appen
I det här avsnittet ansluter du till en ogiltig URL för att simulera ett offline-scenario. När du lägger till data objekt lagras de i det lokala kärn data lagret, men de synkroniseras inte med Server delen för mobila appar.

1. Ändra mobil-app-URL: en i **QSTodoService. m** till en ogiltig URL och kör appen igen:

   **Mål-C**. I QSTodoService. m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Swift**. I ToDoTableViewController. SWIFT:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Lägg till några att-göra-objekt. Avsluta simulatorn (eller framtvinga stängning av appen) och starta sedan om den. Kontrol lera att ändringarna har sparats.

3. Visa innehållet i den fjärranslutna **TodoItem** -tabellen:
   * För en Node. js-Server del går du till [Azure Portal](https://portal.azure.com/) och klickar på **enkla tabeller**@no__t **-2 på**Server sidan i Mobile-app.  
   * För en .NET-Server del använder du antingen ett SQL-verktyg, till exempel SQL Server Management Studio eller en REST-klient, till exempel Fiddler eller Postman.  

4. Kontrol lera att de nya objekten *inte* har synkroniserats med-servern.

5. Ändra URL: en till rätt adress i **QSTodoService. m**och kör appen igen.

6. Utför uppdaterings gesten genom att dra ned listan med objekt.  
En rotations ruta visas.

7. Visa **TodoItem** -data igen. De nya och ändrade att göra-objekten ska nu visas.

## <a name="summary"></a>Sammanfattning
För att stödja funktionen offline-synkronisering använde vi gränssnittet `MSSyncTable` och initierade `MSClient.syncContext` med ett lokalt Arkiv. I det här fallet var det lokala arkivet en grundläggande data baserad databas.

När du använder ett lokalt huvud data lager måste du definiera flera tabeller med [rätt system egenskaper](#review-core-data).

Normala åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD) för mobila appar fungerar som om appen fortfarande är ansluten, men alla åtgärder sker mot det lokala arkivet.

När vi synkroniserade det lokala arkivet med servern använde vi metoden **MSSyncTable. pullWithQuery** .

## <a name="additional-resources"></a>Ytterligare resurser
* [Datasynkronisering offline i Mobile Apps]
* [Cloud Cover: offline-synkronisering i Azure Mobile Services] \(The video är ungefär Mobile Services, men Mobile Apps offlinesynkronisering fungerar på ett liknande sätt. \)

<!-- URLs. -->


[Skapa en iOS-app]: app-service-mobile-ios-get-started.md
[Datasynkronisering offline i Mobile Apps]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloud Cover: offline-synkronisering i Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
