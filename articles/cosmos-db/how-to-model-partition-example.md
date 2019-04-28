---
title: Så här modellen och partitionera data i Azure Cosmos DB med ett verkliga exempel
description: Lär dig hur du modellera och partitionera en verkliga exempel på användning av Azure Cosmos DB Core API
author: rockboyfor
ms.service: cosmos-db
ms.topic: sample
origin.date: 03/27/2019
ms.date: 04/15/2019
ms.author: v-yeche
ms.openlocfilehash: ac1b94de4b439aab202d53b23b0d0da616a9f851
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61057623"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>Så här modellen och partitionera data i Azure Cosmos DB med ett verkliga exempel

Den här artikeln bygger vidare på flera Azure Cosmos DB-begrepp som [datamodellering](modeling-data.md), [partitionering](partitioning-overview.md), och [etablerat dataflöde](request-units.md) att demonstrera hur du ska hantera en verklig data arbetet.

Om du arbetar vanligtvis med relationsdatabaser, har du förmodligen byggt vanor och intuitions om hur du utformar en datamodell. På grund av de särskilda begränsningarna, men även unika starka sidor i Azure Cosmos DB, de flesta av dessa metodtips översätta inte bra och dra i icke-optimal lösningar. Målet med den här artikeln är att guida dig genom processen för en verklig användningsfall i Azure Cosmos DB från objektet modellering till entiteten samordning och behållaren partitionering.

## <a name="the-scenario"></a>Scenariot

I den här övningen ska du överväga att domänen för en bloggplattform där *användare* kan skapa *inlägg*. Användarna kan också *som* och Lägg till *kommentarer* till dessa inlägg.

> [!TIP]
> Vi har markerat några ord i *kursiv*; dessa ord identifiera vilken typ av ”saker” vår modell kommer att behöva ändra.

Lägga till flera krav vår specifikationen:

- En klient sida som visar en feed med nyligen skapade inlägg
- Vi kan hämta alla poster för en användare, alla kommentarer om ett inlägg och alla gilla-markeringar för ett inlägg
- Inlägg som returneras med användarnamnet för författarna och räkna hur många kommentarer och gilla-markeringar som de har
- Kommentarer och gilla-markeringar returneras också med användarnamnet för de användare som har skapat dem,
- När visas som listor kan ha inlägg endast att presentera en trunkerade sammanfattning av deras innehåll.

## <a name="identify-the-main-access-patterns"></a>Visar de viktigaste åtkomstmönster

Om du vill starta, ger vi vissa struktur vår första specifikationen genom att identifiera mönster i databasåtkomst vår lösning. När du utformar en datamodell för Azure Cosmos DB, är det viktigt att förstå vilka begäranden som vår modell måste fungera för att se till att modellen hantera dessa begäranden effektivt.

Du kan förenkla den övergripande processen följer vi kategorisera dessa olika förfrågningar som kommandon eller frågor, lånar vissa ordförråd från [CQRS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation) där kommandon är skrivåtgärder (det vill säga avsikter att uppdatera systemet) och frågorna är skrivskyddade förfrågningar.

Här är listan med begäranden som vår plattform måste exponera:

- **[C1]**  Skapa/redigera en användare
- **[Q1]**  Hämta en användare
- **[C2]**  Skapa/redigera ett inlägg
- **[2]**  Hämta ett inlägg
- **KVARTAL [3]**  Lista över en användares inlägg i kortformen
- **[C3]**  Skapar en kommentar
- **KVARTAL [4]**  Lista en publicera kommentarer
- **[C4]**  Som ett inlägg
- **[F5]**  Lista ett inlägg likes
- **[Q6]**  Lista de *x* senaste inläggen skapat kortfattat formulär (matning)

Som det här skedet inte har vi tror att information om vad varje entitet (användare, post o.s.v.) innehåller. Det här steget är vanligtvis bland de första att utföras när du utformar mot en relationslagringsplats eftersom vi behöver ta reda på hur entiteterna översätter när det gäller tabeller, kolumner, sekundärnycklar osv. Det är mycket mindre bekymmer med en dokumentdatabas som inte framtvinga ett schema vid skrivning.

Huvudskälet till varför det är viktigt att identifiera våra mönster i databasåtkomst från början, beror på den här listan med begäranden ska bli vår testsvit. Varje gång vi iterera över våra datamodellen ska vi gå igenom begäranden och kontrollera dess prestanda och skalbarhet.

## <a name="v1-a-first-version"></a>V1: Första versionen

Vi börjar med två behållare: `users` och `posts`.

### <a name="users-container"></a>Användarbehållaren

Den här behållaren lagrar bara användardata:

    {
      "id": "<user-id>",
      "username": "<username>"
    }

Vi partitionera den här behållaren genom `id`, vilket innebär att varje logisk partition i behållaren ska bara innehålla ett objekt.

### <a name="posts-container"></a>Inlägg behållare

Den här behållaren är värd för inlägg, kommentarer, och gillar:

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "title": "<post-title>",
      "content": "<post-content>",
      "creationDate": "<post-creation-date>"
    }

    {
      "id": "<comment-id>",
      "type": "comment",
      "postId": "<post-id>",
      "userId": "<comment-author-id>",
      "content": "<comment-content>",
      "creationDate": "<comment-creation-date>"
    }

    {
      "id": "<like-id>",
      "type": "like",
      "postId": "<post-id>",
      "userId": "<liker-id>",
      "creationDate": "<like-creation-date>"
    }

Vi partitionera den här behållaren genom `postId`, vilket innebär att varje logisk partition i behållaren innehåller en post, alla kommentarer om inlägget och alla likes för inlägget.

Observera att vi har introducerat en `type` -egenskapen i objekt som lagras i den här behållaren att skilja mellan de tre typerna av enheter att denna behållare-värdar.

Dessutom har vi valt att referera till relaterade data i stället för att bädda in den (kontrollera [i det här avsnittet](modeling-data.md) mer information om de här koncepten) eftersom:

- Det finns ingen övre gräns för hur många inlägg som en användare kan skapa
- inlägg kan vara godtyckligt lång.
- Det finns ingen övre gräns för hur många kommentarer och gilla-markeringar som en post kan ha
- Vi vill kunna lägga till en kommentar eller en like på ett inlägg utan att behöva uppdatera inlägget själva.

## <a name="how-well-does-our-model-perform"></a>Hur väl utför vår modell?

Nu är det dags att utvärdera prestanda och skalbarhet för den första versionen. För var och en av de förfrågningar som redan har identifierat vi mäter dess svarstid och begära hur många enheter som har det förbrukar. Denna mätning görs mot en dummy datauppsättning som innehåller 100 000 användare med 5 till 50 inlägg per användare och upp till 25 kommentarer och 100 likes per post.

### <a name="c1-createedit-a-user"></a>[C1] Skapa/redigera en användare

Den här begäran är enkel att implementera när vi skapar eller uppdaterar ett objekt i den `users` behållare. Begäranden sprids snyggt för alla partitioner tack vare den `id` partitionsnyckel.

![Skriva ett enskilt objekt till användarbehållaren](./media/how-to-model-partition-example/V1-C1.png)

| **Svarstid** | **RU kostnad** | **Prestanda** |
| --- | --- | --- |
| 7 ms | 5.71 RU | ✅ |

### <a name="q1-retrieve-a-user"></a>[Q1] Hämta en användare

Hämta en användare gör du genom att läsa motsvarande objekt från den `users` behållare.

![Hämta ett enskilt objekt från användarbehållaren](./media/how-to-model-partition-example/V1-Q1.png)

| **Svarstid** | **RU kostnad** | **Prestanda** |
| --- | --- | --- |
| 2 ms | 1 RU | ✅ |

### <a name="c2-createedit-a-post"></a>[C2] Skapa/redigera ett inlägg

På samma sätt till **[C1]**, behöver vi bara att skriva till den `posts` behållare.

![Skriva ett enskilt objekt till behållaren för inlägg](./media/how-to-model-partition-example/V1-C2.png)

| **Svarstid** | **RU kostnad** | **Prestanda** |
| --- | --- | --- |
| 9 ms | 8.76 RU | ✅ |

### <a name="q2-retrieve-a-post"></a>[2] Hämta ett inlägg

Vi börjar genom att hämta motsvarande dokumentet från den `posts` behållare. Men som inte är tillräckligt, enligt våra specifikationen vi har också aggregera användarnamnet för det inlägget författare och antal hur många kommentarer och hur många gillar det här inlägget har, vilket kräver 3 ytterligare SQL-frågor utfärdas.

![Hämta ett inlägg och aggregering av ytterligare data](./media/how-to-model-partition-example/V1-Q2.png)

Var och en av de ytterligare frågor filter på Partitionsnyckeln respektive behållarens, vilket är exakt vad vi vill maximera prestanda och skalbarhet. Men vi har så småningom att utföra fyra åtgärder för att returnera ett enskilt inlägg, så att vi kan förbättra som i nästa iteration.

| **Svarstid** | **RU kostnad** | **Prestanda** |
| --- | --- | --- |
| 9 ms | 19.54 RU | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>KVARTAL [3] Lista över en användares inlägg i kortformen

Först måste vi hämta önskade-inlägg med en SQL-fråga som hämtar inlägg som motsvarande till den specifika användaren. Men vi också behöva skicka ytterligare frågor för att aggregera författarens användarnamn och antal kommentarer och gillar.

![Hämta alla poster för en användare och deras ytterligare datainsamling](./media/how-to-model-partition-example/V1-Q3.png)

Den här implementeringen visar många nackdelar:

- frågorna sammanställa antalet kommentarer och gilla-markeringar som har utfärdats för varje post som returneras av den första frågan
- huvudfrågan inte filtrerar på Partitionsnyckeln för den `posts` behållare, vilket leder till en fan-out och en partition sökning i behållaren.

| **Svarstid** | **RU kostnad** | **Prestanda** |
| --- | --- | --- |
| 130 ms | 619.41 RU | ⚠ |

### <a name="c3-create-a-comment"></a>[C3] Skapa en kommentar

En kommentar har skapats genom att skriva motsvarande objekt den `posts` behållare.

![Skriva ett enskilt objekt till behållaren för inlägg](./media/how-to-model-partition-example/V1-C2.png)

| **Svarstid** | **RU kostnad** | **Prestanda** |
| --- | --- | --- |
| 7 ms | 8.57 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>KVARTAL [4] Lista över en publicera kommentarer

Vi börjar med en fråga som hämtar alla kommentarer om inlägget och igen, måste vi även sammanställd användarnamn separat för varje kommentar.

![Hämta alla kommentarer om ett inlägg och deras ytterligare datainsamling](./media/how-to-model-partition-example/V1-Q4.png)

Även om huvudfrågan filtrera på behållarens partitionsnyckel, penalizes aggregering användarnamnen separat övergripande prestanda. Vi kan förbättra som vid ett senare tillfälle.

| **Svarstid** | **RU kostnad** | **Prestanda** |
| --- | --- | --- |
| 23 ms | 27.72 RU | ⚠ |

### <a name="c4-like-a-post"></a>[C4] Som ett inlägg

Precis som **[C3]**, vi skapar motsvarande objekt i den `posts` behållare.

![Skriva ett enskilt objekt till behållaren för inlägg](./media/how-to-model-partition-example/V1-C2.png)

| **Svarstid** | **RU kostnad** | **Prestanda** |
| --- | --- | --- |
| 6 ms | 7.05 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[F5] Lista över ett inlägg likes

Precis som **[kvartal 4]**, vi fråga likes för inlägget, och sedan aggregera deras användarnamn.

![Hämta alla gillar för ett inlägg och deras ytterligare datainsamling](./media/how-to-model-partition-example/V1-Q5.png)

| **Svarstid** | **RU kostnad** | **Prestanda** |
| --- | --- | --- |
| 59 ms | 58.92 RU | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Lista de senaste x-inlägg som skapats kortfattat formuläret (matning)

Vi hämta de senaste inläggen genom att fråga den `posts` behållare sorterade efter fallande skapandedatum, och sedan sammanställd användarnamn och antal kommentarer och gilla-markeringar för var och en av posterna.

![Hämta senaste inläggen och deras ytterligare datainsamling](./media/how-to-model-partition-example/V1-Q6.png)

Vår första fråga inte igen, filtrera på Partitionsnyckeln för den `posts` behållare som utlöser en kostsamma fan-out. Det här är ännu värre när vi rikta en mycket större resultatmängd och sortera resultaten med en `ORDER BY` satsen, vilket gör det dyrare när det gäller begäransenheter.

| **Svarstid** | **RU kostnad** | **Prestanda** |
| --- | --- | --- |
| 306 ms | 2063.54 RU | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>Återger på prestanda hos V1

Vi kan titta på prestandaproblem som vi har i föregående avsnitt, för att identifiera två huvudsakliga typer av problem:

- vissa begäranden kräver flera frågor utfärdas för att samla in all information som vi behöver ska returneras
- vissa frågor Filtrera inte efter Partitionsnyckeln för de behållare som de är riktade mot, vilket leder till en fan-out som hindrar våra skalbarhet.

Nu ska vi lösa var och en av de här problemen och från och med den första mallen för.

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>V2: Introduktion till denormalisering optimera skrivskyddade frågor

Orsaken till varför vi behöver skicka ytterligare begäranden i vissa fall beror resultatet av den första begäran inte innehåller alla data som vi behöver för att returnera. När du arbetar med en icke-relationellt datalager som Azure Cosmos DB, kan den här typen av problemet ofta lösas genom att avnormalisera data i vår datauppsättning.

I vårt exempel har vi ändra inlägg för att lägga till användarnamnet för det inlägget författare, antal kommentarer och antal likes:

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

Vi kan också ändra kommentar och som objekt att lägga till användarnamnet för användaren som skapade dem:

    {
      "id": "<comment-id>",
      "type": "comment",
      "postId": "<post-id>",
      "userId": "<comment-author-id>",
      "userUsername": "<comment-author-username>",
      "content": "<comment-content>",
      "creationDate": "<comment-creation-date>"
    }

    {
      "id": "<like-id>",
      "type": "like",
      "postId": "<post-id>",
      "userId": "<liker-id>",
      "userUsername": "<liker-username>",
      "creationDate": "<like-creation-date>"
    }

### <a name="denormalizing-comment-and-like-counts"></a>Avnormalisera kommentar och som antal

Vad vi vill uppnå är att varje gång vi lägger till en kommentar eller ett liknande kan vi också öka den `commentCount` eller `likeCount` i motsvarande inlägget. Som våra `posts` behållare har partitionerats med `postId`, det nya objektet (kommentera eller en) och dess motsvarande post finns i samma logiska partition. Därför kan vi använda en [lagrade proceduren](stored-procedures-triggers-udfs.md) att utföra åtgärden.

Nu när du skapar en kommentar (**[C3]**), i stället för bara att lägga till ett nytt objekt i den `posts` behållare vi anropa följande lagrade procedur i den behållaren:

```javascript
function createComment(postId, comment) {
  var collection = getContext().getCollection();

  collection.readDocument(
    `${collection.getAltLink()}/docs/${postId}`,
    function (err, post) {
      if (err) throw err;

      post.commentCount++;
      collection.replaceDocument(
        post._self,
        post,
        function (err) {
          if (err) throw err;

          comment.postId = postId;
          collection.createDocument(
            collection.getSelfLink(),
            comment
          );
        }
      );
    })
}
```

Den här lagrade proceduren tar ID för inlägget och innehållet i den nya kommentaren som parametrar och sedan:

- hämtar inlägget
- steg i `commentCount`
- ersätter inlägget
- lägger till den nya kommentaren

Som lagrade procedurer körs som atomiska transaktioner, är det säkert som värdet för `commentCount` och det faktiska antalet kommentarer alltid förblir synkroniserade.

Vi kallar naturligtvis en liknande lagrad procedur när du lägger till nya gillar att öka den `likeCount`.

### <a name="denormalizing-usernames"></a>Avnormalisera användarnamn

Användarnamn kräver en annan metod som användare inte bara finns i olika partitioner, men i en annan behållare. När vi har avnormalisera data över partitioner och behållare, vi kan använda källbehållare [ändringsflödet](change-feed.md).

I vårt exempel använder vi ändringsfeed av den `users` behållare för att reagera när användare uppdaterar sina användarnamn. När det sker så vi att sprida ändringen genom att anropa en annan lagrad procedur på den `posts` behållare:

![Avnormalisera användarnamn till behållaren inlägg](./media/how-to-model-partition-example/denormalization-1.png)

```javascript
function updateUsernames(userId, username) {
  var collection = getContext().getCollection();

  collection.queryDocuments(
    collection.getSelfLink(),
    `SELECT * FROM p WHERE p.userId = '${userId}'`,
    function (err, results) {
      if (err) throw err;

      for (var i in results) {
        var doc = results[i];
        doc.userUsername = username;

        collection.upsertDocument(
          collection.getSelfLink(),
          doc);
      }
    });
}
```

Den här lagrade proceduren tar ID för användaren och användarens nytt användarnamn som parametrar och sedan:

- hämtar alla objekt som matchar den `userId` (vilket kan vara inlägg, kommentarer, eller gillar)
- för var och en av de objekt
  - ersätter den `userUsername`
  - ersätter objektet

> [!IMPORTANT]
> Den här åtgärden är dyra eftersom den kräver den här lagrade proceduren som ska köras på varje partition för den `posts` behållare. Vi förutsätter att de flesta användare väljer en lämplig användarnamn under registreringen och någonsin ändras inte den, så att den här uppdateringen körs mycket sällan.

## <a name="what-are-the-performance-gains-of-v2"></a>Vad är prestandavinster på V2?

### <a name="q2-retrieve-a-post"></a>[2] Hämta ett inlägg

Nu när våra denormalisering är på plats, behöver vi bara hämta ett enskilt objekt för att hantera begäran.

![Hämta ett enskilt objekt från behållaren inlägg](./media/how-to-model-partition-example/V2-Q2.png)

| **Svarstid** | **RU kostnad** | **Prestanda** |
| --- | --- | --- |
| 2 ms | 1 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>KVARTAL [4] Lista över en publicera kommentarer

Här igen, vi och spara de extra begäranden som hämtas de användarnamn och slutar med en enkel fråga som filtrerar på Partitionsnyckeln.

![Hämtar alla kommentarer om ett inlägg](./media/how-to-model-partition-example/V2-Q4.png)

| **Svarstid** | **RU kostnad** | **Prestanda** |
| --- | --- | --- |
| 4 ms | 7.72 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[F5] Lista över ett inlägg likes

Exakt samma situation när likes.

![Hämta alla gillar för ett inlägg](./media/how-to-model-partition-example/V2-Q5.png)

| **Svarstid** | **RU kostnad** | **Prestanda** |
| --- | --- | --- |
| 4 ms | 8.92 RU | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>V3: Att se till att är alla begäranden som skalbara

Titta på vår övergripande prestandaförbättringar, det fortfarande finns två begäranden som vi inte har fullständigt optimerad: **[kvartal 3]** och **[Q6]**. De är de förfrågningar som rör frågor som inte filtrerar på Partitionsnyckeln för de behållare som de är riktade mot.

### <a name="q3-list-a-users-posts-in-short-form"></a>KVARTAL [3] Lista över en användares inlägg i kortformen

Den här begäran redan dra nytta av förbättringarna i V2, som besparar ytterligare frågor.

![Hämta alla poster för en användare](./media/how-to-model-partition-example/V2-Q3.png)

Men återstående frågan fortfarande inte filtrerar på Partitionsnyckeln för den `posts` behållare.

Sätt att tänka på den här situationen är faktiskt enkel:

1. Den här begäran *har* att filtrera på den `userId` eftersom vi vill hämta alla poster för en viss användare
1. Den inte utföra skala eftersom den körs mot den `posts` behållare som inte har partitionerats med `userId`
1. Om den uppenbar, skulle vi lösa våra prestandaproblem genom att köra den här begäran mot en behållare som *är* partitionerade av `userId`
1. Det visar sig att vi redan har en sådan behållare: den `users` behållare!

Så vi introducera en andra nivå av denormalisering genom att duplicera hela inlägg till den `users` behållare. Genom att göra som vi effektivt hämta en kopia av våra inlägg endast partitioneras i en dimensioner, vilket gör dem vägen mer effektivt att hämta med sina `userId`.

Den `users` behållaren innehåller nu 2 typer av objekt:

    {
      "id": "<user-id>",
      "type": "user",
      "userId": "<user-id>",
      "username": "<username>"
    }

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

Tänk på följande:

- Vi har introducerat en `type` i objektet för användaren att skilja användare från inlägg,
- Vi har också lagt till en `userId` i det objekt som användaren som är redundant med den `id` fältet krävs men som den `users` behållaren nu har partitionerats med `userId` (och inte `id` som tidigare)

För att uppnå den denormalisering, använder vi återigen ändringen feed. Den här tiden kan vi reagera på ändringsfeed av den `posts` behållare för att skicka det nya eller uppdaterade inlägget till den `users` behållare. Och eftersom lista inlägg inte kräver för att returnera sina hela innehållet, vi trunkera dem i processen.

![Avnormalisera inlägg i användarbehållaren](./media/how-to-model-partition-example/denormalization-2.png)

Nu kan vi vidarebefordra frågan till den `users` behållare, filtrering på behållarens partitionsnyckel.

![Hämta alla poster för en användare](./media/how-to-model-partition-example/V3-Q3.png)

| **Svarstid** | **RU kostnad** | **Prestanda** |
| --- | --- | --- |
| 4 ms | 6.46 RU | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Lista de senaste x-inlägg som skapats kortfattat formuläret (matning)

Vi behöver hantera en liknande situation: även efter återställning ytterligare frågor har lämnat onödiga denormalisering som introducerades i V2, återstående frågan inte filtrerar på behållarens partitionsnyckel:

![Hämtar senaste inläggen](./media/how-to-model-partition-example/V2-Q6.png)

Enligt samma metod, maximera prestanda och skalbarhet för denna begäran kräver att den når en partition. Detta är möjligt eftersom vi behöver bara returnera ett begränsat antal objekt; för att fylla i vår bloggplattform startsida, behöver vi bara hämta de 100 senaste inläggen utan att behöva sidbryta genom hela datauppsättningen.

Så för att optimera den här senaste begäran vi introducera en tredje behållare till vår design, dedikerad helt till betjänar denna begäran. Vi avnormalisera vår inlägg till att nya `feed` behållare:

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

Den här behållaren har partitionerats med `type`, som alltid kommer att vara `post` i våra artiklar. Gör som säkerställer att alla objekt i den här behållaren ska vara i samma partition.

För att uppnå denormalisering, behöver vi bara koppla på ändringsflödet pipeline som tidigare har vi lagt för att skicka ut inlägg till den nya behållaren. En viktig sak till utan tänka på är att vi måste se till att vi bara lagrar 100 senaste inläggen; i annat fall kan innehållet i behållaren växa överskrider den maximala storleken för en partition. Detta görs genom att anropa en [efter utlösaren](stored-procedures-triggers-udfs.md#triggers) varje gång ett dokument läggs till i behållaren:

![Avnormalisera inlägg till feed behållaren](./media/how-to-model-partition-example/denormalization-3.png)

Här är brödtexten i efter utlösaren som trunkerar samlingen:

```javascript
function truncateFeed() {
  const maxDocs = 100;
  var context = getContext();
  var collection = context.getCollection();

  collection.queryDocuments(
    collection.getSelfLink(),
    "SELECT VALUE COUNT(1) FROM f",
    function (err, results) {
      if (err) throw err;

      processCountResults(results);
    });

  function processCountResults(results) {
    // + 1 because the query didn't count the newly inserted doc
    if ((results[0] + 1) > maxDocs) {
      var docsToRemove = results[0] + 1 - maxDocs;
      collection.queryDocuments(
        collection.getSelfLink(),
        `SELECT TOP ${docsToRemove} * FROM f ORDER BY f.creationDate`,
        function (err, results) {
          if (err) throw err;

          processDocsToRemove(results, 0);
        });
    }
  }

  function processDocsToRemove(results, index) {
    var doc = results[index];
    if (doc) {
      collection.deleteDocument(
        doc._self,
        function (err) {
          if (err) throw err;

          processDocsToRemove(results, index + 1);
        });
    }
  }
}
```

Det sista steget är att skicka frågan till vår nya `feed` behållare:

![Hämtar senaste inläggen](./media/how-to-model-partition-example/V3-Q6.png)

| **Svarstid** | **RU kostnad** | **Prestanda** |
| --- | --- | --- |
| 9 ms | 16.97 RU | ✅ |

## <a name="conclusion"></a>Sammanfattning

Låt oss ta en titt på de övergripande prestanda och skalbarhet förbättringar som vi har introducerat över olika versioner av våra design.

| | V1 | V2 | V3 |
| --- | --- | --- | --- |
| **[C1]** | 7 ms / 5.71 RU | 7 ms / 5.71 RU | 7 ms / 5.71 RU |
| **[Q1]** | 2 ms / 1 RU | 2 ms / 1 RU | 2 ms / 1 RU |
| **[C2]** | 9 ms / 8.76 RU | 9 ms / 8.76 RU | 9 ms / 8.76 RU |
| **[Q2]** | 9 ms / 19.54 RU | 2 ms / 1 RU | 2 ms / 1 RU |
| **[Q3]** | 130 ms / 619.41 RU | 28 ms / 201.54 RU | 4 ms / 6.46 RU |
| **[C3]** | 7 ms / 8,57 RU | 7 ms / 15.27 RU | 7 ms / 15.27 RU |
| **[Q4]** | 23 ms / 27.72 RU | 4 ms / 7.72 RU | 4 ms / 7.72 RU |
| **[C4]** | 6 ms / 7.05 RU | 7 ms / 14.67 RU | 7 ms / 14.67 RU |
| **[Q5]** | 59 ms / 58.92 RU | 4 ms / 8.92 RU | 4 ms / 8.92 RU |
| **[Q6]** | 306 ms / 2063.54 RU | 83 ms / 532.33 RU | 9 ms / 16.97 RU |

### <a name="we-have-optimized-a-read-heavy-scenario"></a>Vi har optimerat ett scenario med Läs-aktiverat

Du kanske har märkt att vi har koncentrerade vårt arbete till att förbättra prestandan för läsbegäranden (frågor) på bekostnad av skrivbegäranden (kommandon). I många fall kan utlösa skrivåtgärder nu efterföljande denormalisering via ändringen feeds, vilket gör dem mer beräkningsmässigt dyra och längre tid att Materialisera.

Detta motiveras av det faktum att en bloggplattform (till exempel mest sociala appar) är Läs omfattande, vilket innebär att mängden läsbegäranden den måste fungera är vanligtvis i storlek överstiger mängden skrivbegäranden. Så det vara bra att göra skrivförfrågningar dyrare att köra för att låta vara läsbegäranden billigare och bättre utför.

Om vi tittar på den mest extrema optimering som vi har gjort **[Q6]** gick från 2000 + ru: er till bara 17 ru: er, vi har uppnått som genom att avnormalisera inlägg till en kostnad av cirka 10 ru: er per artikel. När vi skulle fungera mer feed begäranden än skapas eller uppdateras med inlägg är kostnaden för den här denormalisering försumbar överväger övergripande besparingarna.

### <a name="denormalization-can-be-applied-incrementally"></a>Denormalisering kan tillämpas stegvis

Skalbarhet-förbättringar som vi har utforskat i den här artikeln omfattar denormalisering och duplicering av data i datauppsättningen. Det bör noteras att dessa optimeringar inte behöver placeras på plats på dag 1. Frågor som filtrerar på partitionsnycklar bättre prestanda i stor skala, men flera partitioner frågor kan vara helt godkända om kallas de data som sällan eller mot en begränsad mängd data. Om du bara skapar en prototyp eller lansera en produkt med en liten och kontrollerad användarbas, kan du förmodligen över dessa förbättringar för senare. Det viktiga sedan är att [övervakaren](use-metrics.md) så att du kan avgöra om och när det är dags att sätta dem din modell-prestanda.

Ändringsfeed att vi använder för att distribuera uppdateringar till andra behållare store allt det uppdateras kontinuerligt. Detta gör det möjligt att begära alla uppdateringar sedan skapandet av behållare och bootstrap Avnormaliserade vyer som en catch-up engångsåtgärd även om systemet redan har stora mängder data.

## <a name="next-steps"></a>Nästa steg

Efter den här introduktionen till praktiska-datamodellerings och partitionering, kanske du vill kontrollera i följande artiklar om du vill granska de begrepp som vi har gått igenom:

- [Arbeta med databaser, behållare och objekt](databases-containers-items.md)
- [Partitionering i Azure Cosmos DB](partitioning-overview.md)
- [Ändringsflödet i Azure Cosmos DB](change-feed.md)

<!--Update_Description: new articles on how to model partition example -->
<!--ms.date: 04/15/2019-->