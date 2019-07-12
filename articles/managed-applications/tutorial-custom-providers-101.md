---
title: Skapa anpassade åtgärder och resurser i Azure
description: Den här självstudien får du gå igenom hur du skapar anpassade åtgärder och resurser i Azure Resource Manager och hur du integrerar dem i anpassade arbetsflöden för Azure Resource Manager-mallar, Azure CLI, Azure Policy och aktivitetsloggen.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 4bbfcf070611e3df5c0fe47070f2ab6961111e07
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800045"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Skapa anpassade åtgärder och resurser i Azure

Anpassade providers kan du anpassa arbetsflöden på Azure. En anpassad provider är ett avtal mellan Azure och en `endpoint`. Det gör att lägga till nya anpassade API: er i Azure Resource Manager för att aktivera ny distribution och hanteringsfunktioner. Den här självstudien går igenom ett enkelt exempel på hur du lägger till nya åtgärder och resurser till Azure och hur du integrerar dem.

Den här självstudien är uppdelad i följande steg:

- Konfigurera Azure Functions för Azure anpassade Providers
- Redigering av en RESTful-slutpunkt för anpassade providers
- Skapa och använda den anpassade providern

## <a name="setup-azure-functions-for-azure-custom-providers"></a>Konfigurera Azure Functions för Azure anpassade Providers

Den här delen av självstudiekursen hamnar i information om hur du ställer in en Azure-funktion för att arbeta med anpassade providers. Anpassade providers kan arbeta med en offentlig URL.

- [Konfigurera Azure Functions för Azure anpassade Providers](./tutorial-custom-providers-function-setup.md)

## <a name="authoring-a-restful-endpoint-for-custom-providers"></a>Redigering av en RESTful-slutpunkt för anpassade providers

Den här delen av självstudiekursen hamnar i detalj hur redigering av en RESTful-slutpunkt för anpassade providers.

- [Redigering av en RESTful-slutpunkt för anpassade providers](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-and-utilizing-the-custom-provider"></a>Skapa och använda den anpassade providern

Den här delen av självstudiekursen hamnar i information om hur du skapar en anpassad provider och använder anpassade åtgärder och resurser.

- [Skapa och använda en anpassad Provider för Azure](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har vi lärt oss om anpassade providers och hur du skapar en sådan. Att Fortsätt med nästa steg i självstudien:

- [Självstudier: Konfigurera Azure Functions för Azure anpassade Providers](./tutorial-custom-providers-function-setup.md)

Om du letar efter referenser eller en Snabbstart, är här några användbara länkar:

- [Snabbstart: Skapa anpassade Azure-Resursprovidern och distribuera anpassade resurser](./create-custom-provider.md)
- [Anvisningar: Att lägga till anpassade åtgärder i Azure REST-API](./custom-providers-action-endpoint-how-to.md)
- [Anvisningar: Att lägga till anpassade resurser i Azure REST-API](./custom-providers-resources-endpoint-how-to.md)
