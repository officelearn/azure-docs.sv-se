---
title: Hantera en arbetsyta för Machine Learning Studio | Microsoft Docs
description: Hantera åtkomst till Azure Machine Learning-arbetsytor och distribuera och hantera ML API-webbtjänster
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: daf3d413-7a77-4beb-9a7a-6b4bdf717719
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 8c5dfd82a7bf0d1985869c8de4e3b313ef885947
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270781"
---
# <a name="manage-an-azure-machine-learning-studio-workspace"></a>Hantera en Azure Machine Learning Studio-arbetsyta

> [!NOTE]
> Information om hur du hanterar webbtjänster i Machine Learning Web Services-portalen finns i [hantera en webbtjänst med hjälp av Azure Machine Learning Web Services-portalen](manage-new-webservice.md).
> 
> 

Du kan hantera Machine Learning-arbetsytor i Azure-portalen.



## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Hantera en arbetsyta i Azure portal:

1. Logga in på den [Azure-portalen](https://portal.azure.com/) med ett administratörskonto för Azure-prenumeration.
2. Ange ”machine learning-arbetsytor” i sökrutan överst på sidan och välj sedan **Machine Learning-arbetsytor**.
3. Klicka på arbetsytan som du vill hantera.

Förutom standard resource management information och alternativ som är tillgängliga kan du:

- Visa **egenskaper** – den här sidan visas information om arbetsytan och resursen och du kan ändra gruppen prenumeration och resursgrupp som den här arbetsytan är kopplad till.
- **Synkronisera om Lagringsnycklar** -arbetsytan har nycklar till lagringskontot. Om lagringskontot ändras nycklar så kan du klicka på **omsynkronisering av nycklar** och synkronisera nycklarna med arbetsytan.

Hantera webbtjänster som är associerade med den här arbetsytan med Machine Learning Web Services-portalen. Se [hantera en webbtjänst med hjälp av Azure Machine Learning Web Services-portalen](manage-new-webservice.md) fullständig information.

> [!NOTE]
> Om du vill distribuera och hantera nya webbtjänster måste du vara tilldelad rollen deltagare / administratör för prenumerationen där webbtjänsten har distribuerats. Om du bjuder in en annan användare till en machine learning-arbetsytan måste du tilldela dem till rollen deltagare / administratör för prenumerationen innan de kan distribuera eller hantera webbtjänster. 
> 
>Läs mer på inställningen åtkomstbehörigheter [hantera åtkomst med RBAC och Azure portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Nästa steg
* Läs mer om [distribuera Machine Learning med Azure Resource Manager-mallar](deploy-with-resource-manager-template.md). 