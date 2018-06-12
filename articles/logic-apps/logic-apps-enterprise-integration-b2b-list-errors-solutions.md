---
title: 'Logic Apps B2B lista över fel och lösningar: Azure App Service | Microsoft Docs'
description: Logic Apps B2B lista över fel och lösningar
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/02/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 8503792d57dd7ed64434d5a306afce850ced77db
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298346"
---
# <a name="logic-apps-b2b-list-of-errors-and-solutions"></a>Logic Apps B2B lista över fel och lösningar  
Den här artikeln hjälper dig att felsöka problem som kan inträffa i Logic Apps B2B-scenarier och ger förslag på åtgärder för att korrigera felen.


## <a name="agreement-resolution"></a>Avtalet upplösning

### <a name="no-agreement-found"></a>* Inget avtal hittades 

|   |   |  
|---|---|
| Felbeskrivning | Inget avtal med avtal upplösning parametrar hittades|    
| Användaråtgärd | Avtalet bör läggas till kontot integrering med överenskomna business identiteter.</br> Företag identiteter måste matcha meddelande-ID: n|  
|   |   |

### <a name="-no-agreement-found-with-identities"></a>* Inget avtal hittades med identiteter

|   |   | 
|---|---|
| Felbeskrivning | Inget avtal med identiteter hittades: 'AS2Identity':: 'Partner1' och 'AS2Identity':: 'Partner3'| 
| Användaråtgärd | Ogiltig AS2-från eller AS2-till konfigurerade för avtalet. </br> Rätt AS2 meddelandet AS2-från eller AS2-meddelande huvuden med avtal konfigurationer i sidhuvuden eller att matcha AS2-ID: n i AS2-avtal |
|   |   |     

## <a name="as2"></a>AS2

### <a name="-missing-as2-message-headers"></a>* Saknas AS2-meddelandehuvuden  

|   |   |  
|---|---|
| Felbeskrivning| Ogiltig AS2-huvuden. En av ' AS2-att ' eller ' AS2-från-huvuden är tom| 
| Användaråtgärd | Ett AS2-meddelande togs emot som inte innehåller AS2-från eller AS2-att eller båda huvuden. </br> Kontrollera AS2-meddelande AS2-från och AS2-huvuden och rätt dem baserat på konfigurationen för avtal |
|  |  | 


### <a name="-missing-as2-message-body-and-headers"></a>* Saknas AS2 meddelandetexten och rubriker    

|   |   |  
|---|---|
| Felbeskrivning| Frågeinnehållet är null eller tomt | 
| Användaråtgärd | Ett AS2-meddelande togs emot som inte innehåller meddelandetext |
|  |  | 

### <a name="-as2-message-decryption-failure"></a>* Krypteringsfel för AS2-meddelande

|   |   | 
|---|---|
| Felbeskrivning |  [bearbetas/fel: dekryptering misslyckades] | 
| Användaråtgärd | Lägg till @base64ToBinary till AS2Message innan du skickar till partner 
```java
            "HTTP": {
                "inputs": {
                    "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
                    "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
                    "method": "POST",
                    "uri": "xxxxx.xxx"
                },
                
``` 

### <a name="-mdn-decryption-failure"></a>* MDN krypteringsfel

|   |   | 
|---|---|
| Felbeskrivning |  [bearbetas/fel: dekryptering misslyckades] | 
| Användaråtgärd | Lägg till @base64ToBinary till MDN innan du skickar till partner 
```java
            "Response": {
                "inputs": {
                    "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
                    "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
                    "statusCode": 200
                },
                
``` 

### <a name="-missing-signing-certificate"></a>* Saknas signeringscertifikat

|   |   |  
|---|---|
| Felbeskrivning| Signeringscertifikat har inte konfigurerats för AS2 part. </br> AS2-från: partner1 AS2-till: partner2 | 
| Användaråtgärd | Konfigurera inställningar för AS2-avtal med rätt certifikat för signatur |
|  |  | 

## <a name="x12-and-edifact"></a>X12 och EDIFACT

### <a name="-leading-or-trailing-space-found"></a>* Inledande eller avslutande blanksteg hittades    
    
|   |   | 
|---|---|
| Felbeskrivning | Ett fel uppstod vid parsning. Ange Edifact-transaktion med id ' 123456 'som finns i interchange (utan grupp) med id ' 987654', med avsändar-id 'Partner1', mottagaren id 'Partner2' pausas med följande fel: inledande avslutande avgränsare hittades |
| Användaråtgärd | Avtalet-inställningar som konfigureras för att tillåta inledande och avslutande blanksteg. </br> Redigera inställningar för avtalet om du vill tillåta inledande och avslutande blanksteg |
|   |   |

![ge utrymme](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="-duplicate-check-has-enabled-in-the-agreement"></a>* Dubbla kontroll har aktiverats i avtalet

|   |   | 
|---|---| 
| Felbeskrivning | Dubblettkontrollnummer |
| Användaråtgärd | Det här felet indikerar det mottagna meddelandet har dubbla kontrollen siffror. </br> Korrigera antalet kontroll och skicka meddelandet igen |
|   |   |

### <a name="-missing-schema-in-the-agreement"></a>* Saknas schemat i avtalet

|   |   | 
|---|---| 
| Felbeskrivning | Ett fel uppstod vid parsning. X12 transaktion uppsättningen med id '564220001' finns i funktionella grupp med id '56422', i utbyte med id '000056422' med ' 12345678-avsändar-id', mottagaren id ' 87654321 ' som pausats med följande fel ”meddelandet har en okänd dokumenttyp och matchade inte någon av de befintliga scheman som konfigureras i avtalet” |
| Användaråtgärd | Konfigurera schemat i inställningarna för avtal  |
|   |   |

### <a name="-incorrect-schema-in-the-agreement"></a>* Felaktigt schema i avtalet

|   |   | 
|---|---| 
| Felbeskrivning | Meddelandet har en okänd dokumenttyp och matchade inte något av de befintliga scheman som konfigurerats i avtalet. |
| Användaråtgärd | Konfigurera rätt schema i inställningarna för avtal  |
|   |   |

## <a name="flat-file"></a>Flat fil

### <a name="-input-message-with-no-body"></a>* Indatameddelande med ingen brödtext

|   |   | 
|---|---|
| Felbeskrivning | InvalidTemplate. Det går inte att bearbeta mallspråksuttryck i indata för åtgärden 'Flat_File_Decoding' på rad '1'- och '1902': ' krävs för egenskapen 'content-förväntar sig ett värde men erhöll null. Sökvägen ''.'. |
| Användaråtgärd | Det här felet indikerar det inkommande meddelandet inte innehåller en text |
|   |   | 

## <a name="learn-more"></a>Läs mer
[Mer information om Enterprise-Integrationspaket](logic-apps-enterprise-integration-overview.md)