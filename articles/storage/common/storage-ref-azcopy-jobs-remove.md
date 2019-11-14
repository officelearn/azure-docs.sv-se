---
title: ta bort AzCopy-jobb | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy Jobs Remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ba403c8d823b7ead0414521ebd51dc6f6601ccd8
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034163"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

Ta bort alla filer som är associerade med angivet jobb-ID.

> [!NOTE] 
> Du kan anpassa platsen där logg och planera filer sparas. Mer information finns i [AzCopy](storage-ref-azcopy-env.md) -kommandot.

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](storage-use-azcopy-blobs.md)
- [Överföra data med AzCopy och fil lagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exempel

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Alternativ

**-h,--hjälp**                Hjälp för att ta bort.

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

**--Cap-Mbit/s UInt32**      CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.

**--Skriv** sträng format för kommandots utdata. Alternativen är: text, JSON. Standardvärdet är ' text '. (standard text)

## <a name="see-also"></a>Se även

- [AzCopy-jobb](storage-ref-azcopy-jobs.md)
