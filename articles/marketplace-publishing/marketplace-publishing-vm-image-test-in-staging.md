---
title: Testa ditt erbjudande för virtuell dator för Marketplace | Microsoft Docs
description: Förstå hur du testar din avbildning för Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: 7a41c3c6-625c-4478-b804-e124dee89040
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: hascipio
ROBOTS: NOINDEX
ms.openlocfilehash: b90353dbbc5d019897735cfc05caa3ee094dfedc
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54078821"
---
# <a name="test-your-vm-offer-for-the-azure-marketplace-in-staging"></a>Testa ditt erbjudande för virtuell dator för Azure Marketplace i Förproduktion
Mellanlagring innebär att du distribuerar din SKU i en privat ”sandlåda” där du kan testa och verifiera dess funktionalitet innan du distribuerar den till Marketplace. SKU: N visas i mellanlagringen precis som en kund som har distribuerat den. Du måste certifiera dina VM-avbildning innan den skickas till mellanlagringen.

## <a name="step-1-push-your-offer-to-staging"></a>Steg 1: Skicka erbjudandet till mellanlagring
1. På den **publicera** fliken **väljer att mellanlagra**.
   
    ![Rita](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)
2. Om Publisher-portalen anger du eventuella fel kan du korrigera dem.
3. I den **vem som kan komma åt erbjudandet mellanlagrad?** dialogrutan anger listan över Azure-prenumerationer som du använder du förhandsgranskar ditt erbjudande i den [Azure-portalen](https://portal.azure.com).
   
   > [!NOTE]
   > När det gäller virtuella datorer och lösningsmallar **inte** listan över godkända prenumerationer av typen CSP, DreamSpark eller Azure i Open.
   > 
   > 
   >
   > När det gäller virtuella datorer, när du klickar på knappen **mellanlagring PUSH TO STAGING**, följande steg utförs bakom scenen. Du kommer att kunna visa förloppet för varje steg under fliken Publicera i publiceringen portal. Du måste markera den här sidan med regelbundna intervall (tills MELLANLAGRA visas status) för fel som behöver korrigering från din sida.

   > - Först går mellanlagrings-begäran till certifieringsteam som verifiera den virtuella hårddisken. Men om din begäran har endast marknadsföring ändringen, hoppas certifieringssteget över.
   > - När certifikatutfärdaren är klar, startar replikering av erbjudandet i alla Azure datacenter. Vanligtvis tar 24-48hours att slutföra replikeringen men kan ta upp till en vecka beroende på storleken på den virtuella hårddisken. Om din begäran har endast marknadsföring ändringen, sedan är replikeringen dock snabbare.
   > - När replikeringen är klar, sedan erbjudandet blir tillgängliga i den [Azure-portalen](http:/portal.azure.com). AT som tid status bli MELLANLAGRAD i publiceringen portal. Erbjudande om en mellanlagrad syns i den [Azure-portalen](http:/portal.azure.com) endast med hjälp av e-post-ID som är associerade med prenumerationen som erbjudandet mellanlagras.

1. Logga in på den [Azure-portalen](https://portal.azure.com) med någon av de Azure-prenumerationerna som visas i föregående steg.
2. Hitta ditt erbjudande och verifiera dina poäng för VM-avbildning:
   
   * Se till att marknadsföring innehåll visas korrekt i Marketplace.
   * Slutpunkt till slutpunkt distribution av avbildningen.
     
      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

> [!IMPORTANT]
> Ditt erbjudande blir kvar i mellanlagringen tills du meddelar Microsoft via Publiceringsportalen [**publicera** fliken > Klicka på knappen **”begär godkännande att Push till produktion”**] när du är redo att skicka till produktion. Det här är en perfekt tid att ha alla medlemmar i teamet Kontrollera förberedelse för ditt erbjudande ska visas.
> 
> Mellanlagrings-plattformen är utformad för att testa erbjudandet i ett läge för förhandsversionen av utgivaren. Vi avråder med den här plattformsöverskridande för mobilkommunikationssystemet syften.
> 
> 

## <a name="next-steps"></a>Nästa steg
Nu när ditt erbjudande ”mellanlagras” och du har testat dess funktionalitet och marknadsföring innehåll, som du kan fortsätta att publicera slutfasen **steg 4**: [Distribuera ditt erbjudande i Marketplace](marketplace-publishing-push-to-production.md).

## <a name="see-also"></a>Se också
* [Komma igång: Hur du publicerar ett erbjudande på Azure Marketplace](marketplace-publishing-getting-started.md)

