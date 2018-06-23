---
title: 'Använda kopplingar i Azure Content kontrollant andra API: er | Microsoft Docs'
description: 'Lär dig hur du kommer åt andra API: er för innehåll kontrollant-arbetsflöden med hjälp av kopplingar.'
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/22/2017
ms.author: sajagtap
ms.openlocfilehash: d8114457e7079ca8772cab830bd011dcddf372f5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351603"
---
# <a name="connectors"></a>Anslutningar

Azure innehåll kontrollant arbetsflöden kan använda andra API: er, utöver innehåll kontrollant API: er. Du har åtkomst till andra API: er med hjälp av en koppling i innehåll kontrollanten. Kopplingen innehåller en länk till den andra API: er.

Innehåll kontrollant innehåller anslutningarna standard:

* Känslo-API
* Ansikts-API
* PhotoDNA Molntjänsten

![Innehåll kontrollant tillgängliga kopplingar](images/connectors-1.png)

## <a name="verify-your-credentials"></a>Verifiera dina autentiseringsuppgifter 

Innan du definierar ett arbetsflöde, se till att du har giltiga autentiseringsuppgifter för kopplingen API som du vill använda:

1.  Välj på verktyget granska instrumentpanelen **inställningar** > **kopplingar**.

  ![Innehåll kontrollant väljer kopplingar](images/connectors-2.png)

2.  Välj den **redigera** symbolen bredvid den koppling som du vill kontrollera autentiseringsuppgifterna för.

  ![Innehåll kontrollant Välj symbolen redigera](images/connectors-3.png)

3.  Nyckeln prenumeration visas. Om du gör några ändringar, Välj **spara** när du är klar.

  ![Innehållssida kontrollant redigera kopplingar](images/connectors-4-1.png)
 
## <a name="add-a-connector"></a>Lägg till en koppling

1.  Innan du lägger till en koppling behöver du en prenumeration för. Välj på verktyget granska instrumentpanelen **inställningar** > **autentiseringsuppgifter**. Markera och kopiera det värde som är i den **Ocp-Admin-prenumeration-nyckeln** rutan.

2.  Välj **kopplingar**. Välj en av de tillgängliga kopplingar som visas på verktyget granska instrumentpanelen. Markera **Anslut**. 

  ![Innehållssida kontrollanten lägger du till koppling](images/connectors-5.png)

3.  I den **Ocp-Admin-prenumeration-nyckeln** klistra in den nyckel som du kopierade. Välj sedan **Spara**.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du använder kopplingar till [definiera anpassade arbetsflöden](workflows.md).
