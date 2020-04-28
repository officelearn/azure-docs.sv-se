---
title: Felsöka oväntade omstarter av virtuella datorer med anslutna virtuella hård diskar på virtuella Azure-datorer | Microsoft Docs
description: Så här felsöker du oväntade omstarter av virtuella datorer.
keywords: ssh-anslutningen nekades, SSH-fel, Azure SSH, SSH-anslutning misslyckades
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 3a06db1afd130d936af868d0d20632c3ec4fbfd2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75358534"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Felsöka oväntade omstarter av virtuella datorer med anslutna virtuella hård diskar

Om en virtuell Azure-dator (VM) har ett stort antal anslutna virtuella hård diskar som finns i samma lagrings konto kan du överskrida skalbarhets målen för ett enskilt lagrings konto, vilket innebär att den virtuella datorn inte startar om. Kontrol lera minut måtten för lagrings kontot (**TotalRequests**/**Total ingress**/**TotalEgress**) för toppar som överskrider skalbarhets målen för ett lagrings konto. Se [måtten Visa en ökning i PercentThrottlingError](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) för att få hjälp med att avgöra om begränsningen har inträffat på ditt lagrings konto.

I allmänhet översätts varje enskild indata-eller utdata-åtgärd på en virtuell hård disk från en virtuell dator för att **Hämta sid** -eller **placerings** åtgärder på den underliggande sid-blobben. Därför kan du använda den uppskattade IOPS för din miljö för att justera hur många virtuella hård diskar du kan ha i ett enda lagrings konto baserat på programmets specifika beteende. Microsoft rekommenderar att du har 40 eller färre diskar i ett enda lagrings konto. Mer information om skalbarhets mål för standard lagrings konton finns i [skalbarhets mål för standard lagrings konton](../../storage/common/scalability-targets-standard-account.md). Mer information om skalbarhets mål för Blob Storage-konton på Premium-sidan finns i [skalbarhets mål för Premium Page Blob Storage-konton](../../storage/blobs/scalability-targets-premium-page-blobs.md).

Om du överskrider skalbarhets målen för ditt lagrings konto ska du placera dina virtuella hård diskar i flera lagrings konton för att minska aktiviteten i varje enskilt konto.
