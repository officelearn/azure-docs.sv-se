---
title: 'Skillnader mellan API: er för hantering och tjänst-API: er'
description: 'API: er fungerar på de olika skikten i Azure Batchs tjänsten.'
ms.topic: conceptual
ms.date: 02/26/2020
ms.custom: seodec18
ms.openlocfilehash: 33b8c5980aba1090155d6b136c6707e928666abf
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83867609"
---
# <a name="service-level-and-management-level-apis"></a>API: er för service nivå och hanterings nivå

Azure Batch har två uppsättnings-API: er, en för Service nivån och en för hanterings nivån. Namnet är ofta detsamma, men de returnerar olika resultat. Om du vill ha aktivitets loggar måste du använda hanterings-API: er. Service nivå-API: er kringgår Azure-resurs hanterings skiktet och loggas inte.


Batch-hantering och batch-tjänsten har båda API: er för pool, till exempel. 
- Detta API för att ta bort poolen är riktat direkt mot batch-kontot:https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Detta API för att ta bort poolen https://docs.microsoft.com/rest/api/batchmanagement/pool/delete riktas mot Management.Azure.com-skiktet.

