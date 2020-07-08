---
title: Undvik inställningar för lagrings brand väggen
description: Nätverks brand Väggs inställningen för ett lagrings konto kan orsaka fel när du skapar ett Azure Blob Storage-mål i Azure HPC cache. Den här artikeln innehåller en lösning för begränsningen tills en program korrigering är på plats.
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.author: rohogue
ms.openlocfilehash: 11e57e5d101e35c55dbaa39615dfe48511a8b87c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85513822"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>Gå runt brandväggsinställningar för bloblagringskonto

En viss inställning som används i lagrings kontots brand väggar kan orsaka att det inte går att skapa Blob Storage-mål. Azure HPC cache-teamet arbetar med en program varu lösning för det här problemet, men du kan lösa det genom att följa anvisningarna i den här artikeln.

Brand Väggs inställningen som endast tillåter åtkomst från valda nätverk kan förhindra att cachen skapar ett Blob Storage-mål. Den här konfigurationen finns på sidan Inställningar för lagrings kontots **brand väggar och virtuella nätverk** .

Problemet är att cache-tjänsten använder ett dolt tjänst nätverk som är skilt från kund miljöer. Det går inte att uttryckligen ge nätverket åtkomst till ditt lagrings konto.

När du skapar ett Blob Storage-mål använder Cache tjänsten det här nätverket för att kontrol lera om behållaren är tom eller inte. Om brand väggen inte tillåter åtkomst från det dolda nätverket, Miss lyckas kontrollen och det går inte att skapa lagrings målet.

Undvik problemet genom att tillfälligt ändra brand Väggs inställningarna när du skapar lagrings målet:

1. Gå till sidan lagrings konto **brand väggar och virtuella nätverk** och ändra inställningen "Tillåt åtkomst från" till **alla nätverk**.
1. Skapa Blob Storage-målet i din Azure HPC-cache.
1. När lagrings målet har skapats ändrar du kontots brand Väggs inställning tillbaka till **valda nätverk**.

Azure HPC cache använder inte tjänstens virtuella nätverk för att komma åt det färdiga lagrings målet.

[Kontakta Microsoft service och support](hpc-cache-support-ticket.md)om du behöver hjälp med den här lösningen.
