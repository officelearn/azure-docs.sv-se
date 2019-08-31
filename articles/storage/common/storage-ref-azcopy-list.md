---
title: AzCopy-lista | Microsoft Docs
description: Den här artikeln innehåller referensinformation för kommandot AzCopy List.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8c40241c49917c6db6663f346aed0ec5d3b96be7
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195950"
---
# <a name="azcopy-list"></a>lista över AzCopy

Visar en lista över entiteterna i en specifik resurs.

## <a name="synopsis"></a>Sammanfattning

Endast BLOB-behållare stöds i den aktuella versionen.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="examples"></a>Exempel

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Alternativ

|Alternativ|Beskrivning|
|--|--|
|-h,--hjälp|Visa hjälp innehåll för kommandot List.|
|--maskinläsbar|Visar en lista över fil storlekar i byte.|
|--megapixlar|Visar enheter i order av 1000, inte 1024.|
|--körs-stämmer|Räknar det totala antalet filer och deras storlek.|

## <a name="options-inherited-from-parent-commands"></a>Alternativ som ärvts från överordnade kommandon

|Alternativ|Beskrivning|
|---|---|
|--Cap-Mbit/s UInt32|CAPS överföringshastigheten i megabit per sekund. Indata genom strömning kan variera något från höljet. Om det här alternativet är inställt på noll, eller utelämnas, är data flödet inte något tak.|
|--typ sträng för utdata|Formatet på kommandots utdata. Alternativen är: text, JSON. Standardvärdet är "text".|

## <a name="see-also"></a>Se också

- [AzCopy](storage-ref-azcopy.md)
