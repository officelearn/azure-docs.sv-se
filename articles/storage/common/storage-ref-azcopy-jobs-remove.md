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
ms.openlocfilehash: 82c399580322334e67c0c9c2b88d1edf6f175e0c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518296"
---
# <a name="azcopy-jobs-remove"></a>ta bort AzCopy-jobb

Ta bort alla filer som är associerade med angivet jobb-ID.

> [!NOTE] 
> Du kan anpassa platsen där logg och planera filer sparas. Mer information finns i [AzCopy](storage-ref-azcopy-env.md) -kommandot.

```
azcopy jobs remove [jobID] [flags]
```

## <a name="examples"></a>Exempel

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Alternativ

**-h,--hjälp**                Hjälp för att ta bort.

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

**--Cap-Mbit/s UInt32**      CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.

**--Skriv** sträng format för kommandots utdata. Alternativen är: text, JSON. Standardvärdet är ' text '. (standard text)

## <a name="see-also"></a>Se också

- [AzCopy-jobb](storage-ref-azcopy-jobs.md)
