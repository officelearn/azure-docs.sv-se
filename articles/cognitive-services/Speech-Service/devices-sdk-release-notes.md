---
title: SDK-dokumentation för Talenheter
titleSuffix: Azure Cognitive Services
description: Viktig information innehåller en logg över uppdateringar, förbättringar, buggfixar och ändringar i Speech Devices SDK. Den här artikeln uppdateras med varje utgåva av Speech Devices SDK.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: 29dcb3c0e74482fd6670d1a0983e751043379d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80371610"
---
# <a name="release-notes-speech-devices-sdk"></a>Viktig information: Talenheter SDK

I följande avsnitt visas ändringar i de senaste versionerna.

## <a name="speech-devices-sdk-190"></a>Talenheter SDK 1.9.0:

- Inledande binärfiler för [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter) (Linux ARM64) tillhandahålls.
- Roobo v1 använder nu Maven för tal SDK
- Uppdaterade [speech SDK-komponenten](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) till version 1.9.0. Mer information finns i [dess viktig information](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-170"></a>Talenheter SDK 1.7.0:

- Linux ARM stöds nu.
- Inledande binärfiler för [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2) tillhandahålls (Linux ARM64).
- Windows-användare `AudioConfig.fromDefaultMicrophoneInput()` kan `AudioConfig.fromMicrophoneInput(deviceName)` använda eller ange vilken mikrofon som ska användas.
- Bibliotekets storlek har optimerats.
- Stöd för multi-turn erkännande med samma tal / avsikt recognizer objekt.
- Åtgärda tillfällig låser som skulle uppstå när du stoppar igenkänningen.
- Exempelappar innehåller nu en exempeldeldeldeldeldeltagare.egenskaper för att visa filens format.
- Uppdaterade [speech SDK-komponenten](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) till version 1.7.0. Mer information finns i [dess viktig information](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-160"></a>Talenheter SDK 1.6.0:

- Stöd [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) på Windows och Linux med vanliga [exempelprogram](https://aka.ms/sdsdk-download)
- Uppdaterade [speech SDK-komponenten](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) till version 1.6.0. Mer information finns i [dess viktig information](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-151"></a>Talenheter SDK 1.5.1:

- Inkludera [konversationsavskrift](conversation-transcription-service.md) i exempelappen.
- Uppdaterade [speech SDK-komponenten](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) till version 1.5.1. Mer information finns i [dess viktig information](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-150-2019-may-release"></a>Talenheter SDK 1.5.0: 2019-maj release

- Speech Devices SDK är nu GA och inte längre en gated förhandsvisning.
- Uppdaterade [speech SDK-komponenten](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) till version 1.5.0. Mer information finns i [dess viktig information](https://aka.ms/csspeech/whatsnew).
- Ny sökordsteknik ger betydande kvalitetsförbättringar, se Breaking Changes.
- Ny ljudbearbetningspipeline för förbättrad långt fältigenkänning.

**Bryta förändringar**

- På grund av den nya sökordstekniken måste alla sökord återskapas på vår förbättrade sökordsportal. Om du vill ta bort gamla nyckelord helt från enheten avinstallerar du den gamla appen.
  - adb avinstallera com.microsoft.cognitiveservices.speech.samples.sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Talenheter SDK 1.4.0: 2019-Apr release

- Uppdaterade [speech SDK-komponenten](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) till version 1.4.0. Mer information finns i [dess viktig information](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Talenheter SDK 1.3.1: 2019-Mar-release

- Uppdaterade [speech SDK-komponenten](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) till version 1.3.1. Mer information finns i [dess viktig information](https://aka.ms/csspeech/whatsnew).
- Uppdaterad sökordshantering, se Bryta ändringar.
- Exempelprogram lägger till språkval för både taligenkänning och översättning.

**Bryta förändringar**

- [Installera ett sökord](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) har förenklats, det är nu en del av appen och behöver inte separat installation på enheten.
- Nyckelordsigenkänningen har ändrats och två händelser stöds.
  - `RecognizingKeyword,`anger att talresultatet innehåller (overifierad) nyckelordstext.
  - `RecognizedKeyword`anger att sökordsigenkänningen har fyllts i när det har visats att det angivna nyckelordet har fyllts i.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Talenheter SDK 1.1.0: 2018-Nov release

- Uppdaterade [speech SDK-komponenten](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) till version 1.1.0. Mer information finns i [dess viktig information](https://aka.ms/csspeech/whatsnew).
- Far Field Taligenkänningsnoggrannhet har förbättrats med vår förbättrade ljudbehandlingsalgoritm.
- Exempelprogram har lagt till stöd för kinesisk taligenkänning.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Talenheter SDK 1.0.1: 2018-okt release

- Uppdaterade [speech SDK-komponenten](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) till version 1.0.1. Mer information finns i [dess viktig information](https://aka.ms/csspeech/whatsnew).
- Taligenkänningsnoggrannheten kommer att förbättras med vår förbättrade ljudbehandlingsalgoritm
- En ljudsessionsbugg för kontinuerlig igenkänning är fast.

**Bryta förändringar**

- Med den här versionen introduceras ett antal brytningsändringar. Kontrollera [denna sida](https://aka.ms/csspeech/breakingchanges_1_0_0) för information om API: er.
- KWS-modellfilerna är inte kompatibla med Talenheter SDK 1.0.1. De befintliga nyckelordsfilerna tas bort när de nya nyckelordsfilerna har skrivits till enheten.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Talenheter SDK 0.5.0: 2018-Aug release

- Förbättrad noggrannhet en taligenkänning genom att åtgärda en bugg i ljudbehandlingskoden.
- Uppdaterade [speech SDK-komponenten](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) till version 0.5.0. Mer information finns i [dess viktig information](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Talenheter SDK 0.2.12733: 2018-Maj release

Den första offentliga förhandsversionen av Cognitive Services Speech Devices SDK.
