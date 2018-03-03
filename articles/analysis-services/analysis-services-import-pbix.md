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
ms.openlocfilehash: e0be0c69b501d7e93c65bcf23d4dd1b6bfa89caf
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="import-a-power-bi-desktop-file"></a>Importera en Power BI Desktop-fil

Du kan skapa en ny modell i Azure som genom att importera en Power BI Desktop-fil (pbix). Modellen metadata, cachelagrade data och datasource anslutningar har importerats. Rapporter och visualiseringar har inte importerats.

**Begränsningar**   
- Pbix modellen måste ansluta till [datakällor som stöds av Analysis Services](analysis-services-datasource.md) endast. 
- Pbix-modellen kan inte ha live eller DirectQuery-anslutningar. 
- Om modellen pbix ansluter till lokala datakällor, ett [lokala datagateway](analysis-services-gateway.md) måste konfigureras för Analysis Services-servern.
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
