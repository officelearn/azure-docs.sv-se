---
title: Skapa en anpassad slut punkt | Microsoft Docs
description: I den här artikeln lär du dig hur du konfigurerar en anpassad slut punkt för att mäta med din Internet Analyzer-resurs.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 44d570746a54154a2f8d3c7a200c761a59b7422b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501927"
---
# <a name="measure-custom-endpoints-to-evaluate-in-your-internet-analyzer-tests"></a>Mät anpassade slut punkter som ska utvärderas i dina Internet Analyzer-tester 

Den här artikeln visar hur du konfigurerar en anpassad slut punkt för att mäta som en del av dina Internet Analyzer-tester. Anpassade slut punkter hjälper till att utvärdera lokala arbets belastningar, arbets belastningar som körs på andra moln leverantörer och anpassade Azure-konfigurationer.  Det går att jämföra två anpassade slut punkter i ett test om en slut punkt är en Azure-resurs. Mer information om Internet Analyzer finns i [översikten](internet-analyzer-overview.md). 

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Innan du börjar

Se till att konfigurera en Internet Analyzer-resurs och välj alternativet Anpassad slut punkt. Internet Analyzer förutsätter att din anpassade slut punkt är tillgänglig för Internet. Mer information finns i [skapa en Internet Analyzer-resurs](internet-analyzer-create-test-portal.md).


## <a name="create-custom-endpoint"></a>Skapa anpassad slut punkt

1. Hämta en genomskinlig test bild för en bild punkt [här](https://fpc.msedge.net/apc/trans.gif). Den här bilden med en bild punkt är den till gång som klientens java script kommer att hämta för att mäta prestanda.
2. I ditt anpassade webb program distribuerar du test avbildningen på en offentlig tillgänglig sökväg. Sökvägen bör fungera över HTTPS. 
3. Kopiera den fullständiga anpassade slut punkts-URL: en (t. ex. https://contoso.com/test/trans.gif) till det anpassade slut punkts fältet vid skapandet av testet.

## <a name="next-steps"></a>Nästa steg

Läs [vanliga frågor och svar om Internet Analyzer](internet-analyzer-faq.md)

