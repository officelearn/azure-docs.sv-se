---
title: Synkronisering av offlinedata i Azure Mobile Apps | Microsoft Docs
description: Konceptuell referens och översikt över funktionen för synkronisering av offlinedata för Azure Mobile Apps
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
ms.openlocfilehash: ab8fb4a567e4c4a7bf1e884999a4e403a98547a0
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471041"
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Datasynkronisering offline i Azure Mobile Apps
## <a name="what-is-offline-data-sync"></a>Vad är synkronisering av offlinedata?
Synkronisering av offlinedata är en klient och server SDK-funktionen i Azure Mobile Apps som gör det enkelt för utvecklare att skapa appar som fungerar utan en nätverksanslutning.

När appen är i offline-läge, kan du fortfarande skapa och ändra data som sparas i ett lokalt Arkiv. När appen är online igen kan kan den synkronisera lokala ändringar med din Azure-Mobilapp-serverdel. Funktionen finns också stöd för identifiering av står i konflikt när samma post ändras för både klienten och serverdelen. Konflikter kan sedan hanteras, antingen på servern eller på klienten.

Offline-synkronisering har flera fördelar:

* Förbättra appens svarstid genom att cachelagra serverdata lokalt på enheten
* Skapa stabila appar som är användbara vid nätverksproblem
* Gör att du kan skapa och ändra data även när det finns ingen nätverksåtkomst, stöd för scenarier med lite eller ingen anslutning
* Synkronisera data på flera enheter och identifiera står i konflikt när samma post ändras av två enheter
* Begränsa nätverksanvändning på lång svarstid eller avgiftsbelagda nätverk

Följande självstudier beskriver hur du lägger till offlinesynkronisering till din mobila klienter som använder Azure Mobile Apps:

* [Android: Aktivera offlinesynkronisering]
* [Apache Cordova: Aktivera offlinesynkronisering](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS: aktivera offlinesynkronisering]
* [Xamarin-iOS: aktivera offlinesynkronisering]
* [Xamarin Android: Aktivera offlinesynkronisering]
* [Xamarin.Forms: Aktivera offline-synkronisering](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Universal Windows Platform: Aktivera offlinesynkronisering]

## <a name="what-is-a-sync-table"></a>Vad är en tabell med sync?
För att komma åt slutpunkten ”/ tabeller” Azure Mobile-klient SDK: er ger gränssnitt som `IMobileServiceTable` (.NET client SDK) eller `MSTable` (iOS-klient). Dessa API: er ansluta direkt till serverdelen för Mobilappen i Azure och misslyckas om klientenheten inte har en nätverksanslutning.

Stöd för användning offline kan din app ska i stället använda den *synkronisering tabell* API: er, som `IMobileServiceSyncTable` (.NET client SDK) eller `MSSyncTable` (iOS-klient). Alla samma CRUD-åtgärder (skapa, läsa, uppdatera, ta bort) fungerar mot synkronisering tabell-API: er, förutom nu de läsa från eller skriva till en *lokalt Arkiv*. Innan alla synkroniseringsåtgärder tabell kan utföras, måste det lokala arkivet initieras.

## <a name="what-is-a-local-store"></a>Vad är ett lokalt Arkiv?
Ett lokalt Arkiv är datalager för persistence på klientenheten. Azure Mobile Apps-klient SDK tillhandahåller en standardimplementering lokalt Arkiv. På Windows, Xamarin och Android, är den baserad på sqlite-felkod. Den är baserad på grundläggande information på iOS.

Om du vill använda implementeringen SQLite-baserade på Windows Phone eller Microsoft Store, som du behöver installera en SQLite-tillägget. Mer information finns i [Universal Windows Platform: aktivera offlinesynkronisering]. Android och iOS levereras med en version av SQLite i enhetens operativsystem, så det inte är nödvändigt att referera till en egen version av SQLite.

Utvecklare kan också implementera sina egna lokala arkivet. Du kan till exempel definiera ett lokalt arkiv som använder SQLCipher för kryptering om du vill lagra data i ett krypterat format på mobil klient.

## <a name="what-is-a-sync-context"></a>Vad är en kontext för synkronisering?
A *synkronisering kontext* är associerad med en mobil klient-objekt (t.ex `IMobileServiceClient` eller `MSClient`) och spårar ändringar som görs med sync tabeller. Synkronisera kontexten upprätthåller en *åtgärden kö*, som ser till att en sorterad lista över CUD åtgärder (skapa, uppdatera, ta bort) som senare skickas till servern.

Ett lokalt arkiv som är associerad med sync-kontext med ett initieringsmetoden som `IMobileServicesSyncContext.InitializeAsync(localstore)` i den [SDK för .NET-klient].

## <a name="how-sync-works"></a>Hur offline fungerar synkronisering
När du använder sync tabeller, styr klientkoden när lokala ändringar synkroniseras med en mobilappsserverdel i Azure. Inget skickas till serverdelen tills det är ett anrop till *push* lokala ändringar. På samma sätt kan det lokala arkivet fylls med nya data endast när det är ett anrop till *pull* data.

* **Push**: push-meddelanden är en åtgärd avseende synkroniseringen kontexten och skickar alla CUD ändringar sedan den senaste push. Observera att det inte går att skicka bara en enskild tabell ändringar, eftersom du annars operations kunde skickas i fel ordning. Push kör en serie med REST-anrop till din Azure-Mobilapp-serverdel, vilket i sin tur ändrar din server-databas.
* **Hämta**: Pull utförs på basis av per tabell och kan anpassas med en fråga för att hämta endast en delmängd av data från servern. Azure Mobile-klient SDK Infoga sedan resultatet i det lokala arkivet.
* **Implicit push-meddelanden**: om en hämtning körs mot en tabell som har väntande lokala uppdateringar, pull först kör en `push()` på sync-kontext. Den här push hjälper till att minimera konflikter mellan ändringar som redan i kö och nya data från servern.
* **Inkrementell synkronisering**: den första parametern för pull-åtgärd är en *Frågenamnet* som används bara på klienten. Om du använder en icke-null Frågenamnet Azure Mobile SDK utför en *inkrementell synkronisering*. Varje gång en pull-åtgärd returnerar en uppsättning resultat, den senaste `updatedAt` tidsstämpel som resultatmängden lagras i lokalt system-tabeller SDK. Efterföljande pull operations hämta bara poster efter den tidsstämpeln.

  Om du vill använda inkrementell synkronisering, din server måste returnera meningsfulla `updatedAt` standardvärden och måste också ha stöd för sortering efter det här fältet. Men eftersom SDK: N lägger till en egen sortera på fältet updatedAt, du kan inte använda en pull-fråga som har sin egen `orderBy` satsen.

  Frågans namn kan vara valfri sträng som du väljer, men det måste vara unikt för varje logisk fråga i din app.
  I annat fall olika pull-åtgärder kan skriva över samma tidsstämpel för inkrementell synkronisering och dina frågor kan returnera felaktiga resultat.

  Om frågan har en parameter, är ett sätt att skapa ett unikt frågenamn att införliva parametervärdet.
  Om du filtrerar på användar-ID kan till exempel frågans namn enligt följande (i C#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Om du vill välja bort inkrementell synkronisering skicka `null` som frågan-ID. I det här fallet alla poster hämtas på varje anrop till `PullAsync`, vilket är potentiellt ineffektiv.
* **Rensa**: du kan radera innehållet i det lokala arkivet med `IMobileServiceSyncTable.PurgeAsync`.
  Rensa kan vara nödvändigt om du har inaktuella data i klientdatabasen, eller om du vill åsidosätta alla väntande ändringar.

  En rensning tar bort en tabell från det lokala arkivet. Om det finns åtgärder som väntar på synkronisering med server-databasen, rensning genereras ett undantag såvida inte den *tvinga Rensa* parametern anges.

  Som ett exempel på inaktuella data på klienten anta att i ”todo list”-exemplet Device1 hämtar endast objekt som inte är slutförda. Ett todoitem ”köpa mjölk” är markerad på servern har slutförts av en annan enhet. Dock har Device1 fortfarande ”köp produktion” todoitem i lokala arkivet, eftersom det bara hämta objekt som inte markerats som slutförd. En loggrensning rensar inaktuella objektet.

## <a name="next-steps"></a>Nästa steg
* [iOS: aktivera offlinesynkronisering]
* [Xamarin-iOS: aktivera offlinesynkronisering]
* [Xamarin Android: Aktivera offlinesynkronisering]
* [Universal Windows Platform: Aktivera offlinesynkronisering]

<!-- Links -->
[SDK för .NET-klient]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Aktivera offlinesynkronisering]: app-service-mobile-android-get-started-offline-data.md
[iOS: aktivera offlinesynkronisering]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin-iOS: aktivera offlinesynkronisering]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: Aktivera offlinesynkronisering]: app-service-mobile-xamarin-android-get-started-offline-data.md
[Universal Windows Platform: Aktivera offlinesynkronisering]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
