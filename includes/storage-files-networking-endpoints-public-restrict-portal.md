---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6c594dbab51c46c382c21b4d87595cd7322f6036
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84465131"
---
Navigera till det lagrings konto för vilket du vill begränsa den offentliga slut punkten till vissa virtuella nätverk. I innehålls förteckningen för lagrings kontot väljer du **brand väggar och virtuella nätverk**. 

Längst upp på sidan väljer du alternativ knappen **valda nätverk** . Detta gör att det inte går att dölja ett antal inställningar för att kontrol lera begränsningen för den offentliga slut punkten. Klicka på **+ Lägg till befintligt virtuellt nätverk** för att välja det enskilda virtuella nätverk som ska få åtkomst till lagrings kontot via den offentliga slut punkten. Detta kräver att du väljer ett virtuellt nätverk och ett undernät för det virtuella nätverket. 

Markera **Tillåt att betrodda Microsoft-tjänster har åtkomst till det här tjänst kontot** för att tillåta betrodda Microsoft-tjänster från första part, till exempel Azure File Sync att komma åt lagrings kontot.

[![Skärm bild av bladet brand väggar och virtuella nätverk med ett särskilt virtuellt nätverk som har behörighet att komma åt lagrings kontot via den offentliga slut punkten](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png#lightbox)