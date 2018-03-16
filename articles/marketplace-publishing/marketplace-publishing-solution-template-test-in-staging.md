---
title: "Testa lösningen mallen erbjudandet på Marketplace | Microsoft Docs"
description: "Förstå hur du testar erbjudandet lösning mall för Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: ef8f9b5e-b98c-49f3-913f-cdf772c14c12
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/04/2015
ms.author: mbaldwin
ms.openlocfilehash: e789d0996e72c935ed9d5f456f9868b73d5ef4ee
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="test-your-solution-template-offer-in-staging"></a>Testa lösningen mallen erbjudandet i Förproduktion
Mellanlagring innebär distribuera erbjudandet i ett privat ”sandbox” där du kan testa och verifiera dess funktioner innan du skickar den till produktion. Erbjudandet visas i Förproduktion precis som när en kund som har distribuerat den. Erbjudandet måste certifierade pushas till mellanlagring.

När erbjudandet mellanlagras, kan du visa och testa erbjudandet i den [Azure Portal](https://portal.azure.com/).

Följ stegen nedan för att push-erbjudandet till Förproduktion och testa den i den [Azure Portal](https://portal.azure.com/):

1. Gå till den [Publiceringsportal](https://publish.windowsazure.com) > **Lösningsmallar** fliken > erbjudandet > **publicera** > **Push till Förproduktion**.
2. Ange listan med Azure-prenumerationer som du använder för att förhandsgranska och testa erbjudandet.
3. Logga in på Azure preview portal med prenumerations-ID som du använde i föregående steg.
4. Utföra minst en gång testa i Azure preview portal på vad som anges nedan:
   * Kontrollera att marknadsföring innehåll visas korrekt i Azure Marketplace.
   * Slutpunkt till slutpunkt för distributionen av topologin.
   * Utföra prestandatester och test.
   * Se till att din topologi följer bästa praxis.

## <a name="next-steps"></a>Nästa steg
Om du är nöjd med resultaten, så kan du fortsätta till fasen slutliga erbjudande publishing **steg 4**: [distribuera erbjudandet på Marketplace](marketplace-publishing-push-to-production.md). I annat fall gör ändringar i erbjudandet och begär certifiering igen.

> [!NOTE]
> Marknadsföring innehållsändringar är certifikatutfärdare inte obligatoriskt.
> 
> 

Se [komma igång: hur du publicerar ett erbjudande på Azure Marketplace](marketplace-publishing-getting-started.md) en guide till alla utgivare aktiviteter.

