---
title: Exportera eller ta bort data – Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Du behåller full kontroll över dina data. Den här artikeln förklarar hur du kan visa, exportera eller ta bort data i Custom Vision Service.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 82d9f4508db376ebbe69ef772c15fb732391a31d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "73718965"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Exportera eller ta bort användar data i Custom Vision

Custom Vision samlar in användar data för att kunna använda tjänsten, men kunderna har fullständig kontroll över att visa, exportera och ta bort sina data med hjälp av [API: er](https://go.microsoft.com/fwlink/?linkid=865446)för Custom vision utbildning.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Information om hur du exporterar och tar bort användar data i Custom Vision finns i följande tabell.

| Data | Exportera åtgärd | Ta bort åtgärd |
| ---- | ---------------- | ---------------- |
| Konto information (prenumerations nycklar) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Ta bort med Azure Portal (Azure-prenumerationer). Eller Använd knappen "ta bort ditt konto" på sidan med inställningar för CustomVision.ai (Microsoft-konto prenumerationer) | 
| Upprepnings information | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Upprepnings prestanda information | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Lista över iterationer | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projekt-och projekt information | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) och [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Bildtaggar | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) och [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Avbildningar | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (tillhandahåller URI för avbildnings hämtning) och [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (tillhandahåller URI för avbildnings hämtning) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Exporterade modeller | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Borttagen vid borttagning av konto |
