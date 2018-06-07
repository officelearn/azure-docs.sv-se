---
title: Ansluta till databasen för IBM Informix - Azure Logic Apps | Microsoft Docs
description: 'Hantera resurser med IBM Informix REST API: er och Azure Logic Apps'
author: gplarsen
manager: cfowler
ms.author: plarsen
ms.date: 09/26/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: c78c6bb669e0945ba1cbacc3ca808a364f3099a5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34609396"
---
# <a name="get-started-with-the-informix-connector"></a>Kom igång med Informix-koppling
Microsoft-anslutaren för Informix ansluter Logic Apps till resurser som lagras i en IBM Informix-databas. Informix-kopplingen innehåller ett Microsoft-klienten att kommunicera med fjärrdatorer för Informix-server i ett TCP/IP-nätverk. Detta omfattar molntjänster databaser, till exempel IBM Informix för Windows körs i Azure virtualisering och lokala databaser med hjälp av lokala datagateway. Finns det [stöds listan](connectors-create-api-informix.md#supported-informix-platforms-and-versions) IBM Informix-plattformar och versioner (i det här avsnittet).

Kopplingen har stöd för följande databasåtgärder för:

* Lista databastabeller
* Läs en rad med väljer
* Läsa alla rader med väljer
* Lägga till en rad med INSERT
* Ändra en rad med hjälp av UPDATE
* Ta bort en rad med DELETE

Det här avsnittet visar hur du använder kopplingen i en logikapp till databasåtgärder i processen.

Läs mer om Logic Apps i [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="available-actions"></a>Tillgängliga åtgärder
Den här anslutningen har stöd för följande logik app åtgärder:

* Getables
* GetRow
* GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Lista tabeller
Skapa en logikapp för någon åtgärd består av många steg utförs via Microsoft Azure-portalen.

Du kan lägga till en åtgärd i logikappen, att lista tabeller i en Informix-databas. Den här åtgärden gör kopplingen för att bearbeta en Informix schema-instruktionen, exempelvis `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I den **Azure startar board**väljer **+** (plustecknet) **webb + mobilt**, och sedan **Logikapp**.
2. Ange den **namn**, som `InformixgetTables`, **prenumeration**, **resursgruppen**, **plats**, och **Apptjänstplan**. Välj **fäst på instrumentpanelen**, och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I den **Logic Apps Designer**väljer **tom LogicApp** i den **mallar** lista.
2. I den **utlösare** väljer **återkommande**. 
3. I den **återkommande** utlösning, Välj **redigera**väljer **frekvens** listrutan för att välja **dag**, och välj sedan **intervall** till typen **7**.  
4. Välj den **+ nytt steg** och välj sedan **lägga till en åtgärd**.
5. I den **åtgärder** anger **informix** i den **Sök efter fler åtgärder** redigera och välj sedan **Informix - Get-tabeller (förhandsgranskning)**.
   
   ![](./media/connectors-create-api-informix/InformixconnectorActions.png)  
6. I den **Informix - Get-tabeller** configuration-fönstret, Välj **kryssrutan** att aktivera **Anslut via lokala datagateway**. Observera att ändra inställningarna från molnet till lokalt.
   
   * Ange värde för **Server**, i form av adress eller alias kolon portnummer. Skriv till exempel `ibmserver01:9089`.
   * Ange värde för **databasen**. Skriv till exempel `nwind`.
   * Välj värde för **autentisering**. Välj exempelvis **grundläggande**.
   * Ange värde för **användarnamn**. Skriv till exempel `informix`.
   * Ange värde för **lösenord**. Skriv till exempel `Password1`.
   * Välj värde för **Gateway**. Välj exempelvis **datagateway01**.
7. Välj **skapa**, och välj sedan **spara**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. I den **InformixgetTables** bladet, inom den **alla körningar** listan **sammanfattning**, väljer du det första i listan (senaste kör).
9. I den **logikapp kör** bladet väljer **kör information**. I den **åtgärd** väljer **Get_tables**. Visa värdet för **Status**, som borde vara **lyckades**. Välj den **indata länken** att visa indata. Välj den **utdata länken**, och visa utdata, som ska innehålla en lista över tabeller.
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Skapa anslutningar
Den här anslutningen har stöd för anslutningar till databasen på lokalt och i molnet med hjälp av följande anslutningsegenskaper. 

| Egenskap  | Beskrivning |
| --- | --- |
| server |Krävs. Accepterar ett strängvärde som representerar en TCP/IP-adress eller ett alias i IPv4- eller IPv6-format, följt (-semikolonavgränsad) av ett TCP/IP-portnummer. |
| databas |Krävs. Accepterar ett strängvärde som representerar en DRDA relationell databas namn (RDBNAM). Informix accepterar en 128 byte-sträng (databas kallas en IBM Informix databasnamnet (dbname)). |
| autentisering |Valfri. Tillåter lista objekt, grundläggande eller Windows (kerberos). |
| användarnamn |Krävs. Accepterar ett strängvärde. |
| lösenord |Krävs. Accepterar ett strängvärde. |
| gateway |Krävs. Accepterar en lista över objekt-värde som representerar lokala datagateway definierad för Logic Apps i lagringsgruppen. |

## <a name="create-the-on-premises-gateway-connection"></a>Skapa lokalt gateway-anslutningen
Den här anslutningen har åtkomst till en lokal Informix-databas med hjälp av lokala datagateway. Se gateway avsnitt för mer information. 

1. I den **Gateways** configuration-fönstret, Välj **kryssrutan** att aktivera **Anslut via gateway**. Se inställningar ändras från molnet till lokalt.
2. Ange värde för **Server**, i form av adress eller alias kolon portnummer. Skriv till exempel `ibmserver01:9089`.
3. Ange värde för **databasen**. Skriv till exempel `nwind`.
4. Välj värde för **autentisering**. Välj exempelvis **grundläggande**.
5. Ange värde för **användarnamn**. Skriv till exempel `informix`.
6. Ange värde för **lösenord**. Skriv till exempel `Password1`.
7. Välj värde för **Gateway**. Välj exempelvis **datagateway01**.
8. Välj **skapa** att fortsätta. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Skapa moln-anslutning
Den här anslutningen har åtkomst till ett moln Informix-databas. 

1. I den **Gateways** configuration fönstret lämna den **kryssrutan** (oanvända) har inaktiverats **Anslut via gateway**. 
2. Ange värde för **anslutningsnamn**. Skriv till exempel `hisdemo2`.
3. Ange värde för **Informix servernamn**, i form av adress eller alias kolon portnummer. Skriv till exempel `hisdemo2.cloudapp.net:9089`.
4. Ange värde för **Informix databasnamnet**. Skriv till exempel `nwind`.
5. Ange värde för **användarnamn**. Skriv till exempel `informix`.
6. Ange värde för **lösenord**. Skriv till exempel `Password1`.
7. Välj **skapa** att fortsätta. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Hämta alla rader med väljer
Du kan skapa en logik app åtgärd för att hämta alla rader i tabellen Informix. Den här åtgärden gör kopplingen för att bearbeta en Informix SELECT-instruktion som `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I den **Azure startar board**väljer **+** (plustecknet) **webb + mobilt**, och sedan **Logikapp**.
2. Ange den **namn** (t.ex.) ”**InformixgetRows**”), **prenumeration**, **resursgruppen**, **plats**, och **Apptjänstplan**. Välj **fäst på instrumentpanelen**, och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I den **Logic Apps Designer**väljer **tom LogicApp** i den **mallar** lista.
2. I den **utlösare** väljer **återkommande**. 
3. I den **återkommande** utlösning, Välj **redigera**väljer **frekvens** listrutan för att välja **dag**, och välj sedan **intervall** till typen **7**. 
4. Välj den **+ nytt steg** och välj sedan **lägga till en åtgärd**.
5. I den **åtgärder** anger **informix** i den **Sök efter fler åtgärder** redigera och välj sedan **Informix - Get-rader (förhandsgranskning)**.
6. I den **hämta rader (förhandsgranskning)** åtgärd, Välj **ändra anslutningen**.
7. I den **anslutningar** configuration-fönstret, Välj **Skapa nytt**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png)
8. I den **Gateways** configuration fönstret lämna den **kryssrutan** (oanvända) har inaktiverats **Anslut via gateway**.
   
   * Ange värde för **anslutningsnamn**. Skriv till exempel `HISDEMO2`.
   * Ange värde för **Informix servernamn**, i form av adress eller alias kolon portnummer. Skriv till exempel `HISDEMO2.cloudapp.net:9089`.
   * Ange värde för **Informix databasnamnet**. Skriv till exempel `NWIND`.
   * Ange värde för **användarnamn**. Skriv till exempel `informix`.
   * Ange värde för **lösenord**. Skriv till exempel `Password1`.
9. Välj **skapa** att fortsätta.
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. I den **tabellnamn** väljer den **NEDPIL**, och välj sedan **området**.
11. Alternativt, Välj **visa avancerade alternativ** ange frågealternativ.
12. Välj **Spara**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. I den **InformixgetRows** bladet, inom den **alla körningar** listan **sammanfattning**, väljer du det första i listan (senaste kör).
14. I den **logikapp kör** bladet väljer **kör information**. I den **åtgärd** väljer **Get_rows**. Visa värdet för **Status**, som borde vara **lyckades**. Välj den **indata länken** att visa indata. Välj den **utdata länken**, och visa utdata, som ska innehålla en lista med rader.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Lägga till en rad med INSERT
Du kan skapa en logik app åtgärd för att lägga till en rad i en Informix-tabell. Den här åtgärden gör kopplingen för att bearbeta en Informix INSERT-instruktionen, exempelvis `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I den **Azure startar board**väljer **+** (plustecknet) **webb + mobilt**, och sedan **Logikapp**.
2. Ange den **namn**, som `InformixinsertRow`, **prenumeration**, **resursgruppen**, **plats**, och **Apptjänstplan**. Välj **fäst på instrumentpanelen**, och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I den **Logic Apps Designer**väljer **tom LogicApp** i den **mallar** lista.
2. I den **utlösare** väljer **återkommande**. 
3. I den **återkommande** utlösning, Välj **redigera**väljer **frekvens** listrutan för att välja **dag**, och välj sedan **intervall** till typen **7**. 
4. Välj den **+ nytt steg** och välj sedan **lägga till en åtgärd**.
5. I den **åtgärder** anger **informix** i den **Sök efter fler åtgärder** redigera och välj sedan **Informix - infogningsraden (förhandsgranskning)**.
6. I den **hämta rader (förhandsgranskning)** åtgärd, Välj **ändra anslutningen**. 
7. I den **anslutningar** configuration-rutan, Välj om du vill välja en anslutning. Välj exempelvis **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. I den **tabellnamn** väljer den **NEDPIL**, och välj sedan **området**.
9. Ange värden för alla obligatoriska kolumner (se röd asterisk). Skriv till exempel `99999` för **AREAID**, typen `Area 99999`, och skriv `102` för **REGIONID**. 
10. Välj **Spara**.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. I den **InformixinsertRow** bladet, inom den **alla körningar** listan **sammanfattning**, väljer du det första i listan (senaste kör).
12. I den **logikapp kör** bladet väljer **kör information**. I den **åtgärd** väljer **Get_rows**. Visa värdet för **Status**, som borde vara **lyckades**. Välj den **indata länken** att visa indata. Välj den **utdata länken**, och visa utdata, som ska innehålla den nya raden.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Hämta en rad med väljer
Du kan skapa en logik app åtgärd för att hämta en rad i en Informix-tabell. Den här åtgärden gör kopplingen för att bearbeta en Informix väljer där instruktionen, exempelvis `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I den **Azure startar board**väljer **+** (plustecknet) **webb + mobilt**, och sedan **Logikapp**.
2. Ange den **namn**, som `InformixgetRow`, **prenumeration**, **resursgruppen**, **plats**, och **Apptjänstplan**. Välj **fäst på instrumentpanelen**, och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I den **Logic Apps Designer**väljer **tom LogicApp** i den **mallar** lista.
2. I den **utlösare** väljer **återkommande**. 
3. I den **återkommande** utlösning, Välj **redigera**väljer **frekvens** listrutan för att välja **dag**, och välj sedan **intervall** till typen **7**. 
4. Välj den **+ nytt steg** och välj sedan **lägga till en åtgärd**.
5. I den **åtgärder** anger **informix** i den **Sök efter fler åtgärder** redigera och välj sedan **Informix - Get-rader (förhandsgranskning)**.
6. I den **hämta rader (förhandsgranskning)** åtgärd, Välj **ändra anslutningen**. 
7. I den **anslutningar** konfigurationer fönstret, Välj att välja en befintlig anslutning. Välj exempelvis **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. I den **tabellnamn** väljer den **NEDPIL**, och välj sedan **området**.
9. Ange värden för alla obligatoriska kolumner (se röd asterisk). Skriv till exempel `99999` för **AREAID**. 
10. Alternativt, Välj **visa avancerade alternativ** ange frågealternativ.
11. Välj **Spara**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. I den **InformixgetRow** bladet, inom den **alla körningar** listan **sammanfattning**, väljer du det första i listan (senaste kör).
13. I den **logikapp kör** bladet väljer **kör information**. I den **åtgärd** väljer **Get_rows**. Visa värdet för **Status**, som borde vara **lyckades**. Välj den **indata länken** att visa indata. Välj den **utdata länken**, och visa utdata, som ska innehålla rad.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Ändra en rad med hjälp av UPDATE
Du kan skapa en logik app-åtgärd om du vill ändra en rad i en Informix-tabell. Den här åtgärden gör kopplingen för att bearbeta en Informix UPDATE-instruktion som `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I den **Azure startar board**väljer **+** (plustecknet) **webb + mobilt**, och sedan **Logikapp**.
2. Ange den **namn**, som `InformixupdateRow`, **prenumeration**, **resursgruppen**, **plats**, och **Apptjänstplan**. Välj **fäst på instrumentpanelen**, och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I den **Logic Apps Designer**väljer **tom LogicApp** i den **mallar** lista.
2. I den **utlösare** väljer **återkommande**. 
3. I den **återkommande** utlösning, Välj **redigera**väljer **frekvens** listrutan för att välja **dag**, och välj sedan **intervall** till typen **7**. 
4. Välj den **+ nytt steg** och välj sedan **lägga till en åtgärd**.
5. I den **åtgärder** anger **informix** i den **Sök efter fler åtgärder** redigera och välj sedan **Informix - raden uppdateringen (förhandsversion)**.
6. I den **hämta rader (förhandsgranskning)** åtgärd, Välj **ändra anslutningen**. 
7. I den **anslutningar** konfigurationer fönstret, Välj att välja en befintlig anslutning. Välj exempelvis **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. I den **tabellnamn** väljer den **NEDPIL**, och välj sedan **området**.
9. Ange värden för alla obligatoriska kolumner (se röd asterisk). Skriv till exempel `99999` för **AREAID**, typen `Updated 99999`, och skriv `102` för **REGIONID**. 
10. Välj **Spara**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. I den **InformixupdateRow** bladet, inom den **alla körningar** listan **sammanfattning**, väljer du det första i listan (senaste kör).
12. I den **logikapp kör** bladet väljer **kör information**. I den **åtgärd** väljer **Get_rows**. Visa värdet för **Status**, som borde vara **lyckades**. Välj den **indata länken** att visa indata. Välj den **utdata länken**, och visa utdata, som ska innehålla den nya raden.
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Ta bort en rad med DELETE
Du kan skapa en logik app åtgärd för att ta bort en rad i en Informix-tabell. Den här åtgärden gör kopplingen för att bearbeta en Informix DELETE-instruktion som `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I den **Azure startar board**väljer **+** (plustecknet) **webb + mobilt**, och sedan **Logikapp**.
2. Ange den **namn**, som `InformixdeleteRow`, **prenumeration**, **resursgruppen**, **plats**, och **Apptjänstplan**. Välj **fäst på instrumentpanelen**, och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I den **Logic Apps Designer**väljer **tom LogicApp** i den **mallar** lista.
2. I den **utlösare** väljer **återkommande**. 
3. I den **återkommande** utlösning, Välj **redigera**väljer **frekvens** listrutan för att välja **dag**, och välj sedan **intervall** till typen **7**. 
4. Välj den **+ nytt steg** och välj sedan **lägga till en åtgärd**.
5. I den **åtgärder** anger **informix** i den **Sök efter fler åtgärder** redigera och välj sedan **Informix - ta bort rad (förhandsgranskning)**.
6. I den **hämta rader (förhandsgranskning)** åtgärd, Välj **ändra anslutningen**. 
7. I den **anslutningar** konfigurationer rutan, Välj en befintlig anslutning. Välj exempelvis **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. I den **tabellnamn** väljer den **NEDPIL**, och välj sedan **området**.
9. Ange värden för alla obligatoriska kolumner (se röd asterisk). Skriv till exempel `99999` för **AREAID**. 
10. Välj **Spara**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. I den **InformixdeleteRow** bladet, inom den **alla körningar** listan **sammanfattning**, väljer du det första i listan (senaste kör).
12. I den **logikapp kör** bladet väljer **kör information**. I den **åtgärd** väljer **Get_rows**. Visa värdet för **Status**, som borde vara **lyckades**. Välj den **indata länken** att visa indata. Välj den **utdata länken**, och visa utdata, som ska innehålla den borttagna raden.
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="supported-informix-platforms-and-versions"></a>Informix-plattformar som stöds och versioner
Den här anslutningen stöder följande IBM Informix-versioner när konfigurerad för att stödja klientanslutningar distribuerade relationella Database Architecture (DRDA).

* IBM Informix 12.1
* IBM Informix 11,7

## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/informix/). 

## <a name="next-steps"></a>Nästa steg
[Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Utforska andra tillgängliga kopplingar i Logic Apps på vår [API: er listan](apis-list.md).

