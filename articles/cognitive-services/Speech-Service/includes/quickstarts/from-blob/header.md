---
title: 'Snabb start: identifiera tal som lagras i Blob Storage-tal service'
titleSuffix: Azure Cognitive Services
description: Inte klart
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: ef61139056ad194d89e2a67cb4ac1b1414a3380b
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828925"
---
I den här snabb starten ska du använda [batch-Avskriften REST API](../../../batch-transcription.md) för att identifiera tal som lagras i en [SAS-BLOB](https://aka.ms/ignite2019/speech/placeholder). Efter att ha uppfyllt några nödvändiga förutsättningar, tar det bara några steg att känna igen tal med en REST API:
> [!div class="checklist"]
> * Skicka JSON-begäran till tal tjänsten för att börja skriva tal.
> * Kontrol lera status för avskriften.
> * Hämta avskrifts resultaten när du är färdig.