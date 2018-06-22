---
title: Övervaka Azure Data Factory med OMS | Microsoft Docs
description: Lär dig hur du övervakar Azure Data Factory genom Routning data till Operations Management Suite (OMS) för analys.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: douglasl
ms.openlocfilehash: 4275a4ddcee51d58949b5bd83e4a898cb3dbb389
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36304425"
---
# <a name="monitor-azure-data-factory-with-operations-management-suite-oms"></a>Övervaka Azure Data Factory med Operations Management Suite (OMS)

Du kan använda Azure Data Factory-integrering med Azure-Monitor att vidarebefordra data till Operations Management Suite (OMS). Den här integreringen är användbart i följande scenarier:

1.  Vill du skriva komplexa frågor på ett stort utbud av mått som publiceras av Data Factory till OMS. Du kan också skapa anpassade aviseringar på dessa frågor via OMS.

2.  Du vill övervaka alla datafabriker. Du kan vidarebefordra data från flera datafabriker till en enda OMS-arbetsyta.

## <a name="get-started"></a>Kom igång

### <a name="configure-diagnostic-settings-and-oms-workspace"></a>Konfigurera inställningar för diagnostik- och OMS-arbetsyta

Aktivera diagnostikinställningar för din data factory.

1.  Välj **Azure-Monitor** -> **diagnostikinställningarna** -> Välj data factory -> Aktivera Diagnostics.

    ![Övervakaren-oms-image1.png](media/data-factory-monitor-oms/monitor-oms-image1.png)

2.  Ange diagnostikinställningar inklusive konfiguration av OMS-arbetsyta.

    ![Övervakaren-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="install-azure-data-factory-analytics-oms-pack-from-azure-marketplace"></a>Installera Azure Data Factory Analytics OMS-paket från Azure Marketplace

![Övervakaren-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![Övervakaren-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Klicka på **skapa** och välj OMS-arbetsytan och inställningar för OMS-arbetsyta.

![Övervakaren-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

## <a name="monitor-azure-data-factory-metrics-using-oms"></a>Övervaka Azure Data Factory med OMS

Installera den **Azure Data Factory Analytics** OMS pack skapar en standarduppsättning vyer som möjliggör följande mått:

- ADF körs-1) pipelinen körs av Data Factory

- ADF körs-2) aktiviteten körs av Data Factory

- ADF körs-3) utlösaren körs av Data Factory

- ADF fel-1) topp 10 Pipeline-fel som Data Factory

- ADF fel-2) topp 10 aktiviteten körs av Data Factory

- ADF fel-3) topp 10 utlösaren fel som Data Factory

- ADF statistik-1) aktiviteten körs efter typ

- ADF statistik-2) utlösaren körs efter typ

- ADF statistik-3) Max Pipeline körs varaktighet

![Övervakaren-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![Övervakaren-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

Du kan visualisera ovan mått, titta på frågor bakom de här måtten, redigera frågorna, skapa aviseringar och så vidare.

![Övervakaren-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="next-steps"></a>Nästa steg

Se [övervaka och hantera pipelines programmässigt](https://docs.microsoft.com/en-us/azure/data-factory/monitor-programmatically) att lära dig om att övervaka och hantera pipelines genom att köra skript.
