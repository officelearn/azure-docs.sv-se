---
title: 'Självstudiekurs: Hantera beräkning med Azure-funktioner'
description: Så här använder du Azure-funktioner för att hantera beräkningen av din SQL-pool i Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/27/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: e0317b3a3e7ab13a78a5d1fe3672d664030436ab
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346644"
---
# <a name="use-azure-functions-to-manage-compute-resources-in-azure-synapse-analytics-sql-pool"></a>Använda Azure Functions för att hantera beräkningsresurser i Azure Synapse Analytics SQL-pool

Den här självstudien använder Azure Functions för att hantera beräkningsresurser för en SQL-pool i Azure Synapse Analytics.

För att kunna använda Azure Function App med SQL-pool måste du skapa ett [tjänsthuvudnamnskonto](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) med deltagaråtkomst under samma prenumeration som din SQL-poolinstans. 

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Distribuera timerbaserad skalning med en Azure Resource Manager-mall

För att distribuera mallen behöver du följande information:

- Namnet på den resursgrupp som SQL-poolinstansen finns i
- Namnet på den logiska server som SQL-poolinstansen finns i
- Namn på SQL-poolinstansen
- Klient-ID (katalog-ID) för din Azure Active Directory
- Prenumerations-ID:t 
- Program-ID för tjänstens huvudkonto
- Hemlig nyckel för tjänstens huvudkonto

När du har den föregående informationen distribuerar du den här mallen:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

När du har distribuerat mallen bör du hitta tre nya resurser: en kostnadsfri Azure App Service Plan, en förbrukningsbaserad funktionsappplan och ett lagringskonto som hanterar loggningen och operationskön. Fortsätta att läsa mer i andra avsnitt om du vill se hur du ändrar de distribuerade funktionerna så att de passar dina behov.

## <a name="change-the-compute-level"></a>Ändra beräkningsnivån

1. Gå till funktionsapptjänsten. Om du har distribuerat mallen med standardvärden ska tjänsten ha namnet *DWOperations*. När funktionsappen är öppen bör du se fem funktioner som har driftsatts inom ramen för din funktionsapptjänst. 

   ![Funktioner som distribueras med mallen](./media/manage-compute-with-azure-functions/five-functions.png)

2. Välj antingen *DWScaleDownTrigger* eller *DWScaleUpTrigger* beroende på om du vill ändra skala upp eller ned tid. Välj Integrera i den nedrullningsbara menyn.

   ![Välj Integrera för funktionen](./media/manage-compute-with-azure-functions/select-integrate.png)

3. Det aktuella värdet ska antingen vara *%ScaleDownTime%* eller *%ScaleUpTime%*. Dessa värden anger att schemat baseras på värden som definierats i dina [programinställningar](../../azure-functions/functions-how-to-use-azure-function-app-settings.md). För tillfället kan du ignorera det här värdet och ändra schemat till önskad tid baserat på nästa steg.

4. I schemaområdet kan du lägga till tid för det CRON-uttryck som du vill ska visa hur ofta du vill att SQL Data Warehouse ska skalas upp. 

   ![Ändra funktionsschemat](./media/manage-compute-with-azure-functions/change-schedule.png)

   Värdet för `schedule` är ett [CRON-uttryck](https://en.wikipedia.org/wiki/Cron#CRON_expression) som innehåller dessa sex fält: 
   ```json
   {second} {minute} {hour} {day} {month} {day-of-week}
   ```

   Till exempel skulle *"0 30 9 * * 1-5"* återspegla en utlösare varje vardag kl 9:30. Mer information finns i Azure Functions [schemaexempel](../../azure-functions/functions-bindings-timer.md#example).


## <a name="change-the-time-of-the-scale-operation"></a>Ändra tid för skalningsåtgärden

1. Gå till funktionsapptjänsten. Om du har distribuerat mallen med standardvärden ska tjänsten ha namnet *DWOperations*. När funktionsappen är öppen bör du se fem funktioner som har driftsatts inom ramen för din funktionsapptjänst. 

2. Välj antingen *DWScaleDownTrigger* eller *DWScaleUpTrigger* beroende på om du vill ändra skala upp eller ned beräkningsvärdet. När du har valt funktionerna ska fönstret innehålla filen *index.js*.

   ![Ändra beräkningsnivån för funktionsutlösaren](././media/manage-compute-with-azure-functions/index-js.png)

3. Ändra värdet för *ServiceLevelObjective* till den nivå som du vill använda och klicka på Spara. Det här värdet är den beräkningsnivå som informationslagerinstansen skalas till baserat på schemat som definierats i avsnittet Integrera.

## <a name="use-pause-or-resume-instead-of-scale"></a>Använd funktionerna Pausa eller Återuppta istället för Skala 

Standardfunktionerna är för närvarande *DWScaleDownTrigger* och *DWScaleUpTrigger*. Om du vill använda funktionerna för att pausa och återuppta istället kan du aktivera *DWPauseTrigger* eller *DWResumeTrigger*.

1. Gå till  funktionsfönstret.

   ![Funktionsfönstret](./media/manage-compute-with-azure-functions/functions-pane.png)



2. Klicka på skjutreglaget för de utlösare som du vill aktivera.

3. Navigera till fliken *Integrera* för respektive utlösare om du vill ändra schemat.

   > [!NOTE]
   > Den funktionella skillnaden mellan skalningsutlösare och paus-/återuppta-utlösare är det meddelande som skickas till kön. Mer information finns i [Lägga till en ny utlösarfunktion](manage-compute-with-azure-functions.md#add-a-new-trigger-function).


## <a name="add-a-new-trigger-function"></a>Lägg till en ny utlösare

För närvarande ingår bara två skalningsfunktioner i mallen. Med dessa funktioner, under loppet av en dag, kan du bara skala ner en gång och upp en gång. För mer detaljerad kontroll, till exempel nedskalning flera gånger per dag eller med olika skalningsbeteende på helgerna, måste du lägga till en annan utlösare.

1. Skapa en ny tom funktion. Välj *+* knappen nära platsen Funktioner om du vill visa funktionsmallsfönstret.

   ![Skapa ny funktion](./media/manage-compute-with-azure-functions/create-new-function.png)

2. Under Språk väljer du *Javascript* och *TimerTrigger*.

   ![Skapa ny funktion](./media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Namnge din funktion och konfigurera ett schema. Bilden visar hur man utlöser en funktion varje lördag vid midnatt (sent fredag kväll).

   ![Skala ned på lördag](./media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Kopiera innehållet i *index.js* från någon av de andra utlösarfunktionerna.

   ![Kopiera index.js](././media/manage-compute-with-azure-functions/index-js.png)

5. Ställ in åtgärdsvariabeln på önskat beteende enligt följande:

   ```javascript
   // Resume the SQL pool instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the SQL pool instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the SQL pool instance to DW600
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600"
   }
   ```


## <a name="complex-scheduling"></a>Komplex schemaläggning

Det här avsnittet visar kortfattat vad som krävs för att få mer komplex schemaläggning av paus-, återuppta- och skalningsfunktioner.

### <a name="example-1"></a>Exempel 1:

Daglig uppskalning till DW600 kl. 08.00 och nedskalning till DW200 kl. 20.00.

| Funktion  | Schema     | Åtgärd                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW600"}` |
| Function2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-2"></a>Exempel 2: 

Daglig skala upp vid 08:00 till DW1000, skala ner en gång till DW600 vid 16:00, och skala ner vid 10:00 till DW200.

| Funktion  | Schema     | Åtgärd                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
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

Läs mer om Azures funktioner för [timerutlösare](../../azure-functions/functions-create-scheduled-function.md).

Checka ut [databasen för SQL-poolprover](https://github.com/Microsoft/sql-data-warehouse-samples).

