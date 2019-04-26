---
title: Anslut till IBM DB2 - Azure Logic Apps
description: 'Hantera resurser med IBM DB2 REST API: er och Azure Logic Apps'
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: plarsen, LADocs
ms.topic: article
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 7785d1788e8d5e9b432a8189345f293ebf05ef7c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60314229"
---
# <a name="manage-ibm-db2-resources-with-azure-logic-apps"></a>Hantera IBM DB2-resurser med Azure Logic Apps

Du kan skapa automatiserade uppgifter och arbetsflöden baserat på de resurser som lagras i DB2-databas med Azure Logic Apps och IBM DB2-koppling. Dina arbetsflöden kan ansluta till resurser i din databas, läsa och lista databastabeller, lägga till rader, ändra rader och ta bort rader. Du kan inkludera åtgärder i dina logikappar som får svar från databasen och göra utdata som är tillgänglig för andra åtgärder.

Den här artikeln visar hur du kan skapa en logikapp som utför olika databasoperationer. Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="supported-platforms-and-versions"></a>Plattformar som stöds och versioner

DB2-koppling innehåller en Microsoft-klient som kommunicerar med DB2 fjärrservrar i ett TCP/IP-nätverk. Du kan använda den här anslutningen för att komma åt molndatabaser, till exempel IBM Bluemix dashDB eller IBM DB2 för Windows som körs i Azure-virtualisering. Du kan också komma åt lokala DB2-databaser efter att du [installera och konfigurera den lokala datagatewayen](../logic-apps/logic-apps-gateway-connection.md).

IBM DB2-anslutningsappen stöder dessa IBM DB2-plattformar och versioner tillsammans med IBM DB2 kompatibla produkter, t.ex IBM Bluemix dashDB som har stöd för distribuerade Relational Database Architecture (DRDA) SQL Access Manager (SQLAM) version 10 och 11:

| Plattform | Version | 
|----------|---------|
| IBM DB2 för z/OS | 11.1, 10.1 |
| IBM DB2 för jag | 7.3, 7.2, 7.1 |
| IBM DB2 för LUW | 11, 10.5 |
|||

## <a name="supported-database-operations"></a>Stöds databasoperationer

IBM DB2-anslutningsappen stöder dessa databasåtgärder, som mappar till motsvarande åtgärder i anslutningen:

| Databasåtgärden | Anslutningstjänsten åtgärd |
|--------------------|------------------|
| Lista databastabeller | Hämta tabeller |
| Läsa en rad med väljer | Hämta rad |
| Läsa alla rader med väljer | Hämta rader |
| Lägga till en rad med INSERT | Infoga rad |
| Redigera en rad med hjälp av UPDATE | Uppdatera raden |
| Ta bort en rad med DELETE | Ta bort rad |
|||

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>.

* En IBM DB2-databasen, antingen molnbaserade eller lokala

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt DB2-databas. Den här anslutningstjänsten tillhandahåller endast åtgärder, så att starta logikappen, Välj en separat utlösare, till exempel, **upprepning** utlösaren.
I exemplen i den här artikeln i **upprepning** utlösaren.

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>Lägg till åtgärd för DB2 - Get-tabeller

1. I den [Azure-portalen](https://portal.azure.com), öppna logikappen i Logic App Designer, om inte redan är öppen.

1. Under utlösaren väljer **nytt steg**.

1. I sökrutan anger du ”db2” som filter. Välj den här åtgärden i det här exemplet under åtgärder: **Hämta tabeller (förhandsversion)**

   ![Välj åtgärd](./media/connectors-create-api-db2/select-db2-action.png)

   Nu uppmanas du att ange anslutningsinformation för DB2-databas.

1. Följ stegen för att skapa anslutningar för [databaser i molnet](#cloud-connection) eller [lokala databaser](#on-premises-connection).

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>Ansluta till DB2-molnet

Om du vill konfigurera anslutningen, ange den här anslutningsinformationen när du uppmanas, Välj **skapa**, och sedan spara din logikapp:

| Egenskap  | Krävs | Beskrivning |
|----------|----------|-------------|
| **Ansluta via en lokal gateway** | Nej | Gäller endast för lokala anslutningar. |
| **Anslutningsnamn** | Ja | Namn på anslutningen, till exempel ”MyLogicApp – DB2-anslutning” |
| **Server** | Ja | Adress eller alias kolon portnumret för DB2-servern, till exempel ”myDB2server.cloudapp.net:50000” <p><p>**Obs!** Det här värdet är en sträng som representerar en TCP/IP-adress eller alias, antingen i IPv4 eller IPv6-format, följt av ett kolon och ett TCP/IP-portnummer. |
| **Databas** | Ja | Namn för din databas <p><p>**Obs!** Det här värdet är en sträng som representerar en DRDA relationell databas namn (RDBNAM): <p>-DB2 z/OS accepterar en 16-bytes sträng där databasen är känt som ”IBM DB2 för z/OS” plats. <br>-DB2 för i godkänner en byte-18-sträng där databasen kallas en ”IBM DB2 för jag” relationsdatabas. <br>-DB2 för LUW accepterar en 8 byte-sträng. |
| **Användarnamn** | Ja | Användarnamnet för databasen <p><p>**Obs!** Det här värdet är en sträng vars längd är baserat på specifika databasen: <p><p>-DB2 z/OS accepterar en 8 byte-sträng. <br>-DB2 för i godkänner en 10-byte-sträng. <br>-DB2 för Linux eller UNIX accepterar en 8 byte-sträng. <br>-DB2 för Windows godkänner en 30-byte-sträng. |
| **Lösenord** | Ja | Ditt lösenord för databasen |
||||

Exempel:

![Anslutningsinformationen för molnbaserade databaser](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>Anslut till den lokala DB2

Innan du skapar anslutningen måste har du redan din lokala datagateway installerad. Annars kan har du inte konfigurerat din anslutning. Om du har din gatewayinstallationen kan fortsätta att tillhandahålla här anslutningsinformationen och välj sedan **skapa**.

| Egenskap  | Krävs | Beskrivning |
|----------|----------|-------------|
| **Ansluta via en lokal gateway** | Ja | Gäller när du vill att en lokal anslutning och visar lokalt anslutningsegenskaper. |
| **Anslutningsnamn** | Ja | Namn på anslutningen, till exempel ”MyLogicApp – DB2-anslutning” | 
| **Server** | Ja | Adress eller alias kolon portnumret för DB2-servern, till exempel ”myDB2server:50000” <p><p>**Obs!** Det här värdet är en sträng som representerar en TCP/IP-adress eller alias, antingen i IPv4 eller IPv6-format, följt av ett kolon och ett TCP/IP-portnummer. |
| **Databas** | Ja | Namn för din databas <p><p>**Obs!** Det här värdet är en sträng som representerar en DRDA relationell databas namn (RDBNAM): <p>-DB2 z/OS accepterar en 16-bytes sträng där databasen är känt som ”IBM DB2 för z/OS” plats. <br>-DB2 för i godkänner en byte-18-sträng där databasen kallas en ”IBM DB2 för jag” relationsdatabas. <br>-DB2 för LUW accepterar en 8 byte-sträng. |
| **Autentisering** | Ja | Autentiseringstyp för anslutningen, till exempel ”Basic” <p><p>**Obs!** Välj det här värdet i listan som innehåller grundläggande eller Windows (Kerberos). |
| **Användarnamn** | Ja | Användarnamnet för databasen <p><p>**Obs!** Det här värdet är en sträng vars längd är baserat på specifika databasen: <p><p>-DB2 z/OS accepterar en 8 byte-sträng. <br>-DB2 för i godkänner en 10-byte-sträng. <br>-DB2 för Linux eller UNIX accepterar en 8 byte-sträng. <br>-DB2 för Windows godkänner en 30-byte-sträng. |
| **Lösenord** | Ja | Ditt lösenord för databasen |
| **Gateway** | Ja | Namn för din installerade lokala datagateway <p><p>**Obs!** Välj det här värdet i listan som innehåller alla installerade datagatewayer i din Azure-prenumeration och resursgrupp. |
||||

Exempel:

![Anslutningsinformationen för lokala databaser](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>Visa utdatatabeller

Om du vill köra logikappen manuellt på verktygsfältet för appdesignern väljer **kör**. När logikappen är klar kan du visa utdata från körningen.

1. På logikappmenyn, väljer **översikt**.

1. Under **sammanfattning**i den **Körningshistorik** väljer du den senaste körningen, vilket är det första objektet i listan.

   ![Visa körningshistorik](./media/connectors-create-api-db2/run-history.png)

1. Under **logikappskörningen**, nu kan du granska status indata, och utdata för varje steg i din logikapp.
Expandera den **Hämta tabeller** åtgärd.

   ![Expandera åtgärden](./media/connectors-create-api-db2/expand-action-step.png)

1. Välj för att visa indata **visa råindata**.

1. Välj för att visa utdata **visa råutdata**.

   Utdata innehåller en lista över tabeller.

   ![Visa utdatatabeller](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>Hämta rad

Om du vill hämta en post i en DB2-tabell i databasen, använder den **hämta rad** åtgärd i din logikapp. Den här åtgärden körs en DB2 `SELECT WHERE` instruktionen, till exempel `SELECT FROM AREA WHERE AREAID = '99999'`.

1. Om du aldrig har använt DB2 åtgärder före i din logikapp, granska stegen i den [lägga till DB2 åtgärd - Get-tabeller](#add-db2-action) avsnittet, men Lägg till den **hämta rad** åtgärd i stället och sedan komma tillbaka hit om du vill fortsätta.

   När du lägger till den **hämta rad** åtgärd, här är hur logikappen exempel visas:

   ![Hämta rad åtgärd](./media/connectors-create-api-db2/db2-get-row-action.png)

1. Ange värden för alla nödvändiga egenskaper (*). När du har valt en tabell visar åtgärden relevanta egenskaper som är specifika för poster i tabellen.

   | Egenskap  | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Tabellnamn** | Ja | Den tabell som innehåller posten du önskar, såsom ”området” i det här exemplet |
   | **Områdes-ID** | Ja | ID för posten du önskar, såsom ”99999” i det här exemplet |
   ||||

   ![Välj tabell](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. När du är klar på verktygsfältet för appdesignern väljer **spara**.

### <a name="view-output-row"></a>Visa utdata rad

Om du vill köra logikappen manuellt på verktygsfältet för appdesignern väljer **kör**. När logikappen är klar kan du visa utdata från körningen.

1. På logikappmenyn, väljer **översikt**.

1. Under **sammanfattning**i den **Körningshistorik** väljer du den senaste körningen, vilket är det första objektet i listan.

1. Under **logikappskörningen**, nu kan du granska status indata, och utdata för varje steg i din logikapp.
Expandera den **hämta rad** åtgärd.

1. Välj för att visa indata **visa råindata**.

1. Välj för att visa utdata **visa råutdata**.

   Utdata innehåller den angivna raden.

   ![Visa utdata rad](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>Hämta rader

Om du vill hämta alla poster i en DB2-tabell i databasen, använder den **hämta rader** åtgärd i din logikapp. Den här åtgärden körs en DB2 `SELECT` instruktionen, till exempel `SELECT * FROM AREA`.

1. Om du aldrig har använt DB2 åtgärder före i din logikapp, granska stegen i den [lägga till DB2 åtgärd - Get-tabeller](#add-db2-action) avsnittet, men Lägg till den **hämta rader** åtgärd i stället och sedan komma tillbaka hit om du vill fortsätta.

   När du lägger till den **hämta rader** åtgärd, här är hur logikappen exempel visas:

   ![Hämta rader åtgärd](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. Öppna den **tabellnamn** och välj den tabell som du vill, vilket är ”området” i det här exemplet:

   ![Välj tabell](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. Om du vill ange filter och frågor för resultat, Välj **visa avancerade alternativ**.

1. När du är klar på verktygsfältet för appdesignern väljer **spara**.

### <a name="view-output-rows"></a>Visa utdata rader

Om du vill köra logikappen manuellt på verktygsfältet för appdesignern väljer **kör**. När logikappen är klar kan du visa utdata från körningen.

1. På logikappmenyn, väljer **översikt**.

1. Under **sammanfattning**i den **Körningshistorik** väljer du den senaste körningen, vilket är det första objektet i listan.

1. Under **logikappskörningen**, nu kan du granska status indata, och utdata för varje steg i din logikapp.
Expandera den **hämta rader** åtgärd.

1. Välj för att visa indata **visa råindata**.

1. Välj för att visa utdata **visa råutdata**.

   Utdata innehåller alla poster i den angivna tabellen.

   ![Visa utdata rader](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>Infoga rad

Lägg till en enskild post i en DB2-tabell i databasen genom att använda den **infogningsraden** åtgärd i din logikapp. Den här åtgärden körs en DB2 `INSERT` instruktionen, till exempel `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

1. Om du aldrig har använt DB2 åtgärder före i din logikapp, granska stegen i den [lägga till DB2 åtgärd - Get-tabeller](#add-db2-action) avsnittet, men Lägg till den **infogningsraden** åtgärd i stället och sedan komma tillbaka hit om du vill fortsätta.

   När du lägger till den **infogningsraden** åtgärd, här är hur logikappen exempel visas:

   ![Infoga rad åtgärd](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. Ange värden för alla nödvändiga egenskaper (*). När du har valt en tabell visar åtgärden relevanta egenskaper som är specifika för poster i tabellen.

   Här följer egenskaperna för det här exemplet:

   | Egenskap  | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Tabellnamn** | Ja | Tabellen var du vill lägga till posten, till exempel ”-område |
   | **Områdes-ID** | Ja | ID för området för att lägga till, till exempel ”99999” |
   | **Beskrivning av** | Ja | Beskrivningen av området att lägga till, till exempel ”området 99999” |
   | **Region-ID** | Ja | ID: T för regionen som att lägga till, till exempel ”102” |
   |||| 

   Exempel:

   ![Välj tabell](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. När du är klar på verktygsfältet för appdesignern väljer **spara**.

### <a name="view-insert-row-outputs"></a>Visa Infoga rad utdata

Om du vill köra logikappen manuellt på verktygsfältet för appdesignern väljer **kör**. När logikappen är klar kan du visa utdata från körningen.

1. På logikappmenyn, väljer **översikt**.

1. Under **sammanfattning**i den **Körningshistorik** väljer du den senaste körningen, vilket är det första objektet i listan.

1. Under **logikappskörningen**, nu kan du granska status indata, och utdata för varje steg i din logikapp.
Expandera den **infogningsraden** åtgärd.

1. Välj för att visa indata **visa råindata**.

1. Välj för att visa utdata **visa råutdata**.

   Utdata innehåller den post som du har lagt till till den angivna tabellen.

   ![Visa utdata med infogade raden](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>Uppdatera raden

Uppdatera en enskild post i en DB2-tabell i databasen med den **Uppdatera rad** åtgärd i din logikapp. Den här åtgärden körs en DB2 `UPDATE` instruktionen, till exempel `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)`.

1. Om du aldrig har använt DB2 åtgärder före i din logikapp, granska stegen i den [lägga till DB2 åtgärd - Get-tabeller](#add-db2-action) avsnittet, men Lägg till den **Uppdatera rad** åtgärd i stället och sedan komma tillbaka hit om du vill fortsätta.

   När du lägger till den **Uppdatera rad** åtgärd, här är hur logikappen exempel visas:

   ![Uppdatera raden åtgärd](./media/connectors-create-api-db2/db2-update-row-action.png)

1. Ange värden för alla nödvändiga egenskaper (*). När du har valt en tabell visar åtgärden relevanta egenskaper som är specifika för poster i tabellen.

   Här följer egenskaperna för det här exemplet:

   | Egenskap  | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Tabellnamn** | Ja | Tabellen var du vill uppdatera posten, till exempel ”-område |
   | **Rad-ID** | Ja | ID för posten som ska uppdateras, till exempel ”99999” |
   | **Områdes-ID** | Ja | Det nya område-ID, till exempel ”99999” |
   | **Beskrivning av** | Ja | Den nya området beskrivningen, till exempel ”uppdaterad 99999” |
   | **Region-ID** | Ja | Den nya region-ID, till exempel ”102” |
   ||||

   Exempel:

   ![Välj tabell](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. När du är klar på verktygsfältet för appdesignern väljer **spara**.

### <a name="view-update-row-outputs"></a>Visa Uppdatera rad matar ut

Om du vill köra logikappen manuellt på verktygsfältet för appdesignern väljer **kör**. När logikappen är klar kan du visa utdata från körningen.

1. På logikappmenyn, väljer **översikt**.

1. Under **sammanfattning**i den **Körningshistorik** väljer du den senaste körningen, vilket är det första objektet i listan.

1. Under **logikappskörningen**, nu kan du granska status indata, och utdata för varje steg i din logikapp.
Expandera den **Uppdatera rad** åtgärd.

1. Välj för att visa indata **visa råindata**.

1. Välj för att visa utdata **visa råutdata**.

   Utdata innehåller den post som du har uppdaterat i den angivna tabellen.

   ![Visa utdata med uppdaterade raden](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>Ta bort rad

Ta bort en post från en DB2-tabell i databasen genom att använda den **ta bort rad** åtgärd i din logikapp. Den här åtgärden körs en DB2 `DELETE` instruktionen, till exempel `DELETE FROM AREA WHERE AREAID = '99999'`.

1. Om du aldrig har använt DB2 åtgärder före i din logikapp, granska stegen i den [lägga till DB2 åtgärd - Get-tabeller](#add-db2-action) avsnittet, men Lägg till den **ta bort rad** åtgärd i stället och sedan komma tillbaka hit om du vill fortsätta.

   När du lägger till den **ta bort rad** åtgärd, här är hur logikappen exempel visas:

   ![Ta bort raden åtgärd](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. Ange värden för alla nödvändiga egenskaper (*). När du har valt en tabell visar åtgärden relevanta egenskaper som är specifika för poster i tabellen.

   Här följer egenskaperna för det här exemplet:

   | Egenskap  | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Tabellnamn** | Ja | Tabellen var du vill ta bort posten, till exempel ”-område |
   | **Rad-ID** | Ja | ID för posten som ska ta bort, till exempel ”99999” |
   ||||

   Exempel:

   ![Välj tabell](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. När du är klar på verktygsfältet för appdesignern väljer **spara**.

### <a name="view-delete-row-outputs"></a>Visa ta bort raden utdata

Om du vill köra logikappen manuellt på verktygsfältet för appdesignern väljer **kör**. När logikappen är klar kan du visa utdata från körningen.

1. På logikappmenyn, väljer **översikt**.

1. Under **sammanfattning**i den **Körningshistorik** väljer du den senaste körningen, vilket är det första objektet i listan.

1. Under **logikappskörningen**, nu kan du granska status indata, och utdata för varje steg i din logikapp.
Expandera den **ta bort rad** åtgärd.

1. Välj för att visa indata **visa råindata**.

1. Välj för att visa utdata **visa råutdata**.

   Utdata innehåller inte längre den post som du har tagits bort från den angivna tabellen.

   ![Visa utdata utan borttagen rad](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare och åtgärder gränser, som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) fil, finns i den [anslutningsappens-referenssida](/connectors/db2/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
