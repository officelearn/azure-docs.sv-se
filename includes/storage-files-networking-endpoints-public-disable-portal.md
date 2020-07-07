---
title: inkludera fil
description: inkludera fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 295b9fc842d926d0a8b264ed5200b60ac7bd2261
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84465153"
---
Navigera till det lagrings konto som du vill begränsa all åtkomst till den offentliga slut punkten. I innehålls förteckningen för lagrings kontot väljer du **brand väggar och virtuella nätverk**.

Längst upp på sidan väljer du alternativ knappen **valda nätverk** . Detta gör att det inte går att dölja ett antal inställningar för att kontrol lera begränsningen för den offentliga slut punkten. Markera **Tillåt att betrodda Microsoft-tjänster har åtkomst till det här tjänst kontot** för att tillåta betrodda Microsoft-tjänster från första part, till exempel Azure File Sync att komma åt lagrings kontot.

[![Skärm bild av bladet brand väggar och virtuella nätverk med lämplig begränsning på plats](media/storage-files-networking-endpoints-public-disable-portal/disable-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-disable-portal/disable-public-endpoint-0.png#lightbox)