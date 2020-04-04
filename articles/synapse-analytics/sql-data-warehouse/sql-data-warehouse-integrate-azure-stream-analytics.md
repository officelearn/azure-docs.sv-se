---
title: Använda Azure Stream Analytics
description: Tips om hur du använder Azure Stream Analytics med ditt informationslager i Azure Synapse för att utveckla lösningar i realtid.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 2/5/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e281f8a1fb3959256d836134b4c59f5399deb9bd
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633289"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Använda Azure Stream Analytics med Azure Synapse Analytics

Azure Stream Analytics är en fullständigt hanterad tjänst som ger låg latens, högtillgänglig, skalbar komplex händelsebearbetning över strömmande data i molnet. Du kan lära dig grunderna genom att läsa [Introduktion till Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Du kan sedan lära dig hur du skapar en heltäckande lösning med Stream Analytics genom att följa guiden [Kom igång med Azure Stream Analytics.](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

I den här artikeln får du lära dig hur du använder ditt informationslager som utdatamottagare för dina Azure Stream Analytics-jobb.

## <a name="prerequisites"></a>Krav

* Azure Stream Analytics Job – Om du vill skapa ett Azure Stream Analytics-jobb följer du stegen i [självstudien Kom igång med Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) för att:  

    1. Skapa en händelsehubbinmatning
    2. Konfigurera och starta händelsegeneratorprogram
    3. Etablera ett Stream Analytics-jobb
    4. Ange jobbinmatning och fråga
* Azure Synapse SQL pool data warehouse - Om du vill skapa ett nytt informationslager följer du stegen i [Snabbstarten för att skapa ett nytt informationslager](create-data-warehouse-portal.md).

## <a name="specify-streaming-output-to-point-to-your-data-warehouse"></a>Ange direktutdata som ska peka på ditt informationslager

### <a name="step-1"></a>Steg 1

Gå till ditt Stream Analytics-jobb på Azure-portalen och klicka på **Utdata** under menyn **Jobbtopologi.**

### <a name="step-2"></a>Steg 2

Klicka på knappen **Lägg till** och välj **SQL Database** på rullgardinsmenyn.

![Välj SQL-databas](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutput.png)

### <a name="step-3"></a>Steg 3

Ange följande värden:

* *utdataalias:* Ange ett eget namn för den här projektutdatan.
* *Prenumeration:*
  * Om ditt informationslager finns i samma prenumeration som Stream Analytics-jobbet klickar du på ***Välj SQL-databas från dina prenumerationer***.
  * Om databasen finns i en annan prenumeration klickar du på Ange INSTÄLLNINGAR för SQL-databas manuellt.
* *Databas*: Välj måldatabasen i listrutan.
* *Användarnamn*: Ange användarnamnet för ett konto som har skrivbehörighet för databasen.
* *Lösenord*: Ange lösenordet för det angivna användarkontot.
* *Tabell*: Ange namnet på måltabellen i databasen.
* Klicka på **knappen Spara**

![Ifyllt SQL-databasformulär](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutputdbsettings.png)

### <a name="step-4"></a>Steg 4

Innan du kan köra ett test måste du skapa tabellen i informationslagret.  Kör följande skript för att skapa tabeller med HJÄLP AV SQL Server Management Studio (SSMS) eller ditt val av frågeverktyg.

```sql
CREATE TABLE SensorLog
(
    RecordType VARCHAR(2)
    , SystemIdentity VARCHAR(2)
    , FileNum INT
    , SwitchNum VARCHAR(50)
    , CallingNum VARCHAR(25)
    , CallingIMSI VARCHAR(25)
    , CalledNum VARCHAR(25)
    , CalledIMSI VARCHAR(25)
    , DateS VARCHAR(25)
    , TimeS VARCHAR(25)
    , TimeType INT
    , CallPeriod INT
    , CallingCellID VARCHAR(25)
    , CalledCellID VARCHAR(25)
    , ServiceType VARCHAR(25)
    , [Transfer] INT
    , IncomingTrunk VARCHAR(25)
    , OutgoingTrunk VARCHAR(25)
    , MSRN VARCHAR(25)
    , CalledNum2 VARCHAR(25)
    , FCIFlag VARCHAR(25)
    , callrecTime VARCHAR(50)
    , EventProcessedUtcTime VARCHAR(50)
    , PartitionId int
    , EventEnqueuedUtcTime VARCHAR(50)
    )
WITH (DISTRIBUTION = ROUND_ROBIN)
```

### <a name="step-5"></a>Steg 5

Klicka på ditt jobbnamn på Azure-portalen för Stream Analytics-jobb.  Klicka på knappen ***Testa*** i fönstret ***Utdatainformation.***

![Testknapp på utpoutinformation](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) När anslutningen till databasen lyckas visas ett meddelande i portalen.

### <a name="step-6"></a>Steg 6

Klicka på ***frågemenyn*** under ***Jobbtopologi*** och ändra frågan för att infoga data i streamutdata som du skapade.  Klicka på knappen ***Testa vald fråga*** för att testa frågan.  Klicka på ***Knappen Spara fråga*** när frågetestet har slutförts.

![Spara fråga](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Steg 7

Starta Azure Stream Analytics-jobbet.  Klicka på ***Start-knappen*** på ***Översiktsmenyn.***

![Starta Stream Analytics-jobb](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Klicka på ***Start-knappen*** i startjobbsfönstret.

![Klicka på Start](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Nästa steg

En översikt över integrationen finns i [Integrera andra tjänster](sql-data-warehouse-overview-integrate.md).
Fler utvecklingstips finns i [Designbeslut och kodningstekniker för datalager](sql-data-warehouse-overview-develop.md).
