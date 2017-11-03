---
title: Datasynkronisering offline i Azure Mobile Apps | Microsoft Docs
description: "Konceptuell referens- och översikt över funktionen för synkronisering av offlinedata för Azure Mobile Apps"
documentationcenter: windows
author: ggailey777
manager: syntaxc4
editor: 
services: app-service\mobile
ms.assetid: 982fb683-8884-40da-96e6-77eeca2500e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/30/2016
ms.author: glenga
ms.openlocfilehash: 8e2bd755d14319f8c66f7ae7ec64fbd10801b39d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Datasynkronisering offline i Azure Mobile Apps
## <a name="what-is-offline-data-sync"></a>Vad är datasynkronisering offline?
Datasynkronisering offline är en klient och server SDK-funktionen i Azure Mobilappar som gör det enkelt för utvecklare att skapa appar som fungerar utan en nätverksanslutning.

När din app är i offlineläge kan du fortfarande skapa och ändra data som sparas i ett lokalt Arkiv. När appen är online, kan den synkronisera lokala ändringar med din mobilappsserverdel i Azure. Funktionen finns också stöd för att upptäcka konflikter när samma post ändras för både klienten och serverdelen. Konflikter kan sedan hanteras på servern eller klienten.

Offlinesynkronisering har flera fördelar:

* Förbättra app svarstider genom cachelagring serverdata lokalt på enheten
* Skapa robust appar som kan vara användbar när det finns problem med nätverk
* Gör att du kan skapa och ändra data, även om det finns ingen nätverksåtkomst stöd för scenarier med lite eller ingen anslutning
* Synkronisera data över flera enheter och identifiera konflikter när samma post ändras av två enheter
* Begränsa nätverksanvändning på tidsfördröjning eller förbrukade nätverk

Följande kurser visar hur du lägger till offlinesynkronisering till dina mobila klienter som använder Azure Mobile Apps:

* [Android: Aktivera offlinesynkronisering]
* [Apache Cordova: Aktivera offlinesynkronisering](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS: aktivera offlinesynkronisering]
* [Xamarin-iOS: aktivera offlinesynkronisering]
* [Xamarin Android: Aktivera offlinesynkronisering]
* [Xamarin.Forms: Aktivera offlinesynkronisering](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Uwp: aktivera offlinesynkronisering]

## <a name="what-is-a-sync-table"></a>Vad är en tabell med synkronisering?
För att komma åt slutpunkten ”/ tabeller” Azure Mobile klienten SDK tillhandahålla gränssnitt som `IMobileServiceTable` (.NET klient SDK) eller `MSTable` (iOS-klient). API: erna ansluta direkt till serverdelen för Mobilappen för Azure och misslyckas om klientenheten inte har en nätverksanslutning.

Stöd för användning offline, din app ska i stället använda den *sync tabell* API: er, t.ex `IMobileServiceSyncTable` (.NET klient SDK) eller `MSSyncTable` (iOS-klient). Alla samma CRUD-åtgärder (skapa, läsa, uppdatera, ta bort) arbeta mot synkronisering tabell API: er, förutom nu de läsa från eller skriva till en *lokalt Arkiv*. Det lokala arkivet måste initieras innan alla synkroniseringsåtgärder tabellen kan utföras.

## <a name="what-is-a-local-store"></a>Vad är ett lokalt Arkiv?
Ett lokalt Arkiv är persistencelager på klientenheten. Azure Mobile Apps klient-SDK: Ange en standardimplementering lokalt Arkiv. Den är baserad på SQLite i Windows, Xamarin och Android. På iOS, är den baserad på grundläggande information.

Om du vill använda implementeringen SQLite-baserade på Windows Phone eller Windows Store 8.1 som du behöver installera tillägget SQLite. Mer information finns i [Uwp: aktivera offlinesynkronisering]. Android och iOS levereras med en version av SQLite i enhetens operativsystem, så det inte är nödvändigt att referera till en egen version av SQLite.

Utvecklare kan också implementeras sina egna lokalt Arkiv. Du kan till exempel definiera ett lokalt arkiv som använder SQLCipher för kryptering om du vill lagra data i ett krypterat format på den mobila klienten.

## <a name="what-is-a-sync-context"></a>Vad är en kontext som synkronisering?
A *sync kontexten* är associerad med ett objekt för mobila (t.ex `IMobileServiceClient` eller `MSClient`) och spårar ändringar som görs med synkronisering tabeller. Kontexten sync upprätthåller en *åtgärden kön*, vilket håller en sorterad lista över CUD åtgärder (skapa, uppdatera, ta bort) som senare skickas till servern.

Ett lokalt arkiv som har associerats med kontexten synkronisering med en initieringsmetoden `IMobileServicesSyncContext.InitializeAsync(localstore)` i den [.NET klient-SDK].

## <a name="how-sync-works"></a>Hur offline synkronisering fungerar
När du använder sync tabeller, kontrollerar klientkoden när lokala ändringar synkroniseras med en mobilappsserverdel i Azure. Inget skickas till serverdelen tills det finns ett anrop till *push* lokala ändringar. På liknande sätt kan det lokala arkivet fylls med nya data när det finns ett anrop till *pull* data.

* **Push**: Push är en åtgärd på sync-kontexten och skickar alla CUD ändringar sedan den senaste push. Observera att det inte går att skicka bara en enskild tabell ändringar eftersom annars åtgärder kan skickas i rätt ordning. Push kör en serie för REST-anrop till din Azure mobilappsserverdel, som i sin tur ändrar server-databas.
* **Hämtar**: Pull utförs på grundval av per tabell och kan anpassas med att hämta bara en delmängd av data från servern. Azure Mobile klient-SDK: Infoga sedan resultatet i det lokala arkivet.
* **Implicit push-meddelanden**: om en pull körs mot en tabell som har väntande uppdateringar lokala pull först kör en `push()` på sync-kontext. Den här push bidrar till att minimera konflikter mellan ändringar som redan finns i kön och nya data från servern.
* **Inkrementell synkronisering**: den första parametern till den pull-operationen är en *Frågenamnet* som används bara på klienten. Om du använder en icke-null Frågenamnet Azure Mobile SDK utför en *inkrementell synkronisering*. Varje gång en pull-åtgärd returnerar en uppsättning resultat, de senaste `updatedAt` tidsstämpel som resultatmängden lagras i tabellerna SDK lokalt system. Efterföljande pull operations hämta bara poster efter tidsstämpeln.

  Om du vill använda inkrementell synkronisering servern måste returnera meningsfulla `updatedAt` värden och måste också ha stöd för sortering av det här fältet. Eftersom SDK lägger till sin egen sortera på fältet updatedAt, kan du använda en pull-fråga som har sin egen `orderBy` satsen.

  Frågans namn kan vara valfri sträng som du väljer, men det måste vara unikt för varje logisk fråga i din app.
  Annars olika pull-åtgärder kan du skriva över samma inkrementell synkronisering tidsstämpel och dina frågor kan returnera felaktiga resultat.

  Om frågan har en parameter, är ett sätt att skapa ett unikt namn att inkludera parametervärdet.
  Om du filtrerar på användar-ID kunde till exempel frågans namn på följande sätt (i C#):

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Om du vill välja bort inkrementell synkronisering skicka `null` som frågan-ID. I så fall måste alla poster som hämtas vid varje anrop till `PullAsync`, som är potentiellt ineffektiv.
* **Rensa**: du kan ta bort innehållet i det lokala arkivet `IMobileServiceSyncTable.PurgeAsync`.
  Rensa kan vara nödvändigt om du har inaktuella data i klientdatabasen, eller om du vill ta bort alla väntande ändringar.

  En rensning tar bort en tabell från det lokala arkivet. Om det finns åtgärder som väntar på synkronisering med server-databasen, rensning genererar ett undantag såvida inte den *tvinga Rensa* parameter har angetts.

  Anta i todo-list ”-exemplet Device1 hämtar endast objekt som inte är slutförda som ett exempel på inaktuella data på klienten. Ett todoitem ”köpa mjölk” är markerad slutförts på servern av en annan enhet. Device1 fortfarande har dock ”köp produktion”-todoitem i lokala arkivet eftersom det bara dra objekt som inte är markerat som slutförda. En rensning tar bort inaktuella objektet.

## <a name="next-steps"></a>Nästa steg
* [iOS: aktivera offlinesynkronisering]
* [Xamarin-iOS: aktivera offlinesynkronisering]
* [Xamarin Android: Aktivera offlinesynkronisering]
* [Uwp: aktivera offlinesynkronisering]

<!-- Links -->
[.NET klient-SDK]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: Aktivera offlinesynkronisering]: app-service-mobile-android-get-started-offline-data.md
[iOS: aktivera offlinesynkronisering]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin-iOS: aktivera offlinesynkronisering]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: Aktivera offlinesynkronisering]: app-service-mobile-xamarin-android-get-started-offline-data.md
[Uwp: aktivera offlinesynkronisering]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
