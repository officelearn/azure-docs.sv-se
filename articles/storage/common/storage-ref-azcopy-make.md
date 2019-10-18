---
title: Skapa AzCopy | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy make.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ebed5c8dbe5001e9beab17bdbff41610277143b2
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514703"
---
# <a name="azcopy-make"></a>azcopy skapa

Skapar en behållare eller fil resurs.

## <a name="synopsis"></a>Sammanfattning

Skapa en behållare eller fil resurs som representeras av den angivna resurs-URL: en.

```azcopy
azcopy make [resourceURL] [flags]
```

## <a name="examples"></a>Exempel

```azcopy
azcopy make "https://[account-name].[blob,file,dfs].core.windows.net/[top-level-resource-name]"
```

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|--|--|
|-h,--hjälp|Visa hjälp innehåll för kommandot gör. |
|– kvot-GB UInt32|Anger den maximala storleken på resursen i gigabyte (GiB), 0 innebär att du accepterar standard kvoten för fil tjänsten.|

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--Cap-Mbit/s UInt32|CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.|
|--typ sträng för utdata|Formatet på kommandots utdata. Alternativen är: text, JSON. Standardvärdet är "text".|

## <a name="see-also"></a>Se också

- [AzCopy](storage-ref-azcopy.md)
