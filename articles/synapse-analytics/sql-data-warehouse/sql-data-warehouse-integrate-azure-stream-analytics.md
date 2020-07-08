---
title: Använd Azure Stream Analytics
description: Tips om hur du använder Azure Stream Analytics med ditt informations lager i Azure Synapse för att utveckla real tids lösningar.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 2/5/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 90e339ba8454dfdfc3f724ea12932a3e8e5912c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213354"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Använda Azure Stream Analytics med Azure Synapse Analytics

Azure Stream Analytics är en helt hanterad tjänst som tillhandahåller låg latens, skalbar komplex händelse behandling med hög tillgänglighet för strömning av data i molnet. Du kan lära dig grunderna genom [att läsa introduktion till Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Du kan sedan lära dig hur du skapar en lösning från slut punkt till slut punkt med Stream Analytics genom att följa själv studie kursen [komma igång med Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

I den här artikeln får du lära dig hur du använder data lagret som en utgående mottagare för dina Azure Stream Analytics jobb.

## <a name="prerequisites"></a>Krav

* Azure Stream Analytics jobb – om du vill skapa ett Azure Stream Analytics jobb följer du stegen i självstudien [komma igång med Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) för att:  

    1. Skapa en Event Hub-inmatare
    2. Konfigurera och starta Event Generator-programmet
    3. Etablera ett Stream Analytics jobb
    4. Ange inmatade jobb och fråga
* Data lager för Azure Synapse SQL-pool – om du vill skapa ett nytt data lager följer du stegen i [snabb starten för att skapa ett nytt informations lager](create-data-warehouse-portal.md).

## <a name="specify-streaming-output-to-point-to-your-data-warehouse"></a>Ange strömmande utdata så att de pekar på ditt informations lager

### <a name="step-1"></a>Steg 1

Gå till ditt Stream Analytics jobb från Azure Portal och klicka på **utdata** på menyn **jobb sto pol Ogin** .

### <a name="step-2"></a>Steg 2

Klicka på knappen **Lägg till** och välj **SQL Database** på den nedrullningsbara menyn.

![Välj SQL Database](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutput.png)

### <a name="step-3"></a>Steg 3

Ange följande värden:

* *Alias för utdata*: Ange ett eget namn för jobbets utdata.
* *Prenumeration*:
  * Om informations lagret är i samma prenumeration som Stream Analytics jobb klickar du på ***välj SQL Database från dina prenumerationer***.
  * Om din databas finns i en annan prenumeration klickar du på Ange SQL Database inställningar manuellt.
* *Databas*: Välj mål databas i list rutan.
* *Användar namn*: Ange användar namnet för ett konto som har Skriv behörighet för databasen.
* *Lösen ord*: Ange lösen ordet för det angivna användar kontot.
* *Tabell*: Ange namnet på mål tabellen i databasen.
* Klicka på knappen **Spara**

![Slutfört SQL Database formulär](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutputdbsettings.png)

### <a name="step-4"></a>Steg 4

Innan du kan köra ett test måste du skapa tabellen i ditt informations lager.  Kör följande skript för att skapa tabell med SQL Server Management Studio (SSMS) eller ditt val av frågeinställningar.

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

Klicka på jobb namnet på Azure Portal för Stream Analytics jobb.  Klicka på knappen ***testa*** i fönstret ***utmatnings information*** .

![Test knapp på Outpout information ](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) när anslutningen till databasen lyckas visas ett meddelande i portalen.

### <a name="step-6"></a>Steg 6

Klicka på menyn ***fråga*** under ***jobb sto pol Ogin*** och ändra frågan om du vill infoga data i Stream-utdata som du skapade.  Testa frågan genom att klicka på knappen ***testa vald fråga*** .  Klicka på knappen ***Spara fråga*** när du har lyckats testa frågan.

![Spara fråga](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Steg 7

Starta Azure Stream Analytics jobbet.  Klicka på ***Start*** -knappen på ***översikts*** menyn.

![Starta Stream Analytics-jobb](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Klicka på ***Start*** -knappen i fönstret Starta jobb.

![Klicka på Start](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Nästa steg

En översikt över integrering finns i [integrera andra tjänster](sql-data-warehouse-overview-integrate.md).
Mer utvecklings tips finns i [design beslut och kod metoder för informations lager](sql-data-warehouse-overview-develop.md).
