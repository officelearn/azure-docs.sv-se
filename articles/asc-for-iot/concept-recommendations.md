---
title: Förstå ASC för IoT-säkerhetsrekommendationer förhandsversion | Microsoft Docs
description: Läs mer om begreppet säkerhetsrekommendationer och hur de används i ASC för IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 02ced504-d3aa-4770-9d10-b79f80af366c
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: 1e4582d93d1e3380ecdabdb241f27839d4da4565
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541864"
---
# <a name="security-recommendations"></a>Säkerhetsrekommendationer

> [!IMPORTANT]
> ASC för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

ASC för IoT tillhandahåller baseras på pågående lösning analys och följande rekommendationer när det behövs för att förbättra och skydda dina enheter, driftstatus och övergripande IoT Hub-miljön. 


## <a name="device-recommendations"></a>Rekommendationer för enhet

Rekommendationer för enhet ger insikter och förslag för att förbättra säkerheten för enheten och beteende. 

| Severity | Namn                                                      | Datakälla | Beskrivning                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Medel   | Öppna portar på enheten                                      | Agent       | En lyssnarslutpunkt hittades på enheten                                                                                                                                                          |
| Medel   | Tillåtande brandväggsprincipen finns på någon av kedjorna. | Agent       | Tillåtna brandväggsprincipen hittades (indata/utdata). Brandväggsprincip ska neka all trafik som standard, och definiera regler för att tillåta nödvändiga kommunikation till och från enheten.                               |
| Medel   | Tillåtande brandväggsregel i indata kedjan hittades     | Agent       | En regel i brandväggen har hittats som innehåller ett Tillåtande mönster för en mängd olika IP-adresser eller portar.                                                                                    |
| Medel   | Tillåtande brandväggsregel i utdata-kedjan hittades    | Agent       | En regel i brandväggen har hittats som innehåller ett Tillåtande mönster för en mängd olika IP-adresser eller portar.                                                                                   |
| Medel   | Det gick inte att baslinjen validering av systemet           | Agent       | Enheten är inte kompatibel med [riktmärken för CIS-Linux](https://www.cisecurity.org/cis-benchmarks/)                                                                                                         |

### <a name="operational-recommendation"></a>Operativa rekommendation

Operativa rekommendationer ger insikter och förslag för att förbättra agentkonfiguration.

| Severity | Namn                                    | Datakälla | Beskrivning                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Låg      | Agenten skickar unutilized meddelanden          | Agent       | minst 10 procent av säkerhetsvarningar var mindre än 4kb under de senaste 24 timmarna.  |
| Låg      | Twin säkerhetskonfiguration inte optimala | Agent       | Twin säkerhetskonfiguration är inte optimalt.                                        |
| Låg      | Security twin konfigurationskonflikt    | Agent       | Konflikter identifierades i twin säkerhetskonfiguration.                           |


## <a name="iot-hub-recommendations"></a>IoT Hub-rekommendationer

Rekommendationen aviseringar ger insikt och förslag på åtgärder för att förbättra säkerhetspositionen för din miljö.  

| Severity | Namn                                                     | Datakälla | Beskrivning                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Hög     | Identiska autentiseringsuppgifter som används av flera enheter | IoT Hub     | IoT Hub-autentiseringsuppgifter som används av flera enheter. Detta kan tyda på en icke-legitimt enhet personifiera en giltig enhet. Duplicerade autentiseringsuppgift ökar risken för enheten personifiering av en skadlig aktör. |
| Medel   | Standardprincipen för IP-filter ska neka                  | IoT Hub     | IP-filterkonfiguration ska har regler som definierats för tillåten trafik och ska som standard, neka alla andra trafik som standard.                                                                                                     |
| Medel   | IP-filterregeln innehåller stora IP-adressintervall                   | IoT Hub     | Ett Tillåt IP-filter rule källa IP-intervall är för stor. Alltför Tillåtande regler kan exponera din IoT hub och skadliga aktörer.                                                                                       |
| Låg      | Aktivera diagnostikloggar i IoT Hub                       | IoT Hub     | Aktivera loggar och behålla för upp till ett år. Behålla loggar kan du återskapa aktivitet spår undersökning när en säkerhetsincident inträffar eller nätverket komprometteras.                                       |
|