---
title: Rensa AzCopy-jobb | Microsoft Docs
description: Den här artikeln innehåller referensinformation om kommandot AzCopy Jobs Clean.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f3e9d70ced0d2974a66717436c28c5b6914f6745
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87287135"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

Ta bort alla logg-och plan-filer för alla jobb

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>Relaterade konceptuella artiklar

- [Kom igång med AzCopy](storage-use-azcopy-v10.md)
- [Överföra data med AzCopy och Blob Storage](storage-use-azcopy-blobs.md)
- [Överföra data med AzCopy och fillagring](storage-use-azcopy-files.md)
- [Konfigurera, optimera och felsöka AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exempel

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Alternativ

**--Hjälp**                Hjälp för ren.

**--med-status** sträng ta endast bort jobb med denna status, tillgängliga värden: `Canceled` , `Completed` , `Failed` , `InProgress` , `All` (standard `All` )

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

**--Cap-Mbit/s float**      CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.

**--Skriv** sträng format för kommandots utdata. Alternativen är: text, JSON. Standardvärdet är ' text '. (standard text)

**--sträng för betrodd-Microsoft-suffix** anger ytterligare domänsuffix där Azure Active Directory inloggnings-token kan skickas.  Standardvärdet är '*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. De som anges här läggs till i standardvärdet. För säkerhet ska du bara placeras Microsoft Azure domäner här. Avgränsa flera poster med semikolon.

## <a name="see-also"></a>Se även

- [azcopy jobb](storage-ref-azcopy-jobs.md)
