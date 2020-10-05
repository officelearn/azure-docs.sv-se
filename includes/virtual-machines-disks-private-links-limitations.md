---
title: inkludera fil
description: inkludera fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 293f0f459e1f1e464fdec16b76eaf08336c92e93
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91377416"
---
- Det går bara att länka ett virtuellt nätverk till ett disk åtkomst objekt.
- Ditt virtuella nätverk måste vara i samma prenumeration som ditt disk åtkomst objekt för att länka dem.
- Upp till 10 diskar eller ögonblicks bilder kan importeras eller exporteras samtidigt med samma disk åtkomst objekt.
- Du kan inte begära manuellt godkännande för att länka ett virtuellt nätverk till ett disk åtkomst objekt.
- Det går inte att exportera stegvisa ögonblicks bilder när de är associerade med ett disk åtkomst objekt.
- Du kan inte använda AzCopy för att ladda ned VHD för en disk/ögonblicks bild som skyddas via privata länkar till ett lagrings konto. Du kan dock använda AzCopy för att ladda ned VHD till dina virtuella datorer.
