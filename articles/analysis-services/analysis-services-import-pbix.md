---
title: Importera en Power BI Desktop-fil till Azure Analysis Services | Microsoft Docs
description: Beskriver hur du importerar en Power BI Desktop-fil (pbix) med hjälp av Azure-portalen.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: aea6f3efcf3740527c43b75a30caadf6b2a8b623
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601079"
---
# <a name="import-a-power-bi-desktop-file"></a>Importera en Power BI Desktop-fil

Du kan importera en datamodell i Power BI Desktop-fil (pbix) till Azure Analysis Services. Modellen metadata, cachelagrade data och datasource anslutningar har importerats. Rapporter och visualiseringar har inte importerats. Importera data från Power BI Desktop-modeller är på nivån 1400 kompatibilitet.

**Begränsningar**   
- Pbix-modell kan ansluta till **Azure SQL Database** och **Azure SQL Data Warehouse** endast datakällor. 
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
