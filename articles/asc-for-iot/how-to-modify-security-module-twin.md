---
title: Hur du anvisningarna för att ändra en ASC för IoT-modultvilling i ASC för förhandsversionen av IoT | Microsoft Docs
description: Lär dig hur du ändrar en ASC för modultvillingen för IoT-säkerhet för ASC för IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 1bc5dc86-0f33-4625-b3d3-f9b6c1a54e14
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 4bb25db7feddea66e75d9dd069aaec7785faf738
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541954"
---
# <a name="modify-an-asc-for-iot-module-twin"></a>Ändra en ASC för IoT-modultvilling

> [!IMPORTANT]
> ASC för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln förklarar hur du ändrar konfigurationen för en befintlig **AzureIoTSecurity modultvilling** för en befintlig enhet. 

Se [skapa en ASC för IoT-modulen](quickstart-create-security-twin.md) att lära dig hur du gör en ny säkerhetsmodul för en ny enhet.  

## <a name="modification-considerations"></a>Ändring av överväganden

> [!IMPORTANT]
> Varje konfiguration i konfigurationen av twin åsidosätter standardkonfigurationen. Om en viss konfiguration finns inte längre i twin konfigurationen, används standardkonfigurationen. 

## <a name="configuration-schema-and-validation"></a>Konfigurationsschemat och validering 

Se till att validera konfigurationen av agenten mot detta [schemat](https://github.com/Azure/asc-for-iot/schema/security_module_twin). En agent startar inte om konfigurationsobjektet inte stämmer överens med schemat.

 
Om medan agenten körs ändras konfigurationsobjektet till en ogiltiga konfiguration (konfigurationen inte matchar schemat), kommer att ignorera ogiltig konfiguration av agenten och kommer att fortsätta använda den aktuella konfigurationen. 

## <a name="edit-a-property"></a>Redigera en egenskap  

Ange alla anpassade egenskaper i konfigurationsobjektet agenten inom modultvilling AzureIoTSecurity. 

Om du vill ange en egenskap att lägga till Egenskapsnyckeln till konfigurationsobjektet med det önskade värdet. Ta bort egenskapen från konfigurationsobjektet för att använda ett standardvärde för egenskapen:

```json
"desired": { //AzureIoTSecurity Module Identity Twin – desired properties section  
  "azureiot*com^securityAgentConfiguration^1*0*0": { //ASC for IoT Agent 
      // configuration section  
    "hubResourceId": "/subscriptions/82392767-31d3-4bd2-883d-9b60596f5f42/resourceGroups/myResourceGroup/providers/Microsoft.Devices/IotHubs/myIotHub",     
    "lowPriorityMessageFrequency": "PT1H",     
    "highPriorityMessageFrequency": "PT7M",    
    "eventPriorityFirewallConfiguration": "High",     
    "eventPriorityConnectionCreate": "Off" 
  } 
}, 
```

## <a name="properties"></a>Egenskaper 
I följande tabell innehåller alla konfigurerbara egenskaper som styr ASC för IoT-agenter. 
          

| Namn| Status | Giltiga värden| Standardvärden| Beskrivning |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Krävs: false |Giltiga värden: Varaktighet i ISO 8601-Format |Standardvärde: PT7M |Maxtid innan du meddelanden med hög prioritet skickas.|
|lowPriorityMessageFrequency |Krävs: false|Giltiga värden: Varaktighet i ISO 8601-Format |Standardvärde: PT5H |Maxtid före med låg prioritet meddelanden skickas.| 
|snapshotFrequency |Kräv: false|Giltiga värden: varaktighet i ISO 8601-Format |Standardvärdet PT13H |Tidsintervall för att skapa ögonblicksbilder av enhetens status.| 
|maxLocalCacheSizeInBytes |Krävs: false |Giltiga värden: |Standardvärde: 2560000, större än 8192 | Maximalt lagringsutrymme (i byte) som tillåts för cacheminnet för statusmeddelanden för en agent. Högsta mängden utrymme som tillåts att lagra meddelanden på enheten innan meddelanden skickas.| 
|maxMessageSizeInBytes |Krävs: false |Giltiga värden: Ett positivt tal större än 8192, mindre än 262144 |Standardvärde: 204800 |Högsta tillåtna storleken på en agent för molnmeddelandet. Den här inställningen styr den maximala data som skickas i varje meddelande. |
|eventPriority${EventName} |Krävs: false |Giltiga värden: Högt, lågt inaktiverat |Standardvärden: |Prioritet för varje agent genereras händelse. | 
|

### <a name="events"></a>Händelser

Följande lista över händelser är alla händelser ASC för IoT-agenten kan samla in från dina enheter. Använd modultvilling AzureIotSecurity för att konfigurera vilka av dessa händelser har samlats in och bestämma deras prioritet i din lösning. 
 
|Händelsenamn| propertyName | Standardvärde| Ögonblicksbild händelse| Information om statusen  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Diagnostisk händelse|eventPriorityDiagnostic| Av| False| Agenten relaterade diagnostikhändelser. Använd den här händelsen för utförlig loggning.| 
Konfigurationsfel |eventPriorityConfigurationError |Låg |False |Det gick inte att parsa konfigurationen av agenten. Kontrollera konfigurationen mot schemat.| 
|Statistik för avbrutna händelser |eventPriorityDroppedEventsStatistics |Låg |True|Agenten relaterade händelsestatistik. |
|Meddelandestatistik|eventPriorityMessageStatistics |Låg |True |Agenten relaterade meddelandestatistik. |
|Anslutna maskinvara|eventPriorityConnectedHardware |Låg |True |Ögonblicksbild av all maskinvara som är anslutna till enheten.|
|Lyssnande portar|eventPriorityListeningPorts |Hög |True |Ögonblicksbild av alla öppna lyssnande portar på enheten.|
| Skapa process |eventPriorityProcessCreate |Låg |False |Granskningar bearbetas skapas på enheten.|
| Avsluta process|eventPriorityProcessTerminate |Låg |False |Granskningar bearbetas avslutning på enheten.| 
 Systeminformation |eventPrioritySystemInformation |Låg |True |En ögonblicksbild av Systeminformation, till exempel OS eller CPU.|
|Lokala användare| eventPriorityLocalUsers |Hög |True|En ögonblicksbild av registrerade lokala användare i systemet. |
|Inloggning|  eventPriorityLogin |Hög|False|Granska inloggningshändelser till enheten (lokala och fjärranslutna inloggningar).|
|Skapa anslutning |eventPriorityConnectionCreate|Låg|False|Granskar TCP-anslutningar som skapats till och från enheten. |
|Brandväggskonfiguration| eventPriorityFirewallConfiguration|Låg|True|Ögonblicksbild av brandväggskonfiguration för enheten (brandväggsregler). |
|Grundregler för operativsystem| eventPriorityOSBaseline| Låg|True|Kontrollera ögonblicksbild av enheten grundregler för operativsystem.| 
|


## <a name="next-steps"></a>Nästa steg

- Läs ASC för IoT [översikt](overview.md)
- Läs mer om ASC för IoT [arkitektur](architecture.md)
- Förstå och utforska [ASC för IoT-aviseringar](concept-security-alerts.md)
- Upptäck hur du kommer åt din [säkerhetsdata](how-to-security-data-access.md)
