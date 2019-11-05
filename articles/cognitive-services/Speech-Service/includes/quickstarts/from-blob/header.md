---
title: 'Snabb start: identifiera tal som lagras i Blob Storage-tal service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 195c4c94f7ab00a8740c9dd14aad0d0fd0daa9b7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503978"
---
I den här snabb starten ska du använda [batch-Avskriften REST API](../../../batch-transcription.md) för att identifiera tal som lagras i en [SAS-BLOB](https://aka.ms/ignite2019/speech/placeholder). Efter att ha uppfyllt några nödvändiga förutsättningar, tar det bara några steg att känna igen tal med en REST API:
> [!div class="checklist"]
> * Skicka JSON-begäran till tal tjänsten för att börja skriva tal.
> * Kontrol lera status för avskriften.
> * Hämta avskrifts resultaten när du är färdig.