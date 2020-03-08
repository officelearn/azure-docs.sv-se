---
title: 'Skillnader mellan hanterings-API: er och tjänst-API: er – Azure Batch | Microsoft Docs'
description: 'API: er fungerar på de olika skikten i Azure Batchs tjänsten.'
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 181515c0f497af8ffadcb909c13e51a40bfbf3b0
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672762"
---
# <a name="service-level-and-management-level-apis"></a>API: er för service nivå och hanterings nivå

Azure Batch har två uppsättnings-API: er, en för Service nivån och en för hanterings nivån. Namnet är ofta detsamma, men de returnerar olika resultat. Om du vill ha aktivitets loggar måste du använda hanterings-API: er. Service nivå-API: er kringgår Azure-resurs hanterings skiktet och loggas inte.


Batch-hantering och batch-tjänsten har båda API: er för pool, till exempel. 
- Detta API för att ta bort poolen är riktat direkt mot batch-kontot: https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Detta API för att ta bort poolen https://docs.microsoft.com/rest/api/batchmanagement/pool/delete riktas mot management.azure.com-skiktet.

