---
title: Exportera eller ta bort dina data - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Du behåller full kontroll över dina data. I den här artikeln beskrivs hur du kan visa, exportera eller ta bort data i tjänsten Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 82d9f4508db376ebbe69ef772c15fb732391a31d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718965"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Exportera eller ta bort användardata i Anpassad syn

Custom Vision samlar in användardata för att driva tjänsten, men kunderna har full kontroll över visning, export och ta bort sina data med hjälp av CUSTOM Vision [Training API:er](https://go.microsoft.com/fwlink/?linkid=865446).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Mer information om hur du exporterar och tar bort användardata i Anpassad syn finns i följande tabell.

| Data | Exportera åtgärd | Ta bort åtgärd |
| ---- | ---------------- | ---------------- |
| Kontoinformation (prenumerationsnycklar) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Ta bort med Azure-portal (Azure-prenumerationer). Eller med knappen Ta bort ditt konto på sidan CustomVision.ai inställningar (Prenumerationer på Microsoft-konto) | 
| Detaljer för iteration | [GetIteration (getiteration)](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Detaljer om iterationsprestanda | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Lista över iterationer | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projekt och projektdetaljer | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) och [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [Ta bortprojekt](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Bildtaggar | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) och [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag (Borttagning)](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Avbildningar | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (ger uri för bild nedladdning) och [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (ger uri för bild nedladdning) | [Ta bortbilder](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Exporterade modeller | [Fåexport](https://go.microsoft.com/fwlink/?linkid=865446) | Raderas vid borttagning av konto |
