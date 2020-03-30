---
title: Synkronisering av offlinedata
description: Begreppsmässig referens och översikt över offlinedatasynkroniseringsfunktionen för Azure Mobile Apps
author: conceptdev
ms.assetid: 982fb683-8884-40da-96e6-77eeca2500e3
ms.devlang: multiple
ms.topic: article
ms.date: 10/30/2016
ms.openlocfilehash: 0cc4309fa57a29997bdd2f650634efd0723e6965
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458757"
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Datasynkronisering offline i Azure Mobile Apps

## <a name="what-is-offline-data-sync"></a>Vad är synkronisering av offlinedata?
Offlinedatasynkronisering är en klient- och server-SDK-funktion i Azure Mobile Apps som gör det enkelt för utvecklare att skapa appar som fungerar utan nätverksanslutning.

När appen är i offlineläge kan du fortfarande skapa och ändra data som sparas i ett lokalt arkiv. När appen är online igen kan den synkronisera lokala ändringar med din Azure Mobile App-serverdel. Funktionen innehåller också stöd för att identifiera konflikter när samma post ändras på både klienten och backend. Konflikter kan sedan hanteras antingen på servern eller på klienten.

Offlinesynkronisering har flera fördelar:

* Förbättra appens svarstider genom att cachelagma serverdata lokalt på enheten
* Skapa robusta appar som fortfarande är användbara när det finns nätverksproblem
* Tillåt slutanvändare att skapa och ändra data även när det inte finns någon nätverksåtkomst, med stöd för scenarier med liten eller ingen anslutning
* Synkronisera data mellan flera enheter och identifiera konflikter när samma post ändras av två enheter
* Begränsa nätverksanvändningen i nätverk med hög latens eller datapriser

Följande självstudier visar hur du lägger till offlinesynkronisering till dina mobila klienter med Azure Mobile Apps:

* [Android: Aktivera offlinesynkronisering]
* [Apache Cordova: Aktivera offlinesynkronisering](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS: Aktivera offlinesynkronisering]
* [Xamarin iOS: Aktivera offlinesynkronisering]
* [Xamarin Android: Aktivera offlinesynkronisering]
* [Xamarin.Forms: Aktivera offlinesynkronisering](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Universell Windows-plattform: Aktivera offlinesynkronisering]

## <a name="what-is-a-sync-table"></a>Vad är en synkroniseringstabell?
För att komma åt slutpunkten "/tabeller" tillhandahåller Azure Mobile-klientens SDK-enheter gränssnitt som `IMobileServiceTable` (.NET-klient SDK) eller `MSTable` (iOS-klient). Dessa API:er ansluter direkt till Azure Mobile App-backend och misslyckas om klientenheten inte har en nätverksanslutning.

För att stödja offlineanvändning bör appen i stället använda `IMobileServiceSyncTable` API:erna för *synkroniseringstabellen,* till exempel (.NET-klienten SDK) eller `MSSyncTable` (iOS-klient). Alla samma CRUD-åtgärder (Skapa, Läs, Uppdatera, Ta bort) arbetar mot synkroniseringstabell-API:er, förutom nu läser de från eller skriver till en *lokal butik*. Innan några synkroniseringstabellåtgärder kan utföras måste det lokala arkivet initieras.

## <a name="what-is-a-local-store"></a>Vad är en lokal butik?
Ett lokalt arkiv är databeständighetsskiktet på klientenheten. Azure Mobile Apps-klientens SDK:er tillhandahåller en standard implementering av lokala butiker. På Windows, Xamarin och Android är det baserat på SQLite. På iOS är den baserad på kärndata.

Om du vill använda den SQLite-baserade implementeringen på Windows Phone eller Microsoft Store måste du installera ett SQLite-tillägg. Mer information finns i [Universell Windows-plattform: Aktivera offlinesynkronisering]. Android och iOS levereras med en version av SQLite i själva enhetens operativsystem, så det är inte nödvändigt att referera till din egen version av SQLite.

Utvecklare kan också implementera sin egen lokala butik. Om du till exempel vill lagra data i krypterat format på den mobila klienten kan du definiera ett lokalt arkiv som använder SQLCipher för kryptering.

## <a name="what-is-a-sync-context"></a>Vad är en synkroniseringskontext?
En *synkroniseringskontext* är associerad med `IMobileServiceClient` `MSClient`ett mobilt klientobjekt (till exempel eller ) och spårar ändringar som görs med synkroniseringstabeller. Synkroniseringskontexten underhåller en *åtgärdskö*, som behåller en ordnad lista över CUD-åtgärder (Skapa, Uppdatera, Ta bort) som senare skickas till servern.

Ett lokalt arkiv associeras med synkroniseringskontexten `IMobileServicesSyncContext.InitializeAsync(localstore)` med en initiera metod, till exempel i [.NET-klientenSDK].

## <a name="how-offline-synchronization-works"></a><a name="how-sync-works"></a>Så här fungerar offlinesynkronisering
När du använder synkroniseringstabeller styr klientkoden när lokala ändringar synkroniseras med en Serverdel i Azure Mobile App. Ingenting skickas till serverdelen förrän det finns ett samtal för att *driva* lokala förändringar. På samma sätt fylls det lokala arkivet med nya data endast när det finns ett anrop för att *hämta* data.

* **Push:** Push är en åtgärd på synkroniseringskontexten och skickar alla CUD-ändringar sedan den senaste pushen. Observera att det inte är möjligt att bara skicka en enskild tabells ändringar, eftersom åtgärder annars kan skickas i slutordning. Push kör en serie REST-anrop till din Azure Mobile App-serverd, vilket i sin tur ändrar serverdatabasen.
* **Pull:** Pull utförs per tabell och kan anpassas med en fråga för att hämta endast en delmängd av serverdata. Azure Mobile-klientens SDK:er infogar sedan de resulterande data i det lokala arkivet.
* **Implicita pushes**: Om ett handtag körs mot en tabell som har `push()` väntande lokala uppdateringar, kör pull först en på synkroniseringskontexten. Den här pushen hjälper till att minimera konflikter mellan ändringar som redan har köats och nya data från servern.
* **Inkrementell synkronisering:** den första parametern till pull-åtgärden är ett *frågenamn* som endast används på klienten. Om du använder ett frågenamn som inte är null utför Azure Mobile SDK en *inkrementell synkronisering*. Varje gång en pull-åtgärd returnerar `updatedAt` en uppsättning resultat lagras den senaste tidsstämpeln från den resultatuppsättningen i SDK:s lokala systemtabeller. Efterföljande pull-åtgärder hämtar endast poster efter den tidsstämpeln.

  Om du vill använda inkrementell synkronisering måste servern returnera meningsfulla `updatedAt` värden och även ha stöd för sortering efter det här fältet. Men eftersom SDK lägger till en egen sortering i fältet updatedAt `orderBy` kan du inte använda en pull-fråga som har en egen sats.

  Frågenamnet kan vara vilken sträng du vill, men det måste vara unikt för varje logisk fråga i appen.
  Annars kan olika pull-åtgärder skriva över samma inkrementella synkroniseringstidsstämpel och dina frågor kan returnera felaktiga resultat.

  Om frågan har en parameter är ett sätt att skapa ett unikt frågenamn att införliva parametervärdet.
  Om du till exempel filtrerar på userid kan frågenamnet vara följande (i C#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Om du vill välja bort inkrementell synkronisering, passera `null` som fråge-ID. I det här fallet hämtas alla `PullAsync`poster på varje samtal till , vilket är potentiellt ineffektivt.
* **Rensning:** Du kan rensa innehållet i `IMobileServiceSyncTable.PurgeAsync`den lokala butiken med .
  Rensning kan vara nödvändigt om du har inaktuella data i klientdatabasen, eller om du vill ignorera alla väntande ändringar.

  En rensning rensar en tabell från den lokala butiken. Om det finns åtgärder som väntar på synkronisering med serverdatabasen, *force purge* genererar rensningen ett undantag om inte force purge-parametern har angetts.

  Som ett exempel på inaktuella data på klienten, anta i "todo list" exempel, Device1 bara drar objekt som inte är slutförda. En todoitem "Köp mjölk" är markerad som färdigställs på servern av en annan enhet. Device1 har dock fortfarande todoitem "Köp mjölk" i lokal butik eftersom det bara är att dra objekt som inte är markerade kompletta. En rensning rensar det här inaktuella objektet.

## <a name="next-steps"></a>Nästa steg
* [iOS: Aktivera offlinesynkronisering]
* [Xamarin iOS: Aktivera offlinesynkronisering]
* [Xamarin Android: Aktivera offlinesynkronisering]
* [Universell Windows-plattform: Aktivera offlinesynkronisering]

<!-- Links -->
[.NET-klient SDK]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Aktivera offlinesynkronisering]: app-service-mobile-android-get-started-offline-data.md
[iOS: Aktivera offlinesynkronisering]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: Aktivera offlinesynkronisering]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: Aktivera offlinesynkronisering]: app-service-mobile-xamarin-android-get-started-offline-data.md
[Universell Windows-plattform: Aktivera offlinesynkronisering]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
