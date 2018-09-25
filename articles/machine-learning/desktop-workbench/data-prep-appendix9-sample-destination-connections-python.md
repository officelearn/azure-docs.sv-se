---
title: Exempel mål/utdata möjligt med Azure Machine Learning databearbetning | Microsoft Docs
description: Det här dokumentet innehåller en uppsättning exempel på anpassade data mål/utdata med Azure Machine Learning förberedelse av data
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 3e827c56caa787ae9fc33119810eea0f268f0120
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958432"
---
# <a name="sample-of-destination-connections-python"></a>Exempel på anslutningar till mål (Python) 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Innan du läser den här bilagan läsa [Python extensibility översikt](data-prep-python-extensibility-overview.md).


## <a name="write-to-excel"></a>Skriva till Excel 


Skrivning till Excel kräver ett ytterligare bibliotek. Att lägga till nya bibliotek dokumenteras i Översikt för utökningsbarhet. `openpyxl` är det bibliotek som du behöver lägga till.

Innan du skriver till Excel, kan några andra ändringar behövas. Några av de data som används i förberedelse av data stöds inte i vissa mål-format. Till exempel om ”Error” objekt finns serialisera de inte korrekt till Excel. Innan du försöker skriva till Excel, måste därför en ”ersätta felvärdena”-transformering, vilket tar bort fel från några kolumner.

Om allt arbete som tidigare har slutförts, skriver följande rad datatabellen till ett blad i ett Excel-dokument. Lägg till en transformering transformera dataflöde (skript). Ange sedan följande kod i ett uttryck-avsnitt.


### <a name="on-windows"></a>På Windows 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

### <a name="on-macosos-x"></a>I macOS/OS X ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
