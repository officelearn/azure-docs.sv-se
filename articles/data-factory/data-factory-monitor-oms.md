---
title: Övervaka Azure-Datafabrik med OMS | Microsoft Docs
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
ms.date: 08/20/2018
ms.author: douglasl
ms.openlocfilehash: fb5da75d0e42dd0700cad008d348ff846d602409
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "42055972"
---
# <a name="monitor-azure-data-factory-with-operations-management-suite-oms"></a>Övervaka Azure-Datafabrik med Operations Management Suite (OMS)

Du kan använda Azure Data Factory-integrering med Azure Monitor för att vidarebefordra data till Operations Management Suite (OMS). Den här integreringen är användbart i följande scenarier:

1.  Vill du skriva komplexa frågor på en omfattande uppsättning mått som publiceras av Data Factory till OMS. Du kan också skapa anpassade varningar på dessa frågor via OMS.

2.  Du vill övervaka i datafabriker. Du kan dirigera data från flera olika datafabriker för till en enda OMS-arbetsyta.

Titta på följande videoklipp för en sju minuters introduktion och demonstration av den här funktionen:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

## <a name="get-started"></a>Kom igång

### <a name="configure-diagnostic-settings-and-oms-workspace"></a>Konfigurera diagnostikinställningar och OMS-arbetsyta

Aktivera diagnostikinställningar för din datafabrik.

1.  Välj **Azure Monitor** -> **diagnostikinställningar** -> Välj data factory -> Aktivera diagnostik.

    ![Övervakare-oms-image1.png](media/data-factory-monitor-oms/monitor-oms-image1.png)

2.  Ange diagnostikinställningar inklusive konfiguration av OMS-arbetsytan.

    ![Övervakare-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="install-azure-data-factory-analytics-oms-pack-from-azure-marketplace"></a>Installera Azure Data Factory Analytics OMS-hanteringspaket från Azure Marketplace

![Övervakare-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![Övervakare-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Klicka på **skapa** och välja OMS-arbetsyta och inställningar för OMS-arbetsyta.

![Övervakare-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

## <a name="monitor-azure-data-factory-metrics-using-oms"></a>Övervaka Azure Data Factory mått med OMS

Installera den **Azure Data Factory Analytics** OMS-hanteringspaketet skapar en standarduppsättning med vyer som möjliggör följande mått:

- ADF körningar-1) Pipeline-körningar av Data Factory

- Aktivitetskörningar för ADF körningar-2) av Data Factory

- ADF körningar – 3) Trigger Runs av Data Factory

- ADF fel-1) översta 10 Pipeline-fel i Data Factory

- ADF fel-2) översta 10 Aktivitetskörningar av Data Factory

- ADF fel – 3) översta 10 utlösaren fel av Data Factory

- ADF statistik-1) Aktivitetskörningar efter typ

- ADF statistik-2) Trigger Runs efter typ

- ADF statistik – 3) Pipelinekörningar Max varaktighet

![Övervakare-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![Övervakare-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

Du kan visualisera måtten som ovan, titta på frågorna som bakom de här måtten, redigera frågor, skapa aviseringar och så vidare.

![Övervakare-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="next-steps"></a>Nästa steg

Se [övervaka och hantera pipelines programmässigt](https://docs.microsoft.com/en-us/azure/data-factory/monitor-programmatically) och lär dig övervaka och hantera pipelines genom att köra skript.
