---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 138e7efd33ee36a77fd56deda155a29017432302
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420301"
---
- Det går bara att länka ett virtuellt nätverk till ett disk åtkomst objekt.
- Ditt virtuella nätverk måste vara i samma prenumeration som ditt disk åtkomst objekt för att länka dem.
- Upp till 10 diskar eller ögonblicks bilder kan importeras eller exporteras samtidigt med samma disk åtkomst objekt.
- Du kan inte begära manuellt godkännande för att länka ett virtuellt nätverk till ett disk åtkomst objekt.
- Differential funktionen stöds inte för stegvisa ögonblicks bilder som är associerade med ett disk åtkomst objekt.
- Du kan inte använda AzCopy för att ladda ned VHD för en disk/ögonblicks bild som skyddas via privata länkar till ett lagrings konto. Du kan dock använda AzCopy för att ladda ned VHD till dina virtuella datorer.
