---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 12/01/2020
ms.author: larryfr
ms.openlocfilehash: 6e9f349f2fd90fdb12d8d26ca904e504b75d9db2
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445181"
---
* När du skapar en ny arbets yta kan du antingen skapa tjänster som behövs av arbets ytan automatiskt eller använda befintliga tjänster. Om du vill använda __befintliga tjänster från en annan Azure-prenumeration__ än arbets ytan måste du registrera Azure Machine Learning namn området i prenumerationen som innehåller tjänsterna. Om du till exempel skapar en arbets yta i prenumeration A som använder ett lagrings konto från prenumeration B måste Azure Machine Learning namn området registreras i prenumeration B innan du kan använda lagrings kontot med arbets ytan.

    Resurs leverantören för Azure Machine Learning är __Microsoft. MachineLearningService__. Information om hur du kan se om den är registrerad och hur du registrerar den finns i artikeln [Azure-resurs leverantörer och typer](../articles/azure-resource-manager/management/resource-providers-and-types.md)  .

    > [!IMPORTANT]
    > Detta gäller endast för resurser som anges när arbets ytan skapas. Azure Storage konton, Azure Container register, Azure Key Vault och Application Insights.