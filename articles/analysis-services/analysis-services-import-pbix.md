---
title: Importera en Power BI Desktop-fil till Azure Analysis Services | Microsoft Docs
description: Beskriver hur du importerar en Power BI Desktop-fil (pbix) med hjälp av Azure-portalen.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 35bf2ba85017de43788f802b6244d61ed2bb62df
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="import-a-power-bi-desktop-file"></a>Importera en Power BI Desktop-fil

Du kan skapa en ny modell i Azure som genom att importera en Power BI Desktop-fil (pbix). Modellen metadata, cachelagrade data och datasource anslutningar har importerats. Rapporter och visualiseringar har inte importerats.

**Begränsningar**   
- Pbix-modell kan ansluta till Azure SQL Database och Azure SQL Data Warehouse-datakällor endast. 
- Pbix-modellen kan inte ha live eller DirectQuery-anslutningar. 
- Importen misslyckas om datamodellen pbix innehåller metadata som inte stöds i Analysis Services.

## <a name="to-import-from-pbix"></a>Importera från pbix

1. I serverns **översikt** > **Webbdesigner**, klickar du på **öppna**.

    ![Skapa en modell i Azure-portalen](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. I **Webbdesigner** > **modeller**, klickar du på **+ Lägg till**.

    ![Skapa en modell i Azure-portalen](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. I **ny modell**, Skriv ett namn och sedan välja Power BI Desktop-filen.

    ![Dialogrutan ny modell i Azure-portalen](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. I **Import**, leta upp och markera filen.

     ![Ansluta dialogrutan i Azure-portalen](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="see-also"></a>Se också

[Skapa en modell i Azure-portalen](analysis-services-create-model-portal.md)   
[Anslut till Azure Analysis Services](analysis-services-connect.md)  
