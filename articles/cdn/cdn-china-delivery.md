---
title: Kina leverans av innehåll med Azure CDN | Microsoft Docs
description: Lär dig mer om hur du använder Azure innehåll innehållsleveransnätverk (CDN) för att leverera innehåll till användare i Kina.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: 59788f301bb501103babd55a2ac37102932f4dcf
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="china-content-delivery-with-azure-cdn"></a>Kina leverans av innehåll med Azure CDN

Azure innehåll innehållsleveransnätverk (CDN) globala kan hantera innehåll till användare i Kina med punkt förekomst (POP) platser nära Kina eller någon POP som ger bästa prestanda på begäranden med ursprung i Kina. Om Kina är en betydande marknad för dina kunder och de behöver snabb prestanda, bör du att använda Azure CDN Kina i stället.

Azure CDN Kina skiljer sig från Azure CDN globala att den levererar innehåll från POP i Kina via en med ett antal lokala providers. På grund av kinesiska efterlevnad och förordning, måste du registrera en separat prenumeration om du vill använda Azure CDN Kina och dina webbplatser måste ha en ICP-licens. Portal och API-funktioner för att aktivera och hantera innehåll leveransen är identiska mellan Azure CDN globala och Azure CDN Kina.

## <a name="comparison-of-azure-cdn-global-and-azure-cdn-china"></a>Jämförelse mellan Azure CDN globala och Azure CDN Kina

Azure CDN globala och Azure CDN Kina har följande funktioner:

- Azure CDN globala:

     - Portal: https://portal.azure.com  

     - Utför leverans av innehåll utanför Kina

     - Fyra prisnivåer: Microsoft standard, Verizon standard, premium Verizon och Akamai standard

     - [Dokumentation](https://docs.microsoft.com/en-us/azure/cdn/)

- Azure CDN Kina:

     - Portal: https://portal.azure.cn

     - Utför leverans av innehåll i Kina

     - Två prisnivåer: Standard och premium

     - [Dokumentation](https://docs.azure.cn/en-us/cdn/)
 

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CDN Kina finns:

- [Content Delivery Network-funktioner](https://www.azure.cn/en-us/home/features/cdn/)

- [Översikt över Azure Content Delivery Network](https://docs.azure.cn/en-us/cdn/cdn-overview)

- [Använd Azure Content Delivery Network](https://docs.azure.cn/en-us/cdn/cdn-how-to-use)

- [Azure-tjänstetillgänglighet i Kina](https://docs.microsoft.com/en-us/azure/china/china-get-started-service-availability)



