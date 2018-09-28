---
title: Felsöka oväntade omstarter av virtuella datorer med anslutna virtuella hårddiskar på virtuella Azure-datorer | Microsoft Docs
description: Så här felsöker du oväntade omstarter av virtuella datorer.
keywords: SSH anslutningen nekades, ssh fel, azure ssh, SSH-anslutningen misslyckades
services: virtual-machines
author: genlin
manager: cshepard
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.openlocfilehash: cffc6166ab7d0864646421b35fbecafdd80eb27e
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47414764"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Felsöka oväntade omstarter av virtuella datorer med anslutna virtuella hårddiskar

Om Azure Virtual Machine (VM) har ett stort antal anslutna virtuella hårddiskar som är i samma lagringskonto, kan du överskrida skalbarhetsmål för en enskild storage-konto, vilket gör den virtuella datorn ska startas om oväntat. Kontrollera minutmått för storage-konto (**TotalRequests**/**TotalIngress**/**TotalEgress**) för toppar som överstiger den skalbarhetsmål för ett lagringskonto. Se [mätningar visar en ökning i PercentThrottlingError](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) mer information om huruvida begränsning har inträffat i ditt storage-konto.

I allmänhet varje enskild indata eller utdata på en virtuell Hårddisk från en virtuell dator översätts till **hämta sidan** eller **placera sidan** åtgärder på den underliggande sidblob. Därför kan du använda den uppskattade IOPS för miljön för att justera hur många virtuella hårddiskar kan i ett enda lagringskonto baserat på vad som sker i ditt program. Microsoft rekommenderar att ha 40 eller färre diskar i ett enda lagringskonto. Se [skalbarhet för lagring av Azure- och prestandamål](../../storage/common/storage-scalability-targets.md) mer information om skalbarhetsmål för storage-konton, särskilt för totala antalet begäranden och den totala bandbredden för typ av storage-konto du använder.

Om du överstiger det för skalbarhetsmål för ditt lagringskonto, placera de virtuella hårddiskarna i flera lagringskonton för att minska aktiviteten i varje enskilt konto.
