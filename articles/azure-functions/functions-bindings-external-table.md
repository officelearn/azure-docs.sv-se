---
title: "Azure Functions extern tabell bindning (förhandsversion) | Microsoft Docs"
description: "Använda extern tabell bindningar i Azure Functions"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.openlocfilehash: e6aa6913bdb2cf8e581b76b7680d0a96c9858bd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-external-table-binding-preview"></a>Azure Functions extern tabell bindning (förhandsgranskning)
Den här artikeln visar hur du hanterar tabelldata på SaaS-providers (t.ex. Sharepoint, Dynamics) i din funktion med inbyggda bindningar. Azure Functions stöder inkommande och utgående bindningar för externa tabeller.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>API-anslutningar

Tabell bindningar utnyttja externa API anslutningar för att autentisera med 3 part SaaS-providers. 

När du tilldelar en bindning kan du antingen skapa en ny API-anslutning eller använda en befintlig anslutning API inom samma resursgrupp

### <a name="supported-api-connections-tables"></a>Stöds API-anslutningar (tabellen) s

|koppling|Utlösare|Indata|Resultat|
|:-----|:---:|:---:|:---:|
|[DB2](https://docs.microsoft.com/azure/connectors/connectors-create-api-db2)||x|x
|[Dynamics 365 för åtgärder](https://ax.help.dynamics.com/wiki/install-and-configure-dynamics-365-for-operations-warehousing/)||x|x
|[Dynamics 365](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[Dynamics NAV](https://msdn.microsoft.com/library/gg481835.aspx)||x|x
|[Google-blad](https://docs.microsoft.com/azure/connectors/connectors-create-api-googledrive)||x|x
|[Informix](https://docs.microsoft.com/azure/connectors/connectors-create-api-informix)||x|x
|[Dynamics 365 för ekonomi](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[MySQL](https://docs.microsoft.com/azure/store-php-create-mysql-database)||x|x
|[Oracle Database](https://docs.microsoft.com/azure/connectors/connectors-create-api-oracledatabase)||x|x
|[Vanliga datatjänst](https://docs.microsoft.com/common-data-service/entity-reference/introduction)||x|x
|[Salesforce](https://docs.microsoft.com/azure/connectors/connectors-create-api-salesforce)||x|x
|[SharePoint](https://docs.microsoft.com/azure/connectors/connectors-create-api-sharepointonline)||x|x
|[SQL Server](https://docs.microsoft.com/azure/connectors/connectors-create-api-sqlazure)||x|x
|[Teradata](http://www.teradata.com/products-and-services/azure/products/)||x|x
|UserVoice||x|x
|Zendesk||x|x


> [!NOTE]
> Den externa tabellen anslutningar kan också användas i [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list)

### <a name="creating-an-api-connection-step-by-step"></a>Skapa en API-anslutning: steg-för-steg

1. Skapa en funktion > egen funktion ![skapa en egen funktion](./media/functions-bindings-storage-table/create-custom-function.jpg)
1. Scenariot `Experimental`  >  `ExternalTable-CSharp` mall > Skapa en ny `External Table connection` 
 ![Välj mall för inkommande](./media/functions-bindings-storage-table/create-template-table.jpg)
1. Välj din SaaS-provider > Välj/Skapa en anslutning ![konfigurera SaaS-anslutning](./media/functions-bindings-storage-table/authorize-API-connection.jpg)
1. Välj API-anslutning > Skapa funktionen ![skapa tabellfunktion](./media/functions-bindings-storage-table/table-template-options.jpg)
1. Välj`Integrate` > `External Table`
    1. Konfigurera anslutningen om du vill använda din måltabellen. De här inställningarna kommer mycket mellan SaaS-providers. De är disposition nedan i [inställningar för datakälla](#datasourcesettings)
![konfigurera tabell](./media/functions-bindings-storage-table/configure-API-connection.jpg)

## <a name="usage"></a>Användning

Det här exemplet ansluter till en tabell med namnet ”kontakta” med Id, efternamn och förnamn kolumner. Koden visar kontaktenheter i tabellen och loggar första och sista namnen.

### <a name="bindings"></a>Bindningar
```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "apiHubTable",
      "direction": "in",
      "name": "table",
      "connection": "ConnectionAppSettingsKey",
      "dataSetName": "default",
      "tableName": "Contact",
      "entityId": "",
    }
  ],
  "disabled": false
}
```
`entityId`måste vara tom för tabell-bindningar.

`ConnectionAppSettingsKey`identifierar appinställningen som lagrar anslutningssträngen API. Appinställningen skapas automatiskt när du lägger till en API-anslutning i integrera Användargränssnittet.

En tabell koppling innehåller datauppsättningar och varje datamängd innehåller tabeller. Namnet på datamängden som standard är ”standard”. Rubrikerna för en datauppsättning och en tabell i olika SaaS-providers visas nedan:

|koppling|Datauppsättning|Tabell|
|:-----|:---|:---| 
|**SharePoint**|plats|SharePoint-lista
|**SQL**|Databas|Tabell 
|**Google-blad**|Kalkylblad|Kalkylblad 
|**Excel**|Excel-fil|Blad 

<!--
See the language-specific sample that copies the input file to the output file.

* [C#](#incsharp)
* [Node.js](#innodejs)

-->
<a name="incsharp"></a>

### <a name="usage-in-c"></a>Användning i C# #

```cs
#r "Microsoft.Azure.ApiHub.Sdk"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.ApiHub;

//Variable name must match column type
//Variable type is dynamically bound to the incoming data
public class Contact
{
    public string Id { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
}

public static async Task Run(string input, ITable<Contact> table, TraceWriter log)
{
    //Iterate over every value in the source table
    ContinuationToken continuationToken = null;
    do
    {   
        //retreive table values
        var contactsSegment = await table.ListEntitiesAsync(
            continuationToken: continuationToken);

        foreach (var contact in contactsSegment.Items)
        {   
            log.Info(string.Format("{0} {1}", contact.FirstName, contact.LastName));
        }

        continuationToken = contactsSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

<!--
<a name="innodejs"></a>

### Usage in Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```
-->
<a name="datasourcesettings"></a>
##Inställningar för datakälla

### <a name="sql-server"></a>SQL Server

Skript för att skapa och fylla i tabellen Kontakt är nedan. dataSetName är ”standard”.

```sql
CREATE TABLE Contact
(
    Id int NOT NULL,
    LastName varchar(20) NOT NULL,
    FirstName varchar(20) NOT NULL,
    CONSTRAINT PK_Contact_Id PRIMARY KEY (Id)
)
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (1, 'Bitt', 'Prad') 
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (2, 'Glooney', 'Ceorge') 
GO
```

### <a name="google-sheets"></a>Google-blad
Skapa ett kalkylblad i Google-dokument med ett kalkylblad med namnet `Contact`. Anslutningen kan inte använda visningsnamnet kalkylblad. Internt namn (i fetstil) behöver användas som dataSetName, till exempel: `docs.google.com/spreadsheets/d/`  **`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`**  lägga till kolumnnamnen `Id`, `LastName`, `FirstName` till den första raden och fyll sedan i data på efterföljande rader.

### <a name="salesforce"></a>Salesforce
dataSetName är ”standard”.

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
