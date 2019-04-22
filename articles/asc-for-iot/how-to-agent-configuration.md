---
title: Konfigurera Azure Security Center för IoT-agenten förhandsversionen | Microsoft Docs
description: Lär dig hur man konfigurerar agenter för användning med Azure Security Center för IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 1e212e752309fd8347836d462a3394da2fca4a15
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58862785"
---
# <a name="tutorial-configure-security-agents"></a>Självstudier: Konfigurera säkerhetsagenter

> [!IMPORTANT]
> Azure Security Center för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln förklarar Azure Security Center (ASC) för IoT security-agenten, hur du ändrar dem konfigurera ASC för agenter för IoT-säkerhet.

> [!div class="checklist"]
> * Konfigurera säkerhetsagenter
> * Ändra beteendet för agenten genom att redigera tvillingegenskaper
> * Identifiera standardkonfigurationen

## <a name="agents"></a>Agenter

ASC för IoT security agenter samla in data från IoT-enheter och utföra security åtgärder för att minimera identifierade säkerhetsrisker. Agenten säkerhetskonfiguration är kontrolleras med hjälp av en modul tvillingegenskaper som du kan anpassa. I allmänhet är sekundära uppdateringar för de här egenskaperna ovanliga.  

ASC för Iot's security agent twin konfigurationsobjekt är ett .json format-objekt. Konfigurationsobjektet är en uppsättning kontrolleras egenskaper som du kan definiera för att styra beteendet för agenten. 

De här konfigurationerna hjälper dig att anpassa agenten för varje scenario som krävs. Exempelvis är automatiskt exkludera vissa händelser eller behålla energiförbrukningen så möjligt genom att konfigurera dessa egenskaper.  

Använd ASC för agentkonfiguration för IoT-säkerhet [schemat](https://aka.ms/iot-security-github-module-schema) att göra ändringar.  

## <a name="configuration-objects"></a>Konfigurationsobjekt 

Varje ASC för IoT-säkerhetsagenten relaterade egenskapen finns i agent-konfigurationsobjekt, i avsnittet önskade egenskaper för den **azureiotsecurity** modulen. 

Om du vill ändra konfigurationen, skapa och ändra det här objektet inuti den **azureiotsecurity** modulen twin identitet. 

Om agenten konfigurationsobjektet inte finns i den **azureiotsecurity** modultvilling, alla egenskapsvärden för security-agenten är inställd på standardvärdet. 

```json
"desired": {
  "azureiot*com^securityAgentConfiguration^1*0*0": {
  } 
}
```

Se till att verifiera agenten konfigurationsändringarna mot detta [schemat](https://aka.ms/iot-security-github-module-schema).
Agenten startar inte om konfigurationsobjektet inte stämmer överens med schemat.

## <a name="configuration-schema-and-validation"></a>Konfigurationsschemat och validering 

Se till att validera konfigurationen av agenten mot detta [schemat](https://aka.ms/iot-security-github-module-schema). En agent startar inte om konfigurationsobjektet inte stämmer överens med schemat.

 
Om medan agenten körs ändras konfigurationsobjektet till en ogiltiga konfiguration (konfigurationen inte matchar schemat), kommer att ignorera ogiltig konfiguration av agenten och kommer att fortsätta använda den aktuella konfigurationen. 

## <a name="editing-a-property"></a>Redigera en egenskap 

Alla anpassade egenskaper måste anges i konfigurationsobjektet agenten inom den **azureiotsecurity** modultvilling.
Om du vill använda ett standardvärde för egenskapen att ta bort egenskapen från konfigurationsobjektet.

### <a name="setting-a-property"></a>Anger en egenskap

1. Leta upp och välj den enhet som du vill ändra i din IoT-hubb.

1. Klicka på din enhet och sedan på **azureiotsecurity** modulen.

1. Klicka på **identitet Modultvilling**.

1. Redigera önskade egenskaper för modul för maskinvarusäkerhet.
   
   Till exempel för att konfigurera anslutningshändelser med hög prioritet och samla in händelser för hög prioritet var 7: e minut, Använd följande konfiguration.
   
   ```json
    "desired": {
      "azureiot*com^securityAgentConfiguration^1*0*0": {
        "highPriorityMessageFrequency": "PT7M",    
        "eventPriorityConnectionCreate": "High" 
      } 
    }, 
    ```

1. Klicka på **Spara**.

### <a name="using-a-default-value"></a>Med hjälp av ett standardvärde

Om du vill använda ett standardvärde för egenskapen att ta bort egenskapen från konfigurationsobjektet.

## <a name="default-properties"></a>Standardegenskaper 

I följande tabell innehåller egenskaperna kan kontrolleras i ASC för agenter för IoT-säkerhet.

Standardvärden är tillgängliga i rätt schemat i [Github](https://aka.ms/iot-security-module-default).

| Namn| Status | Giltiga värden| Standardvärden| Beskrivning |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Krävs: false |Giltiga värden: Varaktighet i ISO 8601-Format |Standardvärde: PT7M |Maxtid innan du meddelanden med hög prioritet skickas.|
|lowPriorityMessageFrequency |Krävs: false|Giltiga värden: Varaktighet i ISO 8601-Format |Standardvärde: PT5H |Maxtid före med låg prioritet meddelanden skickas.| 
|snapshotFrequency |Kräv: false|Giltiga värden: varaktighet i ISO 8601-Format |Standardvärdet PT13H |Tidsintervall för att skapa ögonblicksbilder av enhetens status.| 
|maxLocalCacheSizeInBytes |Krävs: false |Giltiga värden: |Standardvärde: 2560000, större än 8192 | Maximalt lagringsutrymme (i byte) som tillåts för cacheminnet för statusmeddelanden för en agent. Högsta mängden utrymme som tillåts att lagra meddelanden på enheten innan meddelanden skickas.| 
|maxMessageSizeInBytes |Krävs: false |Giltiga värden: Ett positivt tal större än 8192, mindre än 262144 |Standardvärde: 204800 |Högsta tillåtna storleken på en agent för molnmeddelandet. Den här inställningen styr den maximala data som skickas i varje meddelande. |
|eventPriority${EventName} |Krävs: false |Giltiga värden: Högt, lågt inaktiverat |Standardvärden: |Prioritet för varje agent genereras händelse | 

### <a name="supported-security-events"></a>Stöds säkerhetshändelser

|Händelsenamn| propertyName | Standardvärde| Ögonblicksbild händelse| Information om Status  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Diagnostisk händelse|eventPriorityDiagnostic| Av| Falskt| Agenten relaterade diagnostikhändelser. Använd den här händelsen för utförlig loggning.| 
|Konfigurationsfel |eventPriorityConfigurationError |Låg |Falskt |Det gick inte att parsa konfigurationen av agenten. Kontrollera konfigurationen mot schemat.| 
|Statistik för avbrutna händelser |eventPriorityDroppedEventsStatistics |Låg |Sant|Agenten relaterade händelsestatistik. |
|Meddelandestatistik|eventPriorityMessageStatistics |Låg |Sant |Agenten relaterade meddelandestatistik. |
|Anslutna maskinvara|eventPriorityConnectedHardware |Låg |Sant |Ögonblicksbild av all maskinvara som är anslutna till enheten.|
|Lyssnande portar|eventPriorityListeningPorts |Hög |Sant |Ögonblicksbild av alla öppna lyssnande portar på enheten.|
|Skapa process |eventPriorityProcessCreate |Låg |Falskt |Granskningar bearbetas skapas på enheten.|
|Avsluta process|eventPriorityProcessTerminate |Låg |Falskt |Granskningar bearbetas avslutning på enheten.| 
|Systeminformation |eventPrioritySystemInformation |Låg |Sant |En ögonblicksbild av Systeminformation (till exempel: OS- eller CPU).| 
|Lokala användare| eventPriorityLocalUsers |Hög |Sant|En ögonblicksbild av registrerade lokala användare i systemet. |
|Logga in|  eventPriorityLogin |Hög|Falskt|Granska inloggningshändelser till enheten (lokala och fjärranslutna inloggningar).|
|Skapa anslutning |eventPriorityConnectionCreate|Låg|Falskt|Granskar TCP-anslutningar som skapats till och från enheten. |
|Brandväggskonfiguration| eventPriorityFirewallConfiguration|Låg|Sant|Ögonblicksbild av brandväggskonfiguration för enheten (brandväggsregler). |
|Grundregler för operativsystem| eventPriorityOSBaseline| Låg|Sant|Kontrollera ögonblicksbild av enheten grundregler för operativsystem.|
 

## <a name="next-steps"></a>Nästa steg

- [Förstå ASC för IoT-rekommendationer](concept-recommendations.md)
- [Utforska ASC för IoT-aviseringar](concept-security-alerts.md)
- [Rå säkerhetsdata för åtkomst](how-to-security-data-access.md)
