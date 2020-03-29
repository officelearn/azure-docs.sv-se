---
title: AS2-spårningsscheman för B2B-meddelanden
description: Skapa spårningsscheman för att övervaka AS2-meddelanden i Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: bccf69362279afd9e8148b20b61ff3ea9b472a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906972"
---
# <a name="create-schemas-for-tracking-as2-messages-in-azure-logic-apps"></a>Skapa scheman för spårning av AS2-meddelanden i Azure Logic Apps

För att hjälpa dig att övervaka framgång, fel och meddelandeegenskaper för B2B-transaktioner (Business-to-Business) kan du använda dessa AS2-spårningsscheman i ditt integrationskonto:

* SCHEMA för spårning av AS2-meddelanden
* MDN-spårningsschema (AS2 Message Disposition Notification)

## <a name="as2-message-tracking-schema"></a>SCHEMA för spårning av AS2-meddelanden

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
| avsändarePartnerName | Inga | String | AS2-meddelandeavsändarens partnernamn |
| mottagarePartnerName | Inga | String | AS2-meddelandemottagarens partnernamn |
| as2To | Ja | String | AS2-meddelandemottagarens namn från rubrikerna i AS2-meddelandet |
| as2Från | Ja | String | AS2-meddelandeavsändarens namn från rubrikerna i AS2-meddelandet |
| avtalNamn | Inga | String | Namnet på det AS2-avtal som meddelandena är lösta till |
| riktning | Ja | String | Meddelandeflödets riktning, som `receive` antingen är eller`send` |
| messageId | Inga | String | AS2-meddelande-ID från rubrikerna i AS2-meddelandet |
| dispositionTyp | Inga | String | MDN-dispositionstypvärde (Message Disposition Notification) |
| fileName | Inga | String | Filnamn från huvudet på AS2-meddelandet |
| isMessageFailed | Ja | Boolean | Om AS2-meddelandet misslyckades |
| isMessageSigned | Ja | Boolean | Om AS2-meddelandet signerades |
| isMessageKrypterad | Ja | Boolean | Om AS2-meddelandet har krypterats |
| isMessageKomprimerad | Ja | Boolean | Om AS2-meddelandet komprimerades |
| korrelationMessageId | Inga | String | AS2-meddelande-ID, för att korrelera meddelanden med MDN |
| incomingHeaders | Inga | Ordbok av JToken | Information om inkommande AS2-meddelandehuvud |
| utgåendeheaders | Inga | Ordbok av JToken | Information om utgående AS2-meddelandehuvud |
| ärNrrEnabled | Ja | Boolean | Om standardvärdet ska användas om värdet inte är känt |
| isMdnExpected | Ja | Boolean | Om standardvärdet ska användas om värdet inte är känt |
| mdnType (olika) | Ja | Enum | Tillåtna `NotConfigured`värden: , `Sync`och`Async` |
|||||

## <a name="as2-mdn-tracking-schema"></a>AS2 MDN-spårningsschema

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
| avsändarePartnerName | Inga | String | AS2-meddelandeavsändarens partnernamn |
| mottagarePartnerName | Inga | String | AS2-meddelandemottagarens partnernamn |
| as2To | Ja | String | Partnernamn som tar emot AS2-meddelandet |
| as2Från | Ja | String | Partnernamn som skickar AS2-meddelandet |
| avtalNamn | Inga | String | Namnet på det AS2-avtal som meddelandena är lösta till |
| riktning | Ja | String | Meddelandeflödets riktning, som `receive` antingen är eller`send` |
| messageId | Inga | String | AS2-meddelande-ID |
| originalMessageId | Inga | String | ORIGINALMEDDELANDE-ID FÖR AS2 |
| dispositionTyp | Inga | String | MDN-dispositionstypvärde |
| isMessageFailed | Ja | Boolean | Om AS2-meddelandet misslyckades |
| isMessageSigned | Ja | Boolean | Om AS2-meddelandet signerades |
| ärNrrEnabled | Ja | Boolean | Om standardvärdet ska användas om värdet inte är känt |
| statusKoda | Ja | Enum | Tillåtna `Accepted`värden: , `Rejected`och`AcceptedWithErrors` |
| micVerificationStatus | Ja | Enum | Tillåtna`NotApplicable`värden: , `Succeeded`och`Failed` |
| korrelationMessageId | Inga | String | Korrelations-ID, som är ID för det ursprungliga meddelandet som har MDN konfigurerat |
| incomingHeaders | Inga | Ordbok av JToken | Information om inkommande meddelandehuvud |
| utgåendeheaders | Inga | Ordbok av JToken | Information om utgående meddelandehuvud |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B-protokollspårningsscheman

Information om B2B-protokollspårningsscheman finns i:

* [X12-spårningsscheman](logic-apps-track-integration-account-x12-tracking-schema.md)
* [B2B anpassade spårningsscheman](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Nästa steg

* [Övervaka B2B-meddelanden med Azure Monitor-loggar](../logic-apps/monitor-b2b-messages-log-analytics.md)