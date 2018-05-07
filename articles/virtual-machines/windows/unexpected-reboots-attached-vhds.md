---
title: Felsökning av oväntad omstarter av virtuella datorer med anslutna virtuella hårddiskar på virtuella Azure Windows-datorer | Microsoft Docs
description: Så här felsöker du oväntat omstarter av virtuella Windows-datorer.
keywords: SSH anslutningen nekades, ssh fel, azure ssh, SSH-anslutning misslyckades
services: virtual-machines-windows
author: tamram
manager: jeconnoc
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/01/2018
ms.author: tamram
ms.openlocfilehash: 2057ffe2d5ec61e1192ff12291114c43dc2cc049
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Felsökning av oväntad omstarter av virtuella datorer med anslutna virtuella hårddiskar

Om Azure virtuell dator (VM) har ett stort antal anslutna virtuella hårddiskar som är i samma lagringskonto, kan du överskrida skalbarhetsmål för enskilda lagringskontot orsakar den virtuella datorn startas om oväntat. Kontrollera minut mätvärden för storage-konto (**TotalRequests**/**TotalIngress**/**TotalEgress**) för toppar som överskrider den skalbarhetsmål för ett lagringskonto. Se [mätvärdena visar en ökning i PercentThrottlingError](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) för hjälp med att fastställa om begränsning har inträffat i ditt lagringskonto.

I allmänhet varje enskild indata eller utdata-åtgärden på en VHD från en virtuell dator innebär **hämta sidan** eller **placera sidan** åtgärder på den underliggande sidblob. Därför kan du använda den uppskattade IOPS för din miljö för att finjustera hur många virtuella hårddiskar kan i ett enda lagringskonto baserat på vad som sker på ditt program. Microsoft rekommenderar att 40 diskar i ett enda storage-konto. Se [Azure Storage skalbarhets- och prestandamål](../../storage/common/storage-scalability-targets.md) för information om skalbarhetsmål för storage-konton, särskilt Totalt antal förfrågningar och den totala bandbredden för typ av storage-konto du använder.

Placera de virtuella hårddiskarna på flera lagringskonton för att minska aktiviteten i varje enskilt konto om du överskrider skalbarhetsmål för ditt lagringskonto.
