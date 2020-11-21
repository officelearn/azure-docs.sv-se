---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 3a32946e10e8a81394b54fc44e4c92e8625e7ad6
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015501"
---
I den här snabb starten lär du dig grundläggande design mönster för Talarigenkänning med hjälp av tal-SDK, inklusive:

* Text beroende och text oberoende verifiering
* Högtalar identifiering för att identifiera ett röst exempel bland en grupp röster
* Ta bort röst profiler

En övergripande överblick över tal igenkännings koncept finns i [översikts](../../../speaker-recognition-overview.md) artikeln.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har ett Azure-konto och en röst tjänst prenumeration. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Talarigenkänning stöds för närvarande *bara* i Azure tal-resurser som skapats i `westus` regionen.

## <a name="text-dependent-verification"></a>Text beroende verifiering

Talarverifiering är en bekräftelse på att en talare matchar en känd eller **registrerad** röst. Det första steget är att **Registrera** en röst profil, så att tjänsten har något att jämföra framtida röst exempel mot. I det här exemplet registrerar du profilen med en **text beroende** strategi, som kräver att en unik lösen fras används för både registrering och verifiering. En lista över lösen fraser som stöds finns i [referens dokumenten](/rest/api/speakerrecognition/) .

Börja med att [skapa en röst profil](/rest/api/speakerrecognition/verification/textdependent/createprofile). Du måste lägga till din prenumerations nyckel och region för röst tjänsten i var och en av de olika Vändnings kommandona i den här artikeln.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile":::

Observera att det finns tre typer av röst profiler:

- Text beroende verifiering
- Text oberoende verifiering
- Text oberoende identifiering

I det här fallet skapar du en text beroende verifierings röst profil. Du bör få följande svar.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile_response":::

Sedan registrerar du [röst profilen](/rest/api/speakerrecognition/verification/textdependent/createenrollment). För `--data-binary` parametervärdet anger du en ljudfil på datorn som innehåller något av de lösen fraser som stöds, till exempel "min röst är mitt Passport, verifiera mig". Du kan spela in en sådan ljudfil med en app som [Windows röst inspelare](https://www.microsoft.com/p/windows-voice-recorder/9wzdncrfhwkn?activetab=pivot:overviewtab)eller så kan du skapa den med [text-till-tal](../../../index-text-to-speech.yml).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll":::

Du bör få följande svar.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_1":::

Det här svaret visar att du behöver registrera två fler ljud exempel.

När du har registrerat totalt tre ljud exempel bör du få följande svar.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_2":::

Nu är du redo att [Verifiera ett ljud exempel mot röst profilen](/rest/api/speakerrecognition/verification/textdependent/verifyprofile). Ljud exemplet bör innehålla samma lösen fras som de exempel som du använde för att registrera röst profilen.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify":::

Du bör få följande svar.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify_response":::

`Accept`Innebär att lösen frasen matchar och verifieringen lyckades. Svaret innehåller också en likhets Poäng som sträcker sig från 0,0-1.0.

[Ta bort röst profilen](/rest/api/speakerrecognition/verification/textdependent/deleteprofile)för att avsluta.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_delete_profile":::

Det finns inget svar.

## <a name="text-independent-verification"></a>Text oberoende verifiering

I motsats till **text beroende** verifiering, **text oberoende** verifiering:

* Kräver ingen viss lösen fras för att kunna läsas, något kan ses över
* Kräver inte tre ljud exempel *, men kräver* 20 sekunders total ljud

Börja med att [skapa en text oberoende verifierings profil](/rest/api/speakerrecognition/verification/textindependent/createprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile":::

Du bör få följande svar.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile_response":::

Registrera sedan [röst profilen](/rest/api/speakerrecognition/verification/textindependent/createenrollment). I stället för att skicka tre ljud exempel måste du skicka ljud exempel som innehåller totalt 20 sekunders ljud.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll":::

När du har skickat tillräckligt många ljud-exempel bör du få följande svar.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll_response":::

Nu är du redo att [Verifiera ett ljud exempel mot röst profilen](/rest/api/speakerrecognition/verification/textindependent/verifyprofile). Detta ljud exempel behöver inte innehålla någon lösen fras. Det kan innehålla ett tal, så länge det innehåller totalt minst fyra sekunders ljud.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify":::

Du bör få följande svar.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify_response":::

`Accept`Innebär att verifieringen lyckades. Svaret innehåller också en likhets Poäng som sträcker sig från 0,0-1.0.

[Ta bort röst profilen](/rest/api/speakerrecognition/verification/textindependent/deleteprofile)för att avsluta.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_delete_profile":::

Det finns inget svar.

## <a name="speaker-identification"></a>Talaridentifiering

Talaridentifiering används för att bestämma **vem som** talar från en specifik grupp med registrerade röster. Processen liknar **text oberoende verifiering**, med den största skillnaden att kunna verifiera mot flera röst profiler samtidigt, i stället för att verifiera mot en enskild profil.

Börja med att [skapa en text oberoende identifierings profil](/rest/api/speakerrecognition/identification/textindependent/createprofile).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile":::

Du bör få följande svar.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile_response":::

Sedan registrerar du [röst profilen](/rest/api/speakerrecognition/identification/textindependent/createenrollment). Återigen måste du skicka ljud exempel som innehåller totalt 20 sekunders ljud. Dessa exempel behöver inte innehålla en lösen fras.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll":::

När du har skickat tillräckligt många ljud-exempel bör du få följande svar.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll_response_2":::

Nu är du redo att [identifiera ett ljud exempel med röst profilen](/rest/api/speakerrecognition/identification/textindependent/identifysinglespeaker). Kommandot identifiera accepterar en kommaavgränsad lista med möjliga röst profil-ID: n. I det här fallet skickar du bara det ID för röst profilen som du skapade tidigare. Men om du vill kan du skicka i flera röst profil-ID: n där varje röst profil registreras med ljud exempel från en annan röst.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify":::

Du bör få följande svar.

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify_response":::

Svaret innehåller ID: t för den röst profil som bäst matchar ljud exemplet du skickade. Den innehåller också en lista över kandidat röst profiler, rankade i likhets ordning.

[Ta bort röst profilen](/rest/api/speakerrecognition/identification/textindependent/deleteprofile)för att avsluta.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_delete_profile":::

Det finns inget svar.