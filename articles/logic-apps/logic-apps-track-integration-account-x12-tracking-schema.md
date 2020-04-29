---
title: X12 spårnings scheman för B2B-meddelanden
description: Skapa spårnings scheman för att övervaka X12-meddelanden för Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76905299"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>Skapa scheman för att spåra X12-meddelanden i Azure Logic Apps

Du kan använda dessa X12 spårnings scheman i integrations kontot för att hjälpa dig att övervaka lyckade, fel och meddelande egenskaper för transaktioner i företags-till-företag.

* X12 spårnings schema för transaktions uppsättning
* Schema för X12 för transaktions uppsättning
* Spårnings schema för X12 Interchange
* Spårnings schema för X12 Interchange-bekräftelse
* X12-funktionellt grupp spårnings schema
* Spårnings schema för X12-funktionell grupp bekräftelse

## <a name="x12-transaction-set-tracking-schema"></a>X12 spårnings schema för transaktions uppsättning

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "transactionSetControlNumber": "",
      "CorrelationMessageId": "",
      "messageType": "",
      "isMessageFailed": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "needAk2LoopForValidMessages": "",
      "segmentsCount": ""
   }
}
```

| Egenskap | Krävs | Typ | Beskrivning |
|----------|----------|------|-------------|
| senderPartnerName | Nej | Sträng | X12 meddelande avsändarens partner namn |
| receiverPartnerName | Nej | Sträng | X12 meddelande mottagares partner namn |
| senderQualifier | Ja | Sträng | Skicka partner kvalificerare |
| senderIdentifier | Ja | Sträng | Skicka partner identifierare |
| receiverQualifier | Ja | Sträng | Ta emot partner kvalificerare |
| receiverIdentifier | Ja | Sträng | Ta emot partner-ID |
| agreementName | Nej | Sträng | Namnet på det X12-avtal som meddelandena ska lösas till |
| riktning | Ja | Enum | Riktningen för meddelande flödet, som är antingen `receive` eller`send` |
| interchangeControlNumber | Nej | Sträng | Utbytes kontroll nummer |
| functionalGroupControlNumber | Nej | Sträng | Funktions kontroll nummer |
| transactionSetControlNumber | Nej | Sträng | Kontroll nummer för transaktions uppsättning |
| CorrelationMessageId | Nej | Sträng | Korrelations meddelande-ID, som är en kombination av {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber} |
| messageType | Nej | Sträng | Transaktions uppsättning eller dokument typ |
| isMessageFailed | Ja | Boolesk | Om X12-meddelandet misslyckades |
| isTechnicalAcknowledgmentExpected | Ja | Boolesk | Om den tekniska bekräftelsen är konfigurerad i X12-avtalet |
| isFunctionalAcknowledgmentExpected | Ja | Boolesk | Om funktions bekräftelsen har kon figurer ATS i X12-avtalet |
| needAk2LoopForValidMessages | Ja | Boolesk | Om AK2-loopen krävs för ett giltigt meddelande |
| segmentsCount | Nej | Integer | Antalet segment i X12-transaktions uppsättningen |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>Schema för X12 för transaktions uppsättning

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "respondingtransactionSetControlNumber": "",
      "respondingTransactionSetId": "",
      "statusCode": "",
      "processingStatus": "",
      "CorrelationMessageId": "",
      "isMessageFailed": "",
      "ak2Segment": "",
      "ak3Segment": "",
      "ak5Segment": ""
   }
}
```

| Egenskap | Krävs | Typ | Beskrivning |
|----------|----------|------|-------------|
| senderPartnerName | Nej | Sträng | X12 meddelande avsändarens partner namn |
| receiverPartnerName | Nej | Sträng | X12 meddelande mottagares partner namn |
| senderQualifier | Ja | Sträng | Skicka partner kvalificerare |
| senderIdentifier | Ja | Sträng | Skicka partner identifierare |
| receiverQualifier | Ja | Sträng | Ta emot partner kvalificerare |
| receiverIdentifier | Ja | Sträng | Ta emot partner-ID |
| agreementName | Nej | Sträng | Namnet på det X12-avtal som meddelandena ska lösas till |
| riktning | Ja | Enum | Riktningen för meddelande flödet, som är antingen `receive` eller`send` |
| interchangeControlNumber | Nej | Sträng | Utbytes kontroll nummer för funktions bekräftelsen. Värdet anges bara för sändnings sidan där funktions bekräftelse tas emot för meddelanden som skickas till partnern. |
| functionalGroupControlNumber | Nej | Sträng | Funktionellt grupp kontroll nummer för funktions bekräftelsen. Värdet anges bara för sändnings sidan där funktions bekräftelse tas emot för meddelanden som skickas till partner |
| isaSegment | Nej | Sträng | ISA-segment i meddelandet. Värdet anges bara för sändnings sidan där funktions bekräftelse tas emot för meddelanden som skickas till partner |
| gsSegment | Nej | Sträng | GS-segment i meddelandet. Värdet anges bara för sändnings sidan där funktions bekräftelse tas emot för meddelanden som skickas till partner |
| respondingfunctionalGroupControlNumber | Nej | Sträng | Kontroll numret för att svara Interchange |
| respondingFunctionalGroupId | Nej | Sträng | ID för den funktionella gruppen som mappar till AK101 i bekräftelsen |
| respondingtransactionSetControlNumber | Nej | Sträng | Kontroll numret för att svara på transaktions uppsättning |
| respondingTransactionSetId | Nej | Sträng | ID för den svarande transaktions uppsättningen som mappar till AK201 i bekräftelsen |
| statusCode | Ja | Boolesk | Bekräftelse status kod för transaktions uppsättning |
| segmentsCount | Ja | Enum | Bekräftelse status kod med följande tillåtna värden: `Accepted`, `Rejected`och`AcceptedWithErrors` |
| processingStatus | Ja | Enum | Bearbetnings status för bekräftelsen med följande tillåtna värden `Received`: `Generated`, och`Sent` |
| CorrelationMessageId | Nej | Sträng | Korrelations meddelande-ID, som är en kombination av {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber} |
| isMessageFailed | Ja | Boolesk | Om X12-meddelandet misslyckades |
| ak2Segment | Nej | Sträng | Bekräftelse för en transaktions uppsättning i den mottagna funktionella gruppen |
| ak3Segment | Nej | Sträng | Rapporterar fel i ett data segment |
| ak5Segment | Nej | Sträng | Rapporter om den transaktions uppsättning som identifierats i AK2-segmentet godkänns eller avvisas, och varför |
|||||

## <a name="x12-interchange-tracking-schema"></a>Spårnings schema för X12 Interchange

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "isa09": "",
      "isa10": "",
      "isa11": "",
      "isa12": "",
      "isa14": "",
      "isa15": "",
      "isa16": ""
   }
}
```

| Egenskap | Krävs | Typ | Beskrivning |
|----------|----------|------|-------------|
| senderPartnerName | Nej | Sträng | X12 meddelande avsändarens partner namn |
| receiverPartnerName | Nej | Sträng | X12 meddelande mottagares partner namn |
| senderQualifier | Ja | Sträng | Skicka partner kvalificerare |
| senderIdentifier | Ja | Sträng | Skicka partner identifierare |
| receiverQualifier | Ja | Sträng | Ta emot partner kvalificerare |
| receiverIdentifier | Ja | Sträng | Ta emot partner-ID |
| agreementName | Nej | Sträng | Namnet på det X12-avtal som meddelandena ska lösas till |
| riktning | Ja | Enum | Riktningen för meddelande flödet, som är antingen `receive` eller`send` |
| interchangeControlNumber | Nej | Sträng | Utbytes kontroll nummer |
| isaSegment | Nej | Sträng | ISA-segment för meddelande |
| isTechnicalAcknowledgmentExpected | Boolesk | Om den tekniska bekräftelsen är konfigurerad i X12-avtalet  |
| isMessageFailed | Ja | Boolesk | Om X12-meddelandet misslyckades |
| isa09 | Nej | Sträng | X12 dokumentets överförings datum |
| isa10 | Nej | Sträng | X12 dokumentets överförings tid |
| isa11 | Nej | Sträng | X12 Interchange Control Standards-ID |
| isa12 | Nej | Sträng | Versions nummer för X12 Interchange Control |
| isa14 | Nej | Sträng | X12 bekräftelse begärs |
| isa15 | Nej | Sträng | Indikator för test eller produktion |
| isa16 | Nej | Sträng | Element avgränsare |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>Spårnings schema för X12 Interchange-bekräftelse

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "respondingInterchangeControlNumber": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ta102": "",
      "ta103": "",
      "ta105": ""
   }
}
```

| Egenskap | Krävs | Typ | Beskrivning |
|----------|----------|------|-------------|
| senderPartnerName | Nej | Sträng | X12 meddelande avsändarens partner namn |
| receiverPartnerName | Nej | Sträng | X12 meddelande mottagares partner namn |
| senderQualifier | Ja | Sträng | Skicka partner kvalificerare |
| senderIdentifier | Ja | Sträng | Skicka partner identifierare |
| receiverQualifier | Ja | Sträng | Ta emot partner kvalificerare |
| receiverIdentifier | Ja | Sträng | Ta emot partner-ID |
| agreementName | Nej | Sträng | Namnet på det X12-avtal som meddelandena ska lösas till |
| riktning | Ja | Enum | Riktningen för meddelande flödet, som är antingen `receive` eller`send` |
| interchangeControlNumber | Nej | Sträng | Utbytes kontroll nummer för den tekniska bekräftelsen som tas emot från partner |
| isaSegment | Nej | Sträng | ISA-segment för den tekniska bekräftelsen som tas emot från partner |
| respondingInterchangeControlNumber | Nej | Sträng | Utbytes kontroll nummer för den tekniska bekräftelsen som tas emot från partner |
| isMessageFailed | Ja | Boolesk | Om X12-meddelandet misslyckades |
| statusCode | Ja | Enum | Status kod för utbytes bekräftelse med följande tillåtna `Accepted`värden `Rejected`:, och`AcceptedWithErrors` |
| processingStatus | Ja | Enum | Bekräftelse status med följande tillåtna värden: `Received`, `Generated`och`Sent` |
| ta102 | Nej | Sträng | Överförings datum |
| ta103 | Nej | Sträng | Utbytes tid |
| ta105 | Nej | Sträng | Kod för växlings meddelande |
|||||

## <a name="x12-functional-group-tracking-schema"></a>X12-funktionellt grupp spårnings schema

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "gsSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "gs01": "",
      "gs02": "",
      "gs03": "",
      "gs04": "",
      "gs05": "",
      "gs07": "",
      "gs08": ""
   }
}
```

| Egenskap | Krävs | Typ | Beskrivning |
|----------|----------|------|-------------|
| senderPartnerName | Nej | Sträng | X12 meddelande avsändarens partner namn |
| receiverPartnerName | Nej | Sträng | X12 meddelande mottagares partner namn |
| senderQualifier | Ja | Sträng | Skicka partner kvalificerare |
| senderIdentifier | Ja | Sträng | Skicka partner identifierare |
| receiverQualifier | Ja | Sträng | Ta emot partner kvalificerare |
| receiverIdentifier | Ja | Sträng | Ta emot partner-ID |
| agreementName | Nej | Sträng | Namnet på X12-avtalet som meddelandena löses till |
| riktning | Ja | Enum | Riktningen för meddelande flödet, antingen ta emot eller skicka |
| interchangeControlNumber | Nej | Sträng | Utbytes kontroll nummer |
| functionalGroupControlNumber | Nej | Sträng | Funktions kontroll nummer |
| gsSegment | Nej | Sträng | Meddelande GS segment |
| isTechnicalAcknowledgmentExpected | Ja | Boolesk | Om den tekniska bekräftelsen är konfigurerad i X12-avtalet |
| isFunctionalAcknowledgmentExpected | Ja | Boolesk | Om funktions bekräftelsen har kon figurer ATS i X12-avtalet |
| isMessageFailed | Ja | Boolesk | Om X12-meddelandet misslyckades |
| gs01 | Nej | Sträng | Funktions identifierings kod |
| gs02 | Nej | Sträng | Program avsändarens kod |
| gs03 | Nej | Sträng | Program mottagares kod |
| gs04 | Nej | Sträng | Funktionellt grupp datum |
| gs05 | Nej | Sträng | Funktionell grupp tid |
| gs07 | Nej | Sträng | Ansvarig byrå kod |
| gs08 | Nej | Sträng | Identifiera kod för version, version eller bransch |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>Spårnings schema för X12-funktionell grupp bekräftelse

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ak903": "",
      "ak904": "",
      "ak9Segment": ""
   }
}
```

| Egenskap | Krävs | Typ | Beskrivning |
|----------|----------|------|-------------|
| senderPartnerName | Nej | Sträng | X12 meddelande avsändarens partner namn |
| receiverPartnerName | Nej | Sträng | X12 meddelande mottagares partner namn |
| senderQualifier | Ja | Sträng | Skicka partner kvalificerare |
| senderIdentifier | Ja | Sträng | Skicka partner identifierare |
| receiverQualifier | Ja | Sträng | Ta emot partner kvalificerare |
| receiverIdentifier | Ja | Sträng | Ta emot partner-ID |
| agreementName | Nej | Sträng | Namnet på det X12-avtal som meddelandena ska lösas till |
| riktning | Ja | Enum | Riktningen för meddelande flödet, som är antingen `receive` eller`send` |
| interchangeControlNumber | Nej | Sträng | Utbytes kontroll nummer som fylls i för sändnings sidan när en teknisk bekräftelse tas emot från partner |
| functionalGroupControlNumber | Nej | Sträng | Funktions grupp kontroll nummer för den tekniska bekräftelsen som fylls i för sändnings sidan när en teknisk bekräftelse tas emot från partner |
| isaSegment | Nej | Sträng | Samma som Interchange kontroll nummer, men endast ifyllt i vissa fall |
| gsSegment | Nej | Sträng | Samma som funktions grupp kontroll nummer, men endast ifyllt i vissa fall |
| respondingfunctionalGroupControlNumber | Nej | Sträng | Kontroll nummer för den ursprungliga funktionella gruppen |
| respondingFunctionalGroupId | Nej | Sträng | Mappar till AK101 i Bekräftelsens funktions grupp-ID |
| isMessageFailed | Boolesk | Om X12-meddelandet misslyckades |
| statusCode | Ja | Enum | Bekräftelse status kod med följande tillåtna värden: `Accepted`, `Rejected`och`AcceptedWithErrors` |
| processingStatus | Ja | Enum | Bearbetnings status för bekräftelsen med följande tillåtna värden `Received`: `Generated`, och`Sent` |
| ak903 | Nej | Sträng | Antal mottagna transaktions uppsättningar |
| ak904 | Nej | Sträng | Antal transaktions uppsättningar som har godkänts i den identifierade funktionella gruppen |
| ak9Segment | Nej | Sträng | Om den funktionella gruppen som identifieras i AK1-segmentet godkänns eller avvisas, och varför |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Spårnings scheman för B2B-protokoll

Information om B2B-protokoll spårnings scheman finns i:

* [AS2-spårningsscheman](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Anpassade B2B-scheman](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Nästa steg

* [Övervaka B2B-meddelanden med Azure Monitor-loggar](../logic-apps/monitor-b2b-messages-log-analytics.md)