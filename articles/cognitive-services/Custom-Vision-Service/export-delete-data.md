---
title: Exportera eller ta bort dina data i anpassade Vision, kognitiva Azure-tjänster | Microsoft Docs
description: Lär dig hur du exporterar eller ta bort dina data i anpassade Vision.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/25/2018
ms.author: v-jaswel
ms.openlocfilehash: 49fc49b3181f56c23167cfbae18911e7db441f8c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355380"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Exportera eller ta bort användardata i anpassade Vision

Innehåll kontrollant samlar in användardata för att använda tjänsten, men kunder har fullständig kontroll över visa, exportera och ta bort sina data med hjälp av tjänsten anpassad Vision [utbildning API](https://go.microsoft.com/fwlink/?linkid=865446).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Mer information om hur du exporterar och ta bort användardata i anpassade Vision finns i följande tabell.

| Data | Exportåtgärden | Borttagningsåtgärd |
| ---- | ---------------- | ---------------- |
| Kontoinformation (prenumeration nycklar) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Ta bort med hjälp av Azure portal (Azure-prenumerationer). Eller med hjälp av knappen ”Ta bort ditt konto” i CustomVision.ai inställningssidan (Microsoft-konto prenumerationer) |
| Iteration information | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Prestandainformation för upprepning | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Lista över iterationer | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projekt och information | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) och [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) |
| Bildtaggar | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) och [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) |
| Avbildningar | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (ger uri för hämtningen) och [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (ger uri för hämtningen) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) |
| Exporterade modeller | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Ta bort vid borttagning av kontot |
