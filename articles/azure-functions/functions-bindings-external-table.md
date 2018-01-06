---
title: "Den externa tabellen bindningen för Azure Functions (försök)"
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
ms.openlocfilehash: 8a4358fa67e45d0b7a2df1519d649099b5ef5850
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
---
# <a name="external-table-binding-for-azure-functions-experimental"></a>Den externa tabellen bindningen för Azure Functions (försök)

Den här artikeln förklarar hur du arbetar med data i tabellformat på SaaS-leverantörer, till exempel Sharepoint och Dynamics i Azure Functions. Azure Functions stöder indata och utdata bindningar för externa tabeller.

> [!IMPORTANT]
> Extern tabell bindningen är experiment och kanske aldrig når vanligtvis tillgänglighet (GA) status. Den är inkluderad i Azure Functions 1.x och det finns inga planer på att lägga till den i Azure Functions 2.x. Överväg att använda för scenarier som kräver åtkomst till data i SaaS-providers [logikappar som anropar funktioner](functions-twitter-email.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>API-anslutningar

Tabell bindningar utnyttja externa API-anslutningar för att autentisera med SaaS leverantörer. 

När du tilldelar en bindning kan du skapa en ny API-anslutning eller använda en befintlig anslutning API inom samma resursgrupp.

### <a name="available-api-connections-tables"></a>Tillgängliga API-anslutningar (tabeller)

|Anslutning|Utlösare|Indata|Resultat|
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
> Den externa tabellen anslutningar kan också användas i [Azure Logikappar](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="creating-an-api-connection-step-by-step"></a>Skapa en API-anslutning: steg-för-steg

1. Klicka på plustecknet på Azure portal sidan för din funktionsapp (**+**) att skapa en funktion.

1. I den **scenariot** väljer **Experimental**.

1. Välj **extern tabell**.

1. Välj ett språk.

2. Under **extern tabell anslutning**väljer du en befintlig anslutning eller **nya**.

1. Konfigurera inställningar för en ny anslutning och väljer **auktorisera**.

1. Välj **skapa** skapa funktionen.

1. Välj **integrera > extern tabell**.

1. Konfigurera anslutningen om du vill använda din måltabellen. Inställningarna varierar mellan SaaS-providers. Exempel finns i följande avsnitt.

## <a name="example"></a>Exempel

Det här exemplet ansluter till en tabell med namnet ”kontakta” med Id, efternamn och förnamn kolumner. Koden visar kontaktenheter i tabellen och loggar första och sista namnen.

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

Här är skriptkod C#:

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

Här är ett skript för att skapa en tabell i SQL Server som ska användas med det här exemplet. `dataSetName`”standard”.

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

### <a name="google-sheets-data-source"></a>Datakälla för Google-blad

Skapa ett kalkylblad för att skapa en tabell som ska användas med det här exemplet i Google-dokument, med ett kalkylblad med namnet `Contact`. Anslutningen kan inte använda visningsnamnet kalkylblad. Internt namn (i fetstil) behöver användas som dataSetName, till exempel: `docs.google.com/spreadsheets/d/`  **`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`**  lägga till kolumnnamnen `Id`, `LastName`, `FirstName` till den första raden och fyll sedan i data på efterföljande rader.

### <a name="salesforce"></a>Salesforce

Du använder det här exemplet med Salesforce, `dataSetName` är ”standard”.

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil.

|Egenskapen Function.JSON | Beskrivning|
|---------|----------------------|
|**typ** | måste anges till `apiHubTable`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | måste anges till `in`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Namnet på variabeln som representerar händelsen i funktionskoden. | 
|**anslutning**| Identifierar appinställningen som lagrar anslutningssträngen API. Appinställningen skapas automatiskt när du lägger till en API-anslutning i integrera Användargränssnittet.|
|**dataSetName**|Namnet på den datamängd som innehåller tabellen om du vill läsa.|
|**tableName**|Namnet på tabellen|
|**ID för entiteterna**|Måste vara tom för tabell-bindningar.

En tabell koppling innehåller datauppsättningar och varje datamängd innehåller tabeller. Namnet på datamängden som standard är ”standard”. Rubrikerna för en datauppsättning och en tabell i olika SaaS-providers visas nedan:

|Anslutning|Datauppsättning|Tabell|
|:-----|:---|:---| 
|**SharePoint**|Webbplats|SharePoint-lista
|**SQL**|Databas|Tabell 
|**Google-blad**|Kalkylblad|Kalkylblad 
|**Excel**|Excel-fil|Blad 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
