---
title: Felsökning av oväntad omstarter av virtuella datorer med anslutna virtuella hårddiskar på virtuella Azure Windows-datorer | Microsoft Docs
description: Så här felsöker du oväntat omstarter av virtuella Windows-datorer.
keywords: SSH anslutningen nekades, ssh fel, azure ssh, SSH-anslutning misslyckades
services: virtual-machines-windows
author: genlin
manager: cshepard
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.openlocfilehash: 5c16c2d08a1a317f8f718330ff6c86a452ac1815
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Felsökning av oväntad omstarter av virtuella datorer med anslutna virtuella hårddiskar

Om Azure virtuell dator (VM) har ett stort antal anslutna virtuella hårddiskar som är i samma lagringskonto, kan du överskrida skalbarhetsmål för enskilda lagringskontot orsakar den virtuella datorn startas om oväntat. Kontrollera minut mätvärden för storage-konto (**TotalRequests**/**TotalIngress**/**TotalEgress**) för toppar som överskrider den skalbarhetsmål för ett lagringskonto. Se [mätvärdena visar en ökning i PercentThrottlingError](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) för hjälp med att fastställa om begränsning har inträffat i ditt lagringskonto.

I allmänhet varje enskild indata eller utdata-åtgärden på en VHD från en virtuell dator innebär **hämta sidan** eller **placera sidan** åtgärder på den underliggande sidblob. Därför kan du använda den uppskattade IOPS för din miljö för att finjustera hur många virtuella hårddiskar kan i ett enda lagringskonto baserat på vad som sker på ditt program. Microsoft rekommenderar att 40 diskar i ett enda storage-konto. Se [Azure Storage skalbarhets- och prestandamål](../../storage/common/storage-scalability-targets.md) för information om skalbarhetsmål för storage-konton, särskilt Totalt antal förfrågningar och den totala bandbredden för typ av storage-konto du använder.

Placera de virtuella hårddiskarna på flera lagringskonton för att minska aktiviteten i varje enskilt konto om du överskrider skalbarhetsmål för ditt lagringskonto.
