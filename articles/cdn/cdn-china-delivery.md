---
title: Kina leverans av innehåll med Azure CDN | Microsoft Docs
description: Lär dig mer om att använda Azure CDN Content Delivery Network () för att leverera innehåll till användare i Kina.
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
ms.openlocfilehash: 12a29e5af287c5cc68c3bd9d7069534ff16c496f
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45573237"
---
# <a name="china-content-delivery-with-azure-cdn"></a>Kina leverans av innehåll med Azure CDN

Azure CDN Content Delivery Network () globala kan fungera innehåll till användare i Kina med point of presence (POP) platser nära Kina eller någon POP som ger bästa prestanda till begäranden från Kina. Om Kina har en betydande marknad för dina kunder och de behöver snabba prestanda, tänka på att använda Azure CDN Kina i stället.

Azure CDN Kina skiljer sig från Azure CDN globala eftersom den levererar innehåll från POP: erna i Kina seglen med ett antal lokala providers. På grund av kinesiska efterlevnad och förordning, måste du registrera en separat prenumeration om du vill använda Azure CDN Kina och dina webbplatser måste ha en ICP-licens. Portal och API-upplevelse för att aktivera och hantera innehåll leveransen är identiskt för Azure CDN globala och Azure CDN i Kina.

## <a name="comparison-of-azure-cdn-global-and-azure-cdn-china"></a>Jämförelse av Azure CDN globala och Azure CDN Kina

Azure CDN globala och Azure CDN Kina har följande funktioner:

- Global Azure CDN:

     - Portal: https://portal.azure.com  

     - Utför innehållsleverans utanför Kina

     - Fyra prisnivåer: Microsoft standard, Verizon standard, premium Verizon och Akamai-standarden

     - [Dokumentation](https://docs.microsoft.com/azure/cdn/)

- Azure CDN i Kina:

     - Portal: https://portal.azure.cn

     - Utför leverans av innehåll i Kina

     - Två prisnivåer: Standard och premium

     - [Dokumentation](https://docs.azure.cn/en-us/cdn/)
 

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CDN i Kina finns:

- [Content Delivery Network-funktioner](https://www.azure.cn/en-us/home/features/cdn/)

- [Översikt över Azure Content Delivery Network](https://docs.azure.cn/en-us/cdn/cdn-overview)

- [Använda Azure Content Delivery Network](https://docs.azure.cn/en-us/cdn/cdn-how-to-use)

- [Tillgänglighet för Azure-tjänsten i Kina](https://docs.microsoft.com/azure/china/china-get-started-service-availability)



