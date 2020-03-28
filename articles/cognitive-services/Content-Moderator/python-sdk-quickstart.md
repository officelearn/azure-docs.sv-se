---
title: 'Snabbstart: Klientbibliotek för Innehållsmoderator för Python'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten kan du lära dig hur du kommer igång med klientbiblioteket Azure Cognitive Services Content Moderator för Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 31cdc9663283b580acc10e7ac8d5a77f9036a7a8
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772362"
---
# <a name="quickstart-content-moderator-client-library-for-python"></a>Snabbstart: Klientbibliotek för Innehållsmoderator för Python

Kom igång med klientbiblioteket Innehållsmoderator för Python. Följ dessa steg för att installera paketet och prova exempelkoden för grundläggande uppgifter. Content Moderator är en kognitiv tjänst som kontrollerar text-, bild- och videoinnehåll för material som kan vara stötande, riskabelt eller på annat sätt oönskat. När sådant material hittas tillämpar tjänsten lämplig etiketter (flaggor) på innehållet. Appen kan sedan hantera flaggat innehåll för att uppfylla krav eller upprätthålla den avsedda miljön för användare.

Använd klientbiblioteket Innehållsmoderator för Python för att:

* [Moderera text](#moderate-text)
* [Använda en lista med anpassade termer](#use-a-custom-terms-list)
* [Måttliga bilder](#moderate-images)
* [Använda en anpassad bildlista](#use-a-custom-image-list)
* [Skapa en recension](#create-a-review)

[Referensdokumentation](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/contentmoderator?view=azure-python) | [Ursprungskod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator) | [Paket (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/) | [Prover](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Inrätta

### <a name="create-a-content-moderator-azure-resource"></a>Skapa en Azure-resurs för innehållsmoderator

Azure Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Content Moderator med Hjälp av [Azure-portalen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på din lokala dator. Du kan också:

* Få en [testnyckel](https://azure.microsoft.com/try/cognitive-services/#decision) giltig i sju dagar gratis. När du har registrerat dig är den tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visa din resurs på [Azure-portalen](https://portal.azure.com/)

När du har fått en nyckel från utvärderingsprenumerationen eller resursen `CONTENT_MODERATOR_ENDPOINT`skapar du [miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel- och slutpunkts-URL:en, med namn `CONTENT_MODERATOR_SUBSCRIPTION_KEY` respektive , respektive.
 
### <a name="create-a-python-script"></a>Skapa ett pythonskript

Skapa ett nytt Python-skript och öppna det i önskad redigerare eller IDE. Lägg sedan `import` till följande satser högst upp i filen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

Skapa sedan variabler för resursens slutpunktsplats och nyckel som miljövariabler. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Om du har skapat miljövariablerna efter att du har startat programmet måste du stänga och öppna redigeraren, IDE:t eller skalet som kör det för att komma åt variablerna.

### <a name="install-the-client-library"></a>Installera klientbiblioteket

Du kan installera klientbiblioteket innehållsmoderator med följande kommando:

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

## <a name="object-model"></a>Objektmodell

Följande klasser hanterar några av de viktigaste funktionerna i Content Moderator Python SDK.

|Namn|Beskrivning|
|---|---|
|[InnehållModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)|Den här klassen behövs för alla funktioner för innehållsmodererator. Du instansierar den med din prenumerationsinformation och använder den för att producera instanser av andra klasser.|
|[ImageModerationOperationer](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python)|Den här klassen tillhandahåller funktioner för att analysera bilder för barnförbjudet innehåll, personlig information eller mänskliga ansikten.|
|[TextModerationOperationer](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)|Den här klassen tillhandahåller funktioner för att analysera text för språk, svordomar, fel och personlig information.|
[RecensionerOperationer](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python)|Den här klassen innehåller funktionerna i gransknings-API:erna, inklusive metoder för att skapa jobb, anpassade arbetsflöden och mänskliga granskningar.|

## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du utför följande uppgifter med klientbiblioteket Innehållsmoderator för Python:

* [Autentisera klienten](#authenticate-the-client)
* [Moderera text](#moderate-text)
* [Använda en lista med anpassade termer](#use-a-custom-terms-list)
* [Måttliga bilder](#moderate-images)
* [Använda en anpassad bildlista](#use-a-custom-image-list)
* [Skapa en recension](#create-a-review)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE]
> Den här snabbstarten förutsätter att du har [skapat miljövariabler](../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) för nyckeln och slutpunkten för Innehållsmoderator.

Instansiera en klient med din slutpunkt och nyckel. Skapa ett [CognitiveServicesCredentials-objekt](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) med nyckeln och använd det med slutpunkten för att skapa ett [ContentModeratorClient-objekt.](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>Moderera text

Följande kod använder en Content Moderator-klient för att analysera en texttext och skriva ut resultaten till konsolen. Skapa först en **text_files/mapp** i projektets rot och lägg till en *content_moderator_text_moderation.txt-fil.* Lägg till din egen text i den här filen eller använd följande exempeltext:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 2065550111
```

Lägg till en referens till den nya mappen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

Lägg sedan till följande kod i Python-skriptet.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>Använda en lista med anpassade termer

Följande kod visar hur du hanterar en lista med anpassade termer för textmoderering. Du kan använda klassen [ListManagementTermListsOperationsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations?view=azure-python) för att skapa en termerlista, hantera de enskilda termerna och visa andra texttexter mot den.

### <a name="get-sample-text"></a>Hämta exempeltext

Om du vill använda det här exemplet måste du skapa en **text_files/mapp** i projektets rot och lägga till en *content_moderator_term_list.txt-fil.* Den här filen ska innehålla organisk text som kommer att kontrolleras mot listan med termer. Du kan använda följande exempeltext:

```
This text contains the terms "term1" and "term2".
```

Lägg till en referens till mappen om du inte redan har definierat en.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>Skapa en lista

Lägg till följande kod i Python-skriptet för att skapa en anpassad termerlista och spara dess ID-värde.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>Definiera listinformation

Du kan använda en listas ID för att redigera dess namn och beskrivning.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>Lägga till en term i listan

Följande kod lägger `"term1"` `"term2"` till villkoren och i listan.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>Få alla termer i listan

Du kan använda list-ID för att returnera alla termer i listan.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>Uppdatera listindexet

När du lägger till eller tar bort termer från listan måste du uppdatera indexet innan du kan använda den uppdaterade listan.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>Skärmtext mot listan

Huvudfunktionerna i listan med anpassade termer är att jämföra en texttext mot listan och ta reda på om det finns några matchande termer. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>Ta bort en term från en lista

Följande kod tar bort `"term1"` termen från listan.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>Ta bort alla termer från en lista

Använd följande kod för att rensa en lista över alla dess termer.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-list"></a>Ta bort lista

Använd följande kod för att ta bort en lista med anpassade termer.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>Måttliga bilder

Följande kod använder en Content Moderator-klient, tillsammans med ett [ImageModerationOperations-objekt,](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python) för att analysera bilder för barnförbjudet och racy-innehåll.

### <a name="get-images"></a>Hämta bilder

Definiera en referens till vissa bilder som ska analyseras.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

Lägg sedan till följande kod för att iterera genom dina bilder. Resten av koden i det här avsnittet kommer att gå in i den här loopen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Sök efter innehåll för vuxna/racy

Följande kod kontrollerar bilden på den angivna webbadressen för barn eller racy innehåll och skriver ut resultat till konsolen. Mer information om vad dessa termer betyder finns i begreppsguiden för [bildmoderering.](./image-moderation-api.md)

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>Sök efter synlig text

Följande kod kontrollerar bilden efter synligt textinnehåll och skriver ut resultat till konsolen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Sök efter ansikten

Följande kod kontrollerar bilden för mänskliga ansikten och skriver ut resultat till konsolen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>Använda en anpassad bildlista

Följande kod visar hur du hanterar en anpassad lista med bilder för bildmoderering. Den här funktionen är användbar om din plattform ofta tar emot instanser av samma uppsättning bilder som du vill sålla bort. Genom att underhålla en lista över dessa specifika bilder kan du förbättra prestanda. Med klassen [ListManagementImageListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations?view=azure-python) kan du skapa en bildlista, hantera de enskilda bilderna i listan och jämföra andra bilder mot den.

Skapa följande textvariabler för att lagra de bildadresser som du ska använda i det här scenariot.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> Detta är inte rätt lista i sig, men en informell `add images` lista över bilder som kommer att läggas till i avsnittet av koden.


### <a name="create-an-image-list"></a>Skapa en bildlista

Lägg till följande kod för att skapa en bildlista och spara en referens till dess ID.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>Lägga till bilder i en lista

Följande kod lägger till alla dina bilder i listan.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

Definiera **add_images** hjälpfunktionen någon annanstans i skriptet.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>Få bilder i listan

Följande kod skriver ut namnen på alla bilder i listan.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>Information om uppdateringslista

Du kan använda list-ID:t för att uppdatera listans namn och beskrivning.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>Få information om listan

Använd följande kod för att skriva ut de aktuella uppgifterna i listan.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>Uppdatera listindexet

När du har lagt till eller tagit bort bilder måste du uppdatera listindexet innan du kan använda det för att visa andra bilder.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>Matcha bilder mot listan

Den huvudsakliga funktionen för bildlistor är att jämföra nya bilder och se om det finns några matchningar.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>Ta bort en bild från listan

Följande kod tar bort ett objekt från listan. I det här fallet är det en bild som inte matchar listkategorin.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>Ta bort alla bilder från en lista

Använd följande kod för att rensa ut en bildlista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>Ta bort bildlistan

Använd följande kod för att ta bort en viss bildlista.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>Skapa en recension

Du kan använda Innehållsmoderatorn Python SDK för att mata in innehåll i [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com) så att mänskliga moderatorer kan granska det. Mer information om granskningsverktyget finns i [konceptguiden för granskningsverktyg](./review-tool-user-guide/human-in-the-loop.md).

Följande kod använder klassen [ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python) för att skapa en granskning, hämta dess ID och kontrollera dess information efter att ha tagit emot mänsklig indata via granskningsverktygets webbportal.

### <a name="get-review-credentials"></a>Hämta granskningsautentiseringsuppgifter

Logga först in på granskningsverktyget och hämta gruppnamnet. Tilldela den sedan till lämplig variabel i koden. Du kan också ställa in en motringningsslutpunkt för att få uppdateringar om granskningens aktivitet.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>Skapa en bildgranskning

Lägg till följande kod för att skapa och publicera en recension för den angivna bild-URL:en. Koden sparar en referens till gransknings-ID. 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>Få information om recension

Använd följande kod för att kontrollera information om en viss granskning. När du har skapat granskningen kan du själv gå till granskningsverktyget och interagera med innehållet. Mer information om hur du gör detta finns i [vägledningen för recensioner](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images). När du är klar kan du köra den här koden igen och hämta resultatet av granskningsprocessen.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

Om du har använt en motringningsslutpunkt i det här scenariot bör den få en händelse i det här formatet:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Köra appen

Kör programmet med `python` kommandot på snabbstartsfilen.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade bort.

* [Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du använder Python-biblioteket Innehållsmoderator för att utföra modereringsuppgifter. Läs sedan mer om måttfullhet av bilder eller andra medier genom att läsa en konceptuell guide.

> [!div class="nextstepaction"]
>[Begrepp för bildmoderering](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Vad är Azure Content Moderator?](./overview.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py).