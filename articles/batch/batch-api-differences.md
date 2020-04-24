---
title: 'Skillnader mellan API: er för hantering och tjänst-API: er'
description: 'API: er fungerar på de olika skikten i Azure Batchs tjänsten.'
ms.topic: conceptual
ms.date: 02/26/2020
ms.custom: seodec18
ms.openlocfilehash: 33b8c5980aba1090155d6b136c6707e928666abf
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115405"
---
# <a name="service-level-and-management-level-apis"></a>API: er för service nivå och hanterings nivå

Azure Batch har två uppsättnings-API: er, en för Service nivån och en för hanterings nivån. Namnet är ofta detsamma, men de returnerar olika resultat. Om du vill ha aktivitets loggar måste du använda hanterings-API: er. Service nivå-API: er kringgår Azure-resurs hanterings skiktet och loggas inte.


Batch-hantering och batch-tjänsten har båda API: er för pool, till exempel. 
- Detta API för att ta bort poolen är riktat direkt mot batch-kontot:https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Detta API för att ta https://docs.microsoft.com/rest/api/batchmanagement/pool/delete bort poolen riktas mot Management.Azure.com-skiktet.

