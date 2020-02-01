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
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
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
| senderPartnerName | Inga | String | AS2 meddelande avsändarens partner namn |
| receiverPartnerName | Inga | String | AS2 meddelande mottagares partner namn |
| as2To | Ja | String | AS2 meddelande mottagarens namn från meddelandehuvuden i AS2-meddelandet |
| as2From | Ja | String | AS2 meddelande avsändarens namn från meddelandehuvuden i AS2-meddelandet |
| agreementName | Inga | String | Namnet på det AS2-avtal som meddelandena ska lösas till |
| riktning | Ja | String | Riktningen för meddelande flödet, som är antingen `receive` eller `send` |
| messageId | Inga | String | AS2 meddelande-ID från huvuden i AS2-meddelandet |
| dispositionType | Inga | String | Dispositions meddelande för meddelande (MDN) dispositions typ värde |
| fileName | Inga | String | Fil namn från rubriken för AS2-meddelandet |
| isMessageFailed | Ja | Boolean | Om AS2-meddelandet misslyckades |
| isMessageSigned | Ja | Boolean | Om AS2-meddelandet har signerats |
| isMessageEncrypted | Ja | Boolean | Om AS2-meddelandet har krypterats |
| isMessageCompressed | Ja | Boolean | Om AS2-meddelandet har komprimerats |
| correlationMessageId | Inga | String | AS2 meddelande-ID, för att korrelera meddelanden med MDNs |
| incomingHeaders | Inga | Ord lista för JToken | Information om inkommande AS2-meddelande huvud |
| outgoingHeaders | Inga | Ord lista för JToken | Information om utgående AS2-meddelande huvud |
| isNrrEnabled | Ja | Boolean | Om du vill använda standardvärdet om värdet inte är känt |
| isMdnExpected | Ja | Boolean | Om du vill använda standardvärdet om värdet inte är känt |
| mdnType | Ja | Enum | Tillåtna värden: `NotConfigured`, `Sync`och `Async` |
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
| senderPartnerName | Inga | String | AS2 meddelande avsändarens partner namn |
| receiverPartnerName | Inga | String | AS2 meddelande mottagares partner namn |
| as2To | Ja | String | Partner namn som tar emot AS2-meddelandet |
| as2From | Ja | String | Partner namn som skickar AS2-meddelandet |
| agreementName | Inga | String | Namnet på det AS2-avtal som meddelandena ska lösas till |
| riktning | Ja | String | Riktningen för meddelande flödet, som är antingen `receive` eller `send` |
| messageId | Inga | String | Meddelande-ID för AS2 |
| originalMessageId | Inga | String | Ursprungligt meddelande-ID för AS2 |
| dispositionType | Inga | String | MDN dispositions typ värde |
| isMessageFailed | Ja | Boolean | Om AS2-meddelandet misslyckades |
| isMessageSigned | Ja | Boolean | Om AS2-meddelandet har signerats |
| isNrrEnabled | Ja | Boolean | Om du vill använda standardvärdet om värdet inte är känt |
| statusCode | Ja | Enum | Tillåtna värden: `Accepted`, `Rejected`och `AcceptedWithErrors` |
| micVerificationStatus | Ja | Enum | Tillåtna värden:`NotApplicable`, `Succeeded`och `Failed` |
| correlationMessageId | Inga | String | Korrelations-ID, vilket är ID: t för det ursprungliga meddelandet där MDN har kon figurer ATS |
| incomingHeaders | Inga | Ord lista för JToken | Information om inkommande meddelande huvud |
| outgoingHeaders | Inga | Ord lista för JToken | Information om utgående meddelande huvud |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Spårnings scheman för B2B-protokoll

Information om B2B-protokoll spårnings scheman finns i:

* [X12-spårningsscheman](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Anpassade B2B-scheman](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Nästa steg

* [Övervaka B2B-meddelanden med Azure Monitor loggar](../logic-apps/monitor-b2b-messages-log-analytics.md)