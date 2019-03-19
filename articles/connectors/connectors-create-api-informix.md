---
title: Anslut till IBM Informix-databas – Azure Logic Apps | Microsoft Docs
description: 'Hantera resurser med IBM Informix REST API: er och Azure Logic Apps'
author: gplarsen
manager: jeconnoc
ms.author: plarsen
ms.date: 09/26/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 6004c02f190bbfcf374b3b5d2a5c478f0e52c961
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58165908"
---
# <a name="get-started-with-the-informix-connector"></a>Kom igång med Informix-anslutningsapp
Microsoft-anslutaren för Informix ansluter Logic Apps till resurser som lagras i en IBM Informix-databas. Informix-kopplingen innehåller ett Microsoft-klienten att kommunicera med fjärrdatorer för Informix-server i ett TCP/IP-nätverk. Detta omfattar cloud-databaser, till exempel IBM Informix för Windows som körs i Azure virtualisering och lokala databaser med hjälp av den lokala datagatewayen. Se den [stöds lista](connectors-create-api-informix.md#supported-informix-platforms-and-versions) IBM Informix-plattformar och versioner (i det här avsnittet).

Stöder följande databasåtgärder för anslutningen:

* Lista databastabeller
* Läsa en rad med väljer
* Läsa alla rader med väljer
* Lägga till en rad med INSERT
* Ändra en rad med hjälp av UPDATE
* Ta bort en rad med DELETE

Det här avsnittet visar hur du använder-anslutningen i en logikapp till processen databasåtgärder.

Läs mer om Logic Apps i [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="available-actions"></a>Tillgängliga åtgärder
Den här anslutningsappen stöder de följande åtgärderna för logikappar:

* Getables
* GetRow
* GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Lista tabeller
Skapa en logikapp för alla åtgärder som består av många steg som utförs via Microsoft Azure-portalen.

I logic app, kan du lägga till en åtgärd lista tabellerna i en Informix-databas. Den här åtgärden instruerar connector att bearbeta en Informix schema-instruktionen som `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I den **Azure starta tavla**väljer **+** (plustecknet) **webb + mobilt**, och sedan **Logikapp**.
2. Ange den **namn**, till exempel `InformixgetTables`, **prenumeration**, **resursgrupp**, **plats**, och **App Service Planera**. Välj **fäst på instrumentpanelen**, och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I den **Logic Apps Designer**väljer **tom LogicApp** i den **mallar** lista.
2. I den **utlösare** väljer **upprepning**. 
3. I den **upprepning** utlösaren och väljer **redigera**väljer **frekvens** listrutan att välja **dag**, och välj sedan  **Intervall** till typen **7**.  
4. Välj den **+ nytt steg** och väljer sedan **Lägg till en åtgärd**.
5. I den **åtgärder** anger **informix** i den **Sök efter fler åtgärder** redigeringsruta och välj sedan **Informix - Get-tabeller (förhandsversion)**.
   
   ![](./media/connectors-create-api-informix/InformixconnectorActions.png)  
6. I den **Informix - Get-tabeller** configuration väljer **kryssrutan** att aktivera **Anslut via lokal datagateway**. Observera att inställningarna ändras från molnet till lokala.
   
   * Ange värdet för **Server**, i form av adress eller alias kolon portnummer. Exempel: `ibmserver01:9089`.
   * Ange värdet för **databasen**. Exempel: `nwind`.
   * Välj värde för **autentisering**. Välj exempelvis **grundläggande**.
   * Ange värdet för **användarnamn**. Exempel: `informix`.
   * Ange värdet för **lösenord**. Exempel: `Password1`.
   * Välj värde för **Gateway**. Välj exempelvis **datagateway01**.
7. Välj **skapa**, och välj sedan **spara**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. I den **InformixgetTables** bladet, i den **alla körningar** listan **sammanfattning**, väljer du det första i listan (de senaste kör).
9. I den **logikappskörningen** bladet väljer **körningsinformation**. I den **åtgärd** väljer **Get_tables**. Visa värdet för **Status**, vilket ska vara **lyckades**. Välj den **indatalänkar** att visa indata. Välj den **utdatalänkar**, och visa utdata, som ska innehålla en lista över tabeller.
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Skapa anslutningarna
Den här anslutningen har stöd för anslutningar till databas lokalt och i molnet med följande anslutningsegenskaper. 

| Egenskap  | Beskrivning |
| --- | --- |
| server |Krävs. Accepterar ett strängvärde som representerar en TCP/IP-adress eller ett alias i IPv4 eller IPv6-format, följt (kolon för avgränsade) av ett TCP/IP-portnummer. |
| databas |Krävs. Accepterar ett strängvärde som representerar en DRDA relationell databas namn (RDBNAM). Informix godkänner en 128 byte-sträng (databas kallas en IBM Informix-databasnamnet (dbname)). |
| autentisering |Valfri. Accepterar en lista över Objektvärde, antingen grundläggande eller Windows (kerberos). |
| användarnamn |Krävs. Accepterar ett strängvärde. |
| lösenord |Krävs. Accepterar ett strängvärde. |
| gateway |Krävs. Accepterar en lista över objekt-värde som representerar den lokala datagatewayen definierats till Logic Apps i lagringsgruppen. |

## <a name="create-the-on-premises-gateway-connection"></a>Skapa lokalt gateway-anslutning
Den här anslutningen kan komma åt en lokal Informix-databas som använder den lokala datagatewayen. I gatewayen ämne finns mer information. 

1. I den **gatewayer** configuration väljer **kryssrutan** att aktivera **Anslut via gateway**. Se inställningarna som ändrar från molnet till lokala.
2. Ange värdet för **Server**, i form av adress eller alias kolon portnummer. Exempel: `ibmserver01:9089`.
3. Ange värdet för **databasen**. Exempel: `nwind`.
4. Välj värde för **autentisering**. Välj exempelvis **grundläggande**.
5. Ange värdet för **användarnamn**. Exempel: `informix`.
6. Ange värdet för **lösenord**. Exempel: `Password1`.
7. Välj värde för **Gateway**. Välj exempelvis **datagateway01**.
8. Välj **skapa** att fortsätta. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Skapa cloud-anslutning
Den här anslutningen har åtkomst till ett moln Informix-databas. 

1. I den **gatewayer** konfigurationsruta lämna den **kryssrutan** inaktiverad (oanvända) **Anslut via gateway**. 
2. Ange värdet för **anslutningsnamn**. Exempel: `hisdemo2`.
3. Ange värdet för **Informix servernamn**, i form av adress eller alias kolon portnummer. Exempel: `hisdemo2.cloudapp.net:9089`.
4. Ange värdet för **Informix databasnamn**. Exempel: `nwind`.
5. Ange värdet för **användarnamn**. Exempel: `informix`.
6. Ange värdet för **lösenord**. Exempel: `Password1`.
7. Välj **skapa** att fortsätta. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Hämta alla rader med väljer
Du kan skapa en logic app-åtgärd för att hämta alla rader i tabellen Informix. Den här åtgärden instruerar connector att bearbeta ett Informix SELECT-uttryck, till exempel `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I den **Azure starta tavla**väljer **+** (plustecknet) **webb + mobilt**, och sedan **Logikapp**.
2. Ange den **namn** (t.ex.) ”**InformixgetRows**”), **prenumeration**, **resursgrupp**, **plats**, och **Apptjänstplan**. Välj **fäst på instrumentpanelen**, och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I den **Logic Apps Designer**väljer **tom LogicApp** i den **mallar** lista.
2. I den **utlösare** väljer **upprepning**. 
3. I den **upprepning** utlösaren och väljer **redigera**väljer **frekvens** listrutan att välja **dag**, och välj sedan  **Intervall** till typen **7**. 
4. Välj den **+ nytt steg** och väljer sedan **Lägg till en åtgärd**.
5. I den **åtgärder** anger **informix** i den **Sök efter fler åtgärder** redigeringsruta och välj sedan **Informix - hämta rader (förhandsversion)**.
6. I den **hämta rader (förhandsversion)** väljer **ändra anslutning**.
7. I den **anslutningar** configuration väljer **Skapa ny**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png)
8. I den **gatewayer** konfigurationsruta lämna den **kryssrutan** inaktiverad (oanvända) **Anslut via gateway**.
   
   * Ange värdet för **anslutningsnamn**. Exempel: `HISDEMO2`.
   * Ange värdet för **Informix servernamn**, i form av adress eller alias kolon portnummer. Exempel: `HISDEMO2.cloudapp.net:9089`.
   * Ange värdet för **Informix databasnamn**. Exempel: `NWIND`.
   * Ange värdet för **användarnamn**. Exempel: `informix`.
   * Ange värdet för **lösenord**. Exempel: `Password1`.
9. Välj **skapa** att fortsätta.
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. I den **tabellnamn** väljer den **nedåtpilen**, och välj sedan **området**.
11. Alternativt kan du välja **visa avancerade alternativ** ange frågealternativ.
12. Välj **Spara**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. I den **InformixgetRows** bladet, i den **alla körningar** listan **sammanfattning**, väljer du det första i listan (de senaste kör).
14. I den **logikappskörningen** bladet väljer **körningsinformation**. I den **åtgärd** väljer **Get_rows**. Visa värdet för **Status**, vilket ska vara **lyckades**. Välj den **indatalänkar** att visa indata. Välj den **utdatalänkar**, och visa utdata, som ska innehålla en lista med rader.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Lägga till en rad med INSERT
Du kan skapa en logic app-åtgärd för att lägga till en rad i en Informix-tabell. Den här åtgärden instruerar connector att bearbeta en Informix INSERT-instruktion som `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I den **Azure starta tavla**väljer **+** (plustecknet) **webb + mobilt**, och sedan **Logikapp**.
2. Ange den **namn**, till exempel `InformixinsertRow`, **prenumeration**, **resursgrupp**, **plats**, och **App Service Planera**. Välj **fäst på instrumentpanelen**, och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I den **Logic Apps Designer**väljer **tom LogicApp** i den **mallar** lista.
2. I den **utlösare** väljer **upprepning**. 
3. I den **upprepning** utlösaren och väljer **redigera**väljer **frekvens** listrutan att välja **dag**, och välj sedan  **Intervall** till typen **7**. 
4. Välj den **+ nytt steg** och väljer sedan **Lägg till en åtgärd**.
5. I den **åtgärder** anger **informix** i den **Sök efter fler åtgärder** redigeringsruta och välj sedan **Informix - infogningsraden (förhandsversion)**.
6. I den **hämta rader (förhandsversion)** väljer **ändra anslutning**. 
7. I den **anslutningar** konfigurationsruta väljer du att välja en anslutning. Välj exempelvis **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. I den **tabellnamn** väljer den **nedåtpilen**, och välj sedan **området**.
9. Ange värden för alla nödvändiga kolumner (se röd asterisk). Skriv exempelvis `99999` för **områdes-ID**, typ `Area 99999`, och skriv `102` för **REGIONID**. 
10. Välj **Spara**.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. I den **InformixinsertRow** bladet, i den **alla körningar** listan **sammanfattning**, väljer du det första i listan (de senaste kör).
12. I den **logikappskörningen** bladet väljer **körningsinformation**. I den **åtgärd** väljer **Get_rows**. Visa värdet för **Status**, vilket ska vara **lyckades**. Välj den **indatalänkar** att visa indata. Välj den **utdatalänkar**, och visa utdata, som ska innehålla den nya raden.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Hämta en rad med väljer
Du kan skapa en logic app-åtgärd för att hämta en rad i en Informix-tabell. Den här åtgärden instruerar connector att bearbeta en Informix Välj där instruktionen, som `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I den **Azure starta tavla**väljer **+** (plustecknet) **webb + mobilt**, och sedan **Logikapp**.
2. Ange den **namn**, till exempel `InformixgetRow`, **prenumeration**, **resursgrupp**, **plats**, och **App Service Planera**. Välj **fäst på instrumentpanelen**, och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I den **Logic Apps Designer**väljer **tom LogicApp** i den **mallar** lista.
2. I den **utlösare** väljer **upprepning**. 
3. I den **upprepning** utlösaren och väljer **redigera**väljer **frekvens** listrutan att välja **dag**, och välj sedan  **Intervall** till typen **7**. 
4. Välj den **+ nytt steg** och väljer sedan **Lägg till en åtgärd**.
5. I den **åtgärder** anger **informix** i den **Sök efter fler åtgärder** redigeringsruta och välj sedan **Informix - hämta rader (förhandsversion)**.
6. I den **hämta rader (förhandsversion)** väljer **ändra anslutning**. 
7. I den **anslutningar** konfigurationer väljer att välja en befintlig anslutning. Välj exempelvis **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. I den **tabellnamn** väljer den **nedåtpilen**, och välj sedan **området**.
9. Ange värden för alla nödvändiga kolumner (se röd asterisk). Skriv exempelvis `99999` för **områdes-ID**. 
10. Alternativt kan du välja **visa avancerade alternativ** ange frågealternativ.
11. Välj **Spara**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. I den **InformixgetRow** bladet, i den **alla körningar** listan **sammanfattning**, väljer du det första i listan (de senaste kör).
13. I den **logikappskörningen** bladet väljer **körningsinformation**. I den **åtgärd** väljer **Get_rows**. Visa värdet för **Status**, vilket ska vara **lyckades**. Välj den **indatalänkar** att visa indata. Välj den **utdatalänkar**, och visa utdata, som ska innehålla raden.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Ändra en rad med hjälp av UPDATE
Du kan skapa en logic app-åtgärd om du vill ändra en rad i en Informix-tabell. Den här åtgärden instruerar connector att bearbeta en Informix-UPDATE-instruktion som `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I den **Azure starta tavla**väljer **+** (plustecknet) **webb + mobilt**, och sedan **Logikapp**.
2. Ange den **namn**, till exempel `InformixupdateRow`, **prenumeration**, **resursgrupp**, **plats**, och **App Service Planera**. Välj **fäst på instrumentpanelen**, och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I den **Logic Apps Designer**väljer **tom LogicApp** i den **mallar** lista.
2. I den **utlösare** väljer **upprepning**. 
3. I den **upprepning** utlösaren och väljer **redigera**väljer **frekvens** listrutan att välja **dag**, och välj sedan  **Intervall** till typen **7**. 
4. Välj den **+ nytt steg** och väljer sedan **Lägg till en åtgärd**.
5. I den **åtgärder** anger **informix** i den **Sök efter fler åtgärder** redigeringsruta och välj sedan **Informix - Uppdatera rad (förhandsversion)**.
6. I den **hämta rader (förhandsversion)** väljer **ändra anslutning**. 
7. I den **anslutningar** konfigurationer väljer att välja en befintlig anslutning. Välj exempelvis **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. I den **tabellnamn** väljer den **nedåtpilen**, och välj sedan **området**.
9. Ange värden för alla nödvändiga kolumner (se röd asterisk). Skriv exempelvis `99999` för **områdes-ID**, typ `Updated 99999`, och skriv `102` för **REGIONID**. 
10. Välj **Spara**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. I den **InformixupdateRow** bladet, i den **alla körningar** listan **sammanfattning**, väljer du det första i listan (de senaste kör).
12. I den **logikappskörningen** bladet väljer **körningsinformation**. I den **åtgärd** väljer **Get_rows**. Visa värdet för **Status**, vilket ska vara **lyckades**. Välj den **indatalänkar** att visa indata. Välj den **utdatalänkar**, och visa utdata, som ska innehålla den nya raden.
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Ta bort en rad med DELETE
Du kan skapa en logic app-åtgärd för att ta bort en rad i en Informix-tabell. Den här åtgärden instruerar connector att bearbeta en Informix DELETE-instruktion som `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I den **Azure starta tavla**väljer **+** (plustecknet) **webb + mobilt**, och sedan **Logikapp**.
2. Ange den **namn**, till exempel `InformixdeleteRow`, **prenumeration**, **resursgrupp**, **plats**, och **App Service Planera**. Välj **fäst på instrumentpanelen**, och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I den **Logic Apps Designer**väljer **tom LogicApp** i den **mallar** lista.
2. I den **utlösare** väljer **upprepning**. 
3. I den **upprepning** utlösaren och väljer **redigera**väljer **frekvens** listrutan att välja **dag**, och välj sedan  **Intervall** till typen **7**. 
4. Välj den **+ nytt steg** och väljer sedan **Lägg till en åtgärd**.
5. I den **åtgärder** anger **informix** i den **Sök efter fler åtgärder** redigeringsruta och välj sedan **Informix - ta bort rad (förhandsversion)**.
6. I den **hämta rader (förhandsversion)** väljer **ändra anslutning**. 
7. I den **anslutningar** konfigurationer fönstret, Välj en befintlig anslutning. Välj exempelvis **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. I den **tabellnamn** väljer den **nedåtpilen**, och välj sedan **området**.
9. Ange värden för alla nödvändiga kolumner (se röd asterisk). Skriv exempelvis `99999` för **områdes-ID**. 
10. Välj **Spara**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. I den **InformixdeleteRow** bladet, i den **alla körningar** listan **sammanfattning**, väljer du det första i listan (de senaste kör).
12. I den **logikappskörningen** bladet väljer **körningsinformation**. I den **åtgärd** väljer **Get_rows**. Visa värdet för **Status**, vilket ska vara **lyckades**. Välj den **indatalänkar** att visa indata. Välj den **utdatalänkar**, och visa utdata, som ska innehålla den borttagna raden.
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="supported-informix-platforms-and-versions"></a>Informix-plattformar och versioner som stöds
Den här anslutningsappen stöder följande IBM Informix-versioner när konfigurerad för att stödja distribuerade Relational Database Architecture (DRDA)-klientanslutningar.

* IBM Informix 12.1
* IBM Informix 11,7

## <a name="connector-specific-details"></a>Information om specifika

Visa alla utlösare och åtgärder som definierats i swagger och får även eventuella gränser i den [anslutningsinformationen](/connectors/informix/). 

## <a name="next-steps"></a>Nästa steg
[Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Utforska andra tillgängliga anslutningsappar i Logic Apps på vår [API: er lista](apis-list.md).

