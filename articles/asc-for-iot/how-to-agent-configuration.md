---
title: Konfigurera säkerhetsagenter
description: Lär dig hur du konfigurerar Azure Security Center för IoT-säkerhetsagenter för användning med azure security center för IoT-säkerhetstjänst.
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
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: e41b7222ca89976674973e1eb700d62765b306f0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311331"
---
# <a name="tutorial-configure-security-agents"></a>Självstudiekurs: Konfigurera säkerhetsagenter

I den här artikeln beskrivs Azure Security Center för IoT-säkerhetsagenter och information om hur du ändrar och konfigurerar dem.

> [!div class="checklist"]
> * Konfigurera säkerhetsagenter
> * Ändra agentbeteende genom att redigera dubbla egenskaper
> * Identifiera standardkonfiguration

## <a name="agents"></a>Agenter

Azure Security Center för IoT-säkerhetsagenter samlar in data från IoT-enheter och utför säkerhetsåtgärder för att minska de identifierade säkerhetsproblemen. Konfigurationen av säkerhetsagenter kan kontrolleras med hjälp av en uppsättning modultvillingegenskaper som du kan anpassa. I allmänhet är sekundära uppdateringar av dessa egenskaper sällsynta.

Azure Security Center för IoT:s tvillingkonfigurationsobjekt för säkerhetsagenter är ett JSON-formatobjekt. Konfigurationsobjektet är en uppsättning kontrollerbara egenskaper som du kan definiera för att styra agentens beteende.

Dessa konfigurationer hjälper dig att anpassa agenten för varje scenario som krävs. Det går till exempel att automatiskt utesluta vissa händelser eller hålla strömförbrukningen till en minimal nivå genom att konfigurera dessa egenskaper.

Använd Azure Security Center för IoT-konfigurationsschema för säkerhetsagenter för att göra ändringar. [schema](https://aka.ms/iot-security-github-module-schema)

## <a name="configuration-objects"></a>Konfigurationsobjekt

Egenskaper som är relaterade till alla Azure Security Center för IoT-säkerhetsagent finns i agentkonfigurationsobjektet, inom avsnittet önskade egenskaper, i **azureiotsecurity-modulen.**

Om du vill ändra konfigurationen skapar och ändrar du det här objektet i den tvillingidentitet för **azureiotsecurity-modulen.**

Om agentkonfigurationsobjektet inte finns i **azureiotsecurity-modulen** twin, är alla säkerhetsagentens egenskapsvärden inställda på standard.

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  }
}
```

## <a name="configuration-schema-and-validation"></a>Konfigurationsschema och validering

Se till att validera agentkonfigurationen mot det här [schemat](https://aka.ms/iot-security-github-module-schema). En agent startar inte om konfigurationsobjektet inte matchar schemat.

Om konfigurationsobjektet ändras till en ogiltig konfiguration medan agenten körs (konfigurationen matchar inte schemat), ignorerar agenten den ogiltiga konfigurationen och fortsätter att använda den aktuella konfigurationen.

### <a name="configuration-validation"></a>Validering av konfiguration

Azure Security Center för IoT-säkerhetsagenten rapporterar sin aktuella konfiguration i avsnittet rapporterade egenskaper i den tvillingidentitet för **azureiotsecurity-modulen.**
Agenten rapporterar alla tillgängliga egenskaper, om en egenskap inte har angetts av användaren, rapporterar agenten standardkonfigurationen.

För att validera konfigurationen jämför du de värden som angetts i önskat avsnitt med de värden som rapporteras i det rapporterade avsnittet.

Om det finns en obalans mellan de önskade och de rapporterade egenskaperna kunde agenten inte tolka konfigurationen.

Validera önskade egenskaper mot [schemat,](https://aka.ms/iot-security-github-module-schema)åtgärda felen och ange önskade egenskaper igen!

> [!NOTE]
> En konfigurationsfelavisering kommer att avfyras från agenten om agenten inte kunde tolka önskad konfiguration.
> Jämför det rapporterade och önskade avsnittet för att förstå om aviseringen fortfarande gäller

## <a name="editing-a-property"></a>Redigera en egenskap

Alla anpassade egenskaper måste anges i agentkonfigurationsobjektet i **azureiotsecurity-modulen** twin.
Om du vill använda ett standardegenskapsvärde tar du bort egenskapen från konfigurationsobjektet.

### <a name="setting-a-property"></a>Ställa in en egenskap

1. Leta reda på och välj den enhet du vill ändra i IoT Hub.

1. Klicka på enheten och sedan på **azureiotsecurity-modulen.**

1. Klicka på **Module Identity Twin**.

1. Redigera de egenskaper som du vill ändra i säkerhetsmodulen.

   Om du till exempel vill konfigurera anslutningshändelser som högprioriterade händelser och samla in händelser med hög prioritet var 7:e minut använder du följande konfiguration.

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

Om du vill använda ett standardegenskapsvärde tar du bort egenskapen från konfigurationsobjektet.

## <a name="default-properties"></a>Standardegenskaper

Följande tabell innehåller de kontrollerbara egenskaperna för Azure Security Center för IoT-säkerhetsagenter.

Standardvärden är tillgängliga i rätt schema i [GitHub](https\://aka.ms/iot-security-module-default).

| Namn| Status | Giltiga värden| Standardvärden| Beskrivning |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Obligatoriskt: falskt |Giltiga värden: Varaktighet i ISO 8601-format |Standardvärde: PT7M |Maxtidsintervall innan meddelanden med hög prioritet skickas.|
|lowPriorityMessageFrequency |Obligatoriskt: falskt|Giltiga värden: Varaktighet i ISO 8601-format |Standardvärde: PT5H |Max tid innan meddelanden med låg prioritet skickas.|
|snapshotFrequency snapshotFrequency snapshotFrequency snapshotFre |Kräv: falskt|Giltiga värden: Varaktighet i ISO 8601-format |Standardvärde PT13H |Tidsintervall för att skapa ögonblicksbilder av enhetsstatus.|
|maxLocalCacheSizeInBytes |Obligatoriskt: falskt |Giltiga värden: |Standardvärde: 2560000, större än 8192 | Maximal lagring (i byte) tillåts för meddelandecachen för en agent. Maximalt utrymme som tillåts lagra meddelanden på enheten innan meddelanden skickas.|
|maxMessageSizeInBytes |Obligatoriskt: falskt |Giltiga värden: Ett positivt tal, större än 8192, mindre än 262144 |Standardvärde: 204800 |Maximal tillåten storlek för en agent till molnmeddelande. Den här inställningen styr mängden maximala data som skickas i varje meddelande. |
|eventPriority${EventName} |Obligatoriskt: falskt |Giltiga värden: Hög, Låg, Av |Standardvärden: |Prioritet för varje agentgenererad händelse |

### <a name="supported-security-events"></a>Säkerhetshändelser som stöds

|Händelsenamn| PropertyName | Standardvärde| Händelse för ögonblicksbilder| Informationsstatus  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Diagnostisk händelse|eventPriorityDiagnostic| Av| False| Agentrelaterade diagnostiska händelser. Använd den här händelsen för utförlig loggning.|
|Konfigurationsfel |eventPriorityConfigurationError |Låg |False |Agenten kunde inte tolka konfigurationen. Verifiera konfigurationen mot schemat.|
|Statistik över tappade händelser |eventPriorityDroppedEventsStatik |Låg |True|Agentrelaterad händelsestatistik. |
|Ansluten maskinvara|eventPriorityConnectedHardware |Låg |True |Ögonblicksbild av all maskinvara som är ansluten till enheten.|
|Lyssna portar|eventPriorityListeringPortar |Hög |True |Ögonblicksbild av alla öppna lyssningsportar på enheten.|
|Skapa process |eventPriorityProcessCreate |Låg |False |Granskningar bearbetar skapandet på enheten.|
|Processen avslutas|eventPriorityProcessTerminate |Låg |False |Granskningar process uppsägning på enheten.|
|Systeminformation |eventPrioritySystemInformation |Låg |True |En ögonblicksbild av systeminformation (till exempel: OS eller CPU).|
|Lokala användare| eventPriorityLocalUsers |Hög |True|En ögonblicksbild av registrerade lokala användare i systemet. |
|Inloggning|  eventPriorityLogin |Hög|False|Granska inloggningshändelserna till enheten (lokala och fjärrinloggningar).|
|Skapa anslutning |eventPriorityConnectionCreate|Låg|False|Granskar TCP-anslutningar som skapats till och från enheten. |
|Konfigurering av brandvägg| eventPriorityFirewallKonfigurering|Låg|True|Ögonblicksbild av enhetsbrandväggskonfiguration (brandväggsregler). |
|Operativsystemets baslinje| händelsePriorityOSBaseline| Låg|True|Ögonblicksbild av enhet OS baslinjekontroll.|
|

## <a name="next-steps"></a>Nästa steg

- [Förstå Azure Security Center för IoT-rekommendationer](concept-recommendations.md)
- [Utforska Azure Security Center för IoT-aviseringar](concept-security-alerts.md)
- [Få åtkomst till råsäkerhetsdata](how-to-security-data-access.md)
