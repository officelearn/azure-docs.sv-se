---
title: Skapa en tabellmodell med hjälp av Azure Analysis Services-webbdesignern | Microsoft Docs
description: Lär dig hur du skapar en Azure Analysis Services-tabellmodell med webbdesignern i Azure-portalen.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0b613a978c15715519ecc0f130ff6ff3ee84e306
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54186973"
---
# <a name="create-a-model-in-azure-portal"></a>Skapa en modell i Azure-portalen

Azure Analysis Services web designer (förhandsgranskning)-funktionen i Azure-portalen ger ett snabbt och enkelt sätt att skapa och redigera tabellmodeller och fråga modellen data direkt i webbläsaren. 

> [!IMPORTANT]
> Den här funktionen är inaktuell. Den kan tas bort eller ändrat i en kommande uppdatering. Vi rekommenderar att du sluta använda den här funktionen i nya och befintliga projekt för att bibehålla kompatibilitet med framtida uppdateringar. För mer avancerade modellen utveckling och testning är det bäst att använda Visual Studio (SSDT) och SQL Server Management Studio (SSMS).


## <a name="before-you-begin"></a>Innan du börjar

- Azure Analysis Services-servern måste vara på nivån Standard eller utvecklare. Nya modeller som skapats med hjälp av webbdesignern är DirectQuery stöds endast av dessa nivåer.
- En Azure SQL Database, Azure SQL Data Warehouse eller Power BI Desktop (.pbix)-fil som en datakälla. Nya modeller som skapats från Power BI Desktop-filer support Azure SQL Database och Azure SQL Data Warehouse.
- Ett SQL Server-konto och lösenord för att ansluta till Azure SQL Database eller Azure SQL Data Warehouse-datakällor.
- Du måste ha administratörsrättigheter för server för att skapa en ny modell. Databasen admin-behörighet krävs för att redigera och fråga en modell med hjälp av designern.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="to-create-a-new-tabular-model"></a>Skapa en ny tabellmodell

1. På din server **översikt** > **webbdesignern**, klickar du på **öppna**.

    ![Skapa en modell i Azure-portalen](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. I **webbdesignern** > **modeller**, klickar du på **+ Lägg till**.

    ![Skapa en modell i Azure-portalen](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. I **nya modellen**, Skriv ett namn och välj sedan en datakälla.

    ![Dialogrutan ny modell i Azure-portalen](./media/analysis-services-create-model-portal/aas-create-portal-new-model.png)

4. I **Connect**, ange anslutningsegenskaperna. Användarnamn och lösenord måste vara en SQL Server-konto.

     ![Ansluta dialogrutan i Azure-portalen](./media/analysis-services-create-model-portal/aas-create-portal-connect.png)

5. I **tabeller och vyer**, Välj tabeller att inkludera i din modell och klickar sedan på **skapa**. Relationer skapas automatiskt mellan tabeller med en nyckel.

     ![Markera tabeller och vyer](./media/analysis-services-create-model-portal/aas-create-portal-tables.png)

Den nya modellen visas i webbläsaren. Härifrån kan göra du följande:   

- Fråga modelldata genom att dra fält till frågedesignern och lägga till filter.
- Skapa nya åtgärder i tabeller.
- Redigera modellmetadata med hjälp av json-redigeraren.
- Öppna modellen i Visual Studio (SSDT), Power BI Desktop eller Excel.

![Markera tabeller och vyer](./media/analysis-services-create-model-portal/aas-create-portal-query.png)

> [!NOTE]
> När du redigerar modellmetadata eller skapa nya mått i webbläsaren, sparar du ändringarna i din modell i Azure. Om du arbetar också på din modell i SSDT, Power BI Desktop eller Excel, kan din modell bli osynkroniserad.


## <a name="next-steps"></a>Nästa steg 
[Hantera databasroller och användare](analysis-services-database-users.md)  
[Anslut till Excel](analysis-services-connect-excel.md)  


