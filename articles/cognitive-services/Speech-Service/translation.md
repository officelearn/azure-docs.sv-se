---
title: Exempel för översättning | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Här är ett exempel för översättning för tal.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: a02a89de1d05ec5388a92f14a0fd206e0ee966a6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355620"
---
# <a name="sample-for-translation"></a>Exempel för översättning

> [!NOTE]
> Anvisningar att hämta det här exemplet och andra finns [prover för tal SDK](samples.md).

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> Följande översta förklaringar bör vara på plats för alla prover nedan:
>
> [!code-csharp [Using Statements](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#toplevel)]
>
> - - -

## <a name="translation-using-the-microphone"></a>Översättning med hjälp av mikrofonen

Kodfragmentet nedan visar hur du översätter talindata från engelska tyska och även hämta röst utdata för översatt text. Mikrofonens används.

[!code-csharp[Translation Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithMicrophoneAsync)]

- - -

## <a name="translation-using-file-input"></a>Översättning med hjälp av filen indata

Kodfragmentet nedan visar hur du översätta talindata från engelska till tyska och franska.
Filen används som indata.

[!code-csharp[Translation Using File Input](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithFileAsync)]

- - -

## <a name="sample-source-code"></a>Exempelkod för källa

Den senaste versionen av prover och mer avancerade exempel finns i ett dedikerat [GitHub-lagringsplatsen](https://github.com/Azure-Samples/cognitive-services-speech-sdk).

## <a name="next-steps"></a>Nästa steg

- [Taligenkänning](./speech-to-text-sample.md)

- [Avsiktshantering igenkänning](./intent.md)
