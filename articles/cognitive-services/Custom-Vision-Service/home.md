---
title: Översikt över Custom Vision Service-maskininlärning – Azure Cognitive Services | Microsoft Docs
description: Custom Vision Service är en Microsoft Cognitive-tjänst där du kan skapa anpassade bildklassificerare på Azure-plattformen.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: de45fc399470a806fb7456cbbe936cecf659ee7c
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37087803"
---
# <a name="what-is-the-custom-vision-service"></a>Vad är Custom Vision Service?

Custom Vision Service är en Microsoft Cognitive-tjänst där du kan skapa anpassade bildklassificerare. Det gör det enkelt och snabbt att bygga, distribuera och förbättra en bildklassificerare. Custom Vision Service tillhandahåller ett REST-API och ett webbgränssnitt för att ladda upp dina bilder och träna klassificeraren.

## <a name="what-does-custom-vision-service-do-well"></a>Hur fungerar Custom Vision Service som bäst?

Custom Vision Service fungerar bäst om ett objekt som du försöker klassificera är framträdande i bilden. 

Få bilder krävs för att skapa en klassificerare eller detektor. 50 bilder per klass räcker för att påbörja din prototyp. Metoderna som Custom Vision Service använder är robusta för skillnader, vilket betyder att du kan börja skapa prototyper med så lite data. Det innebär att Custom Vision Service inte passar så bra för scenarier där du vill identifiera små skillnader. Till exempel små fel i kvalitetskontroller.

## <a name="release-notes"></a>Viktig information

### <a name="may-7-2018"></a>7 maj, 2018
- Introducerade förhandsfunktion för objektidentifiering för begränsad utvärderingsversion-projekt.
- Uppgraderade till 2.0 API:er
- Utökade av S0-nivån till upp till 250 taggar och 50 000 bilder. 
- Betydande serverförbättringar av maskininlärningspipeline för bildklassificeringsprojekt. Projekt som tränas efter den 27 april 2018 kan dra nytta av dessa uppdateringar.
- Lade till modellexport för ONNX, för användning med Windows ML.
- Lade till modellexport till DockerFile. På så sätt kan du ladda ned artefakter för att skapa dina egna Windows- och Linux-containrar, till exempel DockerFile, TensorFlow-modell och tjänstkod. 
- För nyligen tränade modeller som exporteras till TensorFlow i allmänna domäner (kompakta) och Landmark-domäner (kompakta) [är medelvärden nu (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample) – konsekvent för alla projekt. 

### <a name="march-1-2018"></a>1 mars 2018
- Publicerade förhandsversion på Azure-portalen. Projekt kan nu bifogas till Azure-resurser med nivå F0 (kostnadsfritt) eller S0 (standard). Introducerade S0-nivån projekt, som tillåter upp till 100 taggar och 25 000 bilder. 
- Serverändringar av maskininlärningspipeline/parametern normalization. Detta ger kunderna bättre kontroll över kompromisser mellan precision och återanrop vid justeringar av sannolikhetströskelvärdet. Som en del av dessa ändringar angavs 50 % för sannolikhetströskelvärdet i CustomVision.ai-portalen.

### <a name="december-19-2017"></a>19 december 2017

- Export till Android (TensorFlow) har lagts till, utöver tidigare export till iOS (CoreML.) På så sätt kan export av en tränad kompakt modell köras offline i ett program.
- Lade till kompakta Retail- och Landmark-domäner för att möjliggöra modellexport för dessa domäner.
- Släppte version [1.2 av Training-API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) och [1.1 av Prediction-API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Uppdaterade API:ernas stöd för modellexport, ny förutsägelseåtgärd som inte sparar bilder i Förutsägelser och introducerade batchåtgärder för Training-API:et.
- Justeringar av användargränssnittet, bland annat möjligheten att se vilken domän som har använts för att träna en iteration.
- Uppdaterade [C# SDK och exempel](https://github.com/Microsoft/Cognitive-CustomVision-Windows).

## <a name="next-steps"></a>Nästa steg

[Lär dig att skapa en klassificerare](getting-started-build-a-classifier.md)
