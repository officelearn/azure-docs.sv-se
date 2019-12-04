---
title: X12 spårnings scheman för B2B-meddelanden
description: Skapa X12 spårnings scheman som övervakar B2B-meddelanden i integrations konton för Azure Logic Apps med Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 6f2356600f5b6a637da731c650b26d968092e2f6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791723"
---
# <a name="create-schemas-for-tracking-x12-messages-in-integration-accounts-for-azure-logic-apps"></a>Skapa scheman för att spåra X12-meddelanden i integrations konton för Azure Logic Apps

Du kan använda dessa X12 spårnings scheman i integrations kontot för att hjälpa dig att övervaka lyckade, fel och meddelande egenskaper för transaktioner i företags-till-företag.

* X12 spårnings schema för transaktions uppsättning
* X12 för registrering av transaktions uppsättning
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
      "needAk2LoopForValidMessages":  "",
      "segmentsCount": ""
   }
}
```

| Egenskap | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | Sträng | X12 meddelande avsändarens partner namn. Valfritt |
| receiverPartnerName | Sträng | X12 meddelande mottagarens partner namn. Valfritt |
| senderQualifier | Sträng | Skicka partner kvalificerare. Erforderlig |
| senderIdentifier | Sträng | Skicka partner identifierare. Erforderlig |
| receiverQualifier | Sträng | Ta emot partner kvalificerare. Erforderlig |
| receiverIdentifier | Sträng | Ta emot partner-ID. Erforderlig |
| agreementName | Sträng | Namnet på X12-avtalet som meddelandena löses till. Valfritt |
| riktning | Enum | Riktningen på meddelande flödet, ta emot eller skicka. Erforderlig |
| interchangeControlNumber | Sträng | Utbytes kontroll nummer. Valfritt |
| functionalGroupControlNumber | Sträng | Funktions kontroll nummer. Valfritt |
| transactionSetControlNumber | Sträng | Kontroll nummer för transaktions uppsättning. Valfritt |
| correlationMessageId | Sträng | Korrelations meddelande-ID. En kombination av {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber}. Valfritt |
| messageType | Sträng | Transaktions uppsättning eller dokument typ. Valfritt |
| isMessageFailed | Boolesk | Om X12-meddelandet misslyckades. Erforderlig |
| isTechnicalAcknowledgmentExpected | Boolesk | Huruvida teknisk bekräftelse har kon figurer ATS i X12-avtalet. Erforderlig |
| isFunctionalAcknowledgmentExpected | Boolesk | Anger om funktions bekräftelsen har kon figurer ATS i X12-avtalet. Erforderlig |
| needAk2LoopForValidMessages | Boolesk | Om AK2-loopen krävs för ett giltigt meddelande. Erforderlig |
| segmentsCount | Integer | Antalet segment i X12-transaktions uppsättningen. Valfritt |
||||

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>X12 för registrering av transaktions uppsättning

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

| Egenskap | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | Sträng | X12 meddelande avsändarens partner namn. Valfritt |
| receiverPartnerName | Sträng | X12 meddelande mottagarens partner namn. Valfritt |
| senderQualifier | Sträng | Skicka partner kvalificerare. Erforderlig |
| senderIdentifier | Sträng | Skicka partner identifierare. Erforderlig |
| receiverQualifier | Sträng | Ta emot partner kvalificerare. Erforderlig |
| receiverIdentifier | Sträng | Ta emot partner-ID. Erforderlig |
| agreementName | Sträng | Namnet på X12-avtalet som meddelandena löses till. Valfritt |
| riktning | Enum | Riktningen på meddelande flödet, ta emot eller skicka. Erforderlig |
| interchangeControlNumber | Sträng | Utbytes kontroll nummer för funktions bekräftelsen. Värdet anges bara för sändnings sidan där funktionell bekräftelse tas emot för de meddelanden som skickas till partnern. Valfritt |
| functionalGroupControlNumber | Sträng | Funktions grupp kontroll nummer för funktions bekräftelsen. Värdet anges bara för sändnings sidan där funktionell bekräftelse tas emot för de meddelanden som skickas till partnern. Valfritt |
| isaSegment | Sträng | ISA-segment i meddelandet. Värdet anges bara för sändnings sidan där funktionell bekräftelse tas emot för de meddelanden som skickas till partnern. Valfritt |
| gsSegment | Sträng | GS-segment i meddelandet. Värdet anges bara för sändnings sidan där funktionell bekräftelse tas emot för de meddelanden som skickas till partnern. Valfritt |
| respondingfunctionalGroupControlNumber | Sträng | Svarar på utbytes kontroll nummer. Valfritt |
| respondingFunctionalGroupId | Sträng | Svarar på funktions grupps-ID, som mappar till AK101 i bekräftelsen. Valfritt |
| respondingtransactionSetControlNumber | Sträng | Svarar på kontroll nummer för transaktions uppsättning. Valfritt |
| respondingTransactionSetId | Sträng | Svarar på transaktions uppsättnings-ID, som mappar till AK201 i bekräftelsen. Valfritt |
| statusCode | Boolesk | Status kod för transaktions uppsättnings bekräftelse. Erforderlig |
| segmentsCount | Enum | Status kod för bekräftelse. Tillåtna värden **godkänns**, **avvisas**och **AcceptedWithErrors**. Erforderlig |
| processingStatus | Enum | Bearbetnings status för bekräftelsen. Tillåtna värden tas **emot**, **genereras**och **skickas**. Erforderlig |
| correlationMessageId | Sträng | Korrelations meddelande-ID. En kombination av {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber}. Valfritt |
| isMessageFailed | Boolesk | Om X12-meddelandet misslyckades. Erforderlig |
| ak2Segment | Sträng | Bekräftelse för en transaktions uppsättning i den mottagna funktionella gruppen. Valfritt |
| ak3Segment | Sträng | Rapporterar fel i ett data segment. Valfritt |
| ak5Segment | Sträng | Rapporter om den transaktions uppsättning som identifierats i AK2-segmentet godkänns eller avvisas, och varför. Valfritt |
||||

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

| Egenskap | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | Sträng | X12 meddelande avsändarens partner namn. Valfritt |
| receiverPartnerName | Sträng | X12 meddelande mottagarens partner namn. Valfritt |
| senderQualifier | Sträng | Skicka partner kvalificerare. Erforderlig |
| senderIdentifier | Sträng | Skicka partner identifierare. Erforderlig |
| receiverQualifier | Sträng | Ta emot partner kvalificerare. Erforderlig |
| receiverIdentifier | Sträng | Ta emot partner-ID. Erforderlig |
| agreementName | Sträng | Namnet på X12-avtalet som meddelandena löses till. Valfritt |
| riktning | Enum | Riktningen på meddelande flödet, ta emot eller skicka. Erforderlig |
| interchangeControlNumber | Sträng | Utbytes kontroll nummer. Valfritt |
| isaSegment | Sträng | Meddelandets ISA-segment. Valfritt |
| isTechnicalAcknowledgmentExpected | Boolesk | Huruvida teknisk bekräftelse har kon figurer ATS i X12-avtalet. Erforderlig |
| isMessageFailed | Boolesk | Om X12-meddelandet misslyckades. Erforderlig |
| isa09 | Sträng | X12 dokumentets överförings datum. Valfritt |
| isa10 | Sträng | X12 Document Interchange Time. Valfritt |
| isa11 | Sträng | X12 Interchange Control Standards-ID. Valfritt |
| isa12 | Sträng | Versions nummer för X12 Interchange Control. Valfritt |
| isa14 | Sträng | X12-bekräftelse har begärts. Valfritt |
| isa15 | Sträng | Indikator för test eller produktion. Valfritt |
| isa16 | Sträng | Element avgränsare. Valfritt |
||||

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>Spårnings schema för X12 Interchange-bekräftelse

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

| Egenskap | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | Sträng | X12 meddelande avsändarens partner namn. Valfritt |
| receiverPartnerName | Sträng | X12 meddelande mottagarens partner namn. Valfritt |
| senderQualifier | Sträng | Skicka partner kvalificerare. Erforderlig |
| senderIdentifier | Sträng | Skicka partner identifierare. Erforderlig |
| receiverQualifier | Sträng | Ta emot partner kvalificerare. Erforderlig |
| receiverIdentifier | Sträng | Ta emot partner-ID. Erforderlig |
| agreementName | Sträng | Namnet på X12-avtalet som meddelandena löses till. Valfritt |
| riktning | Enum | Riktningen på meddelande flödet, ta emot eller skicka. Erforderlig |
| interchangeControlNumber | Sträng | Utbytes kontroll nummer för det tekniska bekräftelsen som tas emot från partner. Valfritt |
| isaSegment | Sträng | ISA-segment för tekniskt godkännande som tas emot från partner. Valfritt |
| respondingInterchangeControlNumber |Sträng | Utbytes kontroll nummer för det tekniska bekräftelsen som tas emot från partner. Valfritt |
| isMessageFailed | Boolesk | Om X12-meddelandet misslyckades. Erforderlig |
| statusCode | Enum | Status kod för utbytes bekräftelse. Tillåtna värden **godkänns**, **avvisas**och **AcceptedWithErrors**. Erforderlig |
| processingStatus | Enum | Bekräftelse status. Tillåtna värden tas **emot**, **genereras**och **skickas**. Erforderlig |
| ta102 | Sträng | Utbytes datum. Valfritt |
| ta103 | Sträng | Utbytes tid. Valfritt |
| ta105 | Sträng | Kod för växlings notering. Valfritt |
||||

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

| Egenskap | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | Sträng | X12 meddelande avsändarens partner namn. Valfritt |
| receiverPartnerName | Sträng | X12 meddelande mottagarens partner namn. Valfritt |
| senderQualifier | Sträng | Skicka partner kvalificerare. Erforderlig |
| senderIdentifier | Sträng | Skicka partner identifierare. Erforderlig |
| receiverQualifier | Sträng | Ta emot partner kvalificerare. Erforderlig |
| receiverIdentifier | Sträng | Ta emot partner-ID. Erforderlig |
| agreementName | Sträng | Namnet på X12-avtalet som meddelandena löses till. Valfritt |
| riktning | Enum | Riktningen på meddelande flödet, ta emot eller skicka. Erforderlig |
| interchangeControlNumber | Sträng | Utbytes kontroll nummer. Valfritt |
| functionalGroupControlNumber | Sträng | Funktions kontroll nummer. Valfritt |
| gsSegment | Sträng | Meddelande GS-segment. Valfritt |
| isTechnicalAcknowledgmentExpected | Boolesk | Huruvida teknisk bekräftelse har kon figurer ATS i X12-avtalet. Erforderlig |
| isFunctionalAcknowledgmentExpected | Boolesk | Anger om funktions bekräftelsen har kon figurer ATS i X12-avtalet. Erforderlig |
| isMessageFailed | Boolesk | Om X12-meddelandet misslyckades. Erforderlig|
| gs01 | Sträng | Funktions identifiera kod. Valfritt |
| gs02 | Sträng | Program avsändarens kod. Valfritt |
| gs03 | Sträng | Program mottagarens kod. Valfritt |
| gs04 | Sträng | Funktionellt grupp datum. Valfritt |
| gs05 | Sträng | Funktionell grupp tid. Valfritt |
| gs07 | Sträng | Ansvarig byrå kod. Valfritt |
| gs08 | Sträng | Version/utgåva/bransch-ID-kod. Valfritt |
||||

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>Spårnings schema för X12-funktionell grupp bekräftelse

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

| Egenskap | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | Sträng | X12 meddelande avsändarens partner namn. Valfritt |
| receiverPartnerName | Sträng | X12 meddelande mottagarens partner namn. Valfritt |
| senderQualifier | Sträng | Skicka partner kvalificerare. Erforderlig |
| senderIdentifier | Sträng | Skicka partner identifierare. Erforderlig |
| receiverQualifier | Sträng | Ta emot partner kvalificerare. Erforderlig |
| receiverIdentifier | Sträng | Ta emot partner-ID. Erforderlig |
| agreementName | Sträng | Namnet på X12-avtalet som meddelandena löses till. Valfritt |
| riktning | Enum | Riktningen på meddelande flödet, ta emot eller skicka. Erforderlig |
| interchangeControlNumber | Sträng | Utbytes kontroll nummer som fylls i för sändnings sidan när en teknisk bekräftelse tas emot från partner. Valfritt |
| functionalGroupControlNumber | Sträng | Funktions grupp kontroll nummer för den tekniska bekräftelsen som fylls i för sändnings sidan när en teknisk bekräftelse tas emot från partner. Valfritt |
| isaSegment | Sträng | Samma som Interchange kontroll nummer, men endast ifyllt i vissa fall. Valfritt |
| gsSegment | Sträng | Samma som funktions grupp kontroll nummer, men endast ifyllt i vissa fall. Valfritt |
| respondingfunctionalGroupControlNumber | Sträng | Kontroll nummer för den ursprungliga funktionella gruppen. Valfritt |
| respondingFunctionalGroupId | Sträng | Mappar till AK101 i ID för Bekräftelsens funktions grupp. Valfritt |
| isMessageFailed | Boolesk | Om X12-meddelandet misslyckades. Erforderlig |
| statusCode | Enum | Status kod för bekräftelse. Tillåtna värden **godkänns**, **avvisas**och **AcceptedWithErrors**. Erforderlig |
| processingStatus | Enum | Bearbetnings status för bekräftelsen. Tillåtna värden tas **emot**, **genereras**och **skickas**. Erforderlig |
| ak903 | Sträng | Antal mottagna transaktions uppsättningar. Valfritt |
| ak904 | Sträng | Antal transaktions uppsättningar som har godkänts i den identifierade funktionella gruppen. Valfritt |
| ak9Segment | Sträng | Om den funktionella gruppen som identifieras i AK1-segmentet godkänns eller avvisas, och varför. Valfritt |
|||| 

## <a name="b2b-protocol-tracking-schemas"></a>Spårnings scheman för B2B-protokoll

Information om B2B-protokoll spårnings scheman finns i:

* [AS2-spårningsscheman](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Anpassade B2B-scheman](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att [övervaka B2B-meddelanden](logic-apps-monitor-b2b-message.md).
* Lär dig mer om att [spåra B2B-meddelanden i Azure Monitor loggar](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
