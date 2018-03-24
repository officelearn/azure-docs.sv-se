---
title: Hantera en arbetsyta i Machine Learning | Microsoft Docs
description: Hantera åtkomst till Azure Machine Learning arbetsytor och distribuera och hantera ml – API-tjänster
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: daf3d413-7a77-4beb-9a7a-6b4bdf717719
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 4d971d75424f85bb07c0be779b9dfedb1ef41c1e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="manage-an-azure-machine-learning-workspace"></a>Hantera en Azure Machine Learning-arbetsyta

> [!NOTE]
> Information om hur du hanterar webbtjänster i Machine Learning-webbtjänster portal finns [hantera en webbtjänst med hjälp av Azure Machine Learning-webbtjänster portal](manage-new-webservice.md).
> 
> 

Du kan hantera Machine Learning arbetsytor i Azure-portalen.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Hantera en arbetsyta i Azure-portalen:

1. Logga in på den [Azure-portalen](https://portal.azure.com/) med ett administratörskonto för Azure-prenumeration.
2. Ange ”dator learning arbetsytor” i sökrutan överst på sidan och välj sedan **Machine Learning arbetsytor**.
3. Klicka på arbetsytan som du vill hantera.

Förutom standard resursinformation för hantering och alternativ som är tillgängliga kan du:

- Visa **egenskaper** – den här sidan visar arbetsytan och resursen och du kan ändra gruppen prenumeration och resurs som den här arbetsytan är kopplad till.
- **Omsynkronisering Lagringsnycklar** -arbetsytan underhåller nycklar till lagringskontot. Om lagringskontot ändras nycklar och du kan klicka på **omsynkronisering nycklar** att synkronisera nycklar med arbetsytan.

Använd portalen Machine Learning-webbtjänster för att hantera de webbtjänster som är associerade med den här arbetsytan. Se [hantera en webbtjänst med hjälp av Azure Machine Learning-webbtjänster portal](manage-new-webservice.md) fullständig information.

> [!NOTE]
> Om du vill distribuera eller hantera nya webbtjänster måste du tilldelas rollen deltagare eller administratör på den prenumeration som webbtjänsten har distribuerats. Om du bjuda in en annan användare till en machine learning-arbetsytan måste du tilldela dem en roll för deltagare eller administratör för prenumerationen innan de kan distribuera eller hantera webbtjänster. 
> 
>Mer information om inställningen åtkomstbehörighet finns [visa åtkomst-tilldelningar för användare och grupper i Azure portal](../../active-directory/role-based-access-control-manage-assignments.md).

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [distribuera Machine Learning med Azure Resource Manager-mallar](deploy-with-resource-manager-template.md). 