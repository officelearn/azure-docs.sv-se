---
title: Importera en Power BI Desktop-fil till Azure Analysis Services | Microsoft Docs
description: "Beskriver hur du importerar en Power BI Desktop-fil (pbix) med hjälp av Azure-portalen."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/26/2018
ms.author: owend
ms.openlocfilehash: 43eab587a1e5209069a248f1e2e1f57af158a2b8
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="import-a-power-bi-desktop-file"></a>Importera en Power BI Desktop-fil

Du kan skapa en ny modell i Azure som genom att importera en Power BI Desktop-fil (pbix). Modellen metadata, cachelagrade data och datasource anslutningar har importerats. Rapporter och visualiseringar har inte importerats. En gång i din server modell kan ändras genom att uppdatera och importera pbix genom att använda funktionen web designer (förhandsversion) i portalen eller med hjälp av SQL Server Management Studio (SSMS). Importerade modeller kan inte öppnas eller exporteras till Visual Studio.

> [!NOTE]
> Om din modell pbix ansluter till lokala datakällor, ett [lokala gateway](analysis-services-gateway.md) måste vara konfigurerad för servern.

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
