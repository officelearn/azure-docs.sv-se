---
author: baanders
description: ta med fil för Azure Digitals flätade – sätt att hantera instanser
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 887d185249f96b5d3be4aab6a96aa3c6c4a85690
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96231420"
---
I den här artikeln beskrivs hur du slutför olika hanterings åtgärder med [Azure Digitals .net- **SDK**(C#)](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true). Du kan också använda samma hanterings samtal med de andra språk-SDK: erna som beskrivs i [*anvisningar: använda Azures digitala dubbla API: er och SDK: er*](../articles/digital-twins/how-to-use-apis-sdks.md).

> [!TIP] 
> Kom ihåg att alla SDK-metoder ingår i synkrona och asynkrona versioner. För växlings anrop returneras asynkrona metoder `AsyncPageable<T>` när de synkrona versionerna returneras `Pageable<T>` .

Ett annat hanterings alternativ är att anropa Azure Digitals [**REST-API: er**](/rest/api/azure-digitaltwins/) för det här avsnittet direkt, via en rest-klient som Postman. Instruktioner för hur du gör detta finns i [*How-to: göra förfrågningar med Postman*](../articles/digital-twins/how-to-use-postman.md).

Slutligen kan du slutföra samma hanterings åtgärder med hjälp av Azure Digitals flätade **CLI**. Mer information om hur du använder CLI finns i [*How-to: använda Azure Digitals flätade CLI*](../articles/digital-twins/how-to-use-cli.md).