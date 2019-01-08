---
title: Testa erbjudandet lösning mall för Marketplace | Microsoft Docs
description: Förstå hur du testar erbjudandet lösning mall för Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: v-miclar
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
ROBOTS: NOINDEX
ms.openlocfilehash: 48c2befbf42f90c31d5631448e9d1276b23b965f
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54078838"
---
# <a name="test-your-solution-template-offer-in-staging"></a>Testa ditt lösning mall erbjudande i mellanlagringen
Mellanlagring innebär att du distribuerar ditt erbjudande i ett privat ”sandlåda” där du kan testa och verifiera dess funktionalitet innan du skickar det till produktion. Erbjudandet visas i mellanlagringen precis som en kund som har distribuerat den. Ett erbjudande måste certifieras innan som skickas till mellanlagring.

När erbjudandet mellanlagras, du kan visa och testa erbjudandet i den [Azure-portalen](https://portal.azure.com/).

Följ stegen nedan för att skicka erbjudandet till mellanlagring och testa den i den [Azure-portalen](https://portal.azure.com/):

1. Gå till den [Publiceringsportalen](https://publish.windowsazure.com) > **Lösningsmallar** fliken > ditt erbjudande > **publicera** > **Push för mellanlagring av**.
2. Ge listan över Azure-prenumerationer som du använder för att förhandsgranska och testa ditt erbjudande.
3. Logga in på Azure Portal med hjälp av prenumerations-ID som du använde i föregående steg.
4. Utföra minst en gång testning i Azure portal på vad som anges nedan:
   * Se till att marknadsföring innehåll visas korrekt i Azure Marketplace.
   * Slutpunkt till slutpunkt distribution av topologin.
   * Utföra prestandatestning och test.
   * Se till att topologin följer bästa praxis.

## <a name="next-steps"></a>Nästa steg
Om du är nöjd med resultaten så du kan fortsätta att publicera fas sista erbjudandet **steg 4**:  [Distribuera ditt erbjudande i Marketplace](marketplace-publishing-push-to-production.md). I annat fall gör ändringar i ditt erbjudande och begär certifiering igen.

> [!NOTE]
> För marknadsföring innehållsändringar krävs certifiering inte.
> 
> 

Se [komma igång: Hur du publicerar ett erbjudande på Azure Marketplace](marketplace-publishing-getting-started.md) en guide till aktiviteterna till utgivaren.

