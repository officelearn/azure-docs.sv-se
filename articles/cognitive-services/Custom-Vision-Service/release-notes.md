---
title: Viktig information - Tjänsten Custom Vision
titleSuffix: Azure Cognitive Services
description: Få den senaste informationen om nya versioner från Custom Vision-teamet.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: b9606c63ec7b53fb0b69918c21bdd9206f34c555
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647561"
---
# <a name="custom-vision-service-release-notes"></a>Viktig information om tjänsten Custom Vision

## <a name="may-2-2019-and-may-10-2019"></a>2 maj 2019 och 10 maj 2019

- Bugfixes och backend förbättringar

## <a name="may-23-2019"></a>den 23 maj 2019

- Förbättrad portal-UX-upplevelse som är relaterad till Azure-prenumerationer, vilket gör det enklare att välja dina Azure-kataloger.

## <a name="april-18-2019"></a>den 18 april 2019 

- Lade till objektidentifieringsexport för Vision AI Dev Kit.
- UI tweaks, inklusive projektsökning.

## <a name="april-3-2019"></a>den 3 april 2019

- Ökad gräns för antalet markeringsrutor per bild till 200. 
- Buggfixar, inklusive omfattande prestandauppdatering för modeller som exporteras till TensorFlow. 

## <a name="march-26-2019"></a>den 26 mars 2019

- Custom Vision-tjänsten har angett allmän tillgänglighet på Azure!
- Lade till avancerad utbildning funktion med en ny maskininlärning backend för bättre prestanda, särskilt på utmanande datamängder och finkornig klassificering. Med avancerad utbildning kan du ange en beräkningstidsbudget för utbildning och Custom Vision identifierar experimentellt de bästa utbildnings- och förstoringsinställningarna. För snabba iterationer kan du fortsätta att använda den befintliga snabbträningen.
- Introducerade 3.0 API:er. Meddelade kommande utfasning av pre-3.0 API:er den 1 oktober 2019. Mer om hur du kommer igång finns i snabbstarterna för [.Net,](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/csharp-tutorial) [Python](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/python-tutorial), [Node](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/node-tutorial), [Java](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/java-tutorial)eller [Gå.](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/go-tutorial)
- Ersatte "Standarditerationer" med Publicera/avpublicera i 3.0 API:erna.
- Nya modellexportmål har lagts till. Dockerfile export har uppgraderats för att stödja ARM för Raspberry Pi 3. Exportstöd har lagts till i [Vision AI Dev Kit.](https://visionaidevkit.com/).
- Ökad gräns för taggar per projekt till 500 för S0-nivå. Ökad gräns för bilder per projekt till 100 000 för S0-nivån.
- Borttagen vuxendomän. Allmän domän rekommenderas i stället.
- Aviserade [priser](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) för allmän tillgänglighet.  

## <a name="february-25-2019"></a>den 25 februari 2019

- Meddelade slutet av begränsade utvärderingsversionsprojekt (projekt som inte är associerade med en Azure-resurs), eftersom Custom Vision närmar sig slutförandet av dess övergång till offentlig azure-förhandsversion. Från och med den 25 mars 2019 stöder CustomVision.ai-webbplatsen endast visningsprojekt som är associerade med en Azure-resurs, till exempel den kostnadsfria Custom Vision-resursen. Till och med den 1 oktober 2019 kan du fortfarande komma åt dina befintliga begränsade testversionsprojekt via API:erna för Anpassade visioner. Detta ger dig tid att uppdatera API-nycklar för alla appar som du har skrivit med Anpassad vision. Efter den 1 oktober 2019 tas alla begränsade utvärderingsprojekt som du inte har flyttat till Azure bort.

## <a name="january-22-2019"></a>22 januari 2019

- Stöd till nya Azure-regioner: Västra USA 2, Östra USA, Östra USA 2, Västeuropa, Norra Europa, Sydostasien, Östra Australien, Centrala Indien, Södra Storbritannien, Östra Japan och Norra centrala USA. Stödet fortsätter för Södra centrala USA.

## <a name="december-12-2018"></a>den 12 december 2018

- Stöd för export för objektidentifieringsmodeller (introducerade Compact Domain för objektidentifiering).
- Fixade ett antal tillgänglighetsproblem för förbättrat stöd för skärmläsare och tangentbordsnavigering.
- UX-uppdateringar för bildvisare och förbättrad taggningsupplevelse för objektidentifiering för snabbare taggning.  
- Uppdaterad basmodell för objektidentifieringsdomän för objektidentifiering av bättre kvalitet.
- Buggfixar.

## <a name="november-6-2018"></a>den 6 november 2018

- Lagt till stöd för Logo Domain i objektidentifiering.

## <a name="october-9-2018"></a>9 oktober 2018

- Objektidentifiering anger betald förhandsgranskning. Du kan nu skapa objektidentifieringsprojekt med en Azure-resurs.
- Lade till funktionen Flytta till Azure på webbplatsen för att göra det enklare att uppgradera ett begränsat utvärderingsprojekt för att länka till en Azure. resurslänkade projekt (F0 eller S0.) Du hittar detta på sidan Inställningar för din produkt.  
- Lade till export till ONNX 1.2 för att stödja Windows 2018 Oktober Update-versionen av Windows ML.
Buggfixar, inklusive för ONNX-export med specialtecken.

## <a name="august-14-2018"></a>den 14 augusti 2018

- Lade till widgeten "Kom igång" för att customvision.ai webbplats för att vägleda användarna genom projektutbildning.
- Ytterligare förbättringar av machine learning pipeline för att gynna multilabel projekt (nya förlust lager).

## <a name="june-28-2018"></a>den 28 juni 2018

- Buggfixar & förbättringar i backend.
- Aktiverad klassificering av fleraklasser för projekt där bilder har exakt en etikett. I Förutsägelser för flerklassläge summerar sannolikheter till en (alla bilder klassificeras bland dina angivna taggar).

## <a name="june-13-2018"></a>13 juni 2018

- UX uppdatering, med fokus på användarvänlighet och tillgänglighet.
- Förbättringar av machine learning pipeline för att gynna multilabel projekt med ett stort antal taggar.
- Fast fel i TensorFlow export. Aktiverad exporterad modellversion, så iterationer kan exporteras mer än en gång.

## <a name="may-7-2018"></a>7 maj, 2018

- Introducerade förhandsfunktion för objektidentifiering för begränsad utvärderingsversion-projekt.
- Uppgraderade till 2.0 API:er
- Utökade av S0-nivån till upp till 250 taggar och 50 000 bilder.
- Betydande serverförbättringar av maskininlärningspipeline för bildklassificeringsprojekt. Projekt som tränas efter den 27 april 2018 kan dra nytta av dessa uppdateringar.
- Lade till modellexport för ONNX, för användning med Windows ML.
- Lade till modellexport till DockerFile. På så sätt kan du ladda ned artefakter för att skapa dina egna Windows- och Linux-containrar, till exempel DockerFile, TensorFlow-modell och tjänstkod.
- För nyutbildade modeller som exporteras till TensorFlow i domänerna Allmänt (Kompakt) och Landmark (Compact) [är medelvärden nu (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample)för konsekvens i alla projekt.

## <a name="march-1-2018"></a>1 mars 2018

- Inskriven betald förhandsversion och inlagd på Azure-portalen. Projekt kan nu bifogas till Azure-resurser med nivå F0 (kostnadsfritt) eller S0 (standard). Introducerade S0-nivån projekt, som tillåter upp till 100 taggar och 25 000 bilder.
- Serverändringar av maskininlärningspipeline/parametern normalization. Detta ger kunderna bättre kontroll över kompromisser mellan precision och återanrop vid justeringar av sannolikhetströskelvärdet. Som en del av dessa ändringar angavs 50 % för sannolikhetströskelvärdet i CustomVision.ai-portalen.

## <a name="december-19-2017"></a>19 december 2017

- Exportera till Android (TensorFlow) tillagd, förutom tidigare släppt export till iOS (CoreML.) Detta gör att export av en tränad kompakt modell kan köras offline i ett program.
- Lade till kompakta Retail- och Landmark-domäner för att möjliggöra modellexport för dessa domäner.
- Släppte version [1.2 av Training-API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) och [1.1 av Prediction-API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Uppdaterade API:ernas stöd för modellexport, ny förutsägelseåtgärd som inte sparar bilder i Förutsägelser och introducerade batchåtgärder för Training-API:et.
- Justeringar av användargränssnittet, bland annat möjligheten att se vilken domän som har använts för att träna en iteration.
- Uppdaterade [C# SDK och exempel](https://github.com/Microsoft/Cognitive-CustomVision-Windows).