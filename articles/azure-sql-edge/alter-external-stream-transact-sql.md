---
title: ÄNDRA extern ström (Transact-SQL) – Azure SQL Edge (för hands version)
description: Lär dig mer om ALTER EXTERNAL STREAM-instruktionen i Azure SQL Edge (för hands version)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 2559c4b4b875403b7c70671e27cb6222a3f1103a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84235200"
---
# <a name="alter-external-stream-transact-sql"></a>ÄNDRA extern ström (Transact-SQL)

Ändrar definitionen för en extern data ström. Det är inte tillåtet att ändra en extern data ström som används av ett strömmande jobb i ett *körnings* tillstånd. 



## <a name="syntax"></a>Syntax

```sql
  ALTER EXTERNAL STREAM external_stream_name 
  SET 
    [DATA_SOURCE] = <data_source_name> 
    , LOCATION = <location_name> 
    , EXTERNAL_FILE_FORMAT = <external_file_format_name> 
    , INPUT_OPTIONS = <input_options> 
    , OUTPUT_OPTIONS = <output_options> 
```

## <a name="arguments"></a>Argument

Mer information om kommandot Alter external Stream kommando argument finns i [skapa extern ström (Transact-SQL)](create-external-stream-transact-sql.md).

## <a name="return-code-values"></a>Retur kod värden

ALTER EXTERNAL STREAM returnerar 0 om det lyckas. Ett retur värde som inte är noll indikerar ett avbrott.


## <a name="see-also"></a>Se även

- [Skapa extern ström (Transact-SQL)](create-external-stream-transact-sql.md) 
- [SLÄPP extern ström (Transact-SQL)](drop-external-stream-transact-sql.md) 
