---
title: Ansluta till DB2 - Azure Logikappar | Microsoft Docs
description: 'Hantera resurser med DB2 REST API: er och Azure Logic Apps'
author: gplarsen
manager: jeconnoc
ms.author: plarsen
ms.date: 09/26/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, estfan
ms.suite: integration
tags: connectors
ms.openlocfilehash: 507bc48b6b775d6a6fb5f855210d33520e187a74
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295099"
---
# <a name="get-started-with-the-db2-connector"></a>Kom igång med DB2-koppling
Microsoft-anslutaren för DB2 ansluter Logic Apps till resurser som är lagrade i en IBM DB2-databas. Denna koppling inkluderar en Microsoft-klient för att kommunicera med fjärrdatorer för DB2-server i ett TCP/IP-nätverk. Detta omfattar molntjänster databaser, till exempel IBM Bluemix dashDB eller IBM DB2 för Windows körs i Azure virtualisering och lokala databaser med hjälp av lokala datagateway. Finns det [stöds listan](connectors-create-api-db2.md#supported-db2-platforms-and-versions) IBM DB2-plattformar och versioner (i det här avsnittet).

DB2-koppling har stöd för följande databasåtgärder för:

* Lista databastabeller
* Läs en rad med väljer
* Läsa alla rader med väljer
* Lägga till en rad med INSERT
* Ändra en rad med hjälp av UPDATE
* Ta bort en rad med DELETE

Det här avsnittet visar hur du använder kopplingen i en logikapp till databasåtgärder i processen.

Läs mer om Logic Apps i [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="available-actions"></a>Tillgängliga åtgärder
DB2-koppling har stöd för följande logik app åtgärder:

* GetTables
* GetRow
* GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Lista tabeller
Skapa en logikapp för någon åtgärd består av många steg utförs via Microsoft Azure-portalen.

Du kan lägga till en åtgärd i logikappen, att lista tabeller i en DB2-databas. Åtgärden instruerar kopplingen för att bearbeta en DB2 schema-instruktionen, exempelvis `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I den **Azure startar board**väljer **+** (plustecknet) **webb + mobilt**, och sedan **Logikapp**.
2. Ange den **namn**, som `Db2getTables`, **prenumeration**, **resursgruppen**, **plats**, och **Apptjänstplan**. Välj **fäst på instrumentpanelen**, och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I den **Logic Apps Designer**väljer **tom LogicApp** i den **mallar** lista.
2. I den **utlösare** väljer **återkommande**. 
3. I den **upprepning** utlösning, Välj **redigera**väljer **frekvens** listrutan för att välja **dag**, och sedan ange den **intervall** till typen **7**.  
4. Välj den **+ nytt steg** och välj sedan **lägga till en åtgärd**.
5. I den **åtgärder** anger `db2` i den **Sök efter fler åtgärder** redigera och välj sedan **DB2 - Get-tabeller (förhandsgranskning)**.
   
   ![](./media/connectors-create-api-db2/Db2connectorActions.png)  
6. I den **DB2 - Get-tabeller** configuration-fönstret, Välj **kryssrutan** att aktivera **Anslut via lokala datagateway**. Observera att ändra inställningarna från molnet till lokalt.
   
   * Ange värde för **Server**, i form av adress eller alias kolon portnummer. Skriv till exempel `ibmserver01:50000`.
   * Ange värde för **databasen**. Skriv till exempel `nwind`.
   * Välj värde för **autentisering**. Välj exempelvis **grundläggande**.
   * Ange värde för **användarnamn**. Skriv till exempel `db2admin`.
   * Ange värde för **lösenord**. Skriv till exempel `Password1`.
   * Välj värde för **Gateway**. Välj exempelvis **datagateway01**.
7. Välj **skapa**, och välj sedan **spara**. 
   
    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)
8. I den **Db2getTables** bladet, inom den **alla körningar** listan **sammanfattning**, väljer du det första i listan (senaste kör).
9. I den **logikapp kör** bladet väljer **kör information**. I den **åtgärd** väljer **Get_tables**. Visa värdet för **Status**, som borde vara **lyckades**. Välj den **indata länken** att visa indata. Välj den **utdata länken**, och visa utdata, som ska innehålla en lista över tabeller.
   
   ![](./media/connectors-create-api-db2/Db2connectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Skapa anslutningar
Den här anslutningen har stöd för anslutningar till databaser som finns på lokalt och i molnet med hjälp av följande anslutningsegenskaper. 

| Egenskap  | Beskrivning |
| --- | --- |
| server |Krävs. Accepterar ett strängvärde som representerar ett TCP/IP-adress eller ett alias i IPv4- eller IPv6-format, följt (-semikolonavgränsad) av ett TCP/IP-portnummer. |
| databas |Krävs. Accepterar ett strängvärde som representerar en DRDA relationell databas namn (RDBNAM). DB2 för z/OS accepterar en sträng med 16 byte (databas kallas en IBM DB2 för z/OS-plats). DB2 för i5/OS accepterar en 18 byte-sträng (databas kallas en IBM DB2 för i relationell databas). DB2 för LUW accepterar en 8 byte-sträng. |
| autentisering |Valfri. Tillåter lista objekt, grundläggande eller Windows (kerberos). |
| användarnamn |Krävs. Accepterar ett strängvärde. DB2 för z/OS accepterar en 8 byte-sträng. DB2 för i accepterar en 10 byte-sträng. DB2 för Linux eller UNIX accepterar en 8 byte-sträng. DB2 för Windows accepterar en 30-byte-sträng. |
| lösenord |Krävs. Accepterar ett strängvärde. |
| gateway |Krävs. Accepterar en lista över objekt-värde som representerar lokala datagateway definierad för Logic Apps i lagringsgruppen. |

## <a name="create-the-on-premises-gateway-connection"></a>Skapa lokalt gateway-anslutningen
Den här anslutningen kan komma åt en lokala DB2-databas med lokala gateway. Se gateway avsnitt för mer information. 

1. I den **Gateways** configuration-fönstret, Välj **kryssrutan** att aktivera **Anslut via gateway**. Observera att ändra inställningarna från molnet till lokalt.
2. Ange värde för **Server**, i form av adress eller alias kolon portnummer. Skriv till exempel `ibmserver01:50000`.
3. Ange värde för **databasen**. Skriv till exempel `nwind`.
4. Välj värde för **autentisering**. Välj exempelvis **grundläggande**.
5. Ange värde för **användarnamn**. Skriv till exempel `db2admin`.
6. Ange värde för **lösenord**. Skriv till exempel `Password1`.
7. Välj värde för **Gateway**. Välj exempelvis **datagateway01**.
8. Välj **skapa** att fortsätta. 
   
    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Skapa moln-anslutning
Den här anslutningen kan komma åt en molnet DB2-databas. 

1. I den **Gateways** configuration fönstret lämna den **kryssrutan** (oanvända) har inaktiverats **Anslut via gateway**. 
2. Ange värde för **anslutningsnamn**. Skriv till exempel `hisdemo2`.
3. Ange värde för **DB2 servernamn**, i form av adress eller alias kolon portnummer. Skriv till exempel `hisdemo2.cloudapp.net:50000`.
4. Ange värde för **DB2 databasnamnet**. Skriv till exempel `nwind`.
5. Ange värde för **användarnamn**. Skriv till exempel `db2admin`.
6. Ange värde för **lösenord**. Skriv till exempel `Password1`.
7. Välj **skapa** att fortsätta. 
   
    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Hämta alla rader med väljer
Du kan definiera en logik app åtgärd för att hämta alla rader i en DB2-tabell. Detta gör att kopplingen för att bearbeta en DB2 SELECT-instruktion som `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I den **Azure startar board**väljer **+** (plustecknet) **webb + mobilt**, och sedan **Logikapp**.
2. Ange den **namn**, som `Db2getRows`, **prenumeration**, **resursgruppen**, **plats**, och **Apptjänstplan**. Välj **fäst på instrumentpanelen**, och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I den **Logic Apps Designer**väljer **tom LogicApp** i den **mallar** lista.
2. I den **utlösare** väljer **återkommande**. 
3. I den **återkommande** utlösning, Välj **redigera**väljer **frekvens** listrutan för att välja **dag**, och välj sedan **intervall** till typen **7**. 
4. Välj den **+ nytt steg** och välj sedan **lägga till en åtgärd**.
5. I den **åtgärder** anger `db2` i den **Sök efter fler åtgärder** redigera och välj sedan **DB2 - Get-rader (förhandsgranskning)**.
6. I den **hämta rader (förhandsgranskning)** åtgärd, Välj **ändra anslutningen**.
7. I den **anslutningar** configuration-fönstret, Välj **Skapa nytt**. 
   
    ![](./media/connectors-create-api-db2/Db2connectorNewConnection.png)
8. I den **Gateways** configuration fönstret lämna den **kryssrutan** (oanvända) har inaktiverats **Anslut via gateway**.
   
   * Ange värde för **anslutningsnamn**. Skriv till exempel `HISDEMO2`.
   * Ange värde för **DB2 servernamn**, i form av adress eller alias kolon portnummer. Skriv till exempel `HISDEMO2.cloudapp.net:50000`.
   * Ange värde för **DB2 databasnamnet**. Skriv till exempel `NWIND`.
   * Ange värde för **användarnamn**. Skriv till exempel `db2admin`.
   * Ange värde för **lösenord**. Skriv till exempel `Password1`.
9. Välj **skapa** att fortsätta.
   
    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)
10. I den **tabellnamn** väljer den **NEDPIL**, och välj sedan **området**.
11. Alternativt, Välj **visa avancerade alternativ** ange frågealternativ.
12. Välj **Spara**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowsTableName.png)
13. I den **Db2getRows** bladet, inom den **alla körningar** listan **sammanfattning**, väljer du det första i listan (senaste kör).
14. I den **logikapp kör** bladet väljer **kör information**. I den **åtgärd** väljer **Get_rows**. Visa värdet för **Status**, som borde vara **lyckades**. Välj den **indata länken** att visa indata. Välj den **utdata länken**, och visa utdata, som ska innehålla en lista med rader.
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Lägga till en rad med INSERT
Du kan definiera en logik app åtgärd för att lägga till en rad i en DB2-tabell. Den här åtgärden gör kopplingen för att bearbeta en DB2 INSERT-instruktionen, exempelvis `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I den **Azure startar board**väljer **+** (plustecknet) **webb + mobilt**, och sedan **Logikapp**.
2. Ange den **namn**, som `Db2insertRow`, **prenumeration**, **resursgruppen**, **plats**, och **Apptjänstplan**. Välj **fäst på instrumentpanelen**, och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I den **Logic Apps Designer**väljer **tom LogicApp** i den **mallar** lista.
2. I den **utlösare** väljer **återkommande**. 
3. I den **återkommande** utlösning, Välj **redigera**väljer **frekvens** listrutan för att välja **dag**, och välj sedan **intervall** till typen **7**. 
4. Välj den **+ nytt steg** och välj sedan **lägga till en åtgärd**.
5. I den **åtgärder** anger **db2** i den **Sök efter fler åtgärder** redigera och välj sedan **DB2 - infogningsraden (förhandsgranskning)**.
6. I den **DB2 - infogningsraden (förhandsgranskning)** åtgärd, Välj **ändra anslutningen**. 
7. I den **anslutningar** configuration rutan, Välj en anslutning. Välj exempelvis **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. I den **tabellnamn** väljer den **NEDPIL**, och välj sedan **området**.
9. Ange värden för alla obligatoriska kolumner (se röd asterisk). Skriv till exempel `99999` för **AREAID**, typen `Area 99999`, och skriv `102` för **REGIONID**. 
10. Välj **Spara**.
    
    ![](./media/connectors-create-api-db2/Db2connectorInsertRowValues.png)
11. I den **Db2insertRow** bladet, inom den **alla körningar** listan **sammanfattning**, väljer du det första i listan (senaste kör).
12. I den **logikapp kör** bladet väljer **kör information**. I den **åtgärd** väljer **Get_rows**. Visa värdet för **Status**, som borde vara **lyckades**. Välj den **indata länken** att visa indata. Välj den **utdata länken**, och visa utdata, som ska innehålla den nya raden.
    
    ![](./media/connectors-create-api-db2/Db2connectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Hämta en rad med väljer
Du kan definiera en logik app åtgärd för att hämta en rad i en DB2-tabell. Den här åtgärden gör kopplingen för att bearbeta en DB2 väljer där instruktionen, exempelvis `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I den **Azure startar board**väljer **+** (plustecknet) **webb + mobilt**, och sedan **Logikapp**.
2. Ange den **namn** (t.ex.) ”**Db2getRow**”), **prenumeration**, **resursgruppen**, **plats**, och **Apptjänstplan**. Välj **fäst på instrumentpanelen**, och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I den **Logic Apps Designer**väljer **tom LogicApp** i den **mallar** lista. 
2. I den **utlösare** väljer **återkommande**. 
3. I den **återkommande** utlösning, Välj **redigera**väljer **frekvens** listrutan för att välja **dag**, och välj sedan **intervall** till typen **7**. 
4. Välj den **+ nytt steg** och välj sedan **lägga till en åtgärd**.
5. I den **åtgärder** anger **db2** i den **Sök efter fler åtgärder** redigera och välj sedan **DB2 - Get-rader (förhandsgranskning)**.
6. I den **hämta rader (förhandsgranskning)** åtgärd, Välj **ändra anslutningen**. 
7. I den **anslutningar** konfigurationer rutan, Välj en befintlig anslutning. Välj exempelvis **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. I den **tabellnamn** väljer den **NEDPIL**, och välj sedan **området**.
9. Ange värden för alla obligatoriska kolumner (se röd asterisk). Skriv till exempel `99999` för **AREAID**. 
10. Alternativt, Välj **visa avancerade alternativ** ange frågealternativ.
11. Välj **Spara**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowValues.png)
12. I den **Db2getRow** bladet, inom den **alla körningar** listan **sammanfattning**, väljer du det första i listan (senaste kör).
13. I den **logikapp kör** bladet väljer **kör information**. I den **åtgärd** väljer **Get_rows**. Visa värdet för **Status**, som borde vara **lyckades**. Välj den **indata länken** att visa indata. Välj den **utdata länken**, och visa utdata, som ska innehålla rad.
    
    ![](./media/connectors-create-api-db2/Db2connectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Ändra en rad med hjälp av UPDATE
Du kan definiera en logik app-åtgärd om du vill ändra en rad i en DB2-tabell. Den här åtgärden gör kopplingen för att bearbeta en DB2 UPDATE-instruktion som `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I den **Azure startar board**väljer **+** (plustecknet) **webb + mobilt**, och sedan **Logikapp**.
2. Ange den **namn**, som `Db2updateRow`, **prenumeration**, **resursgruppen**, **plats**, och **Apptjänstplan**. Välj **fäst på instrumentpanelen**, och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I den **Logic Apps Designer**väljer **tom LogicApp** i den **mallar** lista.
2. I den **utlösare** väljer **återkommande**. 
3. I den **återkommande** utlösning, Välj **redigera**väljer **frekvens** listrutan för att välja **dag**, och välj sedan **intervall** till typen **7**. 
4. Välj den **+ nytt steg** och välj sedan **lägga till en åtgärd**.
5. I den **åtgärder** anger **db2** i den **Sök efter fler åtgärder** redigera och välj sedan **DB2 - raden uppdateringen (förhandsversion)**.
6. I den **DB2 - raden uppdateringen (förhandsversion)** åtgärd, Välj **ändra anslutningen**. 
7. I den **anslutningar** konfigurationer fönstret, Välj att välja en befintlig anslutning. Välj exempelvis **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. I den **tabellnamn** väljer den **NEDPIL**, och välj sedan **området**.
9. Ange värden för alla obligatoriska kolumner (se röd asterisk). Skriv till exempel `99999` för **AREAID**, typen `Updated 99999`, och skriv `102` för **REGIONID**. 
10. Välj **Spara**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowValues.png)
11. I den **Db2updateRow** bladet, inom den **alla körningar** listan **sammanfattning**, väljer du det första i listan (senaste kör).
12. I den **logikapp kör** bladet väljer **kör information**. I den **åtgärd** väljer **Get_rows**. Visa värdet för **Status**, som borde vara **lyckades**. Välj den **indata länken** att visa indata. Välj den **utdata länken**, och visa utdata, som ska innehålla den nya raden.
    
    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Ta bort en rad med DELETE
Du kan definiera en logik app åtgärd för att ta bort en rad i en DB2-tabell. Den här åtgärden gör kopplingen för att bearbeta en DB2 DELETE-instruktion som `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I den **Azure startar board**väljer **+** (plustecknet) **webb + mobilt**, och sedan **Logikapp**.
2. Ange den **namn**, som `Db2deleteRow`, **prenumeration**, **resursgruppen**, **plats**, och **Apptjänstplan**. Välj **fäst på instrumentpanelen**, och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I den **Logic Apps Designer**väljer **tom LogicApp** i den **mallar** lista. 
2. I den **utlösare** väljer **återkommande**. 
3. I den **återkommande** utlösning, Välj **redigera**väljer **frekvens** listrutan för att välja **dag**, och välj sedan **intervall** till typen **7**. 
4. Välj den **+ nytt steg** och välj sedan **lägga till en åtgärd**.
5. I den **åtgärder** väljer **db2** i den **Sök efter fler åtgärder** redigera och välj sedan **DB2 - ta bort rad (förhandsgranskning)**.
6. I den **DB2 - ta bort rad (förhandsgranskning)** åtgärd, Välj **ändra anslutningen**. 
7. I den **anslutningar** konfigurationer rutan, Välj en befintlig anslutning. Välj exempelvis **hisdemo2**.
   
    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)
8. I den **tabellnamn** väljer den **NEDPIL**, och välj sedan **området**.
9. Ange värden för alla obligatoriska kolumner (se röd asterisk). Skriv till exempel `99999` för **AREAID**. 
10. Välj **Spara**. 
    
    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowValues.png)
11. I den **Db2deleteRow** bladet, inom den **alla körningar** listan **sammanfattning**, väljer du det första i listan (senaste kör).
12. I den **logikapp kör** bladet väljer **kör information**. I den **åtgärd** väljer **Get_rows**. Visa värdet för **Status**, som borde vara **lyckades**. Välj den **indata länken** att visa indata. Välj den **utdata länken**, och visa utdata, som ska innehålla den borttagna raden.
    
    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowOutputs.png)

## <a name="supported-db2-platforms-and-versions"></a>DB2-plattformar som stöds och versioner
Den här anslutningen har stöd för de följande IBM DB2-plattformar och versioner, samt IBM DB2 kompatibla produkter (t.ex. IBM Bluemix dashDB) som har stöd för distribuerade relationella Database Architecture (DRDA) SQL åtkomst Manager (SQLAM) version 10 och 11:

* IBM DB2 för z/OS 11,1
* IBM DB2 för z/OS 10.1
* IBM DB2 för i 7.3
* IBM DB2 för i 7.2
* IBM DB2 för i 7.1
* IBM DB2 för LUW 11
* IBM DB2 för LUW 10.5

## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/db2/). 

## <a name="next-steps"></a>Nästa steg
[Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Utforska andra tillgängliga kopplingar i Logic Apps på vår [API: er listan](apis-list.md).

