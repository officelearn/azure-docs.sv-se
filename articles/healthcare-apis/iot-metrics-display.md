---
title: Visa och konfigurera Azure IoT-anslutningsprogram för FHIR-mått (förhandsversion)
description: Den här artikeln förklarar hur du visar och konfigurerar Azure IoT Connector för FHIR (för hands version) mått
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/29/2020
ms.author: jasteppe
ms.openlocfilehash: 1cdae789b8286be408735fff92e2de46e28ce514
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394296"
---
# <a name="view-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Visa och konfigurera Azure IoT-anslutningsprogram för FHIR-mått (förhandsversion) 

I den här artikeln får du lära dig hur du visar och konfigurerar Azure IoT Connector för FHIR *-mått. 

> [!TIP]
> Följ anvisningarna i [Exportera mått för Azure IoT Connector för FHIR (för hands version) via diagnostikinställningar](./iot-metrics-diagnostics-export.md) för att lära dig hur du konfigurerar export av mått data.

## <a name="view-metrics-for-azure-iot-connector-for-fhir-preview"></a>Visa mått för Azure IoT Connector för FHIR (för hands version)
1. Om du vill visa mått för IoT-kopplingar väljer du din Azure API för FHIR-tjänst i Azure Portal. 

2. Navigera till **mått** 

3. Välj fliken **IoT-koppling** .

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="IoT-Connector1" lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. Välj en IoT-koppling för att visa dess mått (till exempel: det finns (4) IoT-kopplingar som är kopplade till det här Azure API för FHIR-tjänsten).

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="IoT-Connector2" lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

> [!NOTE]
> På fliken **anpassad** kan du skapa vissa tids-/datum kombinationer för att Visa IoT Connector-mått.

5. Välj tids period för IoT Connector-mått som ska visas (till exempel: 1 timme, 24 timmar, 7 dagar eller anpassat).

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="IoT-Connector3" lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metrics-types-for-azure-iot-connector-for-fhir-preview"></a>Mått typer för Azure IoT Connector för FHIR (för hands version) 
De visade måtten för IoT-koppling är följande:

|Mått typ|Mått syfte| 
|-----------|--------------|
|Antal inkommande meddelanden|Antalet mottagna rå inkommande meddelanden (till exempel: enhets händelser).|
|Antal normaliserade meddelanden|Antalet normaliserade meddelanden.|
|Antal meddelande grupper|Antalet grupper som har sammanställt meddelanden i den angivna tids perioden.|
|Genomsnittlig normaliserad fas fördröjning|Genomsnittlig fördröjning för normaliserings fasen. Normalisering av steg är att utföra normalisering på rå inkommande meddelanden.|
|Genomsnittlig svars tid för grupp fas|Genomsnittlig svars tid för grupp stadiet. Group Stage är för att utföra buffring, agg regering och gruppering av normaliserade meddelanden.| 
|Totalt antal fel|Totalt antal fel.| 

## <a name="focusing-and-configuring-azure-iot-connector-for-fhir-preview-metrics"></a>Fokusera och konfigurera Azure IoT Connector för FHIR (för hands version) mått
I det här exemplet kommer vi att fokusera på **antalet inkommande meddelande** mått.

1. Välj en tidpunkt som du vill fokusera på.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="IoT-Connector4" lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. Från den här skärmen kan du **lägga till mått** , **lägga till filter** och **tillämpa delning** för ytterligare anpassningar. 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="IoT-Connector5" lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>Slutsats 
Att ha åtkomst till data Plans mått är viktigt för övervakning och fel sökning.  Azure IoT Connector för FHIR hjälper dig att utföra dessa åtgärder via mått. 

## <a name="next-steps"></a>Nästa steg

Ta en titt på vanliga frågor om Azure IoT Connector för FHIR.

>[!div class="nextstepaction"]
>[Vanliga frågor och svar om Azure IoT Connector för FHIR](fhir-faq.md)

* I Azure Portal kallas Azure IoT Connector för FHIR IoT Connector (för hands version).

FHIR är ett registrerat varumärke som tillhör HL7 och används med tillåtelse av HL7.