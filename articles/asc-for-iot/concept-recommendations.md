---
title: Förstå Azure Security Center för IoT-säkerhetsrekommendationer förhandsversion | Microsoft Docs
description: Läs mer om begreppet säkerhetsrekommendationer och hur de används i Azure Security Center för IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 02ced504-d3aa-4770-9d10-b79f80af366c
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: 3364ff6bcf824e5f7d925fdb6d0826d5c3c32369
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794600"
---
# <a name="security-recommendations"></a>Säkerhetsrekommendationer

> [!IMPORTANT]
> Azure Security Center för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Security Center (ASC) för IoT söker igenom dina Azure-resurser och IoT-enheter och ger säkerhetsrekommendationer för att minska din attackyta. Säkerhetsrekommendationer är användbara och syftar till att hjälpa kunderna att som uppfyller till säkerhetsmetoder.

Du hittar en lista över rekommendationer som kan utlösas på din IoT Hub och/eller IoT-enheter i den här artikeln.

## <a name="recommendations-for-iot-devices"></a>Rekommendationer för IoT-enheter

Rekommendationer för enhet ger insikter och förslag för att förbättra säkerhetspositionen för enheten. 

| Allvarsgrad | Namn                                                      | Datakälla | Beskrivning                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Medel   | Öppna portar på enheten                                      | Agent       | En lyssnarslutpunkt hittades på enheten                                                                                                                                                          |
| Medel   | Tillåtande brandväggsprincipen finns på någon av kedjorna. | Agent       | Tillåtna brandväggsprincipen hittades (indata/utdata). Brandväggsprincip ska neka all trafik som standard, och definiera regler för att tillåta nödvändiga kommunikation till och från enheten.                               |
| Medel   | Tillåtande brandväggsregel i indata kedjan hittades     | Agent       | En regel i brandväggen har hittats som innehåller ett Tillåtande mönster för en mängd olika IP-adresser eller portar.                                                                                    |
| Medel   | Tillåtande brandväggsregel i utdata-kedjan hittades    | Agent       | En regel i brandväggen har hittats som innehåller ett Tillåtande mönster för en mängd olika IP-adresser eller portar.                                                                                   |
| Medel   | Det gick inte att baslinjen validering av systemet           | Agent       | Enheten är inte kompatibel med [riktmärken för CIS-Linux](https://www.cisecurity.org/cis-benchmarks/)                                                                                                         |

### <a name="operational-recommendations-for-iot-devices"></a>Operativa rekommendationer för IoT-enheter

Operativa rekommendationer ger insikter och förslag för att förbättra säkerhetskonfiguration för agenten.

| Allvarsgrad | Namn                                    | Datakälla | Beskrivning                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Låg      | Agenten skickar unutilized meddelanden          | Agent       | minst 10 procent av säkerhetsvarningar var mindre än 4kb under de senaste 24 timmarna.  |
| Låg      | Twin säkerhetskonfiguration inte optimala | Agent       | Twin säkerhetskonfiguration är inte optimalt.                                        |
| Låg      | Security twin konfigurationskonflikt    | Agent       | Konflikter identifierades i twin säkerhetskonfiguration.                           |


## <a name="recommendations-for-iot-hub"></a>Rekommendationer för IoT Hub

Rekommendationen aviseringar ger insikt och förslag på åtgärder för att förbättra säkerhetspositionen för din miljö.  

| Allvarsgrad | Namn                                                     | Datakälla | Beskrivning                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Hög     | Identiska autentiseringsuppgifter som används av flera enheter | IoT Hub     | IoT Hub-autentiseringsuppgifter som används av flera enheter. Detta kan tyda på en icke-legitimt enhet personifiera en giltig enhet. Duplicerade autentiseringsuppgift ökar risken för enheten personifiering av en skadlig aktör. |
| Medel   | Standardprincipen för IP-filter ska neka                  | IoT Hub     | IP-filterkonfiguration ska har regler som definierats för tillåten trafik och ska som standard, neka alla andra trafik som standard.                                                                                                     |
| Medel   | IP-filterregeln innehåller stora IP-adressintervall                   | IoT Hub     | Ett Tillåt IP-filter rule källa IP-intervall är för stor. Alltför Tillåtande regler kan exponera din IoT hub och skadliga aktörer.                                                                                       |
| Låg      | Aktivera diagnostikloggar i IoT Hub                       | IoT Hub     | Aktivera loggar och behålla för upp till ett år. Behålla loggar kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.                                       |
|