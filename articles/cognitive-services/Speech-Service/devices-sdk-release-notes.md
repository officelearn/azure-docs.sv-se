---
title: Dokumentation om tal enheter SDK
titleSuffix: Azure Cognitive Services
description: Viktig information innehåller en logg över uppdateringar, förbättringar, fel korrigeringar och ändringar av tal enheternas SDK. Den här artikeln uppdateras med varje version av tal enheter SDK.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: 29dcb3c0e74482fd6670d1a0983e751043379d6e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80371610"
---
# <a name="release-notes-speech-devices-sdk"></a>Viktig information: tal enheter SDK

I följande avsnitt listas ändringar i de senaste versionerna.

## <a name="speech-devices-sdk-190"></a>1.9.0 för tal enheter SDK:

- Inledande binärfiler för [URBETTER DDK](https://aka.ms/sdsdk-download-urbetter) (Linux arm64) tillhandahålls.
- Roobo v1 använder nu Maven för tal-SDK: n
- Versionen av [tal-SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) -komponenten har uppdaterats till version 1.9.0. Mer information finns i [versions anteckningarna](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-170"></a>1.7.0 för tal enheter SDK:

- Linux ARM stöds nu.
- Första binärfiler för [Roobo v2-DDK](https://aka.ms/sdsdk-download-roobov2) tillhandahålls (Linux-arm64).
- Windows-användare kan `AudioConfig.fromDefaultMicrophoneInput()` använda `AudioConfig.fromMicrophoneInput(deviceName)` eller för att ange vilken mikrofon som ska användas.
- Biblioteks storleken har optimerats.
- Stöd för multi-turn-igenkänning med samma tal-/avsikts igenkännings objekt.
- Korrigera tillfällig låsning som skulle inträffa när du stoppar igenkänningen.
- Exempel appar innehåller nu en exempel deltagare. Properties-fil för att demonstrera fil formatet.
- Versionen av [tal-SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) -komponenten har uppdaterats till version 1.7.0. Mer information finns i [versions anteckningarna](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-160"></a>1.6.0 för tal enheter SDK:

- Stöd för [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) på Windows och Linux med gemensamt [exempel program](https://aka.ms/sdsdk-download)
- Versionen av [tal-SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) -komponenten har uppdaterats till version 1.6.0. Mer information finns i [versions anteckningarna](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-151"></a>1.5.1 för tal enheter SDK:

- Inkludera [konversations avskrift](conversation-transcription-service.md) i exempel appen.
- Versionen av [tal-SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) -komponenten har uppdaterats till version 1.5.1. Mer information finns i [versions anteckningarna](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-150-2019-may-release"></a>Tal enheter SDK-1.5.0:2019-maj-utgåva

- Tal enheter SDK är nu GA och inte längre en gated Preview.
- Versionen av [tal-SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) -komponenten har uppdaterats till version 1.5.0. Mer information finns i [versions anteckningarna](https://aka.ms/csspeech/whatsnew).
- Nya nyckelords teknik ger avsevärda kvalitets förbättringar, se bryta ändringar.
- Ny pipeline för ljud bearbetning för förbättrad igenkänning i långt fält.

**Icke-bakåtkompatibla ändringar**

- På grund av den nya nyckelords tekniken måste alla nyckelord skapas på nytt i den förbättrade nyckelords portalen. För att helt ta bort gamla nyckelord från enheten avinstallera den gamla appen.
  - ADB Uninstall com. Microsoft. cognitiveservices. Speech. Samples. sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Tal enheter SDK-1.4.0:2019-apr-version

- Versionen av [tal-SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) -komponenten har uppdaterats till version 1.4.0. Mer information finns i [versions anteckningarna](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Tal enheter SDK-1.3.1:2019-Mar-version

- Versionen av [tal-SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) -komponenten har uppdaterats till version 1.3.1. Mer information finns i [versions anteckningarna](https://aka.ms/csspeech/whatsnew).
- Uppdaterad nyckelords hantering, se bryta ändringar.
- Exempel programmet lägger till val av språk för både tal igenkänning och översättning.

**Icke-bakåtkompatibla ändringar**

- Att [Installera ett nyckelord](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) har förenklats, det är nu en del av appen och behöver inte separat installation på enheten.
- Nyckelords igenkänningen har ändrats och två händelser stöds.
  - `RecognizingKeyword,`anger att tal resultatet innehåller (ej verifierade) nyckelords text.
  - `RecognizedKeyword`, anger att nyckelords igenkänningen har slutfört att det aktuella nyckelordet har identifierats.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Tal enheter SDK-1.1.0:2018-nov-version

- Versionen av [tal-SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) -komponenten har uppdaterats till version 1.1.0. Mer information finns i [versions anteckningarna](https://aka.ms/csspeech/whatsnew).
- Långt fält tal igenkännings precision har förbättrats med vår Enhanced Audio Processing algorithm.
- Exempel programmet har lagt till stöd för kinesiska tal igenkänning.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Tal enheter SDK 1.0.1:2018 – oktober version

- Versionen av [tal-SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) -komponenten har uppdaterats till version 1.0.1. Mer information finns i [versions anteckningarna](https://aka.ms/csspeech/whatsnew).
- Tal igenkännings precisionen förbättras med vår förbättrade algoritm för ljud bearbetning
- Ett program med kontinuerlig igenkännings ljud session har åtgärd ATS.

**Icke-bakåtkompatibla ändringar**

- I den här versionen introduceras ett antal avbrytande ändringar. Mer information om API: erna finns i [den här sidan](https://aka.ms/csspeech/breakingchanges_1_0_0) .
- KWS-modellens filer är inte kompatibla med tal enheter SDK-1.0.1. De befintliga nyckelorden tas bort när de nya nyckelorden skrivs till enheten.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Tal enheter SDK-0.5.0:2018-aug-version

- Bättre precision för tal igenkänning genom att åtgärda ett fel i koden för ljud bearbetning.
- Versionen av [tal-SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) -komponenten har uppdaterats till version 0.5.0. Mer information finns i [versions anteckningarna](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Tal enheter SDK-0.2.12733:2018-maj-utgåva

Den första offentliga för hands versionen av Cognitive Services Speech-enheter SDK.
