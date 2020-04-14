---
title: Leverans av Kinainnehåll med Azure CDN | Microsoft-dokument
description: Lär dig mer om hur du använder CDN (Azure Content Delivery Network) för att leverera innehåll till Kina-användare.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: fa144c7ebd68e6f5dd192fca83dc6f306d7b8d63
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254112"
---
# <a name="china-content-delivery-with-azure-cdn"></a>Leverans av Kina-innehåll med Azure CDN

Azure Content Delivery Network (CDN) global kan hantera innehåll till Kina-användare med point-of-presence (POP) platser nära Kina eller någon POP som ger bästa prestanda för förfrågningar från Kina. Men om Kina är en viktig marknad för dina kunder och de behöver snabb prestanda, överväga att använda Azure CDN Kina istället.

Azure CDN China skiljer sig från Azure CDN globalt genom att det levererar innehåll från POP inuti Kina genom att samarbeta med ett antal lokala leverantörer. På grund av kinesisk efterlevnad och reglering måste du registrera en separat prenumeration för att använda Azure CDN China och dina webbplatser måste ha en ICP-licens. Portal- och API-upplevelsen för att aktivera och hantera innehållsleverans är identisk mellan Azure CDN global och Azure CDN China.

## <a name="comparison-of-azure-cdn-global-and-azure-cdn-china"></a>Jämförelse av Azure CDN global och Azure CDN China

Azure CDN global och Azure CDN China har följande funktioner:

- Azure CDN globalt:

     - Portal:https://portal.azure.com  

     - Utför innehållsleverans utanför Kina

     - Fyra prisnivåer: Microsoft standard, Verizon standard, Verizon premium och Akamai standard

     - [Dokumentation](https://docs.microsoft.com/azure/cdn/)

- Azure CDN Kina:

     - Portal:https://portal.azure.cn

     - Utför innehållsleverans inom Kina

     - Två prisnivåer: Standard och premium

     - [Dokumentation](https://docs.azure.cn/en-us/cdn/)
 

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CDN China finns i:

- [Funktioner i innehållsleveransnätverk](https://www.azure.cn/en-us/home/features/cdn/)

- [Översikt över Azure Content Delivery Network](https://docs.azure.cn/en-us/cdn/cdn-overview)

- [Använda Azure Content Delivery Network](https://docs.azure.cn/en-us/cdn/cdn-how-to-use)

- [Azure-tjänsttillgänglighet i Kina](https://docs.microsoft.com/azure/china/concepts-service-availability)



