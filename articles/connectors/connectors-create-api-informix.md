---
title: Ansluta till IBM Informix-databas
description: 'Hantera resurser med IBM Informix REST API: er och Azure Logic Apps'
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: plarsen
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/26/2016
tags: connectors
ms.openlocfilehash: d6f768bc76d19c0aa21a245c008a4b05588f8f43
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789728"
---
# <a name="get-started-with-the-informix-connector"></a>Kom igång med Informix-anslutningen
Microsoft Connector för Informix ansluter Logic Apps till resurser som är lagrade i en IBM Informix-databas. Informix-anslutningen innehåller en Microsoft-klient för att kommunicera med fjärranslutna Informix-serverdatorer över ett TCP/IP-nätverk. Detta inkluderar moln databaser, till exempel IBM Informix för Windows som körs i Azure Virtualization, och lokala databaser med hjälp av den lokala datagatewayen. Se [listan över stödda](connectors-create-api-informix.md#supported-informix-platforms-and-versions) av IBM Informix-plattformar och versioner (i det här avsnittet).

Anslutningen har stöd för följande databas åtgärder:

* Lista databas tabeller
* Läs en rad med SELECT
* Läs alla rader med SELECT
* Lägg till en rad med hjälp av Infoga
* Ändra en rad med hjälp av UPDATE
* Ta bort en rad med hjälp av ta bort

Det här avsnittet visar hur du använder-anslutningen i en Logic app för att bearbeta databas åtgärder.

Mer information om Logic Apps finns i [skapa en Logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="available-actions"></a>Tillgängliga åtgärder
Den här anslutningen har stöd för följande Logic app-åtgärder:

* Getables
* GetRow
* GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>List tabeller
Att skapa en logisk app för alla åtgärder består av många steg som utförs via Microsoft Azure-portalen.

I Logic-appen kan du lägga till en åtgärd för att lista tabeller i en Informix-databas. Den här åtgärden instruerar anslutningen att bearbeta en Informix-schema-instruktion, till exempel `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I **Azure start-tavlan**väljer du **+** (plus tecken), **webb och mobilt**och sedan **Logic app**.
2. Ange **namnet**, till exempel `InformixgetTables`, **prenumeration**, **resurs grupp**, **plats**och **App Service plan**. Välj **Fäst på instrument panelen**och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I **Logic Apps designer**väljer du **Tom LogicApp** i listan **mallar** .
2. I listan **utlösare** väljer du **upprepning**. 
3. I **upprepnings** utlösaren väljer du **Redigera**, Välj **frekvens** och väljer **dag**. Välj sedan **intervall** till typ **7**.  
4. Markera rutan **+ nytt steg** och välj sedan **Lägg till en åtgärd**.
5. I listan **åtgärder** skriver du **Informix** i redigerings rutan **Sök efter fler åtgärder** och väljer sedan **Informix-Hämta tabeller (förhands granskning)** .
   
   ![](./media/connectors-create-api-informix/InformixconnectorActions.png)  
6. I fönstret konfiguration av **Informix-Hämta tabeller** väljer du **kryss rutan** för att aktivera **Anslut via lokal datagateway**. Observera att inställningarna ändras från molnet till lokalt.
   
   * Skriv värde för **Server**, i form av ett kolon-portnummer för adress eller alias. Skriv till exempel `ibmserver01:9089`.
   * Skriv värde för **databas**. Skriv till exempel `nwind`.
   * Välj värde för **autentisering**. Välj till exempel **Basic**.
   * Skriv värde för **användar namn**. Skriv till exempel `informix`.
   * Skriv värde för **lösen ord**. Skriv till exempel `Password1`.
   * Välj värde för **Gateway**. Välj till exempel **datagateway01**.
7. Välj **skapa**och välj sedan **Spara**. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. På bladet **InformixgetTables** i listan **alla körningar** under **Sammanfattning**väljer du det första-listade objektet (senaste körning).
9. Välj **Kör information**i bladet **Logic app-körning** . I listan **åtgärd** väljer du **Get_tables**. Se värdet för **status**, som bör vara **slutfört**. Välj **länken indata** för att Visa indata. Välj **länken utdata**och visa utdata. Det bör innehålla en lista över tabeller.
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Skapa anslutningarna
Den här anslutningen har stöd för anslutningar till databasen lokalt och i molnet med hjälp av följande anslutnings egenskaper. 

| Egenskap | Beskrivning |
| --- | --- |
| server |Krävs. Accepterar ett sträng värde som representerar en TCP/IP-adress eller ett alias, i antingen IPv4-eller IPv6-format, följt av (semikolonavgränsade) med ett TCP/IP-portnummer. |
| databas |Krävs. Accepterar ett sträng värde som representerar ett DRDA Relations databas namn (RDBNAM). Informix accepterar en 128 byte-sträng (databasen kallas ett IBM Informix-databas namn (dbname)). |
| autentisering |Valfri. Accepterar ett List objekt värde, antingen Basic eller Windows (Kerberos). |
| användarnamn |Krävs. Accepterar ett sträng värde. |
| lösenord |Krävs. Accepterar ett sträng värde. |
| Nyckeln |Krävs. Accepterar ett List objekt värde som representerar den lokala datagatewayen som definierats för Logic Apps i lagrings gruppen. |

## <a name="create-the-on-premises-gateway-connection"></a>Skapa den lokala gateway-anslutningen
Den här anslutningen kan komma åt en lokal Informix-databas med hjälp av den lokala datagatewayen. Mer information finns i avsnittet om gateway. 

1. I konfigurations fönstret **gateways** väljer du **kryss rutan** för att aktivera **Anslut via gateway**. Se vilka inställningar som ändras från molnet till lokalt.
2. Skriv värde för **Server**, i form av ett kolon-portnummer för adress eller alias. Skriv till exempel `ibmserver01:9089`.
3. Skriv värde för **databas**. Skriv till exempel `nwind`.
4. Välj värde för **autentisering**. Välj till exempel **Basic**.
5. Skriv värde för **användar namn**. Skriv till exempel `informix`.
6. Skriv värde för **lösen ord**. Skriv till exempel `Password1`.
7. Välj värde för **Gateway**. Välj till exempel **datagateway01**.
8. Fortsätt genom att välja **skapa** . 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Skapa moln anslutningen
Den här anslutningen har åtkomst till en molnbaserad Informix-databas. 

1. I konfigurations fönstret **gateways** lämnar du **kryss rutan** inaktive rad (inte klickat) **Anslut via gateway**. 
2. Skriv värde för **anslutnings namn**. Skriv till exempel `hisdemo2`.
3. Skriv värde för **Informix-servernamn**i formatet adress eller alias kolon port nummer. Skriv till exempel `hisdemo2.cloudapp.net:9089`.
4. Ange värde för **Informix-databasens namn**. Skriv till exempel `nwind`.
5. Skriv värde för **användar namn**. Skriv till exempel `informix`.
6. Skriv värde för **lösen ord**. Skriv till exempel `Password1`.
7. Fortsätt genom att välja **skapa** . 
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Hämta alla rader med SELECT
Du kan skapa en Logic app-åtgärd för att hämta alla rader i Informix-tabellen. Den här åtgärden instruerar anslutningen att bearbeta en Informix SELECT-instruktion, till exempel `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I **Azure start-tavlan**väljer du **+** (plus tecken), **webb och mobilt**och sedan **Logic app**.
2. Ange **namnet** (t. ex. **"InformixgetRows**") **, prenumeration**, **resurs grupp**, **plats**och **App Service plan**. Välj **Fäst på instrument panelen**och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I **Logic Apps designer**väljer du **Tom LogicApp** i listan **mallar** .
2. I listan **utlösare** väljer du **upprepning**. 
3. I **upprepnings** utlösaren väljer du **Redigera**, Välj **frekvens** och väljer **dag**. Välj sedan **intervall** till typ **7**. 
4. Markera rutan **+ nytt steg** och välj sedan **Lägg till en åtgärd**.
5. I listan **åtgärder** skriver du **Informix** i redigerings rutan **Sök efter fler åtgärder** och väljer sedan **Informix-hämta rader (förhands granskning)** .
6. I åtgärden **Hämta rader (förhands granskning)** väljer du **ändra anslutning**.
7. Välj **Skapa ny**i fönstret konfiguration av **anslutningar** . 
   
    ![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png)
8. I konfigurations fönstret **gateways** lämnar du **kryss rutan** inaktive rad (inte klickat) **Anslut via gateway**.
   
   * Skriv värde för **anslutnings namn**. Skriv till exempel `HISDEMO2`.
   * Skriv värde för **Informix-servernamn**i formatet adress eller alias kolon port nummer. Skriv till exempel `HISDEMO2.cloudapp.net:9089`.
   * Ange värde för **Informix-databasens namn**. Skriv till exempel `NWIND`.
   * Skriv värde för **användar namn**. Skriv till exempel `informix`.
   * Skriv värde för **lösen ord**. Skriv till exempel `Password1`.
9. Fortsätt genom att välja **skapa** .
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. I listan **tabell namn** **väljer du nedåtpilen och väljer**sedan **Area**.
11. Du kan också välja **Visa avancerade alternativ** för att ange frågealternativ.
12. Välj **Spara**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. På bladet **InformixgetRows** i listan **alla körningar** under **Sammanfattning**väljer du det första-listade objektet (senaste körning).
14. Välj **Kör information**i bladet **Logic app-körning** . I listan **åtgärd** väljer du **Get_rows**. Se värdet för **status**, som bör vara **slutfört**. Välj **länken indata** för att Visa indata. Välj **länken utdata**och visa utdata. som ska innehålla en lista med rader.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Lägg till en rad med hjälp av Infoga
Du kan skapa en Logic app-åtgärd för att lägga till en rad i en Informix-tabell. Den här åtgärden instruerar kopplingen att bearbeta en Informix INSERT-instruktion, till exempel `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I **Azure start-tavlan**väljer du **+** (plus tecken), **webb och mobilt**och sedan **Logic app**.
2. Ange **namnet**, till exempel `InformixinsertRow`, **prenumeration**, **resurs grupp**, **plats**och **App Service plan**. Välj **Fäst på instrument panelen**och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I **Logic Apps designer**väljer du **Tom LogicApp** i listan **mallar** .
2. I listan **utlösare** väljer du **upprepning**. 
3. I **upprepnings** utlösaren väljer du **Redigera**, Välj **frekvens** och väljer **dag**. Välj sedan **intervall** till typ **7**. 
4. Markera rutan **+ nytt steg** och välj sedan **Lägg till en åtgärd**.
5. I listan **åtgärder** skriver du **Informix** i redigerings rutan **Sök efter fler åtgärder** och väljer sedan **Informix-Infoga rad (för hands version)** .
6. I åtgärden **Hämta rader (förhands granskning)** väljer du **ändra anslutning**. 
7. Välj en anslutning i fönstret **anslutningar** konfiguration. Välj till exempel **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. I listan **tabell namn** **väljer du nedåtpilen och väljer**sedan **Area**.
9. Ange värden för alla obligatoriska kolumner (se röd asterisk). Skriv till exempel `99999` för **AREAID**, skriv `Area 99999`och skriv `102` för **REGIONID**. 
10. Välj **Spara**.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. På bladet **InformixinsertRow** i listan **alla körningar** under **Sammanfattning**väljer du det första-listade objektet (senaste körning).
12. Välj **Kör information**i bladet **Logic app-körning** . I listan **åtgärd** väljer du **Get_rows**. Se värdet för **status**, som bör vara **slutfört**. Välj **länken indata** för att Visa indata. Välj **länken utdata**och visa utdata. den nya raden bör inkluderas.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Hämta en rad med SELECT
Du kan skapa en Logic app-åtgärd för att hämta en rad i en Informix-tabell. Den här åtgärden instruerar anslutningen att bearbeta en Informix SELECT WHERE-instruktion, till exempel `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I **Azure start-tavlan**väljer du **+** (plus tecken), **webb och mobilt**och sedan **Logic app**.
2. Ange **namnet**, till exempel `InformixgetRow`, **prenumeration**, **resurs grupp**, **plats**och **App Service plan**. Välj **Fäst på instrument panelen**och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I **Logic Apps designer**väljer du **Tom LogicApp** i listan **mallar** .
2. I listan **utlösare** väljer du **upprepning**. 
3. I **upprepnings** utlösaren väljer du **Redigera**, Välj **frekvens** och väljer **dag**. Välj sedan **intervall** till typ **7**. 
4. Markera rutan **+ nytt steg** och välj sedan **Lägg till en åtgärd**.
5. I listan **åtgärder** skriver du **Informix** i redigerings rutan **Sök efter fler åtgärder** och väljer sedan **Informix-hämta rader (förhands granskning)** .
6. I åtgärden **Hämta rader (förhands granskning)** väljer du **ändra anslutning**. 
7. I fönstret **anslutningar** konfigurationer väljer du att välja en befintlig anslutning. Välj till exempel **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. I listan **tabell namn** **väljer du nedåtpilen och väljer**sedan **Area**.
9. Ange värden för alla obligatoriska kolumner (se röd asterisk). Skriv till exempel `99999` för **AREAID**. 
10. Du kan också välja **Visa avancerade alternativ** för att ange frågealternativ.
11. Välj **Spara**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. På bladet **InformixgetRow** i listan **alla körningar** under **Sammanfattning**väljer du det första-listade objektet (senaste körning).
13. Välj **Kör information**i bladet **Logic app-körning** . I listan **åtgärd** väljer du **Get_rows**. Se värdet för **status**, som bör vara **slutfört**. Välj **länken indata** för att Visa indata. Välj **länken utdata**och visa utdata. den ska innehålla rad.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Ändra en rad med hjälp av UPDATE
Du kan skapa en Logic app-åtgärd för att ändra en rad i en Informix-tabell. Den här åtgärden instruerar anslutningen att bearbeta en UPPDATERINGs instruktion i Informix, till exempel `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I **Azure start-tavlan**väljer du **+** (plus tecken), **webb och mobilt**och sedan **Logic app**.
2. Ange **namnet**, till exempel `InformixupdateRow`, **prenumeration**, **resurs grupp**, **plats**och **App Service plan**. Välj **Fäst på instrument panelen**och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I **Logic Apps designer**väljer du **Tom LogicApp** i listan **mallar** .
2. I listan **utlösare** väljer du **upprepning**. 
3. I **upprepnings** utlösaren väljer du **Redigera**, Välj **frekvens** och väljer **dag**. Välj sedan **intervall** till typ **7**. 
4. Markera rutan **+ nytt steg** och välj sedan **Lägg till en åtgärd**.
5. I listan **åtgärder** skriver du **Informix** i redigerings rutan **Sök efter fler åtgärder** och väljer sedan **Informix-uppdaterings rad (för hands version)** .
6. I åtgärden **Hämta rader (förhands granskning)** väljer du **ändra anslutning**. 
7. I fönstret **anslutningar** konfigurationer väljer du att välja en befintlig anslutning. Välj till exempel **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. I listan **tabell namn** **väljer du nedåtpilen och väljer**sedan **Area**.
9. Ange värden för alla obligatoriska kolumner (se röd asterisk). Skriv till exempel `99999` för **AREAID**, skriv `Updated 99999`och skriv `102` för **REGIONID**. 
10. Välj **Spara**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. På bladet **InformixupdateRow** i listan **alla körningar** under **Sammanfattning**väljer du det första-listade objektet (senaste körning).
12. Välj **Kör information**i bladet **Logic app-körning** . I listan **åtgärd** väljer du **Get_rows**. Se värdet för **status**, som bör vara **slutfört**. Välj **länken indata** för att Visa indata. Välj **länken utdata**och visa utdata. den nya raden bör inkluderas.
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Ta bort en rad med hjälp av ta bort
Du kan skapa en Logic app-åtgärd för att ta bort en rad i en Informix-tabell. Den här åtgärden instruerar anslutningen att bearbeta en Informix DELETE-instruktion, till exempel `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Skapa en logikapp
1. I **Azure start-tavlan**väljer du **+** (plus tecken), **webb och mobilt**och sedan **Logic app**.
2. Ange **namnet**, till exempel `InformixdeleteRow`, **prenumeration**, **resurs grupp**, **plats**och **App Service plan**. Välj **Fäst på instrument panelen**och välj sedan **skapa**.

### <a name="add-a-trigger-and-action"></a>Lägg till en utlösare och åtgärd
1. I **Logic Apps designer**väljer du **Tom LogicApp** i listan **mallar** .
2. I listan **utlösare** väljer du **upprepning**. 
3. I **upprepnings** utlösaren väljer du **Redigera**, Välj **frekvens** och väljer **dag**. Välj sedan **intervall** till typ **7**. 
4. Markera rutan **+ nytt steg** och välj sedan **Lägg till en åtgärd**.
5. I listan **åtgärder** skriver du **Informix** i redigerings rutan **Sök efter fler åtgärder** och väljer sedan **Informix-ta bort rad (för hands version)** .
6. I åtgärden **Hämta rader (förhands granskning)** väljer du **ändra anslutning**. 
7. I fönstret **anslutningar** konfigurationer väljer du en befintlig anslutning. Välj till exempel **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. I listan **tabell namn** **väljer du nedåtpilen och väljer**sedan **Area**.
9. Ange värden för alla obligatoriska kolumner (se röd asterisk). Skriv till exempel `99999` för **AREAID**. 
10. Välj **Spara**. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. På bladet **InformixdeleteRow** i listan **alla körningar** under **Sammanfattning**väljer du det första-listade objektet (senaste körning).
12. Välj **Kör information**i bladet **Logic app-körning** . I listan **åtgärd** väljer du **Get_rows**. Se värdet för **status**, som bör vara **slutfört**. Välj **länken indata** för att Visa indata. Välj **länken utdata**och visa utdata. den borttagna raden bör inkluderas.
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="supported-informix-platforms-and-versions"></a>Informix-plattformar och-versioner som stöds
Den här anslutningen har stöd för följande IBM Informix-versioner, när de konfigureras som stöd för DRDA-klient anslutningar (Distributed Relations databas arkitektur).

* IBM Informix 12,1
* IBM Informix 11,7

## <a name="connector-specific-details"></a>Anslutningsspecifika Detaljer

Visa eventuella utlösare och åtgärder som definierats i Swagger och se även eventuella begränsningar i [anslutnings informationen](/connectors/informix/). 

## <a name="next-steps"></a>Nästa steg
[Skapa en Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Utforska de andra tillgängliga anslutningarna i Logic Apps i vår [API-lista](apis-list.md).

