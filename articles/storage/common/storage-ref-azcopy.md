---
title: azoskopi | Microsoft-dokument
description: Den här artikeln innehåller referensinformation för azkoposkopikommandot.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 5da3a2e5d003a191bff66af6599cae4d34ab60c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74038096"
---
# <a name="azcopy"></a>azcopy

AzCopy är ett kommandoradsverktyg som flyttar data till och från Azure Storage.

## <a name="synopsis"></a>Synopsis

Det allmänna formatet för kommandona är: `azcopy [command] [arguments] --[flag-name]=[flag-value]`.

Information om hur du rapporterar problem [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy)eller lär dig mer om verktyget finns i .

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy- och Blob-lagring](storage-use-azcopy-blobs.md)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Alternativ

**--cap-mbps uint32 --cap-mbps**   Caps överföringshastigheten, i megabit per sekund. Moment-för-ögonblick genomströmning kan variera något från locket. Om det här alternativet är noll, eller om det utelämnas, begränsas inte dataflödet.

**-h, --hjälp** Hjälp för azcopy
      
**--utdatatyp**  Format för kommandots utdata. Alternativen är: text, json. Standardvärdet är "text". (standard "text")

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
