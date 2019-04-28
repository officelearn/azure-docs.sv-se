---
title: Fel och lösningar för B2B - scenarier för Azure Logic Apps | Microsoft Docs
description: Hitta fel och lösningar för B2B-scenarier i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/02/2017
ms.openlocfilehash: f0591b47ce7ba6837f300088c856c0098fb66710
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60998838"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>B2B-fel och lösningar för Azure Logic Apps

Den här artikeln hjälper dig att felsöka fel som kan inträffa i Logic Apps B2B-scenarier och ger förslag på åtgärder för att korrigera dessa fel.

## <a name="agreement-resolution"></a>Avtalsmatchningen

### <a name="no-agreement-found"></a>Hitta något avtal 

|   |   |  
|---|---|
| Felbeskrivning | Hitta något avtal med avtal upplösning parametrar. | 
| Användaråtgärd | Avtalet ska läggas till integrationskontot med överenskomna företagsidentiteter. </br>Företagsidentiteter måste matcha det inkommande meddelandet ID: N. |  
|   |   |

### <a name="no-agreement-found-with-identities"></a>Hitta något avtal med identiteter

|   |   | 
|---|---|
| Felbeskrivning | Hitta något avtal med identiteter: ”AS2Identity”:: ”Partner1' och 'AS2Identity”:: ”Partner3” | 
| Användaråtgärd | Ogiltigt AS2-från eller AS2-till konfigurerade för avtalet. </br>Korrigera AS2-meddelandet ”AS2-från” eller ”AS2-att” rubriker eller avtalet som matchar AS2-ID: N i meddelandehuvudena AS2 med avtal konfigurationer. |
|   |   |     

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>Saknas rubriker för AS2-meddelande  

|   |   |  
|---|---|
| Felbeskrivning | Ogiltigt AS2-huvuden. En av de ”AS2-att” eller ”AS2-från” sidhuvuden är tomma. | 
| Användaråtgärd | Ett AS2-meddelande togs emot, vilket inte innehöll AS2-från eller AS2-att eller båda rubriker. </br> Kontrollera AS2-meddelandet AS2-från och AS2-sidhuvuden och korrekt dem baserat på konfigurationen för avtalet. |
|  |  | 

### <a name="missing-as2-message-body-and-headers"></a>Saknade AS2 meddelandetexten och rubriker    

|   |   |  
|---|---|
| Felbeskrivning | Innehållet i förfrågan är null eller tomt. | 
| Användaråtgärd | Ett AS2-meddelande togs emot, vilket inte innehöll meddelandetexten. |
|  |  | 

### <a name="as2-message-decryption-failure"></a>Krypteringsfel för AS2-meddelande

|   |   | 
|---|---|
| Felbeskrivning |  [bearbetas/fel: Dekrypteringen misslyckades] | 
| Användaråtgärd | Lägg till @base64ToBinary till AS2Message innan de skickas till partner. |
|||

Exempel:

```json
"HTTP": {
   "inputs": {
   "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
   "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
   "method": "POST",
   "uri": "xxxxx.xxx"
},
``` 

### <a name="mdn-decryption-failure"></a>MDN krypteringsfel

|   |   | 
|---|---|
| Felbeskrivning |  [bearbetas/fel: Dekrypteringen misslyckades] | 
| Användaråtgärd | Lägg till @base64ToBinary till MDN innan de skickas till partner. | 
|||

Exempel:

```json
"Response": {
   "inputs": {
   "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
   "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
   "statusCode": 200
},               
``` 

### <a name="missing-signing-certificate"></a>Signeringscertifikatet saknas

|   |   |  
|---|---|
| Felbeskrivning| Signeringscertifikatet har inte konfigurerats för AS2-parten. </br>AS2-från: partner1 AS2-till: partner2 | 
| Användaråtgärd | Konfigurera inställningar för AS2-avtal med rätt certifikat för signaturen. |
|  |  | 

## <a name="x12-and-edifact"></a>X12 och EDIFACT

### <a name="leading-or-trailing-space-found"></a>Inledande eller avslutande blanksteg har påträffats    
    
|   |   | 
|---|---|
| Felbeskrivning | Ett fel påträffades under parsning. Ange EDIFACT-transaktionsuppsättningen med ID ”123456” som finns i utbytet (utan grupp) med ID: t 987654 ”, med avsändar-ID: 'Partner1', Mottagar-ID:” Partner2 ”har inaktiverats med följande fel: <p>”Inledande avslutande avgränsare har påträffats” |
| Användaråtgärd | Inställningar för avtal att konfigureras för att tillåta inledande och avslutande blanksteg. </br>Redigera avtal inställningar för att tillåta ledande och avslutande blanksteg. |
|   |   |

![Tillåt utrymme](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>Dubbla Kontrollera har aktiverats i avtalet

|   |   | 
|---|---| 
| Felbeskrivning | Dubblettkontrollnummer |
| Användaråtgärd | Det här felet tyder det mottagna meddelandet har duplicerade kontrollnummer. </br>Korrigera kontrollnummer och skicka meddelandet igen. |
|   |   |

### <a name="missing-schema-in-the-agreement"></a>Saknas schemat i avtalet

|   |   | 
|---|---| 
| Felbeskrivning | Ett fel påträffades under parsning. X12 transaktionsuppsättningen med ID '564220001' ingår i funktionsgruppen med ID '56422', i utbytet med ID '000056422 ”, med avsändar-ID: t 12345678-, Mottagar-ID: t 87654321” har inaktiverats med följande fel: <p>”Meddelandet har en okänd dokumenttyp och matchade inte till någon av de befintliga scheman som konfigurerats i avtalet” |
| Användaråtgärd | Konfigurera schema i inställningarna för avtalet.  |
|   |   |

### <a name="incorrect-schema-in-the-agreement"></a>Felaktigt schema i avtalet

|   |   | 
|---|---| 
| Felbeskrivning | Meddelandet har en okänd dokumenttyp och matchade inte något av de befintliga scheman som konfigurerats i avtalet. |
| Användaråtgärd | Konfigurera rätt schema i inställningarna för avtalet. |
|   |   |

## <a name="flat-file"></a>Flat fil

### <a name="input-message-with-no-body"></a>Indatameddelande med ingen brödtext

|   |   | 
|---|---|
| Felbeskrivning | InvalidTemplate. Det går inte att bearbeta mallspråksuttryck i praktiken 'Flat_File_Decoding' indata på rad '1' och kolumnen '1902 ”: ”Krävs för egenskapen” innehåll ”förväntar sig ett värde men har null. Sökvägen '.'. |
| Användaråtgärd | Det här felet indikerar det inkommande meddelandet inte innehåller en brödtext. |
|   |   | 

