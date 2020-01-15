---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: dapine
ms.openlocfilehash: 1022a744564ed61a90973f7bba3eb32e9a632b46
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942775"
---
## <a name="prerequisites"></a>Krav

Innan du börjar ska du se till att:

> [!div class="checklist"]
> * [Konfigurera utvecklings miljön](../../../../quickstarts/setup-platform.md?tabs=vs&pivots=programmming-language-csharp)
> * [Skapa ett tomt exempel projekt](../../../../quickstarts/create-project.md?pivots=programmming-language-csharp)
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Ladda upp en källfil till en Azure-Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="open-your-project-in-visual-studio"></a>Öppna projektet i Visual Studio

Det första steget är att se till att projektet är öppet i Visual Studio.

1. Starta Visual Studio 2019.
2. Läs in projektet och öppna `Program.cs`.

## <a name="add-a-reference-to-newtonsoftjson"></a>Lägg till en referens till Newtonsoft. JSON

1. I Solution Explorer högerklickar du på projektet **HelloWorld** och väljer sedan **Hantera NuGet-paket** för att Visa NuGet Package Manager.

1. Leta upp List rutan **paket källa** i det övre högra hörnet och kontrol lera att **`nuget.org`** är markerat.

1. I det övre vänstra hörnet väljer du **Bläddra**.

1. Skriv *Newtonsoft. JSON* i rutan Sök och välj **RETUR**.

1. Från Sök resultaten väljer du paketet [**Newtonsoft. JSON**](https://www.nuget.org/packages/Newtonsoft.Json) och väljer sedan **Installera** för att installera den senaste stabila versionen.

1. Acceptera alla avtal och licenser för att starta installationen.

   När paketet har installerats visas en bekräftelse i fönstret **Package Manager-konsol** .

## <a name="start-with-some-boilerplate-code"></a>Börja med viss exempel kod

Nu ska vi lägga till kod som fungerar som en Skeleton för vårt projekt.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=6-43,138,277)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>JSON-omslutningar

Som REST API utföra begär anden i JSON-format och även returnera resultat i JSON kan vi interagera med dem med enbart strängar, men det rekommenderas inte.
För att göra det lättare att hantera förfrågningar och svar kan vi deklarera några klasser som ska användas för serialisering/deserialisering av JSON.

Gå vidare och placera deras deklarationer efter `TranscribeAsync`.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=140-276)]

## <a name="create-and-configure-an-http-client"></a>Skapa och konfigurera en http-klient
Det första vi behöver är en http-klient som har rätt bas-URL och autentisering.
Infoga den här koden i `TranscribeAsync` [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=46-50)]

## <a name="generate-a-transcription-request"></a>Generera en avskrifts förfrågan
Nu ska vi skapa en avskrifts förfrågan. Lägg till den här koden i `TranscribeAsync` [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=52-57)]

## <a name="send-the-request-and-check-its-status"></a>Skicka begäran och kontrol lera dess status
Nu ska vi publicera begäran till tal tjänsten och kontrol lera den ursprungliga svars koden. Den här svars koden anger bara om tjänsten har tagit emot begäran. Tjänsten returnerar en URL i svarshuvuden som är den plats där den kommer att lagra avskrifts status.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=59-70)]

## <a name="wait-for-the-transcription-to-complete"></a>Vänta tills avskriften har slutförts
Eftersom tjänsten bearbetar avskriften asynkront måste vi avsöka efter status varje så ofta. Vi ska kontrol lera var femte sekund.

Vi kan kontrol lera statusen genom att hämta innehållet på URL: en som vi fick när du publicerade begäran. När vi får tillbaka innehållet deserialiserar vi det till någon av våra hjälp klasser för att göra det enklare att interagera med.

Här är avsöknings koden med status visning för allt, förutom att slutföra slut för ande, vi ska göra det härnäst.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=72-106,121-137)]

## <a name="display-the-transcription-results"></a>Visa avskrifts resultatet
När tjänsten har slutfört avskriften kommer resultatet att lagras i en annan URL som vi kan få från status svaret.

Här gör vi en begäran om att ladda ned dessa resultat till en temporär fil innan de läser och deserialiserar dem.
När resultatet har lästs in kan vi skriva ut dem till-konsolen.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=107-120)]

## <a name="check-your-code"></a>Kontrol lera koden
I det här läget bör din kod se ut så här: (vi har lagt till några kommentarer till den här versionen) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=6-277)]

## <a name="build-and-run-your-app"></a>Skapa och kör din app

Nu är du redo att bygga din app och testa vår tal igenkänning med röst tjänsten.

1. **Kompilera koden** – från meny raden i Visual Studio väljer du **Bygg** > build- **lösning**.
2. **Starta din app** – från meny raden väljer du **Felsök** > **Starta fel sökning** eller tryck på **F5**.
3. **Starta igenkänning** – du uppmanas att tala en fras på engelska. Ditt tal skickas till tal tjänsten, skrivs som text och återges i-konsolen.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]
