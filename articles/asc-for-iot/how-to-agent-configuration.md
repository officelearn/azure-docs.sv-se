---
title: Konfigurera Azure Security Center för IoT-agenten | Microsoft Docs
description: Lär dig hur du konfigurerar agenter för användning med Azure Security Center för IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: mlottner
ms.openlocfilehash: 095c8fa080d96c9dc6d40261ee5afc559e9ca06b
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933888"
---
# <a name="tutorial-configure-security-agents"></a>Självstudier: Konfigurera säkerhetsagenter

I den här artikeln beskrivs Azure Security Center för IoT-säkerhetsagenter och information om hur du ändrar och konfigurerar dem. 

> [!div class="checklist"]
> * Konfigurera säkerhetsagenter
> * Ändra agent beteende genom att redigera dubbla egenskaper
> * Identifiera standard konfiguration

## <a name="agents"></a>Agenter

Azure Security Center för IoT-säkerhetsagenter samlar in data från IoT-enheter och utför säkerhets åtgärder för att minimera identifierade sårbarheter. Konfigurationen av säkerhets agenten kan kontrol leras med hjälp av en uppsättning av modulens dubbla egenskaper som du kan anpassa. I allmänhet är sekundära uppdateringar av dessa egenskaper ovanliga.  

Azure Security Center för IoT: s Säkerhetsagenten med dubbla konfigurations objekt är ett JSON-format-objekt. Konfigurationsobjektet är en uppsättning kontrollerbara egenskaper som du kan definiera för att styra agentens beteende. 

Med de här konfigurationerna kan du anpassa agenten för varje scenario som krävs. Du kan till exempel automatiskt utesluta vissa händelser eller behålla energi förbrukningen till en minimal nivå genom att konfigurera dessa egenskaper.  

Använd Azure Security Center för konfigurations [schema](https://aka.ms/iot-security-github-module-schema) för IoT-Säkerhetsagenten för att göra ändringar.  

## <a name="configuration-objects"></a>Konfigurations objekt 

Egenskaper som är relaterade till varje Azure Security Center för IoT Security Agent finns i agentens konfigurations objekt, i avsnittet önskade egenskaper i **azureiotsecurity** -modulen. 

Ändra konfigurationen genom att skapa och ändra det här objektet i **azureiotsecurity** -modulens dubbla identitet. 

Om agentens konfigurations objekt inte finns i **azureiotsecurity** -modulen, anges alla egenskaps värden för säkerhets agenten till default. 

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  } 
}
```

## <a name="configuration-schema-and-validation"></a>Konfigurations schema och verifiering 

Se till att verifiera agent konfigurationen mot [schemat](https://aka.ms/iot-security-github-module-schema). En agent startas inte om konfigurationsobjektet inte matchar schemat.

 
Om agenten körs ändras konfigurationsobjektet till en icke-giltig konfiguration (konfigurationen matchar inte schemat), men agenten ignorerar den ogiltiga konfigurationen och fortsätter att använda den aktuella konfigurationen. 

### <a name="configuration-validation"></a>Konfigurations validering

Azure Security Center för IoT Security Agent rapporterar den aktuella konfigurationen i avsnittet rapporterade egenskaper i **azureiotsecurity** -modulens dubbla identitet.
Agenten rapporterar alla tillgängliga egenskaper, om en egenskap inte har angetts av användaren, rapporterar agenten standard konfigurationen.

Jämför värdena som anges i det önskade avsnittet med de värden som rapporteras i avsnittet rapportering för att verifiera konfigurationen.

Om det finns ett matchnings fel mellan de önskade och de rapporterade egenskaperna, kunde agenten inte parsa konfigurationen.

Verifiera dina önskade egenskaper mot [schemat](https://aka.ms/iot-security-github-module-schema), korrigera felen och ange önskade egenskaper igen!

> [!NOTE]
> En konfigurations fel avisering kommer att skickas från agenten om agenten inte kunde parsa den önskade konfigurationen.
> Jämför det rapporterade och önskade avsnittet för att förstå om aviseringen fortfarande gäller

## <a name="editing-a-property"></a>Redigera en egenskap 

Alla anpassade egenskaper måste anges inuti agentens konfigurations objekt i **azureiotsecurity** -modulen.
Om du vill använda ett standard egenskaps värde tar du bort egenskapen från konfigurationsobjektet.

### <a name="setting-a-property"></a>Ange en egenskap

1. Leta upp och välj den enhet som du vill ändra i IoT Hub.

1. Klicka på enheten och sedan på **azureiotsecurity** -modulen.

1. Klicka på **modulens identitet, delad**.

1. Redigera egenskaperna som du vill ändra i säkerhetsmodulen.
   
   Om du till exempel vill konfigurera anslutnings händelser som hög prioritet och samla in händelser med hög prioritet var 7: e minut, använder du följande konfiguration.
   
    ```json
    "desired": {
        "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
            "highPriorityMessageFrequency": {
                "value": "PT7M"
            },
            "eventPriorityConnectionCreate": {
                "value": "High"
            }
        }
    }
    ```

1. Klicka på **Spara**.

### <a name="using-a-default-value"></a>Använda ett standardvärde

Om du vill använda ett standard egenskaps värde tar du bort egenskapen från konfigurationsobjektet.

## <a name="default-properties"></a>Standard egenskaper 

Följande tabell innehåller de kontrollerbara egenskaperna för Azure Security Center för IoT-säkerhetsagenter.

Standardvärden är tillgängliga i rätt schema i [GitHub](https\://aka.ms/iot-security-module-default).

| Name| State | Giltiga värden| Standardvärden| Beskrivning |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Krävs: falskt |Giltiga värden: Varaktighet i ISO 8601-format |Standardvärde: PT7M |Max tidsintervall innan meddelanden med hög prioritet skickas.|
|lowPriorityMessageFrequency |Krävs: falskt|Giltiga värden: Varaktighet i ISO 8601-format |Standardvärde: PT5H |Max tid innan meddelanden med låg prioritet skickas.| 
|snapshotFrequency |Kräv: falskt|Giltiga värden: Varaktighet i ISO 8601-format |Standardvärde PT13H |Tidsintervall för att skapa ögonblicks bilder av enhets status.| 
|maxLocalCacheSizeInBytes |Krävs: falskt |Giltiga värden: |Standardvärde: 2560000, större än 8192 | Maximalt lagrings utrymme (i byte) som tillåts för meddelande-cache för en agent. Högsta mängd utrymme som tillåts för att lagra meddelanden på enheten innan meddelanden skickas.| 
|maxMessageSizeInBytes |Krävs: falskt |Giltiga värden: Ett positivt tal som är större än 8192, mindre än 262144 |Standardvärde: 204800 |Högsta tillåtna storlek på en agent till moln meddelande. Den här inställningen styr mängden maximalt antal data som skickas i varje meddelande. |
|eventPriority${EventName} |Krävs: falskt |Giltiga värden: Hög, låg, av |Standardvärden: |Prioritet för varje agent-genererad händelse | 

### <a name="supported-security-events"></a>Säkerhets händelser som stöds

|Händelsenamn| propertyName | Default Value| Ögonblicks bilds händelse| Information status  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Diagnostisk händelse|eventPriorityDiagnostic| Av| False| Agent relaterade diagnostiska händelser. Använd den här händelsen för utförlig loggning.| 
|Konfigurations fel |eventPriorityConfigurationError |Låg |False |Agenten kunde inte parsa konfigurationen. Verifiera konfigurationen mot schemat.| 
|Statistik över ignorerade händelser |eventPriorityDroppedEventsStatistics |Låg |Sant|Agent relaterad händelse statistik. |
|Meddelande statistik|eventPriorityMessageStatistics |Låg |Sant |Agent relaterad meddelande statistik. |
|Ansluten maskin vara|eventPriorityConnectedHardware |Låg |Sant |Ögonblicks bild av all maskin vara som är ansluten till enheten.|
|Lyssnande portar|eventPriorityListeningPorts |Hög |Sant |Ögonblicks bild av alla öppna lyssnings portar på enheten.|
|Skapa process |eventPriorityProcessCreate |Låg |False |Granskar process skapande på enheten.|
|Processen avslutas|eventPriorityProcessTerminate |Låg |False |Granskar avslutning av processen på enheten.| 
|System information |eventPrioritySystemInformation |Låg |Sant |En ögonblicks bild av system information (till exempel: Operativ system eller processor).| 
|Lokala användare| eventPriorityLocalUsers |Hög |Sant|En ögonblicks bild av registrerade lokala användare i systemet. |
|Logga in|  eventPriorityLogin |Hög|False|Granska inloggnings händelser för enheten (lokala och fjärranslutna inloggningar).|
|Skapa anslutning |eventPriorityConnectionCreate|Låg|False|Granskar TCP-anslutningar som skapats till och från enheten. |
|Brand Väggs konfiguration| eventPriorityFirewallConfiguration|Låg|Sant|Ögonblicks bild av enhets brand Väggs konfiguration (brand Väggs regler). |
|OS-bas linje| eventPriorityOSBaseline| Låg|Sant|Ögonblicks bild av bas linje kontroll för enhetens operativ system.|
|
 

## <a name="next-steps"></a>Nästa steg

- [Förstå Azure Security Center för IoT-rekommendationer](concept-recommendations.md)
- [Utforska Azure Security Center för IoT-aviseringar](concept-security-alerts.md)
- [Åtkomst till rå säkerhets data](how-to-security-data-access.md)
