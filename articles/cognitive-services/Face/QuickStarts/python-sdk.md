---
title: 'Snabbstart: Face-klientbibliotek för Python'
description: Den här snabbstarten hjälper dig att komma igång med Face-klientbiblioteket för Python för att upptäcka, hitta liknande, identifiera, verifiera med mera.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 7ac1b85ee44627990931cfc7b3a65f6f7a661b3f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76165822"
---
# <a name="quickstart-face-client-library-for-python"></a>Snabbstart: Face-klientbibliotek för Python

Kom igång med Face-klientbiblioteket för Python. Följ dessa steg för att installera paketet och prova exempelkoden för grundläggande uppgifter. Face-tjänsten ger dig tillgång till avancerade algoritmer för att upptäcka och känna igen mänskliga ansikten i bilder.

Använd Face-klientbiblioteket för Python för att:

* Identifiera ansikten i en bild
* Hitta liknande ansikten
* Skapa och träna en persongrupp
* Identifiera ett ansikte
* Verifiera ansikten
* Ta en ögonblicksbild för datamigrering

[Referensdokumentation](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python) | [Ursprungskod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face) | [Paket (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/) | [Prover](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Inrätta

### <a name="create-a-face-azure-resource"></a>Skapa en Face Azure-resurs

Azure Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Face med [Azure-portalen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på din lokala dator. Du kan också:

* Få en [testnyckel](https://azure.microsoft.com/try/cognitive-services/#decision) giltig i sju dagar gratis. När du har registrerat dig är den tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visa din resurs på [Azure-portalen](https://portal.azure.com/)

När du har fått en nyckel från utvärderingsprenumerationen `FACE_SUBSCRIPTION_KEY`eller resursen skapar du en [miljövariabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln med namnet .
 
### <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

Skapa ett nytt&mdash;Python-skript*quickstart-file.py*, till exempel. Öppna den sedan i önskad redigerare eller IDE och importera följande bibliotek.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

Skapa sedan variabler för resursens Azure-slutpunkt och nyckel. Du kan behöva ändra den första delen`westus`av slutpunkten ( ) för att matcha din prenumeration.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigeraren, IDE:t eller skalet som kör den för att komma åt variabeln.

### <a name="install-the-client-library"></a>Installera klientbiblioteket

Du kan installera klientbiblioteket med:

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

## <a name="object-model"></a>Objektmodell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Face Python SDK.

|Namn|Beskrivning|
|---|---|
|[FaceClient (70)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | Den här klassen representerar din behörighet att använda Face-tjänsten och du behöver den för alla Face-funktioner. Du instansierar den med din prenumerationsinformation och använder den för att producera instanser av andra klasser. |
|[FaceOperations (FaceOperations)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|Den här klassen hanterar de grundläggande identifierings- och igenkänningsuppgifter som du kan göra med mänskliga ansikten. |
|[DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|Den här klassen representerar alla data som har identifierats från ett enda ansikte i en bild. Du kan använda den för att hämta detaljerad information om ansiktet.|
|[FaceListOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|Den här klassen hanterar molnlagrade **FaceList-konstruktioner** som lagrar en olika uppsättning ansikten. |
|[PersonGroupPersonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| Den här klassen hanterar **molnlagringspersonkonstruktioner,** som lagrar en uppsättning ansikten som tillhör en enda person.|
|[PersonGroupOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| Den här klassen hanterar de molnbevarade **PersonGroup-konstruktionerna,** som lagrar en uppsättning olika **personobjekt.** |
|[ShapshotOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|Den här klassen hanterar funktionen Ögonblicksbild. Du kan använda den för att tillfälligt spara alla dina molnbaserade ansiktsdata och migrera dessa data till en ny Azure-prenumeration. |

## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du utför följande uppgifter med Face-klientbiblioteket för Python:

* [Autentisera klienten](#authenticate-the-client)
* [Identifiera ansikten i en bild](#detect-faces-in-an-image)
* [Hitta liknande ansikten](#find-similar-faces)
* [Skapa och träna en persongrupp](#create-and-train-a-person-group)
* [Identifiera ett ansikte](#identify-a-face)
* [Verifiera ansikten](#verify-faces)
* [Ta en ögonblicksbild för datamigrering](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE]
> Den här snabbstarten förutsätter att du har skapat `FACE_SUBSCRIPTION_KEY`en [miljövariabel](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) för ansiktsnyckeln med namnet .

Instansiera en klient med din slutpunkt och nyckel. Skapa ett [CognitiveServicesCredentials-objekt](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) med nyckeln och använd det med slutpunkten för att skapa ett [FaceClient-objekt.](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python)

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Identifiera ansikten i en bild

Följande kod identifierar ett ansikte i en fjärravbildning. Den skriver ut det upptäckta ansiktets ID till konsolen och lagrar det även i programminnet. Sedan upptäcker den ansikten i en bild med flera personer och skriver ut sina ID:n till konsolen också. Genom att ändra parametrarna i [detect_with_url-metoden](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) kan du returnera olika information med varje [DetectedFace-objekt.](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

Mer identifieringsscenarier finns i exempelkoden på [GitHub.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py)

### <a name="display-and-frame-faces"></a>Visa och rama in ytor

Följande kod matar ut den givna bilden till displayen och ritar rektanglar runt ytorna med egenskapen DetectedFace.faceRectangle.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![En ung kvinna med den röd rektangel ritad runt ansiktet](../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>Hitta liknande ansikten

Följande kod tar ett enda upptäckt ansikte och söker i en uppsättning andra ansikten för att hitta matchningar. När en matchning hittas skrivs rektangelkoordinaterna för den matchade ytan ut till konsolen. 

### <a name="find-matches"></a>Hitta matchningar

Kör först koden i avsnittet ovan ([Identifiera ansikten i en bild](#detect-faces-in-an-image)) för att spara en referens till ett enda ansikte. Kör sedan följande kod för att hämta referenser till flera ansikten i en gruppavbildning.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Lägg sedan till följande kodblock för att hitta förekomster av det första ansiktet i gruppen. Läs [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) metod för att lära dig hur du ändrar det här beteendet.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Skriv ut matchningar

Använd följande kod för att skriva ut matchningsinformationen till konsolen.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>Skapa och träna en persongrupp

Följande kod skapar en **persongrupp** med tre olika **personobjekt.** Det associerar varje **person** med en uppsättning exempel bilder, och sedan det tåg för att kunna känna igen varje person. 

### <a name="create-persongroup"></a>Skapa persongrupp

Om du vill gå igenom det här scenariot måste du https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/imagesspara följande bilder i projektets rotkatalog: .

Den här gruppen bilder innehåller tre uppsättningar ansiktsbilder som motsvarar tre olika personer. Koden definierar tre **personobjekt** och associerar dem `woman` `man`med `child`bildfiler som börjar med , och .

När du har konfigurerat bilderna definierar du en etikett högst upp i skriptet för det **Persongrupp-objekt** som du ska skapa.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Lägg sedan till följande kod längst ned i skriptet. Den här koden skapar en **persongrupp** och tre **personobjekt.**

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Tilldela ansikten till personer

Följande kod sorterar dina bilder efter deras prefix, identifierar ansikten och tilldelar ansiktena till varje **personobjekt.**

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>Tåg PersonGroup

När du har tilldelat ansikten måste du träna **Persongroup** så att den kan identifiera de visuella funktioner som är associerade med var och en av dess **personobjekt.** Följande kod anropar den asynkrona **tågmetoden** och avsöker resultatet och skriver ut statusen till konsolen.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>Identifiera ett ansikte

Följande kod tar en bild med flera ansikten och letar efter identiteten på varje person i bilden. Den jämför varje upptäckt ansikte med en **PersonGroup**, en databas med olika **personobjekt** vars ansiktsdrag är kända.

> [!IMPORTANT]
> För att kunna köra det här exemplet måste du först köra koden i [Skapa och träna en persongrupp](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Få en testbild

Följande kod söker i roten till projektet efter en bild _test-image-person-group.jpg_ och identifierar ansikten i bilden. Du hittar den här bilden med de https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/imagesbilder som används för **PersonGroup-hantering:** .

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Identifiera ansikten

Identifiera **metoden** tar en matris med identifierade ansikten och jämför dem med en **persongrupp**. Om den kan matcha ett upptäckt ansikte med en **person**sparas resultatet. Den här koden skriver ut detaljerade matchningsresultat till konsolen.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>Verifiera ansikten

Åtgärden Verifiera tar ett ansikts-ID och antingen ett annat ansikts-ID eller ett **personobjekt** och avgör om de tillhör samma person.

Följande kod identifierar ansikten i två källbilder och verifierar dem sedan mot ett ansikte som upptäckts från en målbild.

### <a name="get-test-images"></a>Hämta testbilder

Följande kodblock deklarerar variabler som pekar på käll- och målavbildningarna för verifieringen.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>Identifiera ansikten för verifiering

Följande kod identifierar ansikten i käll- och målbilderna och sparar dem i variabler.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>Få verifieringsresultat

Följande kod jämför var och en av källbilderna med målbilden och skriver ut ett meddelande som anger om de tillhör samma person.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="take-a-snapshot-for-data-migration"></a>Ta en ögonblicksbild för datamigrering

Med funktionen Ögonblicksbilder kan du flytta sparade ansiktsdata, till exempel en tränad **PersonGroup,** till en annan Azure Cognitive Services Face-prenumeration. Du kanske vill använda den här funktionen om du till exempel har skapat ett **PersonGroup-objekt** med en kostnadsfri utvärderingsprenumeration och nu vill migrera det till en betald prenumeration. Se [Migrera dina ansiktsdata](../Face-API-How-to-Topics/how-to-migrate-face-data.md) för en bred översikt över funktionen Ögonblicksbilder.

I det här exemplet migrerar du den **persongrupp som** du skapade i [Skapa och tränar en persongrupp](#create-and-train-a-person-group). Du kan antingen fylla i det avsnittet först eller använda dina egna ansiktsdatakonstruktioner.

### <a name="set-up-target-subscription"></a>Ställ in målprenumeration

Först måste du ha en andra Azure-prenumeration med en Face-resurs. Du kan göra detta genom att följa stegen i avsnittet [Konfigurera.](#setting-up) 

Skapa sedan följande variabler högst upp i skriptet. Du måste också skapa nya miljövariabler för prenumerations-ID för ditt Azure-konto, samt nyckel-, slutpunkt och prenumerations-ID för ditt nya (mål)konto. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshotvars)]

### <a name="authenticate-target-client"></a>Autentisera målklient

Senare i skriptet sparar du det aktuella klientobjektet som källklient och autentiserar sedan ett nytt klientobjekt för målprenumerationen. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_auth)]

### <a name="use-a-snapshot"></a>Använda en ögonblicksbild

Resten av ögonblicksbildåtgärderna sker inom en asynkron funktion. 

1. Det första steget är att **ta** ögonblicksbilden, som sparar din ursprungliga prenumerations ansiktsdata till en tillfällig molnplats. Den här metoden returnerar ett ID som du använder för att fråga åtgärdens status.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_take)]

1. Fråga sedan ID:t tills åtgärden har slutförts.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait)]

    Denna kod använder `wait_for_operation` sig av funktionen, som du bör definiera separat:

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_waitforop)]

1. Gå tillbaka till din asynkrona funktion. Använd **funktionen apply** för att skriva dina ansiktsdata till din målprenumeration. Den här metoden returnerar också ett ID.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_apply)]

1. Återigen, använd `wait_for_operation` funktionen för att fråga ID tills åtgärden har slutförts.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait2)]

När du har slutfört de här stegen kan du komma åt dina ansiktsdatakonstruktioner från din nya (mål)prenumeration.

## <a name="run-the-application"></a>Köra appen

Kör programmet med `python` kommandot på snabbstartsfilen.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade bort.

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

Om du har skapat en **PersonGroup** i den här snabbstarten och vill ta bort den kör du följande kod i skriptet:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

Om du migrerade data med hjälp av funktionen Ögonblicksbild i den här snabbstarten måste du också ta bort den **persongrupp som** sparats i målprenumerationen.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletetargetgroup)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du använder Face-biblioteket för Python för att göra basuppgifter. Därefter kan du utforska referensdokumentationen om du vill veta mer om biblioteket.

> [!div class="nextstepaction"]
> [Referens för ansikts-API (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [Vad är tjänsten Ansiktsigenkänning?](../overview.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py).
