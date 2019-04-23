---
title: Viktig information – Custom Vision Service
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: 2b0d8b8a86c3105b1bda7fb0d72cbcb72ed82995
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785733"
---
# <a name="custom-vision-service-release-notes"></a>Custom Vision Service viktig information

## <a name="march-26-2019"></a>26 mars 2019

- Custom Vision Service har in allmänt tillgängliga på Azure!
- Har lagts till avancerade utbildning-funktion med en ny machine learning-serverdelen för bättre prestanda, särskilt på utmanande datauppsättningar och detaljerad klassificering. Du kan ange en beräkning tid budget för utbildning och Custom Vision experimentellt identifierar den bästa inställningen för tokenomvandling och utbildning med avancerade utbildning. Du kan fortsätta att använda befintliga snabb utbildning för snabba iterationer.
- Introducerade 3.0 API: er. Meddelade kommande utfasningen av äldre än 3.0 API: er på den 1 oktober 2019. Se dokumentationen snabbstarter för [.Net](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/csharp-tutorial), [Python](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/python-tutorial), [nod](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/node-tutorial), [Java](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/java-tutorial), eller [Gå](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/go-tutorial) exempel information om hur du kommer igång.
- Ersatt ”standard iterationer” med publicera/avpublicera i 3.0 API: er.
- Nya modellen export mål har lagts till. Dockerfile export har uppgraderats för att stödja ARM för Raspberry Pi 3. Stöd för export har lagts till i [Vision AI Dev Kit.](https://visionaidevkit.com/).
- Ökade gränsen på taggar per projekt till 500 för S0-nivån. Ökade gränsen på bilder per projekt till 100 000 för S0-nivån.
- Ta bort vuxet domän. Allmän domän rekommenderar i stället.
- Meddelade [priser](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) för allmän tillgänglighet.  

## <a name="february-25-2019"></a>Den 25 februari 2019

- Meddelade slutet av begränsad utvärderingsversion projekt (projekt som inte är associerade med en Azure-resurs), när Custom Vision närmar sig slutförandet av flytten till Azure offentlig förhandsversion. Från och den 25 mars 2019 stöder CustomVision.ai plats bara visning av projekt som är associerade med en Azure-resurs, till exempel den kostnadsfria Custom Vision-resursen. Via den 1 oktober 2019 kommer du fortfarande att kunna komma åt dina befintliga begränsad utvärderingsversion projekt Custom Vision-API: er. Detta ger du har tid att uppdatera API-nycklar för alla appar som du har skrivit med Custom Vision. Efter den 1 oktober 2019 tas begränsad utvärderingsversion projekt som du inte har flyttats till Azure bort.

## <a name="january-22-2019"></a>Den 22 januari 2019

- Stöd för nya Azure-regioner: Västra USA 2, östra USA, östra USA 2, Västeuropa, Nordeuropa, Sydostasien, östra, centrala Indien, Storbritannien, södra, Japan, östra och norra centrala USA. Support fortsätter för södra centrala USA.

## <a name="december-12-2018"></a>12 december 2018

- Stöder export för objektidentifiering modeller (introducerats objektet identifiering Compact domän).
- Fasta ett antal problem med förbättrad skärmläsare och tangentbordet navigering support.
- UX-uppdateringar för bildvisningsprogram och förbättrad objektidentifiering taggning upplevelse för snabbare taggar.  
- Uppdaterade basmodellen för objektet identifiering domän för bättre kvalitet objektidentifiering.
- Felkorrigeringar.

## <a name="november-6-2018"></a>6 november 2018

- Stöd har lagts till för logotyp domän i objektidentifiering.

## <a name="october-9-2018"></a>9 oktober 2018

- Objektidentifiering försätts betalad förhandsversion. Du kan nu skapa objektidentifiering projekt med en Azure-resurs.
- Har lagts till ”Flytta till Azure”-funktion till webbplatsen för att göra det enklare att uppgradera en begränsad utvärderingsversion-projektet för att länka till en Azure. resursen länkade projektet (F0 eller S0.) Du hittar den på inställningssidan för din produkt.  
- Har lagts till export till ONNX 1.2, att stödja Windows 2018 oktober uppdaterad version av Windows-ML.
Felkorrigeringar, inklusive för ONNX exportera med specialtecken.

## <a name="august-14-2018"></a>Den 14 augusti 2018

- Har lagts till ”Kom igång” widget till customvision.ai plats som leder användarna genom utbildning.
- Ytterligare förbättringar för maskininlärningspipeline som gynnar multilabel projekt (nya förlust layer).

## <a name="june-28-2018"></a>28 juni 2018

- Felkorrigeringar och förbättringar för serverdelen.
- Aktivera multiklass-baserad klassificering för projekt där bilder har exakt en etikett. I förutsägelser för multiklass-baserad läge summera sannolikhet till en (alla avbildningar klassificeras bland angivna taggarna).

## <a name="june-13-2018"></a>13 juni 2018

- UX-uppdatering, fokuserar på enkel användning och hjälpmedel.
- Förbättringar för machine learning-pipeline som gynnar multilabel projekt med ett stort antal taggar.
- En bugg har åtgärdats i TensorFlow export. Aktiverad exporterade modellen versionshantering, så iterationer kan exporteras mer än en gång.

## <a name="may-7-2018"></a>7 maj, 2018

- Introducerade förhandsfunktion för objektidentifiering för begränsad utvärderingsversion-projekt.
- Uppgraderade till 2.0 API:er
- Utökade av S0-nivån till upp till 250 taggar och 50 000 bilder.
- Betydande serverförbättringar av maskininlärningspipeline för bildklassificeringsprojekt. Projekt som tränas efter den 27 april 2018 kan dra nytta av dessa uppdateringar.
- Lade till modellexport för ONNX, för användning med Windows ML.
- Lade till modellexport till DockerFile. På så sätt kan du ladda ned artefakter för att skapa dina egna Windows- och Linux-containrar, till exempel DockerFile, TensorFlow-modell och tjänstkod.
- För nyligen utbildade modeller som exporteras till TensorFlow i Allmänt (CD) och landmärken (CD)-domäner, [innebär att värden är nu (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample), konsekvent på alla projekt.

## <a name="march-1-2018"></a>1 mars 2018

- Angivna betalad förhandsversion och publicerats till Azure-portalen. Projekt kan nu bifogas till Azure-resurser med nivå F0 (kostnadsfritt) eller S0 (standard). Introducerade S0-nivån projekt, som tillåter upp till 100 taggar och 25 000 bilder.
- Serverändringar av maskininlärningspipeline/parametern normalization. Detta ger kunderna bättre kontroll över kompromisser mellan precision och återanrop vid justeringar av sannolikhetströskelvärdet. Som en del av dessa ändringar angavs 50 % för sannolikhetströskelvärdet i CustomVision.ai-portalen.

## <a name="december-19-2017"></a>19 december 2017

- Export till Android (TensorFlow) har lagts till, utöver tidigare export till iOS (CoreML.) På så sätt kan export av en tränad kompakt modell köras offline i ett program.
- Lade till kompakta Retail- och Landmark-domäner för att möjliggöra modellexport för dessa domäner.
- Släppte version [1.2 av Training-API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) och [1.1 av Prediction-API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Uppdaterade API:ernas stöd för modellexport, ny förutsägelseåtgärd som inte sparar bilder i Förutsägelser och introducerade batchåtgärder för Training-API:et.
- Justeringar av användargränssnittet, bland annat möjligheten att se vilken domän som har använts för att träna en iteration.
- Uppdaterade [C# SDK och exempel](https://github.com/Microsoft/Cognitive-CustomVision-Windows).