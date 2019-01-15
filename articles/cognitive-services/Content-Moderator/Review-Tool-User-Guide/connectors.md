---
title: Ansluta till andra tjänster vid kontrollera innehåll – Content Moderator
titlesuffix: Azure Cognitive Services
description: 'Lär dig mer om att få åtkomst till API: er för Content Moderator-arbetsflöden med hjälp av kopplingar.'
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 99d8b3603278a9c6c432ca32a1d85e9abe34e1da
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265591"
---
# <a name="connect-to-other-cognitive-services"></a>Ansluta till andra cognitive services

Azure Content Moderator-arbetsflöden kan använda API: er förutom Content Moderator API: er. Du har åtkomst till API: er med hjälp av en koppling i Content Moderator. Anslutningen innehåller en länk till den andra API: er.

Content Moderator innehåller dessa standard-anslutningar:

* Känslo-API
* Ansikts-API
* PhotoDNA Cloud Service

![Content Moderator tillgängliga anslutningar](images/connectors-1.png)

## <a name="verify-your-credentials"></a>Ange autentiseringsuppgifterna 

Innan du definierar ett arbetsflöde kan du kontrollera att du har giltiga autentiseringsuppgifter för connector API: er som du vill använda:

1.  Granskningsverktyget instrumentpanelen, Välj **inställningar** > **Anslutningsappar**.

  ![Content Moderator väljer kopplingar](images/connectors-2.png)

2.  Välj den **redigera** symbolen bredvid den koppling som du vill verifiera autentiseringsuppgifter för.

  ![Välj Redigera symbolen för Content Moderator](images/connectors-3.png)

3.  Prenumerationsnyckeln visas. Om du gör alla ändringar, Välj **spara** när du är klar.

  ![Content Moderator redigera sidan](images/connectors-4-1.png)
 
## <a name="add-a-connector"></a>Lägg till en koppling

1.  Innan du lägger till en anslutning, måste en prenumerationsnyckel. Granskningsverktyget instrumentpanelen, Välj **inställningar** > **autentiseringsuppgifter**. Markera och kopiera värdet som tillhör den **Ocp-Admin-Subscription-Key** box.

2.  Välj **Anslutningsappar**. Välj en av de tillgängliga kopplingar som visas på granskningsverktyget instrumentpanelen. Välj **Connect**. 

  ![Content Moderator Lägg till Anslutningsapp-sidan](images/connectors-5.png)

3.  I den **Ocp-Admin-Subscription-Key** rutan, klistra in den nyckel som du kopierade. Välj sedan **Spara**.

## <a name="next-steps"></a>Nästa steg

* Lär dig att använda anslutningsappar för [definiera anpassade arbetsflöden](workflows.md).
