---
title: Exportera eller ta bort dina data – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Lär dig mer om att exportera eller ta bort dina data i Custom Vision Service.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: pafarley
ms.openlocfilehash: e3932c27b7741f04dfeda2a64f88a890b1e908ad
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54054987"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Exportera eller ta bort användardata i Custom Vision

Content Moderator samlar in användardata för att driva tjänsten, men kunder har fullständig kontroll över visa, exportera och ta bort sina data med hjälp av Custom Vision Service [utbildning API](https://go.microsoft.com/fwlink/?linkid=865446).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Mer information om hur du exporterar och ta bort användardata i anpassat visuellt innehåll finns i följande tabell.

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
