---
title: Custom Vision Service viktig information
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.date: 08/28/2018
ms.author: anroth
ms.openlocfilehash: 8423051fa5169eb8acddc7297e36188e297cb9e4
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43288670"
---
# <a name="custom-vision-service-release-notes"></a>Custom Vision Service viktig information

## <a name="august-14-2018"></a>Den 14 augusti 2018
- Har lagts till ”Kom igång” widget till customvision.ai plats som leder användarna genom utbildning. 
- Ytterligare förbättringar för maskininlärningspipeline som gynnar multilabel projekt (nya förlust layer).

## <a name="june-28-2018"></a>28 juni 2018
- Förbättringar av Bugfixes & serverdel.
- Enabeled Multiclass klassificering för projekt där bilder har exakt en etikett. I förutsägelser för multiklass-baserad läge summera sannolikhet till en (alla avbildningar klassificeras bland angivna taggarna).

## <a name="june-13-2018"></a>13 juni 2018
- UX-uppdatering, fokuserar på enkel användning och hjälpmedel. 
- Förbättringar för machine learning-pipeline som gynnar multilabel projekt med ett stort antal taggar.
- En bugg har åtgärdats i TensorFlow export. Enabeled exporteras modellen versionshantering, så iterationer kan exporteras mer än en gång. 

## <a name="may-7-2018"></a>7 maj, 2018
- Introducerade förhandsfunktion för objektidentifiering för begränsad utvärderingsversion-projekt.
- Uppgraderade till 2.0 API:er
- Utökade av S0-nivån till upp till 250 taggar och 50 000 bilder. 
- Betydande serverförbättringar av maskininlärningspipeline för bildklassificeringsprojekt. Projekt som tränas efter den 27 april 2018 kan dra nytta av dessa uppdateringar.
- Lade till modellexport för ONNX, för användning med Windows ML.
- Lade till modellexport till DockerFile. På så sätt kan du ladda ned artefakter för att skapa dina egna Windows- och Linux-containrar, till exempel DockerFile, TensorFlow-modell och tjänstkod. 
- För nyligen tränade modeller som exporteras till TensorFlow i allmänna domäner (kompakta) och Landmark-domäner (kompakta) [är medelvärden nu (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample) – konsekvent för alla projekt. 

## <a name="march-1-2018"></a>1 mars 2018
- Publicerade förhandsversion på Azure-portalen. Projekt kan nu bifogas till Azure-resurser med nivå F0 (kostnadsfritt) eller S0 (standard). Introducerade S0-nivån projekt, som tillåter upp till 100 taggar och 25 000 bilder. 
- Serverändringar av maskininlärningspipeline/parametern normalization. Detta ger kunderna bättre kontroll över kompromisser mellan precision och återanrop vid justeringar av sannolikhetströskelvärdet. Som en del av dessa ändringar angavs 50 % för sannolikhetströskelvärdet i CustomVision.ai-portalen.

## <a name="december-19-2017"></a>19 december 2017

- Export till Android (TensorFlow) har lagts till, utöver tidigare export till iOS (CoreML.) På så sätt kan export av en tränad kompakt modell köras offline i ett program.
- Lade till kompakta Retail- och Landmark-domäner för att möjliggöra modellexport för dessa domäner.
- Släppte version [1.2 av Training-API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) och [1.1 av Prediction-API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Uppdaterade API:ernas stöd för modellexport, ny förutsägelseåtgärd som inte sparar bilder i Förutsägelser och introducerade batchåtgärder för Training-API:et.
- Justeringar av användargränssnittet, bland annat möjligheten att se vilken domän som har använts för att träna en iteration.
- Uppdaterade [C# SDK och exempel](https://github.com/Microsoft/Cognitive-CustomVision-Windows).

