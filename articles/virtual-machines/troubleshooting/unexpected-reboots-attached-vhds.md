---
title: Felsöka oväntade omstarter av virtuella datorer med anslutna virtuella hårddiskar på virtuella Azure-datorer | Microsoft-dokument
description: Felsöker oväntade omstarter av virtuella datorer.
keywords: ssh-anslutning vägrade, ssh-fel, azure ssh, SSH-anslutning misslyckades
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 3a06db1afd130d936af868d0d20632c3ec4fbfd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75358534"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Felsöka oväntade omstarter av virtuella datorer med anslutna virtuella hårddiskar

Om en virtuell Azure-dator (VM) har ett stort antal anslutna virtuella hårddiskar som finns i samma lagringskonto kan du överskrida skalbarhetsmålen för ett enskilt lagringskonto, vilket gör att den virtuella datorn oväntat startas om. Kontrollera minutmåtten för lagringskontot (**TotalRequests**/**TotalIngress**/**TotalEgress**) för toppar som överskrider skalbarhetsmålen för ett lagringskonto. Se [Mått visar en ökning av PercentThrottlingError](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) för hjälp med att avgöra om begränsning har inträffat på ditt lagringskonto.

I allmänhet översätts varje enskild indata- eller utdataåtgärd på en virtuell hårddisk från en virtuell dator till **åtgärder för att hämta sida** eller Placera **sida** på den underliggande sidblobben. Därför kan du använda den uppskattade IOPS för din miljö för att ställa in hur många virtuella hårddiskar du kan ha i ett enda lagringskonto baserat på det specifika beteendet för ditt program. Microsoft rekommenderar att du har 40 eller färre diskar i ett enda lagringskonto. Mer information om skalbarhetsmål för standardlagringskonton finns i [Skalbarhetsmål för standardlagringskonton](../../storage/common/scalability-targets-standard-account.md). Mer information om skalbarhetsmål för premium-sidblobblagringskonton finns i [Skalbarhetsmål för premiumsida blob storage-konton](../../storage/blobs/scalability-targets-premium-page-blobs.md).

Om du överskrider skalbarhetsmålen för ditt lagringskonto placerar du dina virtuella hårddiskar i flera lagringskonton för att minska aktiviteten i varje enskilt konto.
