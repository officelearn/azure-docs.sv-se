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
ms.openlocfilehash: 31529155ee44b2bcfad90e8634053403dfe8fc8c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518517"
---
# <a name="azcopy-jobs-clean"></a>rensning av AzCopy-jobb

Ta bort alla logg-och plan-filer för alla jobb

```
azcopy jobs clean [flags]
```

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

## <a name="see-also"></a>Se också

- [AzCopy-jobb](storage-ref-azcopy-jobs.md)
