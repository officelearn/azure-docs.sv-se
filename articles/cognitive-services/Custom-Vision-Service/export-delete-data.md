---
title: Exportera eller ta bort dina data – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Lär dig mer om att exportera eller ta bort dina data i Custom Vision Service.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: pafarley
ms.openlocfilehash: f4b83c332387e7ea19028efec76771ace42f612b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55209901"
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
