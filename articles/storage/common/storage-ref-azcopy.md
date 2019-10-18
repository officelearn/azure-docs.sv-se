---
title: AzCopy | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 984d0c570c6c0d5048d58377f113319157411244
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513380"
---
# <a name="azcopy"></a>azcopy

AzCopy är ett kommando rads verktyg som flyttar data till och från Azure Storage.

## <a name="synopsis"></a>Sammanfattning

Det allmänna formatet för kommandon är: `azcopy [command] [arguments] --[flag-name]=[flag-value]`.

Information om hur du rapporterar problem eller Lär dig mer om verktyget finns i [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy).

## <a name="options"></a>Alternativ

**--Cap-Mbit/s UInt32**   CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.

**-h,--hjälp** Hjälp för AzCopy
      
**--typ av utdata**  Formatet på kommandots utdata. Alternativen är: text, JSON. Standardvärdet är ' text '. (standard text)

## <a name="see-also"></a>Se också

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [AzCopy kopia](storage-ref-azcopy-copy.md)
- [AzCopy-dok](storage-ref-azcopy-doc.md)
- [AzCopy-kuvert](storage-ref-azcopy-env.md)
- [AzCopy-jobb](storage-ref-azcopy-jobs.md)
- [lista över AzCopy](storage-ref-azcopy-list.md)
- [AzCopy-inloggning](storage-ref-azcopy-login.md)
- [AzCopy-utloggning](storage-ref-azcopy-logout.md)
- [AzCopy-fabrikat](storage-ref-azcopy-make.md)
- [ta bort AzCopy](storage-ref-azcopy-remove.md)
- [AzCopy-synkronisering](storage-ref-azcopy-sync.md)
