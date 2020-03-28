---
title: Skapa anpassade åtgärder och resurser
description: Den här självstudien går igenom hur du skapar anpassade åtgärder och resurser i Azure Resource Manager. Den visar också hur anpassade arbetsflöden samverkar med Azure Resource Manager-mallar, Azure CLI, Azure Policy och Azure Activity Log.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 45f18727b53b802ba746da41b47fe955543ed9d0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75649899"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Skapa anpassade åtgärder och resurser i Azure

En anpassad provider är ett kontrakt mellan Azure och en slutpunkt. Med anpassade leverantörer kan du ändra arbetsflöden i Azure genom att lägga till nya API:er i Azure Resource Manager. Med dessa anpassade API:er kan Resource Manager använda nya distributions- och hanteringsfunktioner.

Den här självstudien går igenom ett enkelt exempel på hur du lägger till nya åtgärder och resurser i Azure och hur du integrerar dem.

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>Konfigurera Azure-funktioner för Azure-anpassade leverantörer

I en av de här självstudien beskrivs hur du konfigurerar en Azure-funktionsapp för att arbeta med anpassade leverantörer:

- [Konfigurera Azure-funktioner för Azure-anpassade leverantörer](./tutorial-custom-providers-function-setup.md)

Anpassade leverantörer kan arbeta med alla offentliga webbadresser.

## <a name="author-a-restful-endpoint-for-custom-providers"></a>Skapa en RESTful-slutpunkt för anpassade leverantörer

I del två av den här självstudien beskrivs hur du skapar en RESTful-slutpunkt för anpassade leverantörer:

- [Skapa en RESTful-slutpunkt för anpassade leverantörer](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>Skapa och använda en anpassad leverantör

I del tre av den här självstudien beskrivs hur du skapar en anpassad provider och använder dess anpassade åtgärder och resurser:

- [Skapa och använda en anpassad leverantör](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig om anpassade leverantörer och hur man bygger en. Information om hur du fortsätter till nästa självstudiekurs finns i [Självstudiekurs: Konfigurera Azure-funktioner för Azure Custom Providers](./tutorial-custom-providers-function-setup.md).

Om du letar efter referenser eller en snabbstart följer här några användbara länkar:

- [Snabbstart: Skapa en anpassad Azure-resursleverantör och distribuera anpassade resurser](./create-custom-provider.md)
- [Så här lägger du till anpassade åtgärder i Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Så här lägger du till anpassade resurser i Azure REST API](./custom-providers-resources-endpoint-how-to.md)
