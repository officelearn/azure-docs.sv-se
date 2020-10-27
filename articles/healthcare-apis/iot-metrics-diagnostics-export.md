---
title: Exportera Azure IoT-anslutningsprogram för FHIR-mått (förhandsversion) via diagnostikinställningar
description: I den här artikeln förklaras hur du exporterar Azure IoT Connector för FHIR (för hands version) mått via diagnostikinställningar
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/16/2020
ms.author: jasteppe
ms.openlocfilehash: d7779c74a562e1237db863d7759b2adcffa2bddf
ms.sourcegitcommit: d3c3f2ded72bfcf2f552e635dc4eb4010491eb75
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92558558"
---
# <a name="export-azure-iot-connector-for-fhir-preview-metrics-through-diagnostic-settings"></a>Exportera Azure IoT-anslutningsprogram för FHIR-mått (förhandsversion) via diagnostikinställningar

I den här artikeln får du lära dig hur du exporterar Azure IoT Connector för FHIR * Metrics-loggar. Den funktion som möjliggör mått loggning är [**diagnostikinställningar**](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) i Azure Portal. 

> [!TIP]
> Följ anvisningarna i [Aktivera diagnostisk loggning i Azure API för FHIR och Azure IoT Connector för FHIR](enable-diagnostic-logging.md#enable-diagnostic-logging-in-azure-api-for-fhir) för att konfigurera gransknings loggning.

## <a name="enable-metric-logging-for-the-azure-iot-connector-for-fhir-preview"></a>Aktivera mått loggning för Azure IoT Connector för FHIR (för hands version)
1. Om du vill aktivera mått loggning för Azure IoT-anslutningsprogrammet för FHIR väljer du Azure API för FHIR-tjänsten i Azure Portal 

2. Navigera till **diagnostikinställningar** 

3. Välj **+ Lägg till diagnostisk inställning**

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="IoT-Connector1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. Ange ett namn i dialog rutan **namn på diagnos inställning** .

5. Välj den metod som du vill använda för att få åtkomst till dina diagnostikloggar:

    1. **Arkivera till ett lagrings konto** för granskning eller manuell kontroll. Det lagrings konto som du vill använda måste redan ha skapats.
    2. **Strömma till händelsehubben** för inmatning av en tjänst från tredje part eller en anpassad analys lösning. Du måste skapa ett namn område och en Event Hub-princip innan du kan konfigurera det här steget.
    3. **Strömma till Log Analytics** arbets ytan i Azure Monitor. Du måste skapa din loggar Analytics-arbetsyta innan du kan välja det här alternativet.

6. Välj **fel, trafik och svars tid** för Azure IoT-ANSLUTNINGSPROGRAMMET för FHIR och eventuella ytterligare mått kategorier som du vill avbilda för Azure API för FHIR.

7. Välj **Spara**

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT-Connector1" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> Det kan ta upp till 15 minuter innan de första mått loggarna visas i valfri lagrings plats.  
 
Mer information om hur du arbetar med diagnostikloggar finns i [dokumentationen för Azure Resource log](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-overview)

## <a name="conclusion"></a>Slutsats 
Att ha åtkomst till mått loggar är viktigt för övervakning och fel sökning.  Med Azure IoT Connector för FHIR kan du utföra dessa åtgärder via mått loggar. 

## <a name="next-steps"></a>Nästa steg

Ta en titt på vanliga frågor om Azure IoT Connector för FHIR.

>[!div class="nextstepaction"]
>[Vanliga frågor och svar om Azure IoT Connector för FHIR](fhir-faq.md)

* I Azure Portal kallas Azure IoT Connector för FHIR IoT Connector (för hands version).

FHIR är ett registrerat varumärke som tillhör HL7 och används med tillåtelse av HL7.
