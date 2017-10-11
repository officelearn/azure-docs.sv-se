---
title: "Datakällor som stöds i Azure Data Catalog | Microsoft Docs"
description: "Den här artikeln innehåller specifikationer av datakällor som för närvarande stöds."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: jstevens
editor: 
tags: 
ms.assetid: fd4345ca-2ed8-4c5e-9c4b-f954be2fc9f9
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: d6867c73bc6ea3c238cceef8a68466d451f3365c
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="supported-data-sources-in-azure-data-catalog"></a>Datakällor som stöds i Azure Data Catalog

Du kan publicera metadata med hjälp av en offentlig API eller klicka-registrering när verktyget eller web portal genom att manuellt ange information direkt till Azure Data Catalog. I följande tabell sammanfattas alla datakällor som stöds av katalogen idag och publishing funktionerna för var och en. Listan finns även externa Dataverktyg som varje datakälla kan starta från vår portal ”öppna i” erfarenhet. Den andra tabellen innehåller en mer teknisk specifikation för varje datakälla Anslutningsegenskapen.


## <a name="list-of-supported-data-sources"></a>Lista över datakällor som stöds

<table>
    <tr>
       <td><b>Datakällobjektet</b></td>
       <td><b>API</b></td>
       <td><b>Manuell inmatning</b></td>
       <td><b>Registreringsverktyget</b></td>
       <td><b>Öppna i Verktyg</b></td>
       <td><b>Anteckningar</b></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store-katalogen</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store-fil</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure Blob Storage</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure Storage-katalogen</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure Storage-tabellen</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>
    <tr>
      <td>HDFS-katalog</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HDFS-fil</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Hive-tabell</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Hive-vyn</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>MySQL-tabell</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Powerbi</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>MySQL-vy</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Powerbi</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Oracle Database-tabellen</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Powerbi</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Oracle databasvy</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Powerbi</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Andra (allmän tillgång)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure SQL Data Warehouse-tabellen</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI kan SQL Server data tools</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Data Warehouse-vy</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI kan SQL Server data tools</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services-dimension</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Powerbi</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services KPI</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Powerbi</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services-mått</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Powerbi</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services-tabell</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Powerbi</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services-rapport</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Webbläsare</font></td>
      <td><font size=2>Enhetligt läge-servrar. SharePoint-läge stöds inte.</font></td>
    </tr>
    <tr>
      <td>SQL Server-tabell</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI kan SQL Server data tools</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server-vy</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, Power BI kan SQL Server data tools</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabell för Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Visa för Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SAP HANA-vy</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>DB2-tabell</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>DB2-vy</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Fil för system</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>FTP-katalog</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>FTP-fil</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HTTP-rapport</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HTTP-slutpunkt</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HTTP-fil</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>OData-entitetsuppsättning</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>OData-funktion</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>PostgreSQL tabell</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>PostgreSQL-vyn</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SAP HANA-vy</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td> Salesforce-objekt</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SharePoint-lista </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure DB Cosmos-samling</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Allmän ODBC-tabell</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Allmän ODBC-vy</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Cassandra tabell</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>Publicera som en allmän ODBC-tillgång</font></td>
    </tr>
    <tr>
      <td>Cassandra vy</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>Publicera som en allmän ODBC-tillgång</font></td>
    </tr>
    <tr>
      <td>Sybase-tabell</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Sybase-vy</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Tabell för MongoDB</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>Publicera som en allmän ODBC-tillgång</font></td>
    </tr>
    <tr>
      <td>Visa MongoDB</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2>Publicera som en allmän ODBC-tillgång</font></td>
    </tr>
</table>

Om du behöver stöd för ytterligare källor kan skicka en funktionsbegäran till den [Azure Data Catalog forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).


## <a name="data-source-reference-specification"></a>Referens-specifikationen för datakälla
> [!NOTE]
> Den **DSL-strukturen** kolumnen i tabellen nedan visas endast de anslutningsegenskaper ”adress” egenskapsuppsättningen som används av Azure Data Catalog. Det vill säga kan ”adress” egenskapsuppsättning innehålla andra anslutningsegenskaper för datakällan som Azure Data Catalog kvarstår, men använder inte.

<table>
    <tr>
       <td><b>Typ av datakälla</b></td>
       <td><b>Tillgångstypen</b></td>
       <td><b>Objekttyper</b></td>
       <td><b>DSL-struktur<b></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>Behållare</td>
      <td>Data Lake</td>
      <td>
        <font size=2>Protokoll: webhdfs <br>Autentisering: {basic, oauth} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL: en</font>
      </td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>Tabell</td>
      <td>Katalogen fil</td>
      <td>
        <font size=2>Protokoll: webhdfs <br>Autentisering: {basic, oauth} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL: en</font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Behållare</td>
      <td>Behållare</td>
      <td>
        <font size=2>Protokoll: azure-BLOB <br>Autentisering: {azure-åtkomstnyckel} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domän <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;konto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;behållaren</font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Tabell</td>
      <td>BLOB, directory</td>
      <td>
        <font size=2>Protokoll: azure-BLOB <br>Autentisering: {azure-åtkomstnyckel} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domän <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;konto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;behållaren <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Namn</font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Behållare</td>
      <td>Behållare</td>
      <td>
        <font size=2>Protokoll: azure-tabeller <br>Autentisering: {azure-åtkomstnyckel} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domän <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;konto</font>
      </td>
    </tr>
    <tr>
      <td>Azure Storage</td>
      <td>Tabell</td>
      <td>Tabell</td>
      <td>
        <font size=2>Protokoll: azure-tabeller <br>Autentisering: {azure-åtkomstnyckel} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;domän <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;konto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Namn</font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Behållare</td>
      <td>Virtuellt kluster</td>
      <td>
        <font size=2>Protokoll: cosmos <br>Autentisering: {basic, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL: en</font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Tabell</td>
      <td>Stream, stream set vy</td>
      <td>
        <font size=2>Protokoll: cosmos <br>Autentisering: {basic, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL: en</font>
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>Behållare</td>
      <td>plats</td>
      <td>
        <font size=2>Protokoll: http <br>Autentisering: {none, basic, windows, oauth} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL: en</font>
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>Rapport</td>
      <td>Rapporten, instrumentpanel</td>
      <td>
        <font size=2>Protokoll: http <br>Autentisering: {none, basic, windows, oauth} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL: en</font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>Behållare</td>
      <td>Databas</td>
      <td>
        <font size=2>Protokoll: db2 <br>Autentisering: {basic, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen</font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>Tabell</td>
      <td>Tabellen, vyn</td>
      <td>
        <font size=2>Protokoll: db2 <br>Autentisering: {basic, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektet <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schemat</font>
      </td>
    </tr>
    <tr>
      <td>Filsystem</td>
      <td>Tabell</td>
      <td>Fil</td>
      <td>
        <font size=2>Protokoll: filen <br>Autentisering: {none, basic, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sökväg</font>
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>Tabell</td>
      <td>Katalogen fil</td>
      <td>
        <font size=2>Protokoll: ftp <br>Autentisering: {none, basic, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL: en</font>
      </td>
    </tr>
    <tr>
      <td>Hadoop Distributed File System</td>
      <td>Behållare</td>
      <td>Kluster</td>
      <td>
        <font size=2>Protokoll: webhdfs <br>Autentisering: {basic, oauth} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL: en</font>
      </td>
    </tr>
    <tr>
      <td>Hadoop Distributed File System</td>
      <td>Tabell</td>
      <td>Katalogen fil</td>
      <td>
        <font size=2>Protokoll: webhdfs <br>Autentisering: {basic, oauth} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL: en</font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Behållare</td>
      <td>Databas</td>
      <td>
        <font size=2>Protokoll: hive <br>Autentisering: {HDInsight, basic, username, ingen} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serverProtocol: {hive2}</font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Tabell</td>
      <td>Tabellen, vyn</td>
      <td>
        <font size=2>Protokoll: hive <br>Autentisering: {HDInsight, basic, username, ingen} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektet <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serverProtocol: {hive2}</font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Behållare</td>
      <td>plats</td>
      <td>
        <font size=2>Protokoll: http <br>Autentisering: {none, basic, windows, oauth} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL: en</font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Rapport</td>
      <td>Rapporten, instrumentpanel</td>
      <td>
        <font size=2>Protokoll: http <br>Autentisering: {none, basic, windows, oauth} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL: en</font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Tabell</td>
      <td>Slutpunkt, fil</td>
      <td>
        <font size=2>Protokoll: http <br>Autentisering: {none, basic, windows, oauth} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL: en</font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Behållare</td>
      <td>Databas</td>
      <td>
        <font size=2>Protokoll: mysql <br>Autentisering: {protokoll, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen</font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Tabell</td>
      <td>Tabellen, vyn</td>
      <td>
        <font size=2>Protokoll: mysql <br>Autentisering: {protokoll, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektet</font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Behållare</td>
      <td>Entitetsbehållaren</td>
      <td>
        <font size=2>Protokoll: odata <br>Autentisering: {none, basic, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL: en</font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Tabell</td>
      <td>Entitetsuppsättningen funktion</td>
      <td>
        <font size=2>Protokoll: odata <br>Autentisering: {none, basic, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL: en <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;resursen</font>
      </td>
    </tr>
    <tr>
      <td>Oracle-databas</td>
      <td>Behållare</td>
      <td>Databas</td>
      <td>
        <font size=2>Protokoll: oracle <br>Autentisering: {protokoll, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen</font>
      </td>
    </tr>
    <tr>
      <td>Oracle-databas</td>
      <td>Tabell</td>
      <td>Tabellen, vyn</td>
      <td>
        <font size=2>Protokoll: oracle <br>Autentisering: {protokoll, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schemat <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektet</font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Behållare</td>
      <td>Databas</td>
      <td>
        <font size=2>Protokoll: postgresql <br>Autentisering: {basic, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen</font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Tabell</td>
      <td>Tabellen, vyn</td>
      <td>
        <font size=2>Protokoll: postgresql <br>Autentisering: {basic, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schemat <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektet</font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Behållare</td>
      <td>plats</td>
      <td>
        <font size=2>Protokoll: http <br>Autentisering: {none, basic, windows, oauth} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL: en</font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Rapport</td>
      <td>Rapporten, instrumentpanel</td>
      <td>
        <font size=2>Protokoll: http <br>Autentisering: {none, basic, windows, oauth} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL: en</font>
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>Tabell</td>
      <td>Data mashup</td>
      <td>
        <font size=2>Protokoll: power-fråga <br>Autentisering: {oauth} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL: en</font>
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>Tabell</td>
      <td>Objekt</td>
      <td>
        <font size=2>Protokoll: salesforce-com <br>Autentisering: {basic, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;loginServer <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;klass <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;itemName</font>
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>Behållare</td>
      <td>Server</td>
      <td>
        <font size=2>Protokoll: sap hana-sql <br>Autentisering: {protokoll, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server</font>
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>Tabell</td>
      <td>Visa</td>
      <td>
        <font size=2>Protokoll: sap hana-sql <br>Autentisering: {protokoll, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schemat <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektet</font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>Tabell</td>
      <td>Visa lista</td>
      <td>
        <font size=2>Protokoll: sharepoint-lista över <br>Autentisering: {basic, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL: en</font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Kommando</td>
      <td>Lagrad procedur</td>
      <td>
        <font size=2>Protokoll: tds <br>Autentisering: {protokoll, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schemat <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektet</font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>TableValuedFunction</td>
      <td>Tabellvärdesfunktion</td>
      <td>
        <font size=2>Protokoll: tds <br>Autentisering: {protokoll, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schemat <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektet</font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Behållare</td>
      <td>Databas</td>
      <td>
        <font size=2>Protokoll: tds <br>Autentisering: {protokoll, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen</font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Tabell</td>
      <td>Tabellen, vyn</td>
      <td>
        <font size=2>Protokoll: tds <br>Autentisering: {protokoll, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schemat <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektet</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Kommando</td>
      <td>Lagrad procedur</td>
      <td>
        <font size=2>Protokoll: tds <br>Autentisering: {protokoll, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schemat <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektet</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>Tabellvärdesfunktion</td>
      <td>
        <font size=2>Protokoll: tds <br>Autentisering: {protokoll, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schemat <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektet</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Behållare</td>
      <td>Databas</td>
      <td>
        <font size=2>Protokoll: tds <br>Autentisering: {protokoll, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Tabell</td>
      <td>Tabellen, vyn</td>
      <td>
        <font size=2>Protokoll: tds <br>Autentisering: {protokoll, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schemat <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektet</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services flerdimensionella</td>
      <td>Behållare</td>
      <td>Modellen</td>
      <td>
        <font size=2>Protokoll: Analystjänster <br>Autentisering: {windows, basic, anonyma, ingen} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modellen</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services flerdimensionella</td>
      <td>KPI: N</td>
      <td>KPI: N</td>
      <td>
        <font size=2>Protokoll: Analystjänster <br>Autentisering: {windows, basic, anonyma, ingen} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modellen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektet <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {KPI}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services flerdimensionella</td>
      <td>Mått</td>
      <td>Mått</td>
      <td>
        <font size=2>Protokoll: Analystjänster <br>Autentisering: {windows, basic, anonyma, ingen} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modellen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektet <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {Measure}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services flerdimensionella</td>
      <td>Tabell</td>
      <td>Dimensionen</td>
      <td>
        <font size=2>Protokoll: Analystjänster <br>Autentisering: {windows, basic, anonyma, ingen} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modellen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektet <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {dimensionen}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services tabular</td>
      <td>Behållare</td>
      <td>Modellen</td>
      <td>
        <font size=2>Protokoll: Analystjänster <br>Autentisering: {windows, basic, anonyma, ingen} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modellen</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services tabular</td>
      <td>KPI: N</td>
      <td>KPI: N</td>
      <td>
        <font size=2>Protokoll: Analystjänster <br>Autentisering: {windows, basic, anonyma, ingen} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modellen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektet <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {KPI}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services tabular</td>
      <td>Mått</td>
      <td>Mått</td>
      <td>
        <font size=2>Protokoll: Analystjänster <br>Autentisering: {windows, basic, anonyma, ingen} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modellen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektet <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {Measure}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services tabular</td>
      <td>Tabell</td>
      <td>Tabell</td>
      <td>
        <font size=2>Protokoll: Analystjänster <br>Autentisering: {windows, basic, anonyma, ingen} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modellen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektet <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {Table}</font>
      </td>
    </tr>
    <tr>
      <td>SQLServer Reporting Services</td>
      <td>Behållare</td>
      <td>Server</td>
      <td>
        <font size=2>Protokoll: Rapporteringstjänster <br>Autentisering: {windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;version: {ReportingService2010}</font>
      </td>
    </tr>
    <tr>
      <td>SQLServer Reporting Services</td>
      <td>Rapport</td>
      <td>Rapport</td>
      <td>
        <font size=2>Protokoll: Rapporteringstjänster <br>Autentisering: {windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;sökväg <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;version: {ReportingService2010}</font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Behållare</td>
      <td>Databas</td>
      <td>
        <font size=2>Protokoll: teradata <br>Autentisering: {protokoll, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen</font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Tabell</td>
      <td>Tabellen, vyn</td>
      <td>
        <font size=2>Protokoll: teradata <br>Autentisering: {protokoll, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektet</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Behållare</td>
      <td>Modellen</td>
      <td>
        <font size="2">Protokoll: mssql-mds <br>Autentisering: {windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL: en <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modellen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;version</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Tabell</td>
      <td>Entitet</td>
      <td>
        <font size="2">Protokoll: mssql-mds <br>Autentisering: {windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL: en <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modellen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;version <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;entitet</font>
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>Behållare</td>
      <td>Databas</td>
      <td>
        <font size=2>Protokoll: dokument-db <br>Autentisering: {azure-åtkomstnyckel} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL: en <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen</font>
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>Samling</td>
      <td>Samling</td>
      <td>
        <font size=2>Protokoll: dokument-db <br>Autentisering: {azure-åtkomstnyckel} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL: en <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;samlingen</font>
      </td>
    </tr>
    <tr>
      <td>ODBC (allmän)</td>
      <td>Behållare</td>
      <td>Databas</td>
      <td>
        <font size=2>Protokoll: odbc <br>Autentisering: {basic, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;alternativ <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen</font>
      </td>
    </tr>
    <tr>
      <td>ODBC (allmän)</td>
      <td>Tabell</td>
      <td>Tabellen, vyn</td>
      <td>
        <font size=2>Protokoll: odbc <br>Autentisering: {basic, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;alternativ <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektet <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schemat</font>
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>Behållare</td>
      <td>Databas</td>
      <td>
        <font size=2>protokoll: sybase <br>Autentisering: {basic, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen</font>
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>Tabell</td>
      <td>Tabellen, vyn</td>
      <td>
        <font size=2>protokoll: sybase <br>Autentisering: {basic, windows} <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;databasen <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schemat <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objektet</font>
      </td>
    </tr>
    <tr>
      <td>Andra (inget av ovanstående)</td>
      <td>\*</td>
      <td>\*</td>
      <td>
        <font size=2>Protokoll: generisk tillgångar <br>Adress: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;assetId</font>
      </td>
    </tr>
</table>
