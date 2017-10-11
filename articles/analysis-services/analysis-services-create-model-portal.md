---
title: "Skapa en tabellmodell med hjälp av Azure Analysis Services Web-designer | Microsoft Docs"
description: "Lär dig hur du skapar en tabellmodell Azure Analysis Services med hjälp av Webbdesigner i Azure-portalen."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/21/2017
ms.author: owend
ms.openlocfilehash: bd58f1845dabf6afb47ce27236d14479677a8808
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="create-a-model-in-azure-portal"></a>Skapa en modell i Azure-portalen

Funktionen Azure Analysis Services web designer (förhandsversion) i Azure-portalen innehåller ett snabbt och enkelt sätt att skapa och redigera tabellmodeller och fråga modellen data direkt i webbläsaren. 

Kom ihåg, Webbdesigner är **preview**. När nya funktioner har lagts till hela tiden, i preview är funktionerna begränsade. För mer avancerade modellen utveckling och testning är det bäst att använda Visual Studio (SSDT) och SQL Server Management Studio (SSMS).

## <a name="prerequisites"></a>Krav

- En Azure Analysis Services-server till Standard eller utvecklare nivån. Nya modeller som skapats med hjälp av Webbdesigner är DirectQuery, stöds endast av dessa nivåer.
- Azure SQL Database, Azure SQL Data Warehouse eller Power BI Desktop (.pbix)-filen som en datakälla. Nya modeller som skapats från Power BI Desktop filer stöd Azure SQL Database, Azure SQL Data Warehouse, Oracle och Teradata-datakällor.
- Ett SQL Server-konto och lösenord för att ansluta till Azure SQL Database eller Azure SQL Data Warehouse-datakällor.

## <a name="to-create-a-new-tabular-model"></a>Skapa en ny tabellmodell

1. I serverns **översikt** bladet > **Webbdesigner**, klickar du på **öppna**.

    ![Skapa en modell i Azure-portalen](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. I **Webbdesigner** > **modeller**, klickar du på **+ Lägg till**.

    ![Skapa en modell i Azure-portalen](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. I **ny modell**, Skriv ett namn och välj sedan en datakälla.

    ![Dialogrutan ny modell i Azure-portalen](./media/analysis-services-create-model-portal/aas-create-portal-new-model.png)

4. I **Anslut**, ange anslutningsegenskaper. Användarnamn och lösenord måste vara en SQL Server-konto.

     ![Ansluta dialogrutan i Azure-portalen](./media/analysis-services-create-model-portal/aas-create-portal-connect.png)

5. I **tabeller och vyer**, Välj tabellerna som du vill inkludera i din modell och klicka sedan på **skapa**. Är att skapa relationer automatiskt mellan tabeller med ett nyckelpar.

     ![Välj tabeller och vyer](./media/analysis-services-create-model-portal/aas-create-portal-tables.png)

Din nya modell visas i webbläsaren. Här kan göra du följande:   

- Fråga modelldata genom att dra fält till frågedesignern och lägga till filter.
- Skapa nya åtgärder i tabeller.
- Redigera modellmetadata med hjälp av json-redigerare.
- Öppna modellen i Visual Studio (SSDT), Power BI Desktop eller Excel.

![Välj tabeller och vyer](./media/analysis-services-create-model-portal/aas-create-portal-query.png)

> [!NOTE]
> När du redigerar modellmetadata eller skapa nya åtgärder i webbläsaren, sparar du ändringarna i modellen i Azure. Om du arbetar också på din modell i SSDT, Power BI Desktop eller Excel, kan du få synkroniserad din modell.


## <a name="next-steps"></a>Nästa steg 
[Hantera databasroller och användare](analysis-services-database-users.md)  
[Anslut till Excel](analysis-services-connect-excel.md)  


