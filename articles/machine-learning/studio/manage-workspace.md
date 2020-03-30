---
title: Hantera arbetsytor
titleSuffix: ML Studio (classic) - Azure
description: Hantera åtkomst till Azure Machine Learning Studio -arbetsytor (klassiska) och distribuera och hantera API-webbtjänster för maskininlärning
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: 68c9ca4c7b5f30d6f62d333014f4098fe7826c99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217954"
---
# <a name="manage-an-azure-machine-learning-studio-classic-workspace"></a>Hantera en arbetsyta i Azure Machine Learning Studio (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> [!NOTE]
> Information om hur du hanterar webbtjänster i Machine Learning Web Services-portalen finns i [Hantera en webbtjänst med hjälp av portalen för Azure Machine Learning Web Services](manage-new-webservice.md).
> 
> 

Du kan hantera Machine Learning Studio -arbetsytor (klassiska) i Azure-portalen.



## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Så här hanterar du en studioarbetsyta (klassisk) i Azure-portalen:

1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett Azure-administratörskonto för prenumeration.
2. I sökrutan högst upp på sidan anger du "machine learning Studio (klassiska) arbetsytor" och väljer sedan **Machine Learning Studio (klassiska) arbetsytor**.
3. Klicka på den arbetsyta som du vill hantera.

Förutom den standardinformation och de tillgängliga resurshanteringsalternativen kan du:

- Visa **egenskaper** – På den här sidan visas arbetsytan och resursinformationen och du kan ändra den prenumerations- och resursgrupp som arbetsytan är kopplad till.
- **Resync Storage Keys** - Arbetsytan underhåller nycklar till lagringskontot. Om lagringskontot ändrar nycklar kan du klicka på **Synkronisera om nycklar** för att synkronisera nycklarna med arbetsytan.

Om du vill hantera de webbtjänster som är associerade med den här studioarbetsytan (klassisk) använder du portalen Machine Learning Web Services. Mer information finns [i Hantera en webbtjänst med hjälp av Azure Machine Learning Web Services-portalen.](manage-new-webservice.md)

> [!NOTE]
> Om du vill distribuera eller hantera nya webbtjänster måste du tilldelas en deltagar- eller administratörsroll på den prenumeration som webbtjänsten distribueras till. Om du bjuder in en annan användare till en arbetsyta för maskininlärningsstudio (klassisk) måste du tilldela dem till en deltagarroll eller administratörsroll i prenumerationen innan de kan distribuera eller hantera webbtjänster. 
> 
>Mer information om hur du anger åtkomstbehörigheter finns i [Hantera åtkomst med RBAC och Azure-portalen](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Nästa steg
* Läs mer om [att distribuera Maskininlärning med Azure Resource Manager-mallar](deploy-with-resource-manager-template.md). 
