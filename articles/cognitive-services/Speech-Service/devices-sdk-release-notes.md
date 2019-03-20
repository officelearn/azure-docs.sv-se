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
ms.openlocfilehash: 4d802b9f71edee1eec4b2c92881e2a8796db2865
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58005519"
---
# <a name="release-notes-of-cognitive-services-speech-devices-sdk"></a>Viktig information för Cognitive Services tal enheter SDK

Följande avsnitt lista över ändringar i de senaste versionerna.

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
