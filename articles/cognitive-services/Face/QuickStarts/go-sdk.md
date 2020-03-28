---
title: 'Snabbstart: Face klientbibliotek för Go | Microsoft-dokument'
description: Kom igång med Face-klientbiblioteket för Go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 2db40150167a8f16242b2feb15b77820fa1970a9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76941432"
---
# <a name="quickstart-face-client-library-for-go"></a>Snabbstart: Face klientbibliotek för Go

Kom igång med Face-klientbiblioteket för Go. Följ dessa steg för att installera biblioteket och prova våra exempel för grundläggande uppgifter. Face-tjänsten ger dig tillgång till avancerade algoritmer för att upptäcka och känna igen mänskliga ansikten i bilder.

Använd klientbiblioteket för Face service för Gå till:

* [Identifiera ansikten i en bild](#detect-faces-in-an-image)
* [Hitta liknande ansikten](#find-similar-faces)
* [Skapa och träna en persongrupp](#create-and-train-a-person-group)
* [Identifiera ett ansikte](#identify-a-face)
* [Ta en ögonblicksbild för datamigrering](#take-a-snapshot-for-data-migration)

[Referensdokumentation](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face) | [Biblioteks källkod](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face) | [SDK-hämta](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration – [skapa en gratis](https://azure.microsoft.com/free/)
* Den senaste versionen av [Go](https://golang.org/dl/)

## <a name="set-up"></a>Konfigurera

### <a name="create-a-face-azure-resource"></a>Skapa en Face Azure-resurs 

Börja använda Face-tjänsten genom att skapa en Azure-resurs. Välj den resurstyp som är rätt för dig:

* En [utvärderingsresurs](https://azure.microsoft.com/try/cognitive-services/#decision) (ingen Azure-prenumeration behövs): 
    * Gäller i sju dagar, gratis. När du har registrerat dig kommer en utvärderingsnyckel och slutpunkt att vara tillgängliga på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/). 
    * Det här är ett bra alternativ om du vill prova Face-tjänsten, men inte har en Azure-prenumeration.
* En [ansiktstjänstresurs:](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)
    * Tillgänglig via Azure-portalen tills du tar bort resursen.
    * Använd den kostnadsfria prisnivån för att prova tjänsten och uppgradera senare till en betald nivå för produktion.
* En [multiserviceresurs:](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)
    * Tillgänglig via Azure-portalen tills du tar bort resursen.  
    * Använd samma nyckel och slutpunkt för dina program, över flera Cognitive Services.

### <a name="create-an-environment-variable"></a>Skapa en miljövariabel

>[!NOTE]
> Slutpunkterna för icke-utvärderingsresurser som skapats efter den 1 juli 2019 använder det anpassade underdomänformatet som visas nedan. Mer information och en fullständig lista över regionala slutpunkter finns i [Anpassade underdomännamn för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Med hjälp av nyckeln och slutpunkten från resursen du skapade skapar du två miljövariabler för autentisering:
* `FACE_SUBSCRIPTION_KEY`- Resursnyckeln för att autentisera dina begäranden.
* `FACE_ENDPOINT`- Resursslutpunkten för att skicka API-begäranden. Det kommer att se ut så här: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Följ instruktionerna för operativsystemet.
<!-- replace the below endpoint and key examples -->
#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx FACE_SUBSCRIPTION_KEY <replace-with-your-product-name-key>
setx FACE_ENDPOINT <replace-with-your-product-name-endpoint>
```

När du har lagt till miljövariabeln startar du om konsolfönstret.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

När du har lagt till miljövariabeln så kör `source ~/.bashrc` från konsolfönstret så att ändringarna träder i kraft.

#### <a name="macos"></a>[Macos](#tab/unix)

Redigera `.bash_profile`din och lägg till miljövariabeln:

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

När du har lagt till miljövariabeln så kör `source .bash_profile` från konsolfönstret så att ändringarna träder i kraft.
***

### <a name="create-a-go-project-directory"></a>Skapa en Go-projektkatalog

Skapa en ny arbetsyta för Go-projektet med namnet `my-app`Go-projektet i ett konsolfönster (cmd, PowerShell, Terminal, Bash) och navigera till den.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Arbetsytan innehåller tre mappar:

* **src** - Den här katalogen innehåller källkod och paket. Alla paket som `go get` installeras med kommandot kommer att finnas i den här mappen.
* **pkg** - Den här katalogen innehåller de kompilerade Go-paketobjekten. Dessa filer har `.a` alla ett tillägg.
* **bin** - Den här katalogen innehåller de binära `go install`körbara filer som skapas när du kör .

> [!TIP]
> Mer information om strukturen på en Go-arbetsyta finns i [dokumentdokumentationen Gå.](https://golang.org/doc/code.html#Workspaces) Den här guiden `$GOPATH` innehåller `$GOROOT`information för inställning och .

### <a name="install-the-client-library-for-go"></a>Installera klientbiblioteket för Go

Installera sedan klientbiblioteket för Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

eller i din lagringsplatskörning om du använder dep:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Skapa ett Go-program

Skapa sedan en fil i **src-katalogen** med namnet `sample-app.go`:

```bash
cd src
touch sample-app.go
```

Öppna `sample-app.go` i önskad IDE- eller textredigerare. Lägg sedan till paketnamnet och importera följande bibliotek:

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

Därefter börjar du lägga till kod för att utföra olika Face-serviceåtgärder.

## <a name="object-model"></a>Objektmodell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Face-tjänsten Go SDK.

|Namn|Beskrivning|
|---|---|
|[BaseClient (avbaskl)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | Den här klassen representerar din behörighet att använda Face-tjänsten och du behöver den för alla Face-funktioner. Du instansierar den med din prenumerationsinformation och använder den för att producera instanser av andra klasser. |
|[Klient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|Den här klassen hanterar de grundläggande identifierings- och igenkänningsuppgifter som du kan göra med mänskliga ansikten. |
|[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|Den här klassen representerar alla data som har identifierats från ett enda ansikte i en bild. Du kan använda den för att hämta detaljerad information om ansiktet.|
|[ListaClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|Den här klassen hanterar molnlagrade **FaceList-konstruktioner** som lagrar en olika uppsättning ansikten. |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| Den här klassen hanterar **molnlagringspersonkonstruktioner,** som lagrar en uppsättning ansikten som tillhör en enda person.|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| Den här klassen hanterar de molnbevarade **PersonGroup-konstruktionerna,** som lagrar en uppsättning olika **personobjekt.** |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|Den här klassen hanterar funktionen Ögonblicksbild. Du kan använda den för att tillfälligt spara alla dina molnbaserade Face-data och migrera dessa data till en ny Azure-prenumeration. |

## <a name="code-examples"></a>Kodexempel

De här kodexemplen visar hur du slutför grundläggande uppgifter med hjälp av Face service-klientbiblioteket för Go:

* [Autentisera klienten](#authenticate-the-client)
* [Identifiera ansikten i en bild](#detect-faces-in-an-image)
* [Hitta liknande ansikten](#find-similar-faces)
* [Skapa och träna en persongrupp](#create-and-train-a-person-group)
* [Identifiera ett ansikte](#identify-a-face)
* [Ta en ögonblicksbild för datamigrering](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE] 
> Den här snabbstarten förutsätter att du har [skapat miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för ansiktsnyckeln och slutpunkten, namngivna `FACE_SUBSCRIPTION_KEY` respektive. `FACE_ENDPOINT`

Skapa en **huvudfunktion** och lägg till följande kod i den för att instansiera en klient med din slutpunkt och nyckel. Du skapar ett **[CognitiveServicesAuthorizer-objekt](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** med nyckeln och använder det med slutpunkten för att skapa ett **[klientobjekt.](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** Den här koden instansierar också ett kontextobjekt, som behövs för att skapa klientobjekt. Den definierar också en fjärrplats där några av exempelavbildningarna i den här snabbstarten hittas.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>Identifiera ansikten i en bild

Lägg till följande kod i **huvudmetoden.** Den här koden definierar en fjärrprovbild och anger vilka ansiktsfunktioner som ska extraheras från bilden. Den anger också vilken AI-modell som ska användas för att extrahera data från de upptäckta ansiktssikterna. Se [Ange en igenkänningsmodell](../Face-API-How-to-Topics/specify-recognition-model.md) för information om dessa alternativ. Slutligen gör **[DetectWithURL-metoden](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** ansiktsigenkänningsåtgärden på avbildningen och sparar resultaten i programminnet.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Visa upptäckta ansiktsdata

Nästa kodblock tar det första elementet i matrisen **[med DetectedFace-objekt](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** och skriver ut dess attribut till konsolen. Om du använde en bild med flera ansikten bör du iterera genom matrisen i stället.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>Hitta liknande ansikten

Följande kod tar en enda identifierad ansikte (källa) och söker i en uppsättning andra ansikten (mål) för att hitta matchningar. När en matchning hittas skrivs ID:t för det matchade ansiktet ut på konsolen.

### <a name="detect-faces-for-comparison"></a>Identifiera ansikten för jämförelse

Spara först en referens till ansiktet som du upptäckte i [avsnittet Identifiera ansikten i en bild.](#detect-faces-in-an-image) Detta ansikte kommer att vara källan.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

Ange sedan följande kod för att identifiera en uppsättning ansikten i en annan bild. Dessa ansikten kommer att vara målet.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>Hitta matchningar

Följande kod använder **[metoden FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** för att hitta alla målytor som matchar källytan.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>Skriv ut matchningar

Följande kod skriver ut matchningsinformationen till konsolen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>Skapa och träna en persongrupp

Om du vill gå igenom det här scenariot måste du https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/imagesspara följande bilder i projektets rotkatalog: .

Den här gruppen bilder innehåller tre uppsättningar av enface-bilder som motsvarar tre olika personer. Koden definierar tre **PersonGroup Person-objekt** och associerar `woman` `man`dem `child`med bildfiler som börjar med , och .

### <a name="create-persongroup"></a>Skapa persongrupp

När du har laddat ned bilderna lägger du **main** till följande kod längst ned på huvudmetoden. Den här koden autentiserar ett **[PersonGroupClient-objekt](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** och använder det sedan för att definiera en ny **PersonGroup**.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>Skapa personer i persongrupper

Nästa kodblock autentiserar en **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** och använder det för att definiera tre nya **PersonGroup** Person-objekt. Dessa objekt representerar var och en en enda person i uppsättningen bilder.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>Tilldela ansikten till personer

Följande kod sorterar bilderna efter deras prefix, identifierar ansikten och tilldelar ansiktena till respektive **PersonGroup** Person-objekt, baserat på bildfilnamnet.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

### <a name="train-persongroup"></a>Tåg PersonGroup

När du har tilldelat ansikten tränar du **Persongroup** så att den kan identifiera de visuella funktioner som är associerade med var och en av dess **personobjekt.** Följande kod anropar den asynkrona **tågmetoden** och avsöker resultatet och skriver ut statusen till konsolen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

## <a name="identify-a-face"></a>Identifiera ett ansikte

Följande kod tar en bild med flera ansikten och letar efter identiteten på varje person i bilden. Den jämför varje upptäckt ansikte med en **PersonGroup**, en databas med olika **personobjekt** vars ansiktsdrag är kända.

> [!IMPORTANT]
> För att kunna köra det här exemplet måste du först köra koden i [Skapa och träna en persongrupp](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Få en testbild

Följande kod söker i roten till projektet efter en bild _test-image-person-group.jpg_ och läser in den i programmets minne. Du kan hitta den här bilden i samma repo som de https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/imagesbilder som används i Skapa och träna en [persongrupp:](#create-and-train-a-person-group).

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>Identifiera källytor i testbilden

Nästa kodblock gör vanlig ansiktsidentifiering på testbilden för att hämta alla ansikten och spara dem i en matris.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>Identifiera ansikten

Metoden **[Identifiera](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** tar matrisen med identifierade ansikten och jämför dem med den angivna **PersonGroup** (definieras och tränas i det tidigare avsnittet). Om den kan matcha ett upptäckt ansikte med en **person** i gruppen sparas resultatet.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

Den här koden skriver sedan ut detaljerade matchningsresultat till konsolen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>Verifiera ansikten

Åtgärden Verifiera tar ett ansikts-ID och antingen ett annat ansikts-ID eller ett **personobjekt** och avgör om de tillhör samma person.

Följande kod identifierar ansikten i två källbilder och verifierar sedan var och en av dem mot ett ansikte som upptäckts från en målbild.

### <a name="get-test-images"></a>Hämta testbilder

Följande kodblock deklarerar variabler som pekar på mål- och källavbildningarna för verifieringen.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>Identifiera ansikten för verifiering

Följande kod identifierar ansikten i käll- och målbilderna och sparar dem i variabler.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>Få verifieringsresultat

Följande kod jämför var och en av källbilderna med målbilden och skriver ut ett meddelande som anger om de tillhör samma person.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]


## <a name="take-a-snapshot-for-data-migration"></a>Ta en ögonblicksbild för datamigrering

Med funktionen Ögonblicksbilder kan du flytta sparade ansiktsdata, till exempel en tränad **PersonGroup,** till en annan Azure Cognitive Services Face-prenumeration. Du kan använda den här funktionen om du till exempel har skapat ett **PersonGroup-objekt** med en kostnadsfri utvärderingsprenumeration och nu vill migrera det till en betald prenumeration. Se [Migrera dina ansiktsdata](../Face-API-How-to-Topics/how-to-migrate-face-data.md) för en bred översikt över funktionen Ögonblicksbilder.

I det här exemplet ska du migrera den **persongrupp som** du skapade i [Skapa och träna en persongrupp](#create-and-train-a-person-group). Du kan antingen fylla i det avsnittet först eller använda dina egna ansiktsdatakonstruktioner.

### <a name="set-up-target-subscription"></a>Ställ in målprenumeration

Först måste du ha en andra Azure-prenumeration med en Face-resurs. Du kan göra detta genom att upprepa stegen i avsnittet [Konfigurera.](#set-up) 

Skapa sedan följande variabler nära toppen **main** av huvudmetoden. Du måste också skapa nya miljövariabler för prenumerations-ID för ditt Azure-konto, samt nyckel-, slutpunkt och prenumerations-ID för ditt nya (mål)konto.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_target_client)]

Placera sedan ditt prenumerations-ID-värde i en matris för nästa steg.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_id)]

### <a name="authenticate-target-client"></a>Autentisera målklient

Senare i skriptet sparar du det ursprungliga klientobjektet som källklient och autentiserar sedan ett nytt klientobjekt för målprenumerationen. 

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_auth)]

### <a name="take-a-snapshot"></a>Ta en ögonblicksbild

Nästa steg är att ta ögonblicksbilden med **[Take](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Take)**, som sparar den ursprungliga prenumerationens ansiktsdata till en tillfällig molnplats. Den här metoden returnerar ett ID som du använder för att fråga åtgärdens status.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_take)]

Fråga sedan ID:t tills åtgärden har slutförts.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_query)]

### <a name="apply-the-snapshot"></a>Använda ögonblicksbilden

Använd åtgärden **[Använd](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Apply)** för att skriva dina nyligen uppladdade ansiktsdata till din målprenumeration. Den här metoden returnerar också ett ID.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply)]

Fråga id:t tills åtgärden har slutförts.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply_query)]

När du har slutfört de här stegen kan du komma åt dina ansiktsdatakonstruktioner från din nya (mål)-prenumeration.

## <a name="run-the-application"></a>Köra appen

Kör go-programmet `go run [arguments]` med kommandot från programkatalogen.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade bort.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Om du har skapat en **persongrupp** i den här snabbstarten och vill ta bort den anropar du metoden **[Ta bort.](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** Om du migrerade data med hjälp av funktionen Ögonblicksbild i den här snabbstarten måste du också ta bort den **persongrupp som** sparats i målprenumerationen.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du använder Face-biblioteket för Go för att göra basuppgifter. Därefter kan du utforska referensdokumentationen om du vill veta mer om biblioteket.

> [!div class="nextstepaction"]
> [Referens för ansikts-API (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [Vad är tjänsten Ansiktsigenkänning?](../overview.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go).
