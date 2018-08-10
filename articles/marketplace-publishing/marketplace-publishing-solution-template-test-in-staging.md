---
title: Testa erbjudandet lösning mall för Marketplace | Microsoft Docs
description: Förstå hur du testar erbjudandet lösning mall för Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: ef8f9b5e-b98c-49f3-913f-cdf772c14c12
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/04/2015
ms.author: hascipio; v-divte
ms.openlocfilehash: da1fc4713fd1d832c7ba91226f72cbef63b241bc
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714833"
---
# <a name="test-your-solution-template-offer-in-staging"></a>Testa ditt lösning mall erbjudande i mellanlagringen
Mellanlagring innebär att du distribuerar ditt erbjudande i ett privat ”sandlåda” där du kan testa och verifiera dess funktionalitet innan du skickar det till produktion. Erbjudandet visas i mellanlagringen precis som en kund som har distribuerat den. Erbjudandet måste certifieras för att överföras till mellanlagring.

När erbjudandet mellanlagras, du kan visa och testa erbjudandet i den [Azure-portalen](https://portal.azure.com/).

Följ stegen nedan för att skicka erbjudandet till mellanlagring och testa den i den [Azure-portalen](https://portal.azure.com/):

1. Gå till den [Publiceringsportalen](https://publish.windowsazure.com) > **Lösningsmallar** fliken > ditt erbjudande > **publicera** > **Push för mellanlagring av**.
2. Ge listan över Azure-prenumerationer som du använder för att förhandsgranska och testa ditt erbjudande.
3. Logga in på Azure preview portal med hjälp av prenumerations-ID som du använde i föregående steg.
4. Utföra minst en gång testning i Azure preview-portalen på vad som anges nedan:
   * Se till att marknadsföring innehåll visas korrekt i Azure Marketplace.
   * Slutpunkt till slutpunkt distribution av topologin.
   * Utföra prestandatestning och test.
   * Se till att topologin följer bästa praxis.

## <a name="next-steps"></a>Nästa steg
Om du är nöjd med resultaten så du kan fortsätta att publicera fas sista erbjudandet **steg 4**: [distribuera ditt erbjudande i Marketplace](marketplace-publishing-push-to-production.md). I annat fall gör ändringar i ditt erbjudande och begär certifiering igen.

> [!NOTE]
> För marknadsföring innehållsändringar krävs certifiering inte.
> 
> 

Se [komma igång: hur du publicerar ett erbjudande på Azure Marketplace](marketplace-publishing-getting-started.md) en guide till aktiviteterna till utgivaren.

