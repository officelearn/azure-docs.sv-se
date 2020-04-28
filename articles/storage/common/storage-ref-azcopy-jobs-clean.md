---
title: Rensa AzCopy-jobb | Microsoft Docs
description: Den här artikeln innehåller referensinformation om kommandot AzCopy Jobs Clean.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7ae14c3606dfe6bffa8481682843f3f2e85c2131
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "74033726"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

Ta bort alla logg-och plan-filer för alla jobb

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](storage-use-azcopy-blobs.md)
- [Överföra data med AzCopy och fil lagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exempel

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Alternativ

**-h,--hjälp**                Hjälp för ren.

**--med-status** sträng ta bara bort jobben med denna status, tillgängliga värden: avbruten, slutförd, misslyckad, pågående, alla (standard)

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

**--Cap-Mbit/s UInt32**      CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.

**--Skriv** sträng format för kommandots utdata. Alternativen är: text, JSON. Standardvärdet är ' text '. (standard text)

## <a name="see-also"></a>Se även

- [azcopy jobb](storage-ref-azcopy-jobs.md)
