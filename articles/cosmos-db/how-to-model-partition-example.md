---
title: Modell- och partitionsdata på Azure Cosmos DB med ett verkligt exempel
description: Lär dig hur du modellerar och partitionerar ett verkligt exempel med hjälp av Azure Cosmos DB Core API
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: 10f8ffd90215a21ca03e112aea463d444c623d06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445381"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>Så här modellerar och partitionerar du data i Azure Cosmos DB med ett verkligt exempel

Den här artikeln bygger på flera Azure Cosmos DB-koncept som [datamodellering,](modeling-data.md) [partitionering](partitioning-overview.md)och [etablerat dataflöde](request-units.md) för att visa hur du hanterar en verklig datadesignövning.

Om du vanligtvis arbetar med relationsdatabaser har du förmodligen byggt upp vanor och intuitioner om hur du utformar en datamodell. På grund av de specifika begränsningarna, men också de unika styrkorna i Azure Cosmos DB, översätter de flesta av dessa metodtips inte bra och kan dra dig till suboptimala lösningar. Målet med den här artikeln är att guida dig genom hela processen med att modellera ett verkligt användningsfall på Azure Cosmos DB, från artikelmodellering till entitetssamlokalisering och behållarpartitionering.

## <a name="the-scenario"></a>Scenariot

För denna övning kommer vi att överväga området för en blogging plattform där *användarna* kan skapa *inlägg*. Användare kan också *gilla* och lägga till *kommentarer* till dessa inlägg.

> [!TIP]
> Vi har lyft fram några ord i *kursivt;* dessa ord identifiera den typ av "saker" vår modell kommer att behöva manipulera.

Lägga till fler krav till vår specifikation:

- En förstasida visar en feed med nyligen skapade inlägg,
- Vi kan hämta alla inlägg för en användare, alla kommentarer för ett inlägg och alla gillar för ett inlägg,
- Inlägg returneras med användarnamn för sina författare och en räkning av hur många kommentarer och gillar de har,
- Kommentarer och gilla-markeringar returneras också med användarnamnet för de användare som har skapat dem,
- När inlägg visas som listor behöver de bara presentera en trunkerad sammanfattning av innehållet.

## <a name="identify-the-main-access-patterns"></a>Identifiera de viktigaste åtkomstmönstren

Till att börja med ger vi en viss struktur till vår ursprungliga specifikation genom att identifiera vår lösnings åtkomstmönster. När du utformar en datamodell för Azure Cosmos DB är det viktigt att förstå vilka begäranden vår modell måste användas för att se till att modellen kommer att hantera dessa begäranden effektivt.

För att göra den övergripande processen enklare att följa kategoriserar vi dessa olika begäranden som antingen kommandon eller frågor, lånar vissa ordförråd från [CQRS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation) där kommandon är skrivbegäranden (det vill säga avsikter att uppdatera systemet) och frågor är skrivskyddade begäranden.

Här är listan över förfrågningar som vår plattform måste exponera:

- **-Jag vet inte vad du går för.** Skapa/redigera en användare
- **[Q1]** Hämta en användare
- **- Jag vet inte vad du går för.** Skapa/redigera ett inlägg
- **[Q2]** Hämta ett inlägg
- **[Q3]** Lista en användares inlägg i kort form
- **- Jag vet inte vad du går för.** Skapa en kommentar
- **[Q4]** Lista ett inläggs kommentarer
- **-Jag vet inte vad du går för.** Gilla ett inlägg
- **[Q5]** Lista ett inläggs gilla-markeringar
- **[Q6]** Lista de *x* senaste inläggen som skapats i kort form (feed)

Eftersom detta skede har vi inte tänkt på detaljer om vad varje enhet (användare, inlägg etc.) kommer att innehålla. Detta steg är oftast bland de första som skall hanteras när man utformar mot en relationell butik, eftersom vi måste räkna ut hur dessa enheter kommer att översätta i form av tabeller, kolumner, utländska nycklar etc. Det är mycket mindre av ett problem med en dokumentdatabas som inte upprätthåller något schema vid skrivning.

Den främsta anledningen till att det är viktigt att identifiera våra åtkomstmönster från början, är att denna lista över förfrågningar kommer att vara vår testsvit. Varje gång vi itererar över vår datamodell kommer vi att gå igenom var och en av begäranden och kontrollera dess prestanda och skalbarhet.

## <a name="v1-a-first-version"></a>V1: En första version

Vi börjar med `users` två `posts`behållare: och .

### <a name="users-container"></a>Användarcontainer

Den här behållaren lagrar endast användarobjekt:

    {
      "id": "<user-id>",
      "username": "<username>"
    }

Vi partitionerar `id`den här behållaren med , vilket innebär att varje logisk partition i behållaren bara innehåller ett objekt.

### <a name="posts-container"></a>Behållare för inlägg

Den här behållaren är värd för inlägg, kommentarer och gilla-markeringar:

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

Vi partitionerar `postId`den här behållaren med , vilket innebär att varje logisk partition i behållaren kommer att innehålla ett inlägg, alla kommentarer för det inlägget och alla gilla-markeringar för det inlägget.

Observera att vi har `type` infört en egenskap i de objekt som lagras i den här behållaren för att skilja mellan de tre typer av entiteter som den här behållaren är värd för.

Dessutom har vi valt att referera till relaterade data istället för att bädda in dem (se det [här avsnittet](modeling-data.md) för mer information om dessa begrepp) eftersom:

- det finns ingen övre gräns för hur många inlägg en användare kan skapa,
- inlägg kan vara godtyckligt lång,
- det finns ingen övre gräns för hur många kommentarer och gillar ett inlägg kan ha,
- vi vill kunna lägga till en kommentar eller en liknande till ett inlägg utan att behöva uppdatera inlägget själv.

## <a name="how-well-does-our-model-perform"></a>Hur bra presterar vår modell?

Det är nu dags att bedöma prestanda och skalbarhet för vår första version. För var och en av de tidigare identifierade begäranden mäter vi dess svarstid och hur många begärandeenheter den förbrukar. Denna mätning görs mot en dummy datauppsättning som innehåller 100.000 användare med 5 till 50 inlägg per användare, och upp till 25 kommentarer och 100 gillar per inlägg.

### <a name="c1-createedit-a-user"></a>-Jag vet inte vad du går för. Skapa/redigera en användare

Den här begäran är enkel att implementera när `users` vi bara skapar eller uppdaterar ett objekt i behållaren. Begäranden kommer fint spridas över `id` alla partitioner tack vare partitionsnyckeln.

![Skriva ett enskilt objekt till behållaren för användare](./media/how-to-model-partition-example/V1-C1.png)

| **Svarstid** | **RU-laddning** | **Prestanda** |
| --- | --- | --- |
| 7 ms | 5.71 RU | ✅ |

### <a name="q1-retrieve-a-user"></a>[Q1] Hämta en användare

Hämtning av en användare görs genom att `users` läsa motsvarande objekt från behållaren.

![Hämta ett enskilt objekt från användarbehållaren](./media/how-to-model-partition-example/V1-Q1.png)

| **Svarstid** | **RU-laddning** | **Prestanda** |
| --- | --- | --- |
| 2 ms | 1 RU | ✅ |

### <a name="c2-createedit-a-post"></a>- Jag vet inte vad du går för. Skapa/redigera ett inlägg

På samma sätt som **[C1],** vi `posts` måste bara skriva till behållaren.

![Skriva ett enda objekt till behållaren inlägg](./media/how-to-model-partition-example/V1-C2.png)

| **Svarstid** | **RU-laddning** | **Prestanda** |
| --- | --- | --- |
| 9 ms | 8.76 RU | ✅ |

### <a name="q2-retrieve-a-post"></a>[Q2] Hämta ett inlägg

Vi börjar med att hämta motsvarande `posts` dokument från behållaren. Men det räcker inte, enligt vår specifikation måste vi också aggregera användarnamnet för inläggets författare och antalet av hur många kommentarer och hur många gillar det här inlägget har, vilket kräver 3 ytterligare SQL-frågor som ska utfärdas.

![Hämta ett inlägg och samla ytterligare data](./media/how-to-model-partition-example/V1-Q2.png)

Var och en av de ytterligare frågor filter på partitionsnyckeln för sina respektive behållare, vilket är precis vad vi vill maximera prestanda och skalbarhet. Men vi måste så småningom utföra fyra operationer för att returnera ett enda inlägg, så vi kommer att förbättra det i en nästa iteration.

| **Svarstid** | **RU-laddning** | **Prestanda** |
| --- | --- | --- |
| 9 ms | 19.54 RU | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>[Q3] Lista en användares inlägg i kort form

Först måste vi hämta önskade inlägg med en SQL-fråga som hämtar de tjänster som motsvarar just den användaren. Men vi måste också utfärda ytterligare frågor för att aggregera författarens användarnamn och antalet kommentarer och gillar.

![Hämta alla inlägg för en användare och samla deras ytterligare data](./media/how-to-model-partition-example/V1-Q3.png)

Denna implementering innebär många nackdelar:

- de frågor som samlar antalet kommentarer och gilla-markeringar måste utfärdas för varje inlägg som returneras av den första frågan.
- Huvudfrågan filtrerar inte på `posts` behållarens partitionsnyckel, vilket leder till en utfläktning och en partitionsgenomsökning över behållaren.

| **Svarstid** | **RU-laddning** | **Prestanda** |
| --- | --- | --- |
| 130 ms | 619,41 RU | ⚠ |

### <a name="c3-create-a-comment"></a>- Jag vet inte vad du går för. Skapa en kommentar

En kommentar skapas genom att `posts` du skriver motsvarande objekt i behållaren.

![Skriva ett enda objekt till behållaren inlägg](./media/how-to-model-partition-example/V1-C2.png)

| **Svarstid** | **RU-laddning** | **Prestanda** |
| --- | --- | --- |
| 7 ms | 8.57 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Q4] Lista ett inläggs kommentarer

Vi börjar med en fråga som hämtar alla kommentarer för det inlägget och återigen måste vi också aggregera användarnamn separat för varje kommentar.

![Hämta alla kommentarer för ett inlägg och samla deras ytterligare data](./media/how-to-model-partition-example/V1-Q4.png)

Även om huvudfrågan filtrerar på behållarens partitionsnyckel, bestraffar aggregera användarnamnen separat den övergripande prestandan. Vi kommer att förbättra det senare.

| **Svarstid** | **RU-laddning** | **Prestanda** |
| --- | --- | --- |
| 23 ms | 27.72 RU | ⚠ |

### <a name="c4-like-a-post"></a>-Jag vet inte vad du går för. Gilla ett inlägg

Precis som **[C3]** skapar vi motsvarande `posts` objekt i behållaren.

![Skriva ett enda objekt till behållaren inlägg](./media/how-to-model-partition-example/V1-C2.png)

| **Svarstid** | **RU-laddning** | **Prestanda** |
| --- | --- | --- |
| 6 ms | 7.05 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] Lista ett inläggs gilla-markeringar

Precis som **[Q4]** frågar vi ut samma sätt som för det inlägget och sammanställer sedan deras användarnamn.

![Hämta alla gilla-markeringar för ett inlägg och samla in ytterligare data](./media/how-to-model-partition-example/V1-Q5.png)

| **Svarstid** | **RU-laddning** | **Prestanda** |
| --- | --- | --- |
| 59 ms | 58.92 RU | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Lista de x senaste inläggen som skapats i kort form (feed)

Vi hämtar de senaste inläggen genom att fråga behållaren `posts` sorterad efter fallande skapandedatum, sedan sammanställa användarnamn och antal kommentarer och gilla-markeringar för vart och ett av inläggen.

![Hämta de senaste inläggen och samla deras ytterligare data](./media/how-to-model-partition-example/V1-Q6.png)

Återigen filtrerar vår första fråga inte på partitionsnyckeln för behållaren, `posts` vilket utlöser en kostsam fan-out. Den här är ännu värre eftersom vi riktar in oss `ORDER BY` på ett mycket större resultat och sorterar resultaten med en klausul, vilket gör det dyrare när det gäller begäranheter.

| **Svarstid** | **RU-laddning** | **Prestanda** |
| --- | --- | --- |
| 306 ms | 2063,54 RU | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>Reflektera över prestanda v1

Om vi tittar på de prestandaproblem vi stod inför i föregående avsnitt kan vi identifiera två huvudklasser av problem:

- vissa förfrågningar kräver att flera frågor utfärdas för att samla in alla data vi behöver för att returnera,
- Vissa frågor filtrerar inte på partitionsnyckeln för behållarna de riktar sig till, vilket leder till en utfläkt som hindrar vår skalbarhet.

Låt oss lösa vart och ett av dessa problem, börjar med den första.

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>V2: Introduktion av denormalisering för att optimera läsfrågor

Anledningen till att vi måste utfärda ytterligare begäranden i vissa fall är att resultaten av den ursprungliga begäran inte innehåller alla data som vi behöver returnera. När du arbetar med ett datalager som inte är relationellt som Azure Cosmos DB löses den här typen av problem ofta genom att avnormalisera data i vår datauppsättning.

I vårt exempel ändrar vi inläggsobjekt för att lägga till användarnamnet för inläggets författare, antalet kommentarer och antalet gilla-markeringar:

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

Vi ändrar också kommentar och liknande objekt för att lägga till användarnamn för den användare som har skapat dem:

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

### <a name="denormalizing-comment-and-like-counts"></a>Denormalizing kommentar och liknande räknas

Vad vi vill uppnå är att varje gång vi lägger `commentCount` till `likeCount` en kommentar eller liknande, vi också öka eller i motsvarande inlägg. Som `posts` vår behållare är `postId`partitionerad av , det nya objektet (kommentar eller liknande) och dess motsvarande inlägg sitta i samma logiska partition. Som ett resultat kan vi använda en [lagrad procedur](stored-procedures-triggers-udfs.md) för att utföra den åtgärden.

Nu när du skapar en kommentar (**[C3]**), `posts` istället för att bara lägga till ett nytt objekt i behållaren vi kallar följande lagrade procedur på den behållaren:

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

Den här lagrade proceduren tar ID:et för inlägget och brödtexten i den nya kommentaren som parametrar, sedan:

- hämtar inlägget
- ökar`commentCount`
- ersätter stolpen
- lägger till den nya kommentaren

Eftersom lagrade procedurer utförs som atomtransaktioner garanteras `commentCount` värdet och det faktiska antalet kommentarer alltid att vara synkroniserat.

Vi kallar naturligtvis en liknande lagrad procedur `likeCount`när du lägger till nya gillar att öka .

### <a name="denormalizing-usernames"></a>Denormalizing användarnamn

Användarnamn kräver en annan metod som användare inte bara sitta i olika partitioner, men i en annan behållare. När vi måste denormalisera data över partitioner och behållare kan vi använda källbehållarens [ändringsflöde](change-feed.md).

I vårt exempel använder vi `users` behållarens ändringsflöde för att reagera när användare uppdaterar sina användarnamn. När det händer sprider vi ändringen genom att `posts` anropa en annan lagrad procedur på behållaren:

![Denormalisera användarnamn i inläggsbehållaren](./media/how-to-model-partition-example/denormalization-1.png)

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

Den här lagrade proceduren tar användarens och användarens nya användarnamns ID som parametrar och sedan:

- hämtar alla objekt `userId` som matchar (som kan vara inlägg, kommentarer eller gilla-markeringar)
- för var och en av dessa poster
  - ersätter`userUsername`
  - ersätter artikeln

> [!IMPORTANT]
> Den här åtgärden är kostsam eftersom den kräver att den `posts` här lagrade proceduren körs på varje partition i behållaren. Vi antar att de flesta användare väljer ett lämpligt användarnamn under registreringen och kommer aldrig att ändra det, så den här uppdateringen kommer att köras mycket sällan.

## <a name="what-are-the-performance-gains-of-v2"></a>Vilka är prestandavinster av V2?

### <a name="q2-retrieve-a-post"></a>[Q2] Hämta ett inlägg

Nu när vår denormalisering är på plats, behöver vi bara hämta ett enda objekt för att hantera denna begäran.

![Hämta ett enskilt objekt från behållaren för inlägg](./media/how-to-model-partition-example/V2-Q2.png)

| **Svarstid** | **RU-laddning** | **Prestanda** |
| --- | --- | --- |
| 2 ms | 1 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Q4] Lista ett inläggs kommentarer

Även här kan vi avvara de extra förfrågningar som hämtade användarnamn och sluta med en enda fråga som filtrerar på partitionsnyckeln.

![Hämta alla kommentarer för ett inlägg](./media/how-to-model-partition-example/V2-Q4.png)

| **Svarstid** | **RU-laddning** | **Prestanda** |
| --- | --- | --- |
| 4 ms | 7.72 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] Lista ett inläggs gilla-markeringar

Exakt samma situation när lista lik.

![Hämta alla gilla-markeringar för ett inlägg](./media/how-to-model-partition-example/V2-Q5.png)

| **Svarstid** | **RU-laddning** | **Prestanda** |
| --- | --- | --- |
| 4 ms | 8.92 RU | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>V3: Se till att alla begäranden är skalbara

Om vi tittar på våra övergripande prestandaförbättringar finns det fortfarande två förfrågningar som vi inte har helt optimerat: **[Q3]** och **[Q6]**. De är de begäranden som involverar frågor som inte filtrerar på partitionsnyckeln för behållarna de riktar sig till.

### <a name="q3-list-a-users-posts-in-short-form"></a>[Q3] Lista en användares inlägg i kort form

Denna begäran drar redan nytta av de förbättringar som införts i V2, som sparar ytterligare frågor.

![Hämta alla inlägg för en användare](./media/how-to-model-partition-example/V2-Q3.png)

Men den återstående frågan filtreras fortfarande inte `posts` på behållarens partitionsnyckel.

Sättet att tänka på denna situation är faktiskt enkelt:

1. Denna begäran *måste* filtrera `userId` på eftersom vi vill hämta alla inlägg för en viss användare
1. Det fungerar inte bra eftersom det körs `posts` mot behållaren, som inte partitioneras av`userId`
1. Om vi anger det uppenbara skulle vi lösa vårt prestandaproblem genom att utföra den här begäran mot en behållare som *är* partitionerad av`userId`
1. Det visar sig att vi redan `users` har en sådan behållare: behållaren!

Så vi introducerar en andra nivå av denormalisering `users` genom att duplicera hela inlägg till behållaren. Genom att göra det får vi effektivt en kopia av våra inlägg, bara delas längs `userId`en annan dimension, vilket gör dem mycket effektivare att hämta genom deras .

Behållaren `users` innehåller nu 2 typer av objekt:

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

- vi har infört `type` ett fält i användarobjektet för att skilja användare från inlägg,
- Vi har också `userId` lagt till ett fält i `id` användarobjektet, som `users` är redundant med `userId` fältet `id` men som krävs eftersom behållaren nu partitioneras av (och inte som tidigare)

För att uppnå denna avnormalisering använder vi återigen förändringsfodret. Den här gången reagerar vi `posts` på behållarens ändringsflöde `users` för att skicka ett nytt eller uppdaterat inlägg till behållaren. Och eftersom notering inlägg inte kräver att returnera sitt fullständiga innehåll, kan vi trunkera dem i processen.

![Denormalisera inlägg i användarbehållaren](./media/how-to-model-partition-example/denormalization-2.png)

Vi kan nu dirigera `users` vår fråga till behållaren, filtrering på behållarens partitionsnyckel.

![Hämta alla inlägg för en användare](./media/how-to-model-partition-example/V3-Q3.png)

| **Svarstid** | **RU-laddning** | **Prestanda** |
| --- | --- | --- |
| 4 ms | 6.46 RU | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Lista de x senaste inläggen som skapats i kort form (feed)

Vi måste ta itu med en liknande situation här: även efter att spara ytterligare frågor kvar onödigt av denormalisering som infördes i V2, filtrerar den återstående frågan inte på behållarens partitionsnyckel:

![Hämtar de senaste inläggen](./media/how-to-model-partition-example/V2-Q6.png)

Enligt samma metod kräver en maximerad begärans prestanda och skalbarhet att den bara träffar en partition. Detta är tänkbart eftersom vi bara behöver returnera ett begränsat antal artiklar. För att fylla vår blogging plattform hemsida, vi behöver bara få de 100 senaste inläggen, utan att behöva paginera genom hela datauppsättningen.

Så för att optimera denna sista begäran introducerar vi en tredje behållare till vår design, helt dedikerade till att betjäna denna begäran. Vi denormalize våra tjänster `feed` till den nya behållaren:

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

Den här behållaren `type`är partitionerad `post` av , som alltid kommer att finnas i våra objekt. Om du gör det ser du till att alla objekt i den här behållaren sitter i samma partition.

För att uppnå denormalisering, vi behöver bara haka på förändringen foder rörledningen vi tidigare har infört för att skicka stolpar till den nya behållaren. En viktig sak att tänka på är att vi måste se till att vi bara lagrar de 100 senaste inläggen. Annars kan innehållet i behållaren växa utöver den maximala storleken på en partition. Detta görs genom att anropa en [post-trigger](stored-procedures-triggers-udfs.md#triggers) varje gång ett dokument läggs till i behållaren:

![Denormala inlägg i foderbehållaren](./media/how-to-model-partition-example/denormalization-3.png)

Här är brödtexten i den post-trigger som trunkerar samlingen:

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

Det sista steget är att omdirigera vår `feed` fråga till vår nya behållare:

![Hämtar de senaste inläggen](./media/how-to-model-partition-example/V3-Q6.png)

| **Svarstid** | **RU-laddning** | **Prestanda** |
| --- | --- | --- |
| 9 ms | 16.97 RU | ✅ |

## <a name="conclusion"></a>Slutsats

Låt oss ta en titt på de övergripande prestanda- och skalbarhetsförbättringar som vi har infört jämfört med de olika versionerna av vår design.

| | V1 | V2 | V3 (på andra) |
| --- | --- | --- | --- |
| **-Jag vet inte vad du går för.** | 7 ms / 5,71 RU | 7 ms / 5,71 RU | 7 ms / 5,71 RU |
| **[Q1]** | 2 ms / 1 RU | 2 ms / 1 RU | 2 ms / 1 RU |
| **- Jag vet inte vad du går för.** | 9 ms / 8,76 RU | 9 ms / 8,76 RU | 9 ms / 8,76 RU |
| **[Q2]** | 9 ms / 19,54 RU | 2 ms / 1 RU | 2 ms / 1 RU |
| **[Q3]** | 130 ms / 619,41 RU | 28 ms / 201,54 RU | 4 ms / 6,46 RU |
| **- Jag vet inte vad du går för.** | 7 ms / 8,57 RU | 7 ms / 15,27 RU | 7 ms / 15,27 RU |
| **[Q4]** | 23 ms / 27,72 RU | 4 ms / 7,72 RU | 4 ms / 7,72 RU |
| **-Jag vet inte vad du går för.** | 6 ms / 7,05 RU | 7 ms / 14,67 RU | 7 ms / 14,67 RU |
| **[Q5]** | 59 ms / 58,92 RU | 4 ms / 8,92 RU | 4 ms / 8,92 RU |
| **[Q6]** | 306 ms / 2063.54 RU | 83 ms / 532.33 RU | 9 ms / 16,97 RU |

### <a name="we-have-optimized-a-read-heavy-scenario"></a>Vi har optimerat ett lästunt scenario

Du kanske har märkt att vi har koncentrerat våra ansträngningar för att förbättra resultatet av läsförfrågningar (frågor) på bekostnad av skrivförfrågningar (kommandon). I många fall utlöser skrivåtgärder nu efterföljande denormalisering genom ändringsflöden, vilket gör dem mer beräkningsmässigt dyra och längre att materialisera.

Detta motiveras av det faktum att en blogging plattform (som de flesta sociala apps) är läs-tung, vilket innebär att mängden läsa förfrågningar den har att tjäna är oftast storleksordningar högre än mängden skrivförfrågningar. Så det är vettigt att göra skriva förfrågningar dyrare att utföra för att låta läsa förfrågningar vara billigare och bättre resultat.

Om vi tittar på den mest extrema optimering vi har gjort, **[Q6]** gick från 2000 + RU till bara 17 RU; Vi har uppnått detta genom att denormalisera tjänster till en kostnad av cirka 10 ru: er per objekt. Eftersom vi skulle tjäna mycket mer foder förfrågningar än skapande eller uppdateringar av tjänster, är kostnaden för denna avnormalisering försumbar med tanke på de totala besparingarna.

### <a name="denormalization-can-be-applied-incrementally"></a>Denormalisering kan tillämpas stegvis

De skalbarhetsförbättringar som vi har utforskat i den här artikeln innebär avnormalisering och dubblering av data i datauppsättningen. Det bör noteras att dessa optimeringar inte behöver införas på dag 1. Frågor som filtrerar på partitionsnycklar presterar bättre i stor skala, men frågor mellan partitioner kan vara helt acceptabla om de kallas sällan eller mot en begränsad datauppsättning. Om du bara bygger en prototyp, eller lanserar en produkt med en liten och kontrollerad användarbas, kan du förmodligen skona dessa förbättringar för senare; Vad som då är viktigt är att [övervaka](use-metrics.md) modellens prestanda så att du kan bestämma om och när det är dags att ta in dem.

Ändringsflödet som vi använder för att distribuera uppdateringar till andra behållare lagrar alla dessa uppdateringar beständigt. Detta gör det möjligt att begära alla uppdateringar sedan skapandet av behållaren och bootstrap denormaliserade vyer som en engångsinsamlingsåtgärd även om ditt system redan har en hel del data.

## <a name="next-steps"></a>Nästa steg

Efter denna introduktion till praktisk datamodellering och partitionering, kanske du vill kontrollera följande artiklar för att granska de begrepp som vi har täckt:

- [Arbeta med databaser, containrar och objekt](databases-containers-items.md)
- [Partitionering i Azure Cosmos DB](partitioning-overview.md)
- [Ändra feed i Azure Cosmos DB](change-feed.md)
