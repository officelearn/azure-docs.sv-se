---
title: "Testa din VM-erbjudande för Marketplace | Microsoft Docs"
description: "Förstå hur du testar dina VM-avbildning för Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 7a41c3c6-625c-4478-b804-e124dee89040
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: hascipio
ms.openlocfilehash: 26f856059b381be91b9cdd1f98a11dc90813c0c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="test-your-vm-offer-for-the-azure-marketplace-in-staging"></a>Testa din VM-erbjudande för Azure Marketplace under mellanlagring
Mellanlagring innebär att distribuera din SKU i ett privat ”sandbox” där du kan testa och validera dess funktioner innan du distribuerar det till Marketplace. SKU: N visas i Förproduktion precis som när en kund som har distribuerat den. VM-avbildning måste certifierade pushas till mellanlagring.

## <a name="step-1-push-your-offer-to-staging"></a>Steg 1: Push erbjudandet till Förproduktion
1. På den **publicera** klickar du på **Push till Förproduktion**.
   
    ![Rita](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)
2. Om Publishing Portal meddelar dig om eventuella fel, rätta dem.
3. I den **vem som kan komma åt erbjudandet mellanlagrade?** dialogrutan Ange listan med Azure-prenumerationer som du använder för att förhandsgranska erbjudandet i den [Azure preview portal](https://portal.azure.com).
   
   > [!NOTE]
   > Om virtuella datorer och lösningsmallar du **inte** godkända prenumerationer av typen CSP DreamSpark eller Azure i Open.
   > 
   > 

    > Om virtuella datorer, när du klickar på knappen **PUSH till FÖRPRODUKTION**, utför följande steg bakom scenen. Du kommer att kunna visa förloppet för varje steg under fliken Publicera i publicering portal. Du måste kontrollera den här sidan med regelbundna intervall (tills status visar MELLANLAGRAD) alla felinformation som behöver korrigering från din slutpunkt.

    > - Först går din fristående begäran till certifieringsteam som verifiera den virtuella hårddisken. Men om din begäran har stött på marknadsföring bara ändra, hoppas certifikatutfärdare steget över.
    > - När certifikatutfärdaren är klar, starta replikering av erbjudandet i de Azure datacenter. Normalt tar 24-48hours att slutföra replikeringen men kan ta upp till en vecka beroende på storleken på den virtuella hårddisken. Om din begäran har stött på marknadsföring bara ändra, sedan är replikeringen dock snabbare.
    > - När replikeringen är klar, sedan erbjudandet ska finnas i den [Azure-portalen](http:/portal.azure.com). AT som tid status blir MELLANLAGRAD i publicering portal. En mellanlagrad erbjudande visas i den [Azure-portalen](http:/portal.azure.com) endast med e-post-ID som är associerade med prenumerationen som mellanlagras erbjudandet.

1. Logga in på den [Azure preview portal](https://portal.azure.com) genom att använda en Azure-prenumerationer som anges i föregående steg.
2. Hitta erbjudandet och verifiera din VM avbildningen punkter:
   
   * Kontrollera att marknadsföring innehåll visas korrekt på Marketplace.
   * Slutpunkt till slutpunkt för distributionen av VM-avbildning.
     
      ![img kartan portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

> [!IMPORTANT]
> Erbjudandet finns kvar i Förproduktion tills du meddela Microsoft via Publishing Portal [**publicera** fliken > Klicka på knappen **”begär godkännande att Push till produktion”**] att du är redo att skicka till produktion. Detta är en perfekt ha alla medlemmar av ditt team kontroll över allt inför erbjudandet ska visas.
> 
> Fristående plattformen är avsedd för testning erbjudandet i ett förhandsgranskningsläge för av utgivaren. Vi avråder med hjälp av den här platofrm för mobilkommunikationssystemet ändamål.
> 
> 

## <a name="next-steps"></a>Nästa steg
Nu när erbjudandet ”mellanlagras” och du har testat dess funktioner och marknadsföring innehåll, kan du fortsätta till publishing slutfasen **steg 4**: [distribuera erbjudandet på Marketplace](marketplace-publishing-push-to-production.md).

## <a name="see-also"></a>Se även
* [Komma igång: hur du publicerar ett erbjudande på Azure Marketplace](marketplace-publishing-getting-started.md)

