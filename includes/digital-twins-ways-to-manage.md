---
author: baanders
description: ta med fil för Azure Digitals flätade – sätt att hantera instanser
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 47de00780fb259e7476c16dea9b7c63bc512a211
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533913"
---
I den här artikeln beskrivs hur du slutför olika hanterings åtgärder med [Azure Digitals .net- **SDK** (C#)](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true). Du kan också använda samma hanterings samtal med de andra språk-SDK: erna som beskrivs i [*anvisningar: använda Azures digitala dubbla API: er och SDK: er*](../articles/digital-twins/how-to-use-apis-sdks.md).

> [!TIP] 
> Kom ihåg att alla SDK-metoder ingår i synkrona och asynkrona versioner. För växlings anrop returneras asynkrona metoder `AsyncPageable<T>` när de synkrona versionerna returneras `Pageable<T>` .

Ett annat hanterings alternativ är att anropa de digitala Azure-dubbla [**REST-API: erna**](/rest/api/azure-digitaltwins/) för det här avsnittet direkt.

Slutligen kan du slutföra samma hanterings åtgärder med hjälp av Azure Digitals flätade **CLI**. Mer information om hur du använder CLI finns i [*How-to: använda Azure Digitals flätade CLI*](../articles/digital-twins/how-to-use-cli.md).