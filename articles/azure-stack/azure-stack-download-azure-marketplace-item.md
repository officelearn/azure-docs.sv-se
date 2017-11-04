---
title: "Hämta marketplace-objekt från Azure | Microsoft Docs"
description: "I min Azure Stack-distribution kan jag hämta marketplace-objekt från Azure."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/03/2017
ms.author: erikje
ms.openlocfilehash: 4d7c335a3c68cc9bb8cb0c823883716a3dd6620a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Hämta marketplace-objekt från Azure till Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Som du bestämmer vilket innehåll som ska ingå i din Azure-stacken marketplace bör du innehållet från Azure marketplace. Du kan hämta från en granskad lista över Azure marketplace-objekt som har förhandstestade körs på Azure-stacken. Nya objekt är ofta läggs till i listan, så se till att kontrollera för nytt innehåll.

Om du vill hämta marketplace-objekt måste du först [registrera Azure stacken med Azure](azure-stack-register.md). 

## <a name="download"></a>Ladda ned
1. Logga in på Azure Stack-administratörsportalen (https://portal.local.azurestack.external).
2. Vissa marketplace-objekt kan vara mycket stor.  Kontrollera att du har tillräckligt med utrymme på datorn genom att klicka på **Resursproviders** > **lagring**.

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

3. Klicka på **fler tjänster** > **Marketplace Management**.

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. Klicka på **Lägg till från Azure** att se en lista med objekt som är tillgängliga för hämtning. Du kan klicka på varje objekt i listan om du vill visa dess beskrivning och filstorlek.

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. Välj ett objekt i listan och klickar sedan på **hämta**. Detta startar hämta VM-avbildning för det valda objektet. Hämtningstider variera.

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. När nedladdningen är klar kan du distribuera din nya marketplace-objektet som ett Azure-stacken operator eller användare. Klicka på **+ ny**söka bland kategorier för nya marketplace-objektet och välj ett objekt.
7. Klicka på **skapa** öppna skapa upplevelsen för det nyligen hämtade objektet. Följ instruktionerna för att distribuera objektet.

## <a name="next-steps"></a>Nästa steg

[Skapa och publicera ett Marketplace-objekt](azure-stack-create-and-publish-marketplace-item.md)
