---
title: Lösningar för vanliga fel och problem i B2B-scenarier
description: Hitta lösningar för vanliga fel och problem när du felsöker B2B-scenarier i Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/02/2017
ms.openlocfilehash: 38e281ce3d8117bff719b1bb572f09acbbb89669
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "75666694"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>B2B-fel och lösningar för Azure Logic Apps

Den här artikeln hjälper dig att felsöka fel som kan uppstå i Logic Apps B2B scenarier och ger förslag på lämpliga åtgärder för att korrigera felen.

## <a name="agreement-resolution"></a>Avtals lösning

### <a name="no-agreement-found"></a>Inget avtal hittades 

|   |   |  
|---|---|
| Felbeskrivning | Det gick inte att hitta några avtal med avtals matchnings parametrar. | 
| Användaråtgärd | Avtalet bör läggas till i integrations kontot med överenskomna affärs identiteter. </br>Affärs identiteter bör matcha ID: n för indatamängden. |  
|   |   |

### <a name="no-agreement-found-with-identities"></a>Inget avtal hittades med identiteter

|   |   | 
|---|---|
| Felbeskrivning | Inget avtal hittades med identiteter: "AS2Identity":: "Partner1" and'AS2Identity "::" Partner3 " | 
| Användaråtgärd | Ogiltig AS2-från-eller AS2-till-konfigurerad för avtal. </br>Korrigera AS2-meddelandets "AS2-from"-eller "AS2-to"-rubriker eller avtalet för att matcha AS2-ID: na i AS2 meddelande rubriker med avtals konfigurationer. |
|   |   |     

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>AS2 meddelande huvud saknas  

|   |   |  
|---|---|
| Felbeskrivning | Ogiltiga AS2-rubriker. En av rubrikerna "AS2-till" eller "AS2-from" är tom. | 
| Användaråtgärd | Ett AS2-meddelande togs emot som inte innehöll AS2-från-eller AS2-till-eller båda huvudena. </br> Kontrol lera AS2-meddelandet AS2-from och AS2-to-headers och korrigera dem baserat på avtals konfigurationen. |
|  |  | 

### <a name="missing-as2-message-body-and-headers"></a>Meddelande text och rubriker för AS2 saknas    

|   |   |  
|---|---|
| Felbeskrivning | Innehållet i begäran är null eller tomt. | 
| Användaråtgärd | Ett AS2-meddelande togs emot som inte innehöll meddelande texten. |
|  |  | 

### <a name="as2-message-decryption-failure"></a>Fel vid dekryptering av AS2-meddelande

|   |   | 
|---|---|
| Felbeskrivning |  [bearbetade/fel: dekryptering-misslyckades] | 
| Användaråtgärd | Lägg till @base64ToBinary i AS2Message innan du skickar till partner. |
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

### <a name="mdn-decryption-failure"></a>MDN-dekrypterings problem

|   |   | 
|---|---|
| Felbeskrivning |  [bearbetade/fel: dekryptering-misslyckades] | 
| Användaråtgärd | Lägg till @base64ToBinary i MDN innan du skickar till partner. | 
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

### <a name="missing-signing-certificate"></a>Signerings certifikat saknas

|   |   |  
|---|---|
| Felbeskrivning| Signerings certifikatet har inte kon figurer ATS för AS2-parten. </br>AS2 – från: partner1 AS2-till: partner2 | 
| Användaråtgärd | Konfigurera inställningar för AS2-avtal med rätt certifikat för signatur. |
|  |  | 

## <a name="x12-and-edifact"></a>X12 och EDIFACT

### <a name="leading-or-trailing-space-found"></a>Inledande eller avslutande blank steg hittades    
    
|   |   | 
|---|---|
| Felbeskrivning | Ett fel uppstod vid parsning. EDIFACT-transaktions uppsättningen med ID: t 123456 som finns i Interchange (utan grupp) med ID: t 987654, med avsändar-ID: t Partner1, mottagarens ID partner2, pausas med följande fel: <p>"Inledande avslutande separator hittades" |
| Användaråtgärd | Avtals inställningarna som ska konfigureras för att tillåta inledande och avslutande blank steg. </br>Redigera avtals inställningar för att tillåta inledande och avslutande blank steg. |
|   |   |

![Tillåt utrymme](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>Duplicerad kontroll har Aktiver ATS i avtalet

|   |   | 
|---|---| 
| Felbeskrivning | Duplicerat kontroll nummer |
| Användaråtgärd | Det här felet indikerar att det mottagna meddelandet har dubbla kontroll nummer. </br>Korrigera kontroll numret och skicka meddelandet igen. |
|   |   |

### <a name="missing-schema-in-the-agreement"></a>Schema som saknas i avtalet

|   |   | 
|---|---| 
| Felbeskrivning | Ett fel uppstod vid parsning. X12-transaktions uppsättningen med ID: t 564220001 i funktions gruppen med ID: t 56422, i Interchange med ID: t 000056422, med avsändar-ID: t "12345678", mottagarens ID "87654321" pausas med följande fel: <p>"Meddelandet har en okänd dokument typ och matchade inte något av de befintliga scheman som kon figurer ATS i avtalet" |
| Användaråtgärd | Konfigurera schemat i avtals inställningarna.  |
|   |   |

### <a name="incorrect-schema-in-the-agreement"></a>Felaktigt schema i avtalet

|   |   | 
|---|---| 
| Felbeskrivning | Meddelandet har en okänd dokument typ och matchade inte något av de befintliga scheman som kon figurer ATS i avtalet. |
| Användaråtgärd | Konfigurera rätt schema i avtals inställningarna. |
|   |   |

## <a name="flat-file"></a>Flat fil

### <a name="input-message-with-no-body"></a>Indatameddelande utan brödtext

|   |   | 
|---|---|
| Felbeskrivning | InvalidTemplate. Det gick inte att bearbeta mallens språk uttryck i åtgärdens Flat_File_Decoding indata på rad 1 och kolumn 1902: obligatoriskt egenskaps innehåll förväntar sig ett värde men null returnerades. Sökväg ' '. '. |
| Användaråtgärd | Det här felet anger att meddelandet inte innehåller någon brödtext. |
|   |   | 

