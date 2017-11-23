---
title: "Azure SQL Data Warehouse – Använd Azure Functions för att automatisera beräkningsnivåer i SQL Data Warehouse | Microsoft Docs"
description: "Så här använder du Azure Functions för att hantera beräkningar i ditt informationslager."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: barbkess
ms.assetid: 52DFC191-E094-4B04-893F-B64D5828A901
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: quickstart
ms.date: 11/06/2017
ms.author: elbutter
ms.openlocfilehash: e2138f26a78338406b466bdd2aa6e756e602726d
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
---
# <a name="use-azure-functions-to-automate-sql-dw-compute-levels"></a>Använd Azure Functions för att automatisera beräkningsnivåer i SQL Data Warehouse

Den här självstudiekursen visar hur du använder Azure Functions för att hantera beräkningsnivåer i Azure SQL Data Warehouse. Dessa arkitekturer rekommenderas för användning med SQL Data Warehouse [Optimerad för Elasticity][Performance Tiers].

Om du vill kunna använda Azures funktionsapp med SQL Data Warehouse måste du skapa ett [huvudkonto för tjänsten](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal) med deltagarbehörighet inom ramen för samma prenumeration som används för din instans av informationslagret. 

## <a name="deploy-timer-based-scaler-with-an-azure-resource-manager-template"></a>Distribuera tidsbegränsad skalning med en Azure Resource Manager-mall

Om du vill distribuera mallen behöver du följande information:

- Namnet på resursgruppen som din instans av Azure SQL Data Warehouse finns i
- Namnet på den logiska server som din instans av Azure SQL Data Warehouse finns i
- Namnet på din instans av Azure SQL Data Warehouse
- Klient-ID (katalog-ID) för din Azure Active Directory
- Prenumerations-ID:t 
- Program-ID för tjänstens huvudkonto
- Hemlig nyckel för tjänstens huvudkonto

När du har inhämtat ovanstående information kan du distribuera den här mallen:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

När du har distribuerat mallen bör du hitta tre nya resurser: en kostnadsfri Azure App Service-plan, en förbrukningsbaserad funktionsapplan och ett lagringskonto som hanterar kön för loggning och åtgärder. Fortsätta att läsa mer i andra avsnitt om du vill se hur du ändrar de distribuerade funktionerna så att de passar dina behov.

### <a name="change-the-scale-up-or-scale-down-compute-level"></a>Ändra beräkningsnivån för uppskalning eller nedskalning

1. Gå till funktionsapptjänsten. Om du har distribuerat mallen med standardvärden ska tjänsten ha namnet *DWOperations*. När funktionsappen är öppen bör du se fem funktioner som har driftsatts inom ramen för din funktionsapptjänst. 

   ![Funktioner som distribueras med mallen](media/manage-compute-with-azure-functions/five-functions.png)

2. Välj antingen *DWScaleDownTrigger* eller *DWScaleUpTrigger* beroende på om du vill ändra skala upp eller ned tid. Välj Integrera i listrutan.

   ![Välj Integrera för funktionen](media/manage-compute-with-azure-functions/select-integrate.png)

3. Det aktuella värdet ska antingen vara *%ScaleDownTime%* eller *%ScaleUpTime%*. Dessa värden anger att schemat baseras på värden som definierats i dina [programinställningar][Application Settings]. För tillfället kan du ignorera detta och ändra schemat till önskad tid enligt nästkommande steg.

4. I schemaområdet kan du lägga till tid för det CRON-uttryck som du vill ska visa hur ofta du vill att SQL Data Warehouse ska skalas upp. 

  ![Ändra funktionsschemat](media/manage-compute-with-azure-functions/change-schedule.png)

  Värdet för `schedule` är ett [CRON-uttryck](http://en.wikipedia.org/wiki/Cron#CRON_expression) som innehåller dessa sex fält: 
  ```json
  {second} {minute} {hour} {day} {month} {day-of-week}
  ```

  Exemplet *”0 30 9 * * 1-5”* utlöser en åtgärd under arbetsveckans alla dagar kl. 09.30. Mer information finns i Azure Functions [schemaexempel][schedule examples].


### <a name="change-the-scale-up-or-scale-down-time"></a>Ändra tiden för uppskalning eller nedskalning

1. Gå till funktionsapptjänsten. Om du har distribuerat mallen med standardvärden ska tjänsten ha namnet *DWOperations*. När funktionsappen är öppen bör du se fem funktioner som har driftsatts inom ramen för din funktionsapptjänst. 

2. Välj antingen *DWScaleDownTrigger* eller *DWScaleUpTrigger* beroende på om du vill ändra skala upp eller ned beräkningsvärdet. När du har valt funktionerna ska fönstret innehålla filen *index.js*.

   ![Ändra beräkningsnivån för funktionsutlösaren](media/manage-compute-with-azure-functions/index-js.png)

3. Ändra värdet för *ServiceLevelObjective* till den nivå som du vill använda och klicka på Spara. Detta är den beräkningsnivå som din informationslagerinstans skalas till, enligt det schema som definierats i avsnittet Integrera.

### <a name="use-pause-or-resume-instead-of-scale"></a>Använd funktionerna Pausa eller Återuppta istället för Skala 

Standardfunktionerna är för närvarande *DWScaleDownTrigger* och *DWScaleUpTrigger*. Om du vill använda funktionerna för att pausa och återuppta istället kan du aktivera *DWPauseTrigger* eller *DWResumeTrigger*.

1. Gå till  funktionsfönstret.

   ![Funktionsfönstret](media/manage-compute-with-azure-functions/functions-pane.png)



2. Klicka på skjutreglaget för de utlösare som du vill aktivera.

3. Navigera till fliken *Integrera* för respektive utlösare om du vill ändra schemat.

   [!NOTE]: The functional difference between the scaling triggers and the pause/resume triggers is the message that is sent to the queue. See [Add a new trigger function][Add a new trigger function] for more information.



### <a name="add-a-new-trigger-function"></a>Lägg till en ny utlösare

För närvarande ingår bara två skalningsfunktioner i mallen. Det innebär att under loppet av en dag kan du endast skala ned en gång och skala upp en gång. För mer finmaskig kontroll, till exempel nedskalning flera gånger per dag eller olika skalningsbeteenden på helgerna, måste du lägga till ännu en utlösare.

1. Skapa en ny tom funktion. Välj knappen *+* nära funktionsplatsen för att öppna mallfönstret.

   ![Skapa ny funktion](media/manage-compute-with-azure-functions/create-new-function.png)

2. Under Språk väljer du *Javascript* och *TimerTrigger*.

   ![Skapa ny funktion](media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Namnge din funktion och konfigurera ett schema. Bilden visar hur man utlöser en funktion varje lördag vid midnatt (sent fredag kväll).

   ![Skala ned på lördag](media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Kopiera innehållet i *index.js* från någon av de andra utlösarfunktionerna.

   ![Kopiera index.js](media/manage-compute-with-azure-functions/index-js.png)

5. Ställ in åtgärdsvariabeln efter önskat beteende enligt följande:

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


### <a name="complex-scheduling"></a>Komplex schemaläggning

Det här avsnittet innehåller en kort beskrivning av vad som behövs för att uppnå en mer komplex schemaläggning av funktionerna pausa, återuppta och skala.

#### <a name="example-1"></a>Exempel 1:

Daglig uppskalning till DW600 kl. 08.00 och nedskalning till DW200 kl. 20.00.

| Funktion  | Schema     | Åtgärd                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW600"}` |
| Function2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

#### <a name="example-2"></a>Exempel 2: 

Daglig uppskalning till DW1000 kl. 08.00, nedskalning till DW600 kl. 16.00 och nedskalning till DW200 kl. 22.00.

| Funktion  | Schema     | Åtgärd                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

#### <a name="example-3"></a>Exempel 3: 

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

[Performance Tiers]: performance-tiers.md
