---
title: Lösningar på vanliga fel och problem i B2B-scenarier
description: Hitta lösningar på vanliga fel och problem vid felsökning av B2B-scenarier i Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/02/2017
ms.openlocfilehash: 38e281ce3d8117bff719b1bb572f09acbbb89669
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666694"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>B2B-fel och lösningar för Azure Logic Apps

Den här artikeln hjälper dig att felsöka fel som kan inträffa i Logic Apps B2B-scenarier och föreslår lämpliga åtgärder för att korrigera dessa fel.

## <a name="agreement-resolution"></a>Lösning av avtal

### <a name="no-agreement-found"></a>Inget avtal hittades 

|   |   |  
|---|---|
| Felbeskrivning | Inget avtal hittades med parametrar för avtalsresolution. | 
| Användaråtgärd | Avtalet bör läggas till på integrationskontot med överenskomna affärsidentiteter. </br>Affärsidentiteterna ska matcha till indatameddelande-ID:n. |  
|   |   |

### <a name="no-agreement-found-with-identities"></a>Inget avtal hittades med identiteter

|   |   | 
|---|---|
| Felbeskrivning | Inget avtal hittades med identiteter: "AS2Identity"::"Partner1" och "AS2Identity"::'Partner3' | 
| Användaråtgärd | Ogiltig AS2-Från eller AS2-To konfigurerad för godkännande. </br>Korrigera AS2-meddelandets "AS2-Från" eller "AS2-To"-rubriker eller avtalet om att matcha AS2-ID:na i AS2-meddelanderubrikerna med avtalskonfigurationer. |
|   |   |     

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>AS2-meddelanderubriker saknas  

|   |   |  
|---|---|
| Felbeskrivning | Ogiltiga AS2-huvuden. En av rubrikerna "AS2-To" eller "AS2-From" är tom. | 
| Användaråtgärd | Ett AS2-meddelande togs emot som inte innehöll AS2-Från eller AS2-Till eller båda rubrikerna. </br> Kontrollera AS2-meddelande AS2-Från och AS2-Till-huvuden och korrigera dem baserat på avtalskonfiguration. |
|  |  | 

### <a name="missing-as2-message-body-and-headers"></a>Namntext och rubriker saknas i AS2-meddelande    

|   |   |  
|---|---|
| Felbeskrivning | Begärandeinnehållet är null eller tomt. | 
| Användaråtgärd | Ett AS2-meddelande togs emot som inte innehöll meddelandetexten. |
|  |  | 

### <a name="as2-message-decryption-failure"></a>AS2-meddelandedekrypteringsfel

|   |   | 
|---|---|
| Felbeskrivning |  [bearbetad/fel: dekryptering misslyckades] | 
| Användaråtgärd | Lägg @base64ToBinary till i AS2Message innan du skickar till partner. |
|||

Ett exempel:

```json
"HTTP": {
   "inputs": {
   "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
   "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
   "method": "POST",
   "uri": "xxxxx.xxx"
},
``` 

### <a name="mdn-decryption-failure"></a>MDN-dekrypteringsfel

|   |   | 
|---|---|
| Felbeskrivning |  [bearbetad/fel: dekryptering misslyckades] | 
| Användaråtgärd | Lägg @base64ToBinary till i MDN innan du skickar till partner. | 
|||

Ett exempel:

```json
"Response": {
   "inputs": {
   "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
   "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
   "statusCode": 200
},               
``` 

### <a name="missing-signing-certificate"></a>Signeringscertifikat saknas

|   |   |  
|---|---|
| Felbeskrivning| Signeringscertifikatet har inte konfigurerats för AS2-part. </br>AS2-Från: partner1 AS2-Till: partner2 | 
| Användaråtgärd | Konfigurera AS2-avtalsinställningar med rätt certifikat för signatur. |
|  |  | 

## <a name="x12-and-edifact"></a>X12 och EDIFACT

### <a name="leading-or-trailing-space-found"></a>Inledande eller avslutande utrymme hittades    
    
|   |   | 
|---|---|
| Felbeskrivning | Fel vid tolkning vid tolkning. EDIFACT-transaktionen med ID '123456' i utbyte (utan grupp) med ID '987654', med avsändande ID 'Partner1', mottagare-ID 'Partner2' avbryts med följande fel: <p>"Inledande avslutande avgränsare hittades" |
| Användaråtgärd | De avtalsinställningar som ska konfigureras så att inledande och avslutande utrymme tillåts. </br>Redigera avtalsinställningar för att tillåta inledande och avslutande utrymme. |
|   |   |

![ge utrymme](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>Dubblettkontrollen har aktiverats i avtalet

|   |   | 
|---|---| 
| Felbeskrivning | Duplicera kontrollnummer |
| Användaråtgärd | Det här felet anger att det mottagna meddelandet har dubblettkontrollnummer. </br>Korrigera kontrollnumret och skicka meddelandet igen. |
|   |   |

### <a name="missing-schema-in-the-agreement"></a>Schema saknas i avtalet

|   |   | 
|---|---| 
| Felbeskrivning | Fel vid tolkning vid tolkning. X12-transaktionen med ID '564220001' i funktionell grupp med ID '56422', i utbyte med ID '000056422', med avsändaren ID '12345678', mottagare-ID '87654321' avbryts med följande fel: <p>"Meddelandet har en okänd dokumenttyp och har inte löst till något av de befintliga scheman som konfigurerats i avtalet" |
| Användaråtgärd | Konfigurera schema i avtalsinställningarna.  |
|   |   |

### <a name="incorrect-schema-in-the-agreement"></a>Felaktigt schema i avtalet

|   |   | 
|---|---| 
| Felbeskrivning | Meddelandet har en okänd dokumenttyp och har inte löst till något av de befintliga scheman som konfigurerats i avtalet. |
| Användaråtgärd | Konfigurera rätt schema i avtalsinställningarna. |
|   |   |

## <a name="flat-file"></a>Flat fil

### <a name="input-message-with-no-body"></a>Inmatningsmeddelande utan brödtext

|   |   | 
|---|---|
| Felbeskrivning | Ogiltigtemplate. Det går inte att bearbeta mallspråksuttryck i åtgärd "Flat_File_Decoding" indata på raden "1" och kolumnen "1902": "Obligatorisk egenskap "innehåll" förväntar sig ett värde men fick null. Vägen ''.. |
| Användaråtgärd | Det här felet anger att indatameddelandet inte innehåller någon brödtext. |
|   |   | 

