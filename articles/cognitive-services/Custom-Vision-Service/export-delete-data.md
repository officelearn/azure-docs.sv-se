---
title: Exportera eller ta bort dina data – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Lär dig mer om att exportera eller ta bort dina data i Custom Vision Service.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.openlocfilehash: 4c395a062b1132710f888cc5a315529db082a805
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57850034"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Exportera eller ta bort användardata i Custom Vision

Custom Vision samlar in användardata för att driva tjänsten, men kunder har fullständig kontroll över visa, exportera och ta bort sina data med hjälp av Custom Vision [utbildning API: er](https://go.microsoft.com/fwlink/?linkid=865446).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Om du vill lära dig mer om att exportera och ta bort användardata i Custom Vision, se tabellen nedan.

| Data | Exportåtgärden | Borttagningsåtgärd |
| ---- | ---------------- | ---------------- |
| Kontoinformation (Prenumerationsnycklar) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Ta bort med hjälp av Azure portal (Azure-prenumerationer). Eller med hjälp av knappen ”Ta bort ditt konto” i inställningssidan för CustomVision.ai (Microsoft-konto prenumerationer) | 
| Iteration information | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Prestandainformation för upprepning | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Lista över iterationer | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projekt och projektinformation | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) och [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Taggar | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) och [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Avbildningar | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (ger uri för hämtningen) och [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (ger uri för avbildning nedladdning) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Exporterade modeller | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Bort när du försöker ta bort |
