---
title: AS2-spårningsscheman för B2B - meddelanden i Azure Logic Apps | Microsoft Docs
description: Skapa AS2-spårningsscheman som övervakar B2B-meddelanden i integrationskonton för Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.date: 01/27/2017
ms.openlocfilehash: 180d90450497b38f107f3601944385a003f50282
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193525"
---
# <a name="create-schemas-for-tracking-as2-messages-and-mdns-in-integration-accounts-for-azure-logic-apps"></a>Skapa scheman för att spåra AS2-meddelanden och mdn måste specificeras i integrationskonton för Azure Logic Apps

För att du övervakar lyckades, fel och meddelandeegenskaper för business-to-business (B2B) transaktioner kan du använda de här AS2-spårningsscheman i ditt integrationskonto:

* Meddelande för AS2-spårningsschema
* AS2 MDN-spårningsschema

## <a name="as2-message-tracking-schema"></a>Meddelande för AS2-spårningsschema

```json
{
   "agreementProperties": {  
      "senderPartnerName": "",  
      "receiverPartnerName": "",  
      "as2To": "",  
      "as2From": "",  
      "agreementName": ""  
   },  
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "dispositionType": "",
      "fileName": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isMessageEncrypted": "",
      "isMessageCompressed": "",
      "correlationMessageId": "",
      "incomingHeaders": {
       },
      "outgoingHeaders": {
       },
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Egenskap  | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | String | AS2 meddelandets avsändare Partnernamn. (Valfritt) |
| receiverPartnerName | String | Mottagare AS2-meddelandet Partnernamn. (Valfritt) |
| as2To | String | Mottagare AS2-meddelandet namnet från rubriker för AS2-meddelandet. (Obligatorisk) |
| as2From | String | AS2 meddelandets avsändare namnet från rubriker för AS2-meddelandet. (Obligatorisk) |
| agreementName | String | Namnet på AS2-avtal som meddelanden har åtgärdats. (Valfritt) |
| riktning | String | Riktning för meddelande-flödet tar emot eller skickar. (Obligatorisk) |
| messageId | String | AS2 meddelande-ID, från rubriker för AS2-meddelande (valfritt) |
| dispositionType |String | Meddelandet Disposition-Notification (MDN) disposition TYPVÄRDE. (Valfritt) |
| fileName | String | Namnet på filen från rubriken för AS2-meddelandet. (Valfritt) |
| isMessageFailed |Boolesk | Om AS2-meddelandet misslyckades. (Obligatorisk) |
| isMessageSigned | Boolesk | Om AS2-meddelandet var signerad. (Obligatorisk) |
| isMessageEncrypted | Boolesk | Om AS2-meddelandet krypterades. (Obligatorisk) |
| isMessageCompressed |Boolesk | Om AS2-meddelandet har komprimerats. (Obligatorisk) |
| correlationMessageId | String | AS2 meddelande-ID, att korrelera meddelanden med mdn måste specificeras. (Valfritt) |
| incomingHeaders |Ordlista med JToken | AS2-huvud detaljer för inkommande meddelanden. (Valfritt) |
| outgoingHeaders |Ordlista med JToken | Utgående AS2 information om meddelandet. (Valfritt) |
| isNrrEnabled | Boolesk | Använd standardvärdet om värdet inte är känd. (Obligatorisk) |
| isMdnExpected | Boolesk | Använd standardvärdet om värdet inte är känd. (Obligatorisk) |
| mdnType | Enum | Tillåtna värden är **NotConfigured**, **synkronisering**, och **Async**. (Obligatorisk) |
||||

## <a name="as2-mdn-tracking-schema"></a>AS2 MDN-spårningsschema

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": "g"
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "originalMessageId": "",
      "dispositionType": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isNrrEnabled": "",
      "statusCode": "",
      "micVerificationStatus": "",
      "correlationMessageId": "",
      "incomingHeaders": {
      },
      "outgoingHeaders": {
      }
   }
}
```

| Egenskap  | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | String | AS2 meddelandets avsändare Partnernamn. (Valfritt) |
| receiverPartnerName | String | Mottagare AS2-meddelandet Partnernamn. (Valfritt) |
| as2To | String | Partnernamn som tar emot AS2-meddelandet. (Obligatorisk) |
| as2From | String | Partnernamn som skickar AS2-meddelandet. (Obligatorisk) |
| agreementName | String | Namnet på AS2-avtal som meddelanden har åtgärdats. (Valfritt) |
| riktning |String | Riktning för meddelande-flödet tar emot eller skickar. (Obligatorisk) |
| messageId | String | AS2-meddelande-ID. (Valfritt) |
| originalMessageId |String | AS2 ursprungliga meddelande-ID. (Valfritt) |
| dispositionType | String | MDN disposition TYPVÄRDE. (Valfritt) |
| isMessageFailed |Boolesk | Om AS2-meddelandet misslyckades. (Obligatorisk) |
| isMessageSigned |Boolesk | Om AS2-meddelandet var signerad. (Obligatorisk) |
| isNrrEnabled | Boolesk | Använd standardvärdet om värdet inte är känd. (Obligatorisk) |
| statusCode | Enum | Tillåtna värden är **godkända**, **Avvisad**, och **AcceptedWithErrors**. (Obligatorisk) |
| micVerificationStatus | Enum | Tillåtna värden är **NotApplicable**, **lyckades**, och **misslyckades**. (Obligatorisk) |
| correlationMessageId | String | Korrelations-ID. Ursprungligt postmeddelandet ID (meddelande-ID för meddelandet som MDN har konfigurerats). (Valfritt) |
| incomingHeaders | Ordlista med JToken | Anger inkommande meddelande information om. (Valfritt) |
| outgoingHeaders |Ordlista med JToken | Anger utgående meddelande information om. (Valfritt) |
||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B-protokollet-spårningsscheman

Information om B2B-protokollet spårningsscheman finns i:

* [X12-spårningsscheman](logic-apps-track-integration-account-x12-tracking-schema.md)
* [B2B anpassade spårningsscheman](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [övervakning B2B-meddelanden](logic-apps-monitor-b2b-message.md)
* Lär dig mer om [spåra B2B-meddelanden i Azure Monitor-loggar](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)