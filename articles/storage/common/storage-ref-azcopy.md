---
title: AzCopy | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 4da9206f4500941179d781a0fe2a57ad15d7393d
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195898"
---
# <a name="azcopy"></a>AzCopy

AzCopy är ett kommando rads verktyg som flyttar data till och från Azure Storage.

## <a name="synopsis"></a>Sammanfattning

Det allmänna formatet för kommandon är: `azcopy [command] [arguments] --[flag-name]=[flag-value]`.

Information om hur du rapporterar problem eller Lär dig mer om verktyget [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy)finns i.

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|---|---|
|--Cap-Mbit/s UInt32|CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.|
|-h,--hjälp|Visar hjälp innehållet för AzCopy.|
|--typ sträng för utdata|Formatet på kommandots utdata. Alternativen är: text, JSON. Standardvärdet är "text".|

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
