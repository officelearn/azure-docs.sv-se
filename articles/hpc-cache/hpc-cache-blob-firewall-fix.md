---
title: Gå runt inställningar för lagringsbrandväggen
description: En lagringskontonätverksbrandväggsinställning kan orsaka fel när du skapar ett Azure Blob-lagringsmål i Azure HPC-cache. Den här artikeln ger en lösning för begränsningen tills en programvarukorrigering är på plats.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/7/2019
ms.author: rohogue
ms.openlocfilehash: 6643662d498db8cbcffcb120a9ceabc46cfc04cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74174413"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>Gå runt brandväggsinställningar för bloblagringskonto

En viss inställning som används i brandväggar för lagringskonto kan orsaka att blob-lagringsmålet misslyckas. Azure HPC-cacheteamet arbetar med en programvarukorrigering för det här problemet, men du kan komma runt det genom att följa instruktionerna i den här artikeln.

Brandväggsinställningen som endast tillåter åtkomst från "valda nätverk" kan förhindra att cachen skapar ett Blob-lagringsmål. Den här konfigurationen finns på **inställningssidan för brandväggar och virtuella nätverk.**

Problemet är att cachetjänsten använder ett virtuellt nätverk för dolda tjänster som är separat från kundmiljöer. Det går inte att uttryckligen auktorisera det här nätverket för åtkomst till ditt lagringskonto.

När du skapar ett Blob-lagringsmål använder cachetjänsten det här nätverket för att kontrollera om behållaren är tom eller inte. Om brandväggen inte tillåter åtkomst från det dolda nätverket misslyckas kontrollen och skapandet av lagringsmål misslyckas.

Du kan lösa problemet genom att tillfälligt ändra brandväggsinställningarna när du skapar lagringsmålet:

1. Gå till sidan **brandväggar för lagringskonto och virtuella nätverk** och ändra inställningen "Tillåt åtkomst från" till alla **nätverk**.
1. Skapa Blob-lagringsmålet i din Azure HPC-cache.
1. När lagringsmålet har skapats ändrar du tillbaka kontots brandväggsinställning till **valda nätverk**.

Azure HPC Cache använder inte tjänsten virtuella nätverket för att komma åt den färdiga lagringsmålet.

Om du vill ha hjälp med den här lösningen [kontaktar du Microsoft Service and Support](hpc-cache-support-ticket.md).
