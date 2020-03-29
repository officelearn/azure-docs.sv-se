---
title: Skillnader mellan hanterings-API:er och tjänst-API:er – Azure Batch | Microsoft-dokument
description: API:er fungerar på de olika lagren i Azure Batch-tjänsten.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 181515c0f497af8ffadcb909c13e51a40bfbf3b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672762"
---
# <a name="service-level-and-management-level-apis"></a>API:er på servicenivå och hanteringsnivå

Azure Batch har två uppsättningar API:er, en för tjänstnivån och en för hanteringsnivån. Namngivningen är ofta liknande men de returnerar olika resultat. Om du vill ha aktivitetsloggar måste du använda hanterings-API:erna. API:er på tjänstnivå kringgår Azure Resource Management-lagret och loggas inte.


Batchhantering och batch-tjänst har båda API:er för pool, till exempel. 
- Det här API:et för att ta bort poolen är direkt inriktat på batchkontot:https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Det här API:et för att ta bort poolen https://docs.microsoft.com/rest/api/batchmanagement/pool/delete är inriktat på management.azure.com lagret.

