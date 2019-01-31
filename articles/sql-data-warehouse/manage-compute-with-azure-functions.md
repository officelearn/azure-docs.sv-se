---
title: 'Självstudier: Hantera beräkning med Azure Functions i Azure SQL Data Warehouse | Microsoft Docs'
description: Så här använder du Azure Functions för att hantera beräkningar i ditt informationslager.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/27/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 0832fbcacd8b58ffaf36ce2e55e3add151a881db
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55470203"
---
# <a name="use-azure-functions-to-manage-compute-resources-in-azure-sql-data-warehouse"></a>Använd Azure Functions för att hantera beräkningsresurser i Azure SQL Data Warehouse

Den här självstudien använder Azure Functions för att hantera beräkningsresurser för ett informationslager i Azure SQL Data Warehouse.

Om du vill kunna använda Azures funktionsapp med SQL Data Warehouse måste du skapa ett [huvudkonto för tjänsten](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) med deltagarbehörighet inom ramen för samma prenumeration som används för din instans av informationslagret. 

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Distribuera timerbaserad skalning med en Azure Resource Manager-mall

Om du vill distribuera mallen behöver du följande information:

- Namnet på resursgruppen som din instans av Azure SQL Data Warehouse finns i
- Namnet på den logiska server som din instans av Azure SQL Data Warehouse finns i
- Namnet på din instans av Azure SQL Data Warehouse
- Klient-ID (katalog-ID) för din Azure Active Directory
- Prenumerations-ID:t 
- Program-ID för tjänstens huvudkonto
- Hemlig nyckel för tjänstens huvudkonto

När du har informationen ovan kan du distribuera den här mallen:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

När du har distribuerat mallen bör du hitta tre nya resurser: en kostnadsfri Azure App Service-Plan, en förbrukningsbaserad Funktionsapp plan och ett lagringskonto som hanterar loggning och operations-kön. Fortsätta att läsa mer i andra avsnitt om du vill se hur du ändrar de distribuerade funktionerna så att de passar dina behov.

## <a name="change-the-compute-level"></a>Ändra Beräkningsnivån

1. Gå till funktionsapptjänsten. Om du har distribuerat mallen med standardvärden ska tjänsten ha namnet *DWOperations*. När funktionsappen är öppen bör du se fem funktioner som har driftsatts inom ramen för din funktionsapptjänst. 

   ![Funktioner som distribueras med mallen](media/manage-compute-with-azure-functions/five-functions.png)

2. Välj antingen *DWScaleDownTrigger* eller *DWScaleUpTrigger* beroende på om du vill ändra skala upp eller ned tid. Välj integrera i den nedrullningsbara menyn.

   ![Välj Integrera för funktionen](media/manage-compute-with-azure-functions/select-integrate.png)

3. Det aktuella värdet ska antingen vara *%ScaleDownTime%* eller *%ScaleUpTime%*. Dessa värden anger att schemat baseras på värden som definierats i dina [programinställningar][Application Settings]. Nu kan du ignorera det här värdet och ändra schemat till önskad tid baserat på nästa steg.

4. I schemaområdet kan du lägga till tid för det CRON-uttryck som du vill ska visa hur ofta du vill att SQL Data Warehouse ska skalas upp. 

  ![Ändra funktionsschemat](media/manage-compute-with-azure-functions/change-schedule.png)

  Värdet för `schedule` är ett [CRON-uttryck](http://en.wikipedia.org/wiki/Cron#CRON_expression) som innehåller dessa sex fält: 
  ```json
  {second} {minute} {hour} {day} {month} {day-of-week}
  ```

  Till exempel *”0 30 9 ** 1-5”* skulle återspeglar en utlösare för varje veckodag kl. 9:30. Mer information finns i Azure Functions [schemaexempel][schedule examples].


## <a name="change-the-time-of-the-scale-operation"></a>Ändra tiden för åtgärden

1. Gå till funktionsapptjänsten. Om du har distribuerat mallen med standardvärden ska tjänsten ha namnet *DWOperations*. När funktionsappen är öppen bör du se fem funktioner som har driftsatts inom ramen för din funktionsapptjänst. 

2. Välj antingen *DWScaleDownTrigger* eller *DWScaleUpTrigger* beroende på om du vill ändra skala upp eller ned beräkningsvärdet. När du har valt funktionerna ska fönstret innehålla filen *index.js*.

   ![Ändra beräkningsnivån för funktionsutlösaren](media/manage-compute-with-azure-functions/index-js.png)

3. Ändra värdet för *ServiceLevelObjective* till den nivå som du vill använda och klicka på Spara. Det här värdet är den beräkningsnivå som din informationslagerinstans skalas till baserat på det schema som definierats i avsnittet integrera.

## <a name="use-pause-or-resume-instead-of-scale"></a>Använd funktionerna Pausa eller Återuppta istället för Skala 

Standardfunktionerna är för närvarande *DWScaleDownTrigger* och *DWScaleUpTrigger*. Om du vill använda funktionerna för att pausa och återuppta istället kan du aktivera *DWPauseTrigger* eller *DWResumeTrigger*.

1. Gå till  funktionsfönstret.

   ![Funktionsfönstret](media/manage-compute-with-azure-functions/functions-pane.png)



2. Klicka på skjutreglaget för de utlösare som du vill aktivera.

3. Navigera till fliken *Integrera* för respektive utlösare om du vill ändra schemat.

   > [!NOTE]
   > Funktionell skillnad mellan skalning utlösare och pausa/återuppta utlösare är meddelandet som skickas till kön. Mer information finns i [lägga till en ny utlösare][Add a new trigger function].


## <a name="add-a-new-trigger-function"></a>Lägg till en ny utlösare

För närvarande ingår bara två skalningsfunktioner i mallen. Med dessa funktioner kan under loppet av en dag, du endast skala ned en gång och upp en gång. För mer finmaskig kontroll, till exempel Nedskalning flera gånger per dag eller olika skalningsbeteenden på helgerna, som du behöver lägga till ännu en utlösare.

1. Skapa en ny tom funktion. Välj den *+* nära funktionsplatsen för att visa panelen funktion.

   ![Skapa ny funktion](media/manage-compute-with-azure-functions/create-new-function.png)

2. Under Språk väljer du *Javascript* och *TimerTrigger*.

   ![Skapa ny funktion](media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Namnge din funktion och konfigurera ett schema. Bilden visar hur man utlöser en funktion varje lördag vid midnatt (sent fredag kväll).

   ![Skala ned på lördag](media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Kopiera innehållet i *index.js* från någon av de andra utlösarfunktionerna.

   ![Kopiera index.js](media/manage-compute-with-azure-functions/index-js.png)

5. Ange åtgärden variabeln till önskat beteende enligt följande:

   ```javascript
   // Resume the data warehouse instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the data warehouse instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the data warehouse instance to DW600
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600"
   }
   ```


## <a name="complex-scheduling"></a>Komplex schemaläggning

Det här avsnittet visar vad som är nödvändigt för att få mer komplex schemaläggning av pausa, återuppta och skala funktioner.

### <a name="example-1"></a>Exempel 1:

Daglig uppskalning till DW600 kl. 08.00 och nedskalning till DW200 kl. 20.00.

| Funktion  | Schema     | Åtgärd                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW600"}` |
| Function2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-2"></a>Exempel 2: 

Daglig uppskalning till DW1000, kl 8 Nedskalning till DW600 klockan 16.00 och Nedskalning till DW200 kl 10.

| Funktion  | Schema     | Åtgärd                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-3"></a>Exempel 3: 

Uppskalning till DW1000 kl. 08.00, nedskalning till DW600 kl. 16.00 under arbetsveckans alla dagar. Pausas fredagar kl. 23.00, återupptas kl. 07.00 på måndag morgon.

| Funktion  | Schema       | Åtgärd                                |
| :-------- | :------------- | :--------------------------------------- |
| Function1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Function4 | 0 0 7 * * 0    | `var operation = {"operationType": "ResumeDw"}` |



## <a name="next-steps"></a>Nästa steg

Läs mer om Azures funktioner för [timerutlösare](../azure-functions/functions-create-scheduled-function.md).

Checka ut [exempeldatabasen](https://github.com/Microsoft/sql-data-warehouse-samples) för SQL Data Warehouse.



[schedule examples]: ../azure-functions/functions-bindings-timer.md#example

[Application Settings]: ../azure-functions/functions-how-to-use-azure-function-app-settings.md
[Add a new trigger function]: manage-compute-with-azure-functions.md#add-a-new-trigger-function
