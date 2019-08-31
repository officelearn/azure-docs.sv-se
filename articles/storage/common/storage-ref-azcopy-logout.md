---
title: AzCopy-utloggning | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy utloggnings kommando.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f42e2517f8c40855e56bd062fe8bc9b22634d4dc
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195924"
---
# <a name="azcopy-logout"></a>AzCopy-utloggning

Loggar ut användaren och avslutar åtkomsten till Azure Storage resurser.

## <a name="synopsis"></a>Sammanfattning

Det här kommandot tar bort all cachelagrad inloggnings information för den aktuella användaren.

```azcopy
azcopy logout [flags]
```

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|--|--|
|-h,--hjälp|Visa hjälp innehåll för kommandot logga ut.|

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--Cap-Mbit/s UInt32|CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.|
|--typ sträng för utdata|Formatet på kommandots utdata. Alternativen är: text, JSON. Standardvärdet är "text".|

## <a name="see-also"></a>Se också

- [AzCopy](storage-ref-azcopy.md)
