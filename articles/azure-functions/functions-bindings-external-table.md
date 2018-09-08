---
title: Externa tabellbindning för Azure Functions (experimentell)
description: Använda extern tabell bindningar i Azure Functions
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: alkarche
ms.openlocfilehash: 24728414747d8ad8a8d7ee0d8a21be2177a15ddd
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44093820"
---
# <a name="external-table-binding-for-azure-functions-experimental"></a>Externa tabellbindning för Azure Functions (experimentell)

Den här artikeln förklarar hur du arbetar med tabelldata på SaaS-leverantörer, till exempel Sharepoint och Dynamics i Azure Functions. Azure Functions stöder indata och utdata-bindningar för externa tabeller.

> [!IMPORTANT]
> Extern tabell bindningen är experimentellt och aldrig kan nå allmänt tillgänglig (GA) status. Det ingår endast i Azure Functions 1.x och det finns inga planer på att lägga till den i Azure Functions 2.x. Överväg att använda för scenarier som kräver åtkomst till data i SaaS-leverantörer, [logikappar som anropar functions](functions-twitter-email.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>API-anslutningar

Tabellbindningar utnyttja externa API-anslutningar för att autentisera med tredje parts SaaS-leverantörer. 

När du tilldelar en bindning kan du skapa en ny API-anslutning eller använda en befintlig API-anslutning i samma resursgrupp.

### <a name="available-api-connections-tables"></a>Tillgängliga API-anslutningar (tabeller)

|Koppling|Utlösare|Indata|Resultat|
|:-----|:---:|:---:|:---:|
|[DB2](https://docs.microsoft.com/azure/connectors/connectors-create-api-db2)||x|x
|[Dynamics 365 for Operations](https://ax.help.dynamics.com/wiki/install-and-configure-dynamics-365-for-operations-warehousing/)||x|x
|[Dynamics 365](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[Dynamics NAV](https://msdn.microsoft.com/library/gg481835.aspx)||x|x
|[Google-blad](https://docs.microsoft.com/azure/connectors/connectors-create-api-googledrive)||x|x
|[Informix](https://docs.microsoft.com/azure/connectors/connectors-create-api-informix)||x|x
|[Dynamics 365 för Financials](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[MySQL](https://docs.microsoft.com/azure/store-php-create-mysql-database)||x|x
|[Oracle Database](https://docs.microsoft.com/azure/connectors/connectors-create-api-oracledatabase)||x|x
|[Common Data Service](https://docs.microsoft.com/common-data-service/entity-reference/introduction)||x|x
|[Salesforce](https://docs.microsoft.com/azure/connectors/connectors-create-api-salesforce)||x|x
|[SharePoint](https://docs.microsoft.com/azure/connectors/connectors-create-api-sharepointonline)||x|x
|[SQL Server](https://docs.microsoft.com/azure/connectors/connectors-create-api-sqlazure)||x|x
|[Teradata](http://www.teradata.com/products-and-services/azure/products/)||x|x
|UserVoice||x|x
|Zendesk||x|x

> [!NOTE]
> Den externa tabellen anslutningar kan också användas i [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="creating-an-api-connection-step-by-step"></a>Skapa en API-anslutning: steg för steg

1. I Azure portal sidan för din funktionsapp, klicka på plustecknet (**+**) att skapa en funktion.

1. I den **scenariot** väljer **Experimental**.

1. Välj **extern tabell**.

1. Välj ett språk.

2. Under **extern tabellanslutning**väljer du en befintlig anslutning eller **nya**.

1. Konfigurera inställningar för en ny anslutning och välj **auktorisera**.

1. Välj **skapa** skapa funktionen.

1. Välj **integrera > extern tabell**.

1. Konfigurera anslutningen till din måltabellen. De här inställningarna varierar mellan SaaS-leverantörer. Exempel som ingår i följande avsnitt.

## <a name="example"></a>Exempel

Det här exemplet ansluter till en tabell med namnet ”kontakta” med ID: T, efternamn och förnamn kolumner. Koden visas entiteterna som kontakt i tabellen och loggar första och sista namnen.

Här är den *function.json* fil:

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

Här är C#-skriptkoden:

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
        //retrieve table values
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

### <a name="sql-server-data-source"></a>SQL Server-datakälla

Om du vill skapa en tabell i SQL Server som ska användas med det här exemplet, är här ett skript. `dataSetName` är ”standard”.

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

### <a name="google-sheets-data-source"></a>Google Sheets-datakälla

Om du vill skapa en tabell som ska användas med det här exemplet i Google Docs, skapa ett kalkylblad med ett kalkylblad med namnet `Contact`. Anslutningen kan inte använda visningsnamnet kalkylblad. Internt namn (i fetstil) behov som ska användas som dataSetName, till exempel: `docs.google.com/spreadsheets/d/` **`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`** lägga till kolumnnamnen `Id`, `LastName`, `FirstName` till den första raden och sedan fylla i data på efterföljande rader.

### <a name="salesforce"></a>Salesforce

Du använder det här exemplet med Salesforce, `dataSetName` är ”standard”.

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil.

|Function.JSON egenskap | Beskrivning|
|---------|----------------------|
|**typ** | Måste anges till `apiHubTable`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Måste anges till `in`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Namnet på variabeln som representerar objektet händelse i funktionskoden. | 
|**anslutning**| Identifierar den appinställning som lagrar anslutningssträngen API. Appinställningen skapas automatiskt när du lägger till en API-anslutning i integrera Användargränssnittet.|
|**dataSetName**|Namnet på den datauppsättning som innehåller tabellen att läsa.|
|**Tabellnamn**|Tabellens namn|
|**entityId**|Måste vara tom för tabellbindningar.

En tabular-anslutningsappen erbjuder datauppsättningar och varje datamängd innehåller tabeller. Namnet på standarddatauppsättningen är ”standard”. Rubrikerna för en datauppsättning och en tabell i olika SaaS-leverantörer visas nedan:

|Koppling|Datauppsättning|Tabell|
|:-----|:---|:---| 
|**SharePoint**|Webbplats|SharePoint-lista
|**SQL**|Databas|Tabell 
|**Google-blad**|Kalkylblad|Kalkylblad 
|**Excel**|Excel-fil|Blad 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
