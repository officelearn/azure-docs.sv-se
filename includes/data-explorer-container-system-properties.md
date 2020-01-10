---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/08/2020
ms.author: orspodek
ms.openlocfilehash: f9788e4623ce60ad55d79558d1d77a17eb2a9f26
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779966"
---
### <a name="event-system-properties-mapping"></a>Mappning av händelse system egenskaper

Om du har valt **händelse system egenskaper** i avsnittet **data källa** i tabellen ovan går du till [webb gränssnittet](https://dataexplorer.azure.com/) för att köra relevant KQL-kommando för att skapa korrekt mappning.

   **För CSV-mappning:**

    ```kusto
    .create table MyTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "messageid", "DataType":"string", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "userid", "DataType":"string", "Properties":{"Ordinal":"1"}},'
    '   { "column" : "other", "DataType":"int", "Properties":{"Ordinal":"2"}}'
    ']'
    ```
 
   **För JSON-mappning:**

    ```kusto
    .create table MyTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "messageid", "datatype" : "string", "Properties":{"Path":"$.message-id"}},'
    '    { "column" : "userid", "Properties":{"Path":"$.user-id"}},'
    '    { "column" : "other", "Properties":{"Path":"$.other"}}'
    ']'
    ```

   > [!TIP]
   > * Du måste inkludera alla valda egenskaper i mappningen. 
   > * Egenskaperna order är viktig i CSV-mappning. System egenskaperna måste anges före alla andra egenskaper och i samma ordning som de visas i list rutan **händelse system egenskaper** .