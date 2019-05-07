---
title: Tal enheter SDK-dokumentation
titleSuffix: Azure Cognitive Services
description: Viktig information – vad som har ändrats i de senaste versionerna
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/07/2018
ms.author: gracez
ms.openlocfilehash: 9147f02f6ef2e2f3f776e67aa1f103e95ef132aa
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153040"
---
# <a name="release-notes-of-cognitive-services-speech-devices-sdk"></a>Viktig information för Cognitive Services tal enheter SDK

Följande avsnitt lista över ändringar i de senaste versionerna.

## <a name="cognitive-services-speech-devices-sdk-150-2019-may-release"></a>Cognitive Services tal enheter SDK 1.5.0: 2019 maj versionen

*   Tal Devices SDK är nu allmänt tillgängliga och inte längre en skyddad förhandsversion.
*   Uppdatera den [tal SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) komponenten till version 1.5.0. Mer information finns i dess [viktig](https://aka.ms/csspeech/whatsnew).
*   Ny wake word teknik ger betydande kvalitet förbättringar finns i icke-bakåtkompatibla ändringar.
*   Ny ljud process-pipelinen för bättre tolkning av längst till fältet.

**Större ändringar**

*   På grund av den nya wake word tekniken måste alla wake ord återskapas på vår förbättrad wake word-portal. Om du vill ta bort avinstallera gamla nyckelord från enheten gamla appen.
    - adb uninstall com.microsoft.coginitiveservices.speech.samples.sdsdkstarterapp

## <a name="cognitive-services-speech-devices-sdk-140-2019-apr-release"></a>Cognitive Services tal enheter SDK 1.4.0: 2019 Apr versionen 

* Uppdatera den [tal SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) komponenten till version 1.4.0. Mer information finns i dess [viktig](https://aka.ms/csspeech/whatsnew). 

## <a name="cognitive-services-speech-devices-sdk-131-2019-mar-release"></a>Cognitive Services tal enheter SDK 1.3.1: 2019 Mar versionen 

* Uppdatera den [tal SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) komponenten till version 1.3.1. Mer information finns i dess [viktig](https://aka.ms/csspeech/whatsnew). 
*   Uppdaterade wake word hantering, se större ändringar.
*   Exempelprogram lägger till val av språk för både taligenkänning och översättning.

**Större ändringar** 

*   [Installera ett wake ord](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg#run-a-sample-application) har förenklad, det är nu en del av appen och behöver inte separat installation på enheten.
*   Wake word erkännande har ändrats och två händelser som stöds.
    - RecognizingKeyword, anger det tal resultatet innehåller (overifierade) Nyckelordstext.
    - RecognizedKeyword, anger den nyckelordet igenkänning av slutförts eftersom det angivna nyckelordet.


## <a name="cognitive-services-speech-devices-sdk-110-2018-nov-release"></a>Cognitive Services tal enheter SDK 1.1.0: 2018-Nov versionen 

* Uppdatera den [tal SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) komponenten till version 1.1.0. Mer information finns i dess [viktig](https://aka.ms/csspeech/whatsnew). 
* Längst till fältet taligenkänningen har förbättrats med vår förbättrad ljud bearbetning algoritm.
* Exempelprogrammet lagt till stöd för kinesiska tal.

## <a name="cognitive-services-speech-devices-sdk-101-2018-oct-release"></a>Cognitive Services tal enheter SDK 1.0.1: 2018-okt versionen 

* Uppdatera den [tal SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) komponenten till version 1.0.1. Mer information finns i dess [viktig](https://aka.ms/csspeech/whatsnew). 
* Taligenkänningen kommer förbättras med vår förbättrad ljud bearbetning-algoritm  
* En kontinuerlig igenkänning av ljud session programfel har åtgärdats.

**Större ändringar** 

* Med den här versionen införs ett antal icke-bakåtkompatibla ändringar. Kontrollera [den här sidan](https://aka.ms/csspeech/breakingchanges_1_0_0) information som rör API: erna. 
* Modellfiler KWS är inte kompatibla med tal Devices SDK 1.0.1. De befintliga Wake Word-filerna tas bort när de nya Wake Word-filerna skrivs till enheten. 

## <a name="cognitive-services-speech-devices-sdk-050-2018-aug-release"></a>Cognitive Services tal enheter SDK 0.5.0: 2018-Aug versionen

* Förbättrat riktighet taligenkänning genom att åtgärda en bugg i bearbetningskoden ljud.
* Uppdatera den [tal SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) komponenten till version 0.5.0. Mer information finns i dess [viktig](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="cognitive-services-speech-devices-sdk-0212733-2018-may-release"></a>Cognitive Services tal enheter SDK 0.2.12733: 2018-maj versionen

Den första offentliga förhandsversionen av Cognitive Services tal enheter SDK.
