---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: trbye
ms.openlocfilehash: 804cc2213379364002a093509f2393e375c3971b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400997"
---
## <a name="prerequisites"></a>Krav

Innan du börjar måste du:

> [!div class="checklist"]
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programmming-language-java)
> * [Skapa en Azure Speech-resurs](../../../../get-started.md)
> * [Ladda upp en källfil till en Azure-blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)


## <a name="open-your-project-in-eclipse"></a>Öppna projektet i Eclipse

Det första steget är att se till att du har ditt projekt öppet i Eclipse.

1. Starta Eclipse
2. Ladda projektet och `Main.java`öppna .

## <a name="add-a-reference-to-gson"></a>Lägga till en referens till Gson
Vi kommer att använda en extern JSON serializer / deserializer i denna snabbstart. För Java har vi valt [Gson.](https://github.com/google/gson)

Öppna pom.xml och lägg till följande referens.

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/pom.xml?range=19-25)]

## <a name="start-with-some-boilerplate-code"></a>Börja med en standardkod

Låt oss lägga till lite kod som fungerar som ett skelett för vårt projekt.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=1-13,95-105,206-207)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>JSON Omslag

Eftersom REST API: s ta förfrågningar i JSON-format och även returnera resultat i JSON vi kunde interagera med dem med bara strängar, men det rekommenderas inte.
För att göra förfrågningar och svar lättare att hantera, kommer vi att deklarera några klasser att använda för serialisering / deserializing JSON.

Gå vidare och lägga `Main`sina förklaringar före .
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=15-93)]

## <a name="create-and-configure-an-http-client"></a>Skapa och konfigurera en Http-klient
Det första vi behöver är en Http-klient som har en korrekt bas-URL och autentiseringsuppsättning.
Infoga den `Main` här koden i[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=106-113)]

## <a name="generate-a-transcription-request"></a>Generera en begäran om transkription
Därefter genererar vi begäran om transkription. Lägg till `Main` den här koden i[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=115-116)]

## <a name="send-the-request-and-check-its-status"></a>Skicka begäran och kontrollera dess status
Nu lägger vi upp begäran till taltjänsten och kontrollerar den ursprungliga svarskoden. Den här svarskoden anger helt enkelt om tjänsten har tagit emot begäran. Tjänsten returnerar en url i svarsrubrikerna som är den plats där den lagrar transkriptionsstatusen.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=118-129)]

## <a name="wait-for-the-transcription-to-complete"></a>Vänta tills transkriptionen är klar
Eftersom tjänsten bearbetar transkriptionen asynkront, måste vi enkät för sin status varje så ofta. Vi kollar var femte sekund.

Vi kan kontrollera status genom att hämta innehållet på webbadressen vi fick när den postat begäran. När vi får tillbaka innehållet deserialiserar vi det till en av våra hjälpklasser för att göra det lättare att interagera med.

Här är avsökningskoden med statusvisning för allt utom ett framgångsrikt slutförande, vi gör det nästa.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=131-159,192-204)]

## <a name="display-the-transcription-results"></a>Visa transkriptionsresultaten
När tjänsten har slutfört transkriptionen kommer resultaten att lagras i en annan webbadress som vi kan få från statussvaret.

Vi hämtar innehållet i webbadressen, deserialiserar JSON och går igenom resultaten som skriver ut visningstexten när vi går.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=6-160-190)]

## <a name="check-your-code"></a>Kontrollera din kod
Vid denna punkt, din kod bör se ut så här: (Vi har lagt till några kommentarer till denna version) [!code-java [](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java]

## <a name="build-and-run-your-app"></a>Skapa och kör din app

Nu är du redo att bygga din app och testa vår taligenkänning med taltjänsten.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]
