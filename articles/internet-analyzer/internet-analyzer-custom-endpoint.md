---
title: Skapa en anpassad slutpunkt | Microsoft-dokument
description: I den här artikeln får du lära dig hur du konfigurerar en anpassad slutpunkt för att mäta med internetanalysresursen.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 6a781e767393f35a1ec22f195ba787e3018d8d1e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76713101"
---
# <a name="measure-custom-endpoints-to-evaluate-in-your-internet-analyzer-tests"></a>Mäta anpassade slutpunkter att utvärdera i dina Internet Analyzer-tester 

Den här artikeln visar hur du ställer in en anpassad slutpunkt som ska mätas som en del av dina Internet Analyzer-tester. Anpassade slutpunkter hjälper till att utvärdera lokala arbetsbelastningar, arbetsbelastningar som körs på andra molnleverantörer och anpassade Azure-konfigurationer.  Det är möjligt att jämföra två anpassade slutpunkter i ett test om en slutpunkt är en Azure-resurs. Mer information om Internet Analyzer finns i [översikten](internet-analyzer-overview.md). 

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du konfigurerar en Internet Analyzer-resurs och väljer alternativet "Anpassad slutpunkt". Internet Analyzer förutsätter att din anpassade slutpunkt är tillgänglig för Internet. Mer information finns i [Skapa en Internet Analyzer-resurs](internet-analyzer-create-test-portal.md).


## <a name="create-custom-endpoint"></a>Skapa anpassad slutpunkt

1. Ladda ner en transparent en-pixel testbild [här](https://fpc.msedge.net/apc/trans.gif). Denna en-pixel bild är den tillgång som klienten JavaScript kommer att hämta för att mäta prestanda.
2. Distribuera testavbildningen i en allmänt tillgänglig sökväg i ditt anpassade webbprogram. Sökvägen ska fungera via HTTPS. 
3. Kopiera den fullständiga anpassade slutpunkts-URL:en (t.ex. https://contoso.com/test/trans.gif) till det anpassade slutpunktsfältet när testet skapas.

## <a name="next-steps"></a>Nästa steg

Läs [vanliga frågor om Internet Analyzer](internet-analyzer-faq.md)

