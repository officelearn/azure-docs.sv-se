---
title: AzCopy | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 18972e991f08db7fa9548454a5c5cdc3ff0f552f
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285194"
---
# <a name="azcopy"></a>azcopy

AzCopy är ett kommando rads verktyg som flyttar data till och från Azure Storage.

## <a name="synopsis"></a>Sammanfattning

Det allmänna formatet för kommandon är: `azcopy [command] [arguments] --[flag-name]=[flag-value]` .

Information om hur du rapporterar problem eller Lär dig mer om verktyget finns i [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy) .

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](storage-use-azcopy-blobs.md)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Alternativ

**--Cap (float-Mbit/s) –** CAPS-överföringshastighet, i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.

**--Hjälp** Hjälp för AzCopy
      
**--** format (sträng) för kommandots utdata. Alternativen är: text, JSON. Standardvärdet är `text`. (standard `text` )

**--Trusted-Microsoft-suffix** (sträng) anger ytterligare domänsuffix där Azure Active Directory inloggnings-token kan skickas.  Standardvärdet är '*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. De som anges här läggs till i standardvärdet. För säkerhet ska du bara placeras Microsoft Azure domäner här. Avgränsa flera poster med semikolon.

## <a name="see-also"></a>Se även

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [azcopy bench](storage-ref-azcopy-bench.md)
- [azcopy kopiera](storage-ref-azcopy-copy.md)
- [azcopy dok](storage-ref-azcopy-doc.md)
- [azcopy miljö](storage-ref-azcopy-env.md)
- [azcopy jobb](storage-ref-azcopy-jobs.md)
- [azcopy jobs clean](storage-ref-azcopy-jobs-clean.md)
- [azcopy jobblista](storage-ref-azcopy-jobs-list.md)
- [azcopy jobs remove](storage-ref-azcopy-jobs-remove.md)
- [azcopy jobb-CV](storage-ref-azcopy-jobs-resume.md)
- [azcopy jobbvisning](storage-ref-azcopy-jobs-show.md)
- [azcopy lista](storage-ref-azcopy-list.md)
- [azcopy inloggning](storage-ref-azcopy-login.md)
- [azcopy utloggning](storage-ref-azcopy-logout.md)
- [azcopy skapa](storage-ref-azcopy-make.md)
- [azcopy ta bort](storage-ref-azcopy-remove.md)
- [azcopy synkronisering](storage-ref-azcopy-sync.md)
