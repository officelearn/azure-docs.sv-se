---
title: Exempel mål/utdata möjligt med Azure Machine Learning förberedelse av data | Microsoft Docs
description: Det här dokumentet innehåller en en uppsättning exempel på anpassade data mål/utdata med Azure Machine Learning förberedelse av data
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 93da07006280b38c9e6539773e6ac22e50e48b57
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831820"
---
# <a name="sample-of-destination-connections-python"></a>Exempel på målanslutningar (Python) 
Innan du läser den här bilagan läsa [Python utökningsbarhet översikt](data-prep-python-extensibility-overview.md).


## <a name="write-to-excel"></a>Skriva till Excel 


Skrivning till Excel kräver ett ytterligare bibliotek. Lägga till nya bibliotek dokumenteras i översikten utökningsbarhet. `openpyxl` är det bibliotek som du behöver lägga till.

Innan du skriver till Excel, kan några andra ändringar behövas. Vissa av de data som används vid förberedelse av data stöds inte i vissa mål-format. Till exempel om ”Error” objekt finns de kommer inte att serialisera korrekt till Excel. Du måste därför en transformering ”ersätter felvärdena”, vilket tar bort fel från alla kolumner innan du försöker att skriva till Excel.

Om allt arbete som tidigare har slutförts, skriver följande rad datatabellen till ett blad i Excel-dokument. Lägg till en transformering transformera dataflöde (skript). Ange följande kod i ett uttryck-avsnitt.


### <a name="on-windows"></a>På Windows 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

### <a name="on-macosos-x"></a>I macOS/OS X ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
