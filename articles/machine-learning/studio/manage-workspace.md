---
title: Hantera en Machine Learning Studio-arbetsyta
titleSuffix: Azure Machine Learning Studio
description: Hantera åtkomst till Azure Machine Learning Studio-arbetsytor och distribuera och hantera Machine Learning API-webbtjänster
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: a947f9a94dd4ceed624e6b04a671b21b8926d25e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322795"
---
# <a name="manage-an-azure-machine-learning-studio-workspace"></a>Hantera en Azure Machine Learning Studio-arbetsyta

> [!NOTE]
> Information om hur du hanterar webbtjänster i Machine Learning Web Services-portalen finns i [hantera en webbtjänst med hjälp av Azure Machine Learning Web Services-portalen](manage-new-webservice.md).
> 
> 

Du kan hantera Machine Learning Studio-arbetsytor i Azure-portalen.



## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Hantera en Studio-arbetsyta i Azure portal:

1. Logga in på den [Azure-portalen](https://portal.azure.com/) med ett administratörskonto för Azure-prenumeration.
2. I sökrutan överst på sidan, anger du ”machine learning Studio-arbetsytor” och välj sedan **Machine Learning Studio-arbetsytor**.
3. Klicka på arbetsytan som du vill hantera.

Förutom standard resource management information och alternativ som är tillgängliga kan du:

- Visa **egenskaper** – den här sidan visas information om arbetsytan och resursen och du kan ändra gruppen prenumeration och resursgrupp som den här arbetsytan är kopplad till.
- **Synkronisera om Lagringsnycklar** -arbetsytan har nycklar till lagringskontot. Om lagringskontot ändras nycklar så kan du klicka på **omsynkronisering av nycklar** och synkronisera nycklarna med arbetsytan.

Hantera webbtjänster som är associerade med den här Studio-arbetsyta med Machine Learning Web Services-portalen. Se [hantera en webbtjänst med hjälp av Azure Machine Learning Web Services-portalen](manage-new-webservice.md) fullständig information.

> [!NOTE]
> Om du vill distribuera och hantera nya webbtjänster måste du vara tilldelad rollen deltagare / administratör för prenumerationen där webbtjänsten har distribuerats. Om du bjuder in en annan användare till en machine learning Studio-arbetsyta måste du tilldela dem till rollen deltagare / administratör för prenumerationen innan de kan distribuera eller hantera webbtjänster. 
> 
>Läs mer på inställningen åtkomstbehörigheter [hantera åtkomst med RBAC och Azure portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Nästa steg
* Läs mer om [distribuera Machine Learning med Azure Resource Manager-mallar](deploy-with-resource-manager-template.md). 
