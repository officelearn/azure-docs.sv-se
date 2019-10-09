---
title: Data synkronisering offline i Azure Mobile Apps | Microsoft Docs
description: Konceptuell referens och översikt över funktionen offline-datasynkronisering för Azure Mobile Apps
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 982fb683-8884-40da-96e6-77eeca2500e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 7f5b24915ddb9fd12085583844770dd7587f2394
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025215"
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Datasynkronisering offline i Azure Mobile Apps

> [!NOTE]
> Visual Studio App Center stöder utveckling av mobila appar från slut punkt till slut punkt och integrerade tjänster. Utvecklare kan använda **bygge**-, **test** -och **distributions** tjänster för att konfigurera kontinuerlig integrering och leverans pipeliner. När appen har distribuerats kan utvecklare övervaka status och användning av appen med hjälp av **analys** -och **diagnos** tjänster och engagera med användare med **push** -tjänsten. Utvecklare kan också utnyttja **auth** för att autentisera sina användare och **data** tjänster för att spara och synkronisera AppData i molnet.
> Om du vill integrera moln tjänster i ditt mobil program kan du registrera dig med App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) idag.

## <a name="what-is-offline-data-sync"></a>Vad är offline-datasynkronisering?
Datasynkronisering offline är en funktion för klient-och Server-SDK i Azure Mobile Apps som gör det enkelt för utvecklare att skapa appar som fungerar utan någon nätverks anslutning.

När din app är i offlineläge kan du fortfarande skapa och ändra data, som sparas i ett lokalt Arkiv. När appen är online igen kan den synkronisera lokala ändringar med din Azure Mobile App-backend. Funktionen innehåller också stöd för identifiering av konflikter när samma post ändras på både klienten och Server delen. Konflikter kan sedan hanteras antingen på-servern eller-klienten.

Offline-synkronisering har flera fördelar:

* Förbättra appens svars tid genom att cachelagra Server data lokalt på enheten
* Skapa robusta appar som är användbara när det finns nätverks problem
* Tillåt slutanvändare att skapa och ändra data även när det inte finns någon nätverks åtkomst, stöd för scenarier med liten eller ingen anslutning
* Synkronisera data över flera enheter och identifiera konflikter när samma post ändras av två enheter
* Begränsa nätverkets användning på hög latens eller avgiftsbelagda nätverk

Följande självstudier visar hur du lägger till offline-synkronisering till dina mobila klienter med Azure Mobile Apps:

* [Android: Aktivera offline-synkronisering @ no__t-0
* [Apache Cordova: Aktivera offline-synkronisering @ no__t-0
* [iOS: Aktivera offline-synkronisering @ no__t-0
* [Xamarin iOS: Aktivera offline-synkronisering @ no__t-0
* [Xamarin Android: Aktivera offline-synkronisering @ no__t-0
* [Xamarin.Forms: Aktivera offline-synkronisering @ no__t-0
* [Universal Windows-plattform: Aktivera offline-synkronisering @ no__t-0

## <a name="what-is-a-sync-table"></a>Vad är en Sync-tabell?
För att få åtkomst till slut punkten "/tables" tillhandahåller Azure Mobile-klientens SDK gränssnitt som `IMobileServiceTable` (.NET-klient-SDK) eller `MSTable` (iOS-klient). Dessa API: er ansluter direkt till Server delen för Azure Mobile-appen och fungerar inte om klient enheten inte har någon nätverks anslutning.

Om du vill ha stöd för offlineanvändning bör du i stället använda *Sync Table* -API: er, till exempel `IMobileServiceSyncTable` (.net-klient-SDK) eller `MSSyncTable` (iOS-klient). Alla samma CRUD-åtgärder (skapa, läsa, uppdatera, ta bort) fungerar för API: er för Sync-tabeller, förutom nu kan de läsa från eller skriva till ett *lokalt Arkiv*. Innan en åtgärd för att synkronisera tabeller kan utföras måste det lokala arkivet initieras.

## <a name="what-is-a-local-store"></a>Vad är ett lokalt Arkiv?
Ett lokalt Arkiv är data beständigt skikt på klient enheten. Azure Mobile Apps-klientens SDK: er tillhandahåller en standard implementering av lokal lagring. På Windows, Xamarin och Android baseras det på SQLite. På iOS baseras det på kärn data.

Om du vill använda den SQLite-baserade implementeringen på Windows Phone eller Microsoft Store måste du installera ett SQLite-tillägg. Mer information finns i [Universal Windows-plattform: Aktivera offline-synkronisering @ no__t-0. Android och iOS levereras med en version av SQLite i själva enhetens operativ system, så det är inte nödvändigt att referera till din egen version av SQLite.

Utvecklare kan även implementera egna lokala lagrings enheter. Om du till exempel vill lagra data i ett krypterat format på den mobila klienten kan du definiera ett lokalt Arkiv som använder SQLCipher för kryptering.

## <a name="what-is-a-sync-context"></a>Vad är en Sync-kontext?
En *Sync-kontext* är associerad med ett mobilt klient objekt (till exempel `IMobileServiceClient` eller `MSClient`) och spårar ändringar som görs med Sync-tabeller. Den synkroniserade kontexten upprätthåller en *transaktionskö*, som innehåller en ordnad lista över CUD-åtgärder (skapa, uppdatera, ta bort) som senare skickas till servern.

Ett lokalt lager är kopplat till Sync-kontexten med hjälp av en initierings metod som `IMobileServicesSyncContext.InitializeAsync(localstore)` i [.NET-klient-SDK].

## <a name="how-sync-works"></a>Så här fungerar offline-synkronisering
När du använder Sync-tabeller styr din klient kod när lokala ändringar synkroniseras med en server del för Azure-mobilappar. Inget skickas till Server delen förrän det finns ett anrop för att *Skicka* lokala ändringar. På samma sätt fylls den lokala lagringen med nya data endast när det finns ett anrop för att *Hämta* data.

* **Push**: Push är en åtgärd i Sync-kontexten och skickar alla CUD-ändringar sedan den senaste push-åtgärden. Observera att det inte går att skicka endast en enskild tabells ändringar, eftersom andra åtgärder kan skickas i rätt ordning. Push kör en serie REST-anrop till din Azure Mobile App-backend, som i sin tur ändrar Server databasen.
* **Hämta**: Pull utförs per tabell och kan anpassas med en fråga för att bara hämta en delmängd av Server data. SDK: erna för Azure Mobile-klienten infogar sedan de resulterande data i det lokala arkivet.
* **Implicita push**-meddelanden: Om en pull körs mot en tabell som har väntande lokala uppdateringar, kör pull-kommandot en `push()` i Sync-kontexten. Den här push-funktionen hjälper till att minimera konflikter mellan ändringar som redan finns i kö och nya data från servern.
* **Stegvis synkronisering**: den första parametern för pull-åtgärden är ett *frågenamn* som endast används på klienten. Om du använder ett frågenamn som inte är null utför Azure Mobile SDK en *stegvis synkronisering*. Varje gången en pull-åtgärd returnerar en uppsättning resultat lagras den senaste `updatedAt`-tidsstämpeln från den resultat uppsättningen i de lokala SDK-system tabellerna. Efterföljande pull-åtgärder hämtar endast poster efter tidsstämpeln.

  Om du vill använda stegvis synkronisering måste servern returnera meningsfulla `updatedAt`-värden och måste också ha stöd för sortering med det här fältet. Men eftersom SDK lägger till en egen sortering i fältet updatedAt kan du inte använda en pull-fråga som har en egen `orderBy`-sats.

  Frågenamnet kan vara vilken sträng du väljer, men det måste vara unikt för varje logisk fråga i din app.
  Annars kan olika pull-åtgärder skriva över samma tidsstämpel för den stegvisa synkroniseringen och dina frågor kan returnera felaktiga resultat.

  Om frågan har en parameter, är ett sätt att skapa ett unikt frågenamn att inkludera parametervärdet.
  Om du till exempel filtrerar på UserID kan ditt frågenamn vara följande (i C#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Om du vill välja bort stegvis synkronisering, pass `null` som fråge-ID. I det här fallet hämtas alla poster vid varje anrop till `PullAsync`, vilket kan vara ineffektivt.
* **Rensa**: Du kan ta bort innehållet i det lokala arkivet med `IMobileServiceSyncTable.PurgeAsync`.
  Du kan behöva rensa om du har inaktuella data i klient databasen eller om du vill ignorera alla väntande ändringar.

  En rensning rensar en tabell från det lokala arkivet. Om det finns åtgärder som väntar på synkronisering med Server databasen, genererar rensningen ett undantag om inte parametern *Framtvinga rensning* är inställd.

  Som exempel på inaktuella data på klienten, anta att i "att göra lista"-exemplet bara hämtar Device1-objekt som inte har slutförts. En todoitem "Köp mjölk" har marker ATS som slutförd på servern av en annan enhet. Device1 har dock fortfarande "Köp mjölk"-todoitem i det lokala lagret eftersom den bara hämtar objekt som inte har marker ATS som slutförda. En rensning tar bort det inaktuella objektet.

## <a name="next-steps"></a>Nästa steg
* [iOS: Aktivera offline-synkronisering @ no__t-0
* [Xamarin iOS: Aktivera offline-synkronisering @ no__t-0
* [Xamarin Android: Aktivera offline-synkronisering @ no__t-0
* [Universal Windows-plattform: Aktivera offline-synkronisering @ no__t-0

<!-- Links -->
[.NET-klient-SDK]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Aktivera offline-synkronisering @ no__t-0
[iOS: Aktivera offline-synkronisering @ no__t-0
[Xamarin iOS: Aktivera offline-synkronisering @ no__t-0
[Xamarin Android: Aktivera offline-synkronisering @ no__t-0
[Universal Windows-plattform: Aktivera offline-synkronisering @ no__t-0
