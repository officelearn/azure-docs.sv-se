---
title: Få tillgång till och hantera IBM DB2-resurser
description: Läsa, redigera, uppdatera och hantera IBM DB2-resurser genom att skapa automatiserade arbetsflöden med Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: plarsen, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 32b482607827ee4420e39b1936586d64f9ea3139
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651390"
---
# <a name="access-and-manage-ibm-db2-resources-by-using-azure-logic-apps"></a>Komma åt och hantera IBM DB2-resurser med hjälp av Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och IBM [DB2-anslutningsappen](/connectors/db2/)kan du skapa automatiserade uppgifter och arbetsflöden baserat på de resurser som lagras i DIN DB2-databas. Dina arbetsflöden kan ansluta till resurserna i databasen, läsa och lista databastabellerna, lägga till rader, ändra rader, ta bort rader med mera. Du kan inkludera åtgärder i logikapparna som får svar från databasen och gör utdata tillgängliga för andra åtgärder.

Den här artikeln visar hur du kan skapa en logikapp som utför olika databasåtgärder. Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="supported-platforms-and-versions"></a>Plattformar och versioner som stöds

DB2-kontakten innehåller en Microsoft-klient som kommunicerar med fjärr-DB2-servrar över ett TCP/IP-nätverk. Du kan använda den här anslutningen för att komma åt molndatabaser som IBM DB2 för Windows som körs i Azure-virtualisering. Du kan också komma åt lokala DB2-databaser när du [har installerat och konfigurerat den lokala datagatewayen](../logic-apps/logic-apps-gateway-connection.md).

IBM DB2-kontakten stöder dessa IBM DB2-plattformar och -versioner tillsammans med IBM DB2-kompatibla produkter som stöder DRDA-versioner (Distributed Relational Database Architecture) (SQLAM) 10 och 11:

| Plattform | Version | 
|----------|---------|
| IBM DB2 för z/OS | 11.1, 10.1 |
| IBM DB2 för i | 7.3, 7.2, 7.1 |
| IBM DB2 för LUW | 11, 10.5 |
|||

## <a name="supported-database-operations"></a>Databasåtgärder som stöds

IBM DB2-anslutningen stöder dessa databasåtgärder som mappas till motsvarande åtgärder i kopplingen:

| Databasåtgärd | Kopplingsåtgärd |
|--------------------|------------------|
| Lista databastabeller | Hämta tabeller |
| Läsa en rad med SELECT | Hämta rad |
| Läsa alla rader med SELECT | Hämta rader |
| Lägga till en rad med INSERT | Infoga rad |
| Redigera en rad med UPPDATERING | Uppdatera rad |
| Ta bort en rad med DELETE | Ta bort rad |
|||

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* En IBM DB2-databas, antingen molnbaserad eller lokal

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt din DB2-databas. Den här kopplingen innehåller endast åtgärder, så för att starta logikappen väljer du en separat utlösare, till exempel **utlösaren För upprepning.**
I exemplen i den här artikeln används **utlösaren Återkommande.**

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>Lägg till DB2-åtgärd - Hämta tabeller

1. Öppna logikappen i Logic App Designer i [Azure-portalen,](https://portal.azure.com)om den inte redan är öppen.

1. Under utlösaren väljer du **Nytt steg**.

1. Skriv "db2" som filter i sökrutan. I det här exemplet väljer du den här åtgärden under åtgärdslistan: **Hämta tabeller (Förhandsgranska)**

   ![Välj åtgärd](./media/connectors-create-api-db2/select-db2-action.png)

   Du uppmanas nu att ange anslutningsinformation för DB2-databasen.

1. Följ stegen för att skapa anslutningar för [molndatabaser](#cloud-connection) eller [lokala databaser](#on-premises-connection).

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>Anslut till cloud DB2

Om du vill konfigurera anslutningen anger du de här anslutningsinformationen när du uppmanas att göra det, väljer **Skapa**och sparar sedan logikappen:

| Egenskap | Krävs | Beskrivning |
|----------|----------|-------------|
| **Anslut via lokal gateway** | Inga | Gäller endast för lokala anslutningar. |
| **Anslutningsnamn** | Ja | Namnet på din anslutning, till exempel "MyLogicApp-DB2-connection" |
| **Server** | Ja | Adressen eller aliaskolonportnumret för DB2-servern, till exempel "myDB2server.cloudapp.net:50000" <p><p>**Det**här värdet är en sträng som representerar en TCP/IP-adress eller ett alias, antingen i IPv4- eller IPv6-format, följt av ett kolon och ett TCP/IP-portnummer. |
| **Databas** | Ja | Namnet på databasen <p><p>**Det**här värdet är en sträng som representerar ett DRDA-relationsdatabasnamn (RDBNAM): <p>- DB2 för z/OS accepterar en sträng på 16 byte där databasen kallas "IBM DB2 för z/OS"-plats. <br>- DB2 för jag accepterar en 18-byte sträng där databasen är känd som en "IBM DB2 för i" relationsdatabas. <br>- DB2 för LUW accepterar en 8-byte sträng. |
| **Användarnamn** | Ja | Ditt användarnamn för databasen <p><p>**Obs:** Det här värdet är en sträng vars längd baseras på den specifika databasen: <p><p>- DB2 för z /OS accepterar en 8-byte sträng. <br>- DB2 för jag accepterar en 10-byte sträng. <br>- DB2 för Linux eller UNIX accepterar en 8-byte sträng. <br>- DB2 för Windows accepterar en 30-byte sträng. |
| **Lösenord** | Ja | Ditt lösenord för databasen |
||||

Ett exempel:

![Anslutningsinformation för molnbaserade databaser](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>Anslut till lokala DB2

Innan du skapar anslutningen måste du redan ha den lokala datagatewayen installerad. Annars kan du inte slutföra anslutningen. Om du har installationen av gatewayen fortsätter du att tillhandahålla dessa anslutningsuppgifter och väljer sedan **Skapa**.

| Egenskap | Krävs | Beskrivning |
|----------|----------|-------------|
| **Anslut via lokal gateway** | Ja | Gäller när du vill ha en lokal anslutning och visar de lokala anslutningsegenskaperna. |
| **Anslutningsnamn** | Ja | Namnet på din anslutning, till exempel "MyLogicApp-DB2-connection" | 
| **Server** | Ja | Adressen eller aliaskolonportnumret för DIN DB2-server, till exempel "myDB2server:50000" <p><p>**Det**här värdet är en sträng som representerar en TCP/IP-adress eller ett alias, antingen i IPv4- eller IPv6-format, följt av ett kolon och ett TCP/IP-portnummer. |
| **Databas** | Ja | Namnet på databasen <p><p>**Det**här värdet är en sträng som representerar ett DRDA-relationsdatabasnamn (RDBNAM): <p>- DB2 för z/OS accepterar en sträng på 16 byte där databasen kallas "IBM DB2 för z/OS"-plats. <br>- DB2 för jag accepterar en 18-byte sträng där databasen är känd som en "IBM DB2 för i" relationsdatabas. <br>- DB2 för LUW accepterar en 8-byte sträng. |
| **Autentisering** | Ja | Autentiseringstypen för din anslutning, till exempel "Basic" <p><p>**Välj**det här värdet i listan, som innehåller Basic eller Windows (Kerberos). |
| **Användarnamn** | Ja | Ditt användarnamn för databasen <p><p>**Obs:** Det här värdet är en sträng vars längd baseras på den specifika databasen: <p><p>- DB2 för z /OS accepterar en 8-byte sträng. <br>- DB2 för jag accepterar en 10-byte sträng. <br>- DB2 för Linux eller UNIX accepterar en 8-byte sträng. <br>- DB2 för Windows accepterar en 30-byte sträng. |
| **Lösenord** | Ja | Ditt lösenord för databasen |
| **Gateway** | Ja | Namnet på den installerade lokala datagatewayen <p><p>**Välj**det här värdet i listan, som innehåller alla installerade datagateways i din Azure-prenumeration och resursgrupp. |
||||

Ett exempel:

![Anslutningsinformation för lokala databaser](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>Visa utdatatabeller

Om du vill köra logikappen manuellt väljer du **Kör**i designerverktygsfältet . När logikappen har körts klart kan du visa utdata från körningen.

1. På logikappmenyn väljer du **Översikt**.

1. Under **Sammanfattning**väljer du den senaste körningen i avsnittet **Runs-historik,** som är det första objektet i listan.

   ![Visa körningshistorik](./media/connectors-create-api-db2/run-history.png)

1. Under **Logikappkörning**kan du nu granska status, indata och utdata för varje steg i logikappen.
Expandera åtgärden **Hämta tabeller.**

   ![Expandera åtgärd](./media/connectors-create-api-db2/expand-action-step.png)

1. Om du vill visa indata väljer du **Visa råingångar**.

1. Om du vill visa utdata väljer du **Visa råutdata**.

   Utdata innehåller en lista med tabeller.

   ![Visa utdatatabeller](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>Hämta rad

Om du vill hämta en post i en DB2-databastabell använder du åtgärden **Hämta rad** i logikappen. Den här åtgärden `SELECT WHERE` kör till exempel `SELECT FROM AREA WHERE AREAID = '99999'`en DB2-sats.

1. Om du aldrig har använt DB2-åtgärder tidigare i logikappen läser du stegen i [åtgärdsförhandlingen Lägg till DB2 - Hämta tabeller,](#add-db2-action) men lägger till åtgärden **Hämta rad** i stället och går sedan tillbaka hit för att fortsätta.

   När du har lagt till åtgärden **Hämta rad** är det så här logikappen för exempel visas:

   ![Hämta radåtgärd](./media/connectors-create-api-db2/db2-get-row-action.png)

1. Ange värden för alla önskade egenskaper (*). När du har markerat en tabell visar åtgärden relevanta egenskaper som är specifika för poster i tabellen.

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Tabellnamn** | Ja | Tabellen som har den post du vill använda, till exempel "AREA" i det här exemplet |
   | **Områdes-ID** | Ja | ID:t för den post du vill använda, till exempel "99999" i det här exemplet |
   ||||

   ![Välj tabell](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. När du är klar väljer du **Spara**i designerverktygsfältet .

### <a name="view-output-row"></a>Visa utdatarad

Om du vill köra logikappen manuellt väljer du **Kör**i designerverktygsfältet . När logikappen har körts klart kan du visa utdata från körningen.

1. På logikappmenyn väljer du **Översikt**.

1. Under **Sammanfattning**väljer du den senaste körningen i avsnittet **Runs-historik,** som är det första objektet i listan.

1. Under **Logikappkörning**kan du nu granska status, indata och utdata för varje steg i logikappen.
Expandera åtgärden **Hämta rad.**

1. Om du vill visa indata väljer du **Visa råingångar**.

1. Om du vill visa utdata väljer du **Visa råutdata**.

   Utdata inkluderar den angivna raden.

   ![Visa utdatarad](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>Hämta rader

Om du vill hämta alla poster i en DB2-databastabell använder du åtgärden **Hämta rader** i logikappen. Den här åtgärden `SELECT` kör till exempel `SELECT * FROM AREA`en DB2-sats.

1. Om du aldrig har använt DB2-åtgärder tidigare i logikappen läser du stegen i [åtgärdsfältet Lägg till DB2 - Hämta tabeller,](#add-db2-action) men lägger till åtgärden **Hämta rader** i stället och går sedan tillbaka hit för att fortsätta.

   När du har lagt till åtgärden **Hämta rader** är det så här logikappen för exempel visas:

   ![Hämta åtgärder för rader](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. Öppna listan **Tabellnamn** och välj sedan den tabell du vill använda, som är "AREA" i det här exemplet:

   ![Välj tabell](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. Om du vill ange ett filter eller en fråga för resultat väljer du **Visa avancerade alternativ**.

1. När du är klar väljer du **Spara**i designerverktygsfältet .

### <a name="view-output-rows"></a>Visa utdatarader

Om du vill köra logikappen manuellt väljer du **Kör**i designerverktygsfältet . När logikappen har körts klart kan du visa utdata från körningen.

1. På logikappmenyn väljer du **Översikt**.

1. Under **Sammanfattning**väljer du den senaste körningen i avsnittet **Runs-historik,** som är det första objektet i listan.

1. Under **Logikappkörning**kan du nu granska status, indata och utdata för varje steg i logikappen.
Expandera åtgärden **Hämta rader.**

1. Om du vill visa indata väljer du **Visa råingångar**.

1. Om du vill visa utdata väljer du **Visa råutdata**.

   Utdata innehåller alla poster i den angivna tabellen.

   ![Visa utdatarader](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>Infoga rad

Om du vill lägga till en enskild post i en DB2-databastabell använder du åtgärden **Infoga rad** i logikappen. Den här åtgärden `INSERT` kör till exempel `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`en DB2-sats.

1. Om du aldrig har använt DB2-åtgärder tidigare i logikappen läser du stegen i [åtgärdsfältet Lägg till DB2 - Hämta tabeller,](#add-db2-action) men lägger till åtgärden **Infoga rad** i stället och går sedan tillbaka hit för att fortsätta.

   När du har lagt till åtgärden **Infoga rad** är det så här logikappen för exempel visas:

   ![Infoga radåtgärd](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. Ange värden för alla önskade egenskaper (*). När du har markerat en tabell visar åtgärden relevanta egenskaper som är specifika för poster i tabellen.

   Här är även egenskaperna:

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Tabellnamn** | Ja | Tabellen där posten ska läggas till, till exempel "AREA" |
   | **Områdes-ID** | Ja | ID:t för området att lägga till, till exempel "99999" |
   | **Områdesbeskrivning** | Ja | Beskrivningen för det område som ska läggas till, till exempel "Area 99999" |
   | **Region-ID** | Ja | ID:t för regionen att lägga till, till exempel "102" |
   |||| 

   Ett exempel:

   ![Välj tabell](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. När du är klar väljer du **Spara**i designerverktygsfältet .

### <a name="view-insert-row-outputs"></a>Visa infoga radutmatningar

Om du vill köra logikappen manuellt väljer du **Kör**i designerverktygsfältet . När logikappen har körts klart kan du visa utdata från körningen.

1. På logikappmenyn väljer du **Översikt**.

1. Under **Sammanfattning**väljer du den senaste körningen i avsnittet **Runs-historik,** som är det första objektet i listan.

1. Under **Logikappkörning**kan du nu granska status, indata och utdata för varje steg i logikappen.
Expandera åtgärden **Infoga rad.**

1. Om du vill visa indata väljer du **Visa råingångar**.

1. Om du vill visa utdata väljer du **Visa råutdata**.

   Utdata innehåller den post som du har lagt till i den angivna tabellen.

   ![Visa utdata med infogad rad](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>Uppdatera rad

Om du vill uppdatera en enskild post i en DB2-databastabell använder du åtgärden **Uppdatera rad** i logikappen. Den här åtgärden `UPDATE` kör till exempel `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)`en DB2-sats.

1. Om du aldrig har använt DB2-åtgärder tidigare i logikappen läser du stegen i [åtgärdsfältet Lägg till DB2 - Hämta tabeller,](#add-db2-action) men lägger till åtgärden **Uppdatera rad** i stället och går sedan tillbaka hit för att fortsätta.

   När du har lagt till åtgärden **Uppdatera rad** är det så här logikappen för exempel visas:

   ![Uppdatera radåtgärd](./media/connectors-create-api-db2/db2-update-row-action.png)

1. Ange värden för alla önskade egenskaper (*). När du har markerat en tabell visar åtgärden relevanta egenskaper som är specifika för poster i tabellen.

   Här är även egenskaperna:

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Tabellnamn** | Ja | Tabellen där posten ska uppdateras, till exempel "AREA" |
   | **Rad-ID** | Ja | ID:t för posten som ska uppdateras, till exempel "99999" |
   | **Områdes-ID** | Ja | Det nya områdes-ID:t, till exempel "99999" |
   | **Områdesbeskrivning** | Ja | Den nya områdesbeskrivningen, till exempel "Uppdaterad 99999" |
   | **Region-ID** | Ja | Det nya region-ID:t, till exempel "102" |
   ||||

   Ett exempel:

   ![Välj tabell](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. När du är klar väljer du **Spara**i designerverktygsfältet .

### <a name="view-update-row-outputs"></a>Visa utskrifter för uppdateringsrader

Om du vill köra logikappen manuellt väljer du **Kör**i designerverktygsfältet . När logikappen har körts klart kan du visa utdata från körningen.

1. På logikappmenyn väljer du **Översikt**.

1. Under **Sammanfattning**väljer du den senaste körningen i avsnittet **Runs-historik,** som är det första objektet i listan.

1. Under **Logikappkörning**kan du nu granska status, indata och utdata för varje steg i logikappen.
Expandera åtgärden **Uppdatera rad.**

1. Om du vill visa indata väljer du **Visa råingångar**.

1. Om du vill visa utdata väljer du **Visa råutdata**.

   Utdata innehåller posten som du uppdaterade i den angivna tabellen.

   ![Visa utdata med uppdaterad rad](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>Ta bort rad

Om du vill ta bort en enskild post från en DB2-databastabell använder du åtgärden **Ta bort rad** i logikappen. Den här åtgärden `DELETE` kör till exempel `DELETE FROM AREA WHERE AREAID = '99999'`en DB2-sats.

1. Om du aldrig har använt DB2-åtgärder tidigare i logikappen läser du stegen i [åtgärdsfältet Lägg till DB2 - Hämta tabeller,](#add-db2-action) men lägger till åtgärden **Ta bort rad** i stället och går sedan tillbaka hit för att fortsätta.

   När du har lagt till åtgärden **Ta bort rad** är det så här logikappen för exempel visas:

   ![Ta bort radåtgärd](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. Ange värden för alla önskade egenskaper (*). När du har markerat en tabell visar åtgärden relevanta egenskaper som är specifika för poster i tabellen.

   Här är även egenskaperna:

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Tabellnamn** | Ja | Tabellen var posten ska tas bort, till exempel "AREA" |
   | **Rad-ID** | Ja | ID:t för posten som ska tas bort, till exempel "99999" |
   ||||

   Ett exempel:

   ![Välj tabell](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. När du är klar väljer du **Spara**i designerverktygsfältet .

### <a name="view-delete-row-outputs"></a>Visa ta bort radutdata

Om du vill köra logikappen manuellt väljer du **Kör**i designerverktygsfältet . När logikappen har körts klart kan du visa utdata från körningen.

1. På logikappmenyn väljer du **Översikt**.

1. Under **Sammanfattning**väljer du den senaste körningen i avsnittet **Runs-historik,** som är det första objektet i listan.

1. Under **Logikappkörning**kan du nu granska status, indata och utdata för varje steg i logikappen.
Expandera åtgärden **Ta bort rad.**

1. Om du vill visa indata väljer du **Visa råingångar**.

1. Om du vill visa utdata väljer du **Visa råutdata**.

   Utdata innehåller inte längre den post som du har tagit bort från den angivna tabellen.

   ![Visa utdata utan borttagen rad](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här kopplingen, till exempel utlösare, åtgärder och begränsningar enligt beskrivningen i kopplingens Swagger-fil, finns på [kopplingens referenssida](https://docs.microsoft.com/connectors/db2/).

> [!NOTE]
> För logikappar i en [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandegränserna](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)
