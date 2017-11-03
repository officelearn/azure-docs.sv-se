---
title: "Exempel mål/utdata möjligt med Azure Machine Learning förberedelse av data | Microsoft Docs"
description: "Det här dokumentet innehåller en en uppsättning exempel på anpassade data mål/utdata med Azure Machine Learning förberedelse av data"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: ca9e7d8c318100ba498ee15be15e213905f1e8d2
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="sample-of-destination-connections-python"></a>Exempel på målanslutningar (Python) 
Innan du läser den här bilagan läsa [Python utökningsbarhet översikt](data-prep-python-extensibility-overview.md).


## <a name="write-to-excel"></a>Skriva till Excel 


Skrivning till Excel kräver ett ytterligare bibliotek. Lägga till nya bibliotek dokumenteras i översikten utökningsbarhet. `openpyxl`är det bibliotek som du behöver lägga till.

Innan du skriver till Excel, kan några andra ändringar behövas. Vissa av de data som används vid förberedelse av data stöds inte i vissa mål-format. Till exempel om ”Error” objekt finns de kommer inte att serialisera korrekt till Excel. Du måste därför en transformering ”ersätter felvärdena”, vilket tar bort fel från alla kolumner innan du försöker att skriva till Excel.

Om allt arbete som tidigare har slutförts, skriver följande rad datatabellen till ett blad i Excel-dokument. Lägg till en transformering skriva dataflöde (skript). Ange följande kod i ett uttryck-avsnitt.


### <a name="on-windows"></a>På Windows 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

### <a name="on-macosos-x"></a>I macOS/OS X ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
