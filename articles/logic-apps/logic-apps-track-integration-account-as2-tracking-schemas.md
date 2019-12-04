---
title: AS2 spårnings scheman för B2B-meddelanden
description: Skapa AS2 spårnings scheman som övervakar B2B-meddelanden i integrations konton för Azure Logic Apps med Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 515d7cfc985ee9929f70de2c862170ff79ae4d60
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792804"
---
# <a name="create-schemas-for-tracking-as2-messages-and-mdns-in-integration-accounts-for-azure-logic-apps"></a>Skapa scheman för att spåra AS2-meddelanden och MDNs i integrations konton för Azure Logic Apps

Du kan använda dessa AS2 spårnings scheman i integrations kontot för att hjälpa dig att övervaka lyckade, fel och meddelande egenskaper för transaktioner i företags-till-företag.

* Schema för spårning av AS2-meddelande
* Spårnings schema för AS2-MDN

## <a name="as2-message-tracking-schema"></a>Schema för spårning av AS2-meddelande

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

| Egenskap | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | Sträng | AS2 meddelande avsändarens partner namn. Valfritt |
| receiverPartnerName | Sträng | AS2 meddelande mottagarens partner namn. Valfritt |
| as2To | Sträng | AS2 meddelande mottagarens namn, från meddelandehuvuden för AS2-meddelandet. Erforderlig |
| as2From | Sträng | AS2 meddelandets avsändarens namn, från meddelandehuvuden för AS2-meddelandet. Erforderlig |
| agreementName | Sträng | Namnet på AS2-avtalet som meddelandena löses till. Valfritt |
| riktning | Sträng | Riktningen på meddelande flödet, ta emot eller skicka. Erforderlig |
| Meddelande | Sträng | AS2 meddelande-ID, från meddelandehuvuden för AS2-meddelandet (valfritt) |
| dispositionType |Sträng | Dispositions meddelande för meddelande (MDN) dispositions typ värde. Valfritt |
| fileName | Sträng | Fil namn, från rubriken för AS2-meddelandet. Valfritt |
| isMessageFailed |Boolesk | Om AS2-meddelandet misslyckades. Erforderlig |
| isMessageSigned | Boolesk | Om AS2-meddelandet har signerats. Erforderlig |
| isMessageEncrypted | Boolesk | Om AS2-meddelandet har krypterats. Erforderlig |
| isMessageCompressed |Boolesk | Anger om AS2-meddelandet har komprimerats. Erforderlig |
| correlationMessageId | Sträng | AS2 meddelande-ID för att korrelera meddelanden med MDNs. Valfritt |
| incomingHeaders |Ord lista för JToken | Information om inkommande AS2-meddelande huvud. Valfritt |
| outgoingHeaders |Ord lista för JToken | Information om utgående AS2-meddelande huvud. Valfritt |
| isNrrEnabled | Boolesk | Använd standardvärdet om värdet inte är känt. Erforderlig |
| isMdnExpected | Boolesk | Använd standardvärdet om värdet inte är känt. Erforderlig |
| mdnType | Enum | Tillåtna värden är **NotConfigured**, **Sync**och **async**. Erforderlig |
||||

## <a name="as2-mdn-tracking-schema"></a>Spårnings schema för AS2-MDN

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

| Egenskap | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | Sträng | AS2 meddelande avsändarens partner namn. Valfritt |
| receiverPartnerName | Sträng | AS2 meddelande mottagarens partner namn. Valfritt |
| as2To | Sträng | Partner namn som tar emot AS2-meddelandet. Erforderlig |
| as2From | Sträng | Partner namn som skickar AS2-meddelandet. Erforderlig |
| agreementName | Sträng | Namnet på AS2-avtalet som meddelandena löses till. Valfritt |
| riktning |Sträng | Riktningen på meddelande flödet, ta emot eller skicka. Erforderlig |
| Meddelande | Sträng | AS2 meddelande-ID. Valfritt |
| originalMessageId |Sträng | AS2 ursprungliga meddelande-ID. Valfritt |
| dispositionType | Sträng | MDN dispositions typ värde. Valfritt |
| isMessageFailed |Boolesk | Om AS2-meddelandet misslyckades. Erforderlig |
| isMessageSigned |Boolesk | Om AS2-meddelandet har signerats. Erforderlig |
| isNrrEnabled | Boolesk | Använd standardvärdet om värdet inte är känt. Erforderlig |
| statusCode | Enum | Tillåtna värden **godkänns**, **avvisas**och **AcceptedWithErrors**. Erforderlig |
| micVerificationStatus | Enum | Tillåtna värden är **NotApplicable**, **lyckades**och **misslyckades**. Erforderlig |
| correlationMessageId | Sträng | Korrelations-ID. Det ursprungliga meddelande-ID: t (meddelande-ID: t för det meddelande som MDN har kon figurer ATS för). Valfritt |
| incomingHeaders | Ord lista för JToken | Anger information om inkommande meddelande huvud. Valfritt |
| outgoingHeaders |Ord lista för JToken | Anger information om utgående meddelande huvud. Valfritt |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Spårnings scheman för B2B-protokoll

Information om B2B-protokoll spårnings scheman finns i:

* [X12-spårningsscheman](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Anpassade B2B-scheman](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att [övervaka B2B-meddelanden](logic-apps-monitor-b2b-message.md)
* Lär dig mer om att [spåra B2B-meddelanden i Azure Monitor loggar](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)