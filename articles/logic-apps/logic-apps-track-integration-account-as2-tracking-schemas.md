---
title: AS2 spårnings scheman för B2B-meddelanden
description: Skapa spårnings scheman för att övervaka AS2-meddelanden i Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: bccf69362279afd9e8148b20b61ff3ea9b472a03
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76906972"
---
# <a name="create-schemas-for-tracking-as2-messages-in-azure-logic-apps"></a>Skapa scheman för att spåra AS2-meddelanden i Azure Logic Apps

Du kan använda dessa AS2 spårnings scheman i integrations kontot för att hjälpa dig att övervaka lyckade, fel och meddelande egenskaper för transaktioner i företags-till-företag.

* Schema för spårning av AS2-meddelande
* Spårnings schema för AS2 meddelande dispositions meddelande (MDN)

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
      "incomingHeaders": {},
      "outgoingHeaders": {},
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Egenskap | Krävs | Typ | Beskrivning |
|----------|----------|------|-------------|
| senderPartnerName | Nej | Sträng | AS2 meddelande avsändarens partner namn |
| receiverPartnerName | Nej | Sträng | AS2 meddelande mottagares partner namn |
| as2To | Ja | Sträng | AS2 meddelande mottagarens namn från meddelandehuvuden i AS2-meddelandet |
| as2From | Ja | Sträng | AS2 meddelande avsändarens namn från meddelandehuvuden i AS2-meddelandet |
| agreementName | Nej | Sträng | Namnet på det AS2-avtal som meddelandena ska lösas till |
| riktning | Ja | Sträng | Riktningen för meddelande flödet, som är antingen `receive` eller`send` |
| messageId | Nej | Sträng | AS2 meddelande-ID från huvuden i AS2-meddelandet |
| dispositionType | Nej | Sträng | Dispositions meddelande för meddelande (MDN) dispositions typ värde |
| fileName | Nej | Sträng | Fil namn från rubriken för AS2-meddelandet |
| isMessageFailed | Ja | Boolesk | Om AS2-meddelandet misslyckades |
| isMessageSigned | Ja | Boolesk | Om AS2-meddelandet har signerats |
| isMessageEncrypted | Ja | Boolesk | Om AS2-meddelandet har krypterats |
| isMessageCompressed | Ja | Boolesk | Om AS2-meddelandet har komprimerats |
| correlationMessageId | Nej | Sträng | AS2 meddelande-ID, för att korrelera meddelanden med MDNs |
| incomingHeaders | Nej | Ord lista för JToken | Information om inkommande AS2-meddelande huvud |
| outgoingHeaders | Nej | Ord lista för JToken | Information om utgående AS2-meddelande huvud |
| isNrrEnabled | Ja | Boolesk | Om du vill använda standardvärdet om värdet inte är känt |
| isMdnExpected | Ja | Boolesk | Om du vill använda standardvärdet om värdet inte är känt |
| mdnType | Ja | Enum | Tillåtna värden: `NotConfigured`, `Sync`, och`Async` |
|||||

## <a name="as2-mdn-tracking-schema"></a>Spårnings schema för AS2-MDN

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

| Egenskap | Krävs | Typ | Beskrivning |
|----------|----------|------|-------------|
| senderPartnerName | Nej | Sträng | AS2 meddelande avsändarens partner namn |
| receiverPartnerName | Nej | Sträng | AS2 meddelande mottagares partner namn |
| as2To | Ja | Sträng | Partner namn som tar emot AS2-meddelandet |
| as2From | Ja | Sträng | Partner namn som skickar AS2-meddelandet |
| agreementName | Nej | Sträng | Namnet på det AS2-avtal som meddelandena ska lösas till |
| riktning | Ja | Sträng | Riktningen för meddelande flödet, som är antingen `receive` eller`send` |
| messageId | Nej | Sträng | Meddelande-ID för AS2 |
| originalMessageId | Nej | Sträng | Ursprungligt meddelande-ID för AS2 |
| dispositionType | Nej | Sträng | MDN dispositions typ värde |
| isMessageFailed | Ja | Boolesk | Om AS2-meddelandet misslyckades |
| isMessageSigned | Ja | Boolesk | Om AS2-meddelandet har signerats |
| isNrrEnabled | Ja | Boolesk | Om du vill använda standardvärdet om värdet inte är känt |
| statusCode | Ja | Enum | Tillåtna värden: `Accepted`, `Rejected`, och`AcceptedWithErrors` |
| micVerificationStatus | Ja | Enum | Tillåtna värden:`NotApplicable`, `Succeeded`, och`Failed` |
| correlationMessageId | Nej | Sträng | Korrelations-ID, vilket är ID: t för det ursprungliga meddelandet där MDN har kon figurer ATS |
| incomingHeaders | Nej | Ord lista för JToken | Information om inkommande meddelande huvud |
| outgoingHeaders | Nej | Ord lista för JToken | Information om utgående meddelande huvud |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Spårnings scheman för B2B-protokoll

Information om B2B-protokoll spårnings scheman finns i:

* [X12-spårningsscheman](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Anpassade B2B-scheman](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Nästa steg

* [Övervaka B2B-meddelanden med Azure Monitor-loggar](../logic-apps/monitor-b2b-messages-log-analytics.md)