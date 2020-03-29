---
title: X12-spårningsscheman för B2B-meddelanden
description: Skapa spårningsscheman för att övervaka X12-meddelanden för Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905299"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>Skapa scheman för spårning av X12-meddelanden i Azure Logic Apps

För att hjälpa dig att övervaka framgång, fel och meddelandeegenskaper för B2B-transaktioner (Business-to-Business) kan du använda dessa X12-spårningsscheman i ditt integrationskonto:

* X12-spårningsschema för transaktionsuppsättning
* X12-schema för spårning av bekräftelser för transaktionsuppsättning
* X12 schema för spårning av utbyte
* X12 schema för spårning av spårning av utbytesbekräftelse
* X12 funktionellt gruppspårningsschema
* X12 funktionellt schema för spårning av funktionell gruppbekräftelse

## <a name="x12-transaction-set-tracking-schema"></a>X12-spårningsschema för transaktionsuppsättning

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
| avsändarePartnerName | Inga | String | X12-meddelandeavsändarens partnernamn |
| mottagarePartnerName | Inga | String | X12-meddelandemottagarens partnernamn |
| avsändareKvalificerare | Ja | String | Skicka partnerkvalificerare |
| avsändareIdentifierare | Ja | String | Skicka partneridentifierare |
| mottagareKvalificerare | Ja | String | Ta emot partnerkvalificerare |
| mottagareIdentifier | Ja | String | Ta emot partneridentifierare |
| avtalNamn | Inga | String | Namnet på det X12-avtal som meddelandena matchas till |
| riktning | Ja | Enum | Meddelandeflödets riktning, som `receive` antingen är eller`send` |
| interchangeControlNumber | Inga | String | Nummer för utbyteskontroll |
| functionalGroupControlNumber | Inga | String | Funktionellt kontrollnummer |
| transaktionSetControlNumber | Inga | String | Kontrollnummer för transaktionsuppsättning |
| CorrelationMessageId | Inga | String | Korrelationsmeddelande-ID, som är en kombination av {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber} |
| messageType (meddelandetyp) | Inga | String | Transaktionsuppsättning eller dokumenttyp |
| isMessageFailed | Ja | Boolean | Om X12-meddelandet misslyckades |
| ärTekniskaAcknowledgmentExpected | Ja | Boolean | Om den tekniska bekräftelsen är konfigurerad i X12-avtalet |
| ärFunktionellAcknowledgmentExpected | Ja | Boolean | Om den funktionella bekräftelsen är konfigurerad i X12-avtalet |
| needAk2LoopForValidMessages | Ja | Boolean | Om AK2-loopen krävs för ett giltigt meddelande |
| segmentCount | Inga | Integer | Antal segment i X12-transaktionsuppsättningen |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>X12-schema för spårning av bekräftelser för transaktionsuppsättning

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
| avsändarePartnerName | Inga | String | X12-meddelandeavsändarens partnernamn |
| mottagarePartnerName | Inga | String | X12-meddelandemottagarens partnernamn |
| avsändareKvalificerare | Ja | String | Skicka partnerkvalificerare |
| avsändareIdentifierare | Ja | String | Skicka partneridentifierare |
| mottagareKvalificerare | Ja | String | Ta emot partnerkvalificerare |
| mottagareIdentifier | Ja | String | Ta emot partneridentifierare |
| avtalNamn | Inga | String | Namnet på det X12-avtal som meddelandena matchas till |
| riktning | Ja | Enum | Meddelandeflödets riktning, som `receive` antingen är eller`send` |
| interchangeControlNumber | Inga | String | Utbyteskontrollnummer för den funktionella bekräftelsen. Värdet fylls bara i för sändningssidan där funktionell bekräftelse tas emot för de meddelanden som skickas till partnern. |
| functionalGroupControlNumber | Inga | String | Funktionell gruppkontrollnummer för den funktionella bekräftelsen. Värdet fylls bara i för den sändningssida där funktionell bekräftelse tas emot för de meddelanden som skickas till partnern |
| isaSegment | Inga | String | ISA-segmentet för meddelandet. Värdet fylls bara i för den sändningssida där funktionell bekräftelse tas emot för de meddelanden som skickas till partnern |
| gsSegment | Inga | String | GS-segmentet i meddelandet. Värdet fylls bara i för den sändningssida där funktionell bekräftelse tas emot för de meddelanden som skickas till partnern |
| svararfunktionellGroupControlNumber | Inga | String | Det svarande utbyteskontrollnumret |
| svaraFunktionellgroupId | Inga | String | Det svarande funktionella grupp-ID:t, som mappar till AK101 i bekräftelsen |
| svarartransaktionerSetControlNumber | Inga | String | Kontrollnumret för den svarande transaktionsuppsättningen |
| svararTransactionSetId | Inga | String | Det svarande transaktionsuppsättnings-ID:t, som mappar till AK201 i bekräftelsen |
| statusKoda | Ja | Boolean | Statuskod för bekräftelse av transaktionsuppsättning |
| segmentCount | Ja | Enum | Statuskod för bekräftelse med `Accepted`dessa `Rejected`tillåtna värden: , och`AcceptedWithErrors` |
| bearbetningStatus | Ja | Enum | Bearbetningsstatus för bekräftelsen med `Received`dessa `Generated`tillåtna värden: , och`Sent` |
| CorrelationMessageId | Inga | String | Korrelationsmeddelande-ID, som är en kombination av {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber} |
| isMessageFailed | Ja | Boolean | Om X12-meddelandet misslyckades |
| ak2Segment (olika) | Inga | String | Bekräftelse för en transaktionsuppsättning inom den mottagna funktionella gruppen |
| ak3Segment | Inga | String | Rapporterar fel i ett datasegment |
| ak5Segment | Inga | String | Rapporterar om den transaktionsuppsättning som identifierats i AK2-segmentet accepteras eller avvisas och varför |
|||||

## <a name="x12-interchange-tracking-schema"></a>X12 schema för spårning av utbyte

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
| avsändarePartnerName | Inga | String | X12-meddelandeavsändarens partnernamn |
| mottagarePartnerName | Inga | String | X12-meddelandemottagarens partnernamn |
| avsändareKvalificerare | Ja | String | Skicka partnerkvalificerare |
| avsändareIdentifierare | Ja | String | Skicka partneridentifierare |
| mottagareKvalificerare | Ja | String | Ta emot partnerkvalificerare |
| mottagareIdentifier | Ja | String | Ta emot partneridentifierare |
| avtalNamn | Inga | String | Namnet på det X12-avtal som meddelandena matchas till |
| riktning | Ja | Enum | Meddelandeflödets riktning, som `receive` antingen är eller`send` |
| interchangeControlNumber | Inga | String | Nummer för utbyteskontroll |
| isaSegment | Inga | String | Meddelande ISA-segment |
| ärTekniskaAcknowledgmentExpected | Boolean | Om den tekniska bekräftelsen är konfigurerad i X12-avtalet  |
| isMessageFailed | Ja | Boolean | Om X12-meddelandet misslyckades |
| isa09 (på) | Inga | String | X12-datum för dokumentutbyte |
| isa10 (på en) | Inga | String | X12 dokument utbyte tid |
| isa11 (på) | Inga | String | X12:s standarder för utbyteskontroll |
| isa12 (på) | Inga | String | X12-versionsnummer för utbyteskontroll |
| isa14 (på) | Inga | String | X12-bekräftelse begärs |
| isa15 (på) | Inga | String | Indikator för provning eller produktion |
| isa16 (på andra) | Inga | String | Elementavgränsare |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>X12 schema för spårning av spårning av utbytesbekräftelse

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
| avsändarePartnerName | Inga | String | X12-meddelandeavsändarens partnernamn |
| mottagarePartnerName | Inga | String | X12-meddelandemottagarens partnernamn |
| avsändareKvalificerare | Ja | String | Skicka partnerkvalificerare |
| avsändareIdentifierare | Ja | String | Skicka partneridentifierare |
| mottagareKvalificerare | Ja | String | Ta emot partnerkvalificerare |
| mottagareIdentifier | Ja | String | Ta emot partneridentifierare |
| avtalNamn | Inga | String | Namnet på det X12-avtal som meddelandena matchas till |
| riktning | Ja | Enum | Meddelandeflödets riktning, som `receive` antingen är eller`send` |
| interchangeControlNumber | Inga | String | Utbyteskontrollnummer för den tekniska bekräftelse som tas emot från partner |
| isaSegment | Inga | String | ISA-segment för den tekniska bekräftelsen som tas emot från partner |
| svararInterchangeControlNumber | Inga | String | Utbyteskontrollnummer för den tekniska bekräftelsen som tas emot från partner |
| isMessageFailed | Ja | Boolean | Om X12-meddelandet misslyckades |
| statusKoda | Ja | Enum | Statuskod för utväxlingsbekräftelse med dessa tillåtna värden: `Accepted`, `Rejected`och`AcceptedWithErrors` |
| bearbetningStatus | Ja | Enum | Bekräftelsestatus med dessa tillåtna värden: `Received`, `Generated`och`Sent` |
| ta102 (på) | Inga | String | Datum för utbyte |
| ta103 (på) | Inga | String | Utbytestid |
| ta105 (på) | Inga | String | Kod för utväxlingsanteckning |
|||||

## <a name="x12-functional-group-tracking-schema"></a>X12 funktionellt gruppspårningsschema

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
| avsändarePartnerName | Inga | String | X12-meddelandeavsändarens partnernamn |
| mottagarePartnerName | Inga | String | X12-meddelandemottagarens partnernamn |
| avsändareKvalificerare | Ja | String | Skicka partnerkvalificerare |
| avsändareIdentifierare | Ja | String | Skicka partneridentifierare |
| mottagareKvalificerare | Ja | String | Ta emot partnerkvalificerare |
| mottagareIdentifier | Ja | String | Ta emot partneridentifierare |
| avtalNamn | Inga | String | Namnet på det X12-avtal som meddelandena matchas till |
| riktning | Ja | Enum | Meddelandeflödets riktning, antingen ta emot eller skicka |
| interchangeControlNumber | Inga | String | Nummer för utbyteskontroll |
| functionalGroupControlNumber | Inga | String | Funktionellt kontrollnummer |
| gsSegment | Inga | String | Segmentet Meddelande GS |
| ärTekniskaAcknowledgmentExpected | Ja | Boolean | Om den tekniska bekräftelsen är konfigurerad i X12-avtalet |
| ärFunktionellAcknowledgmentExpected | Ja | Boolean | Om den funktionella bekräftelsen är konfigurerad i X12-avtalet |
| isMessageFailed | Ja | Boolean | Om X12-meddelandet misslyckades |
| gs01 (på annat sätt) | Inga | String | Funktionell identifierare kod |
| gs02 (på annat sätt) | Inga | String | Programavsändarens kod |
| gs03 (på andra) | Inga | String | Programmottagarens kod |
| gs04 (på andra) | Inga | String | Funktionellt gruppdatum |
| gs05 (på ett sätt) | Inga | String | Funktionell grupptid |
| gs07 (på andra sätt) | Inga | String | Ansvarig myndighetskod |
| gs08 (på annat sätt) | Inga | String | Identifierare för version, utgåva eller bransch |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>X12 funktionellt schema för spårning av funktionell gruppbekräftelse

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
| avsändarePartnerName | Inga | String | X12-meddelandeavsändarens partnernamn |
| mottagarePartnerName | Inga | String | X12-meddelandemottagarens partnernamn |
| avsändareKvalificerare | Ja | String | Skicka partnerkvalificerare |
| avsändareIdentifierare | Ja | String | Skicka partneridentifierare |
| mottagareKvalificerare | Ja | String | Ta emot partnerkvalificerare |
| mottagareIdentifier | Ja | String | Ta emot partneridentifierare |
| avtalNamn | Inga | String | Namnet på det X12-avtal som meddelandena matchas till |
| riktning | Ja | Enum | Meddelandeflödets riktning, som `receive` antingen är eller`send` |
| interchangeControlNumber | Inga | String | Utbyteskontrollnummer, som fyller i för den sända sidan när en teknisk bekräftelse tas emot från partners |
| functionalGroupControlNumber | Inga | String | Funktionell grupp kontrollerar numrerar av den tekniska bekräftelsen, som befolkar för den sända sidan, när en teknisk bekräftelse mottas från partners |
| isaSegment | Inga | String | Samma som utbyteskontrollnummer, men fylls bara i vissa fall |
| gsSegment | Inga | String | Samma som funktionellt gruppkontrollnummer, men fylls bara i vissa fall |
| svararfunktionellGroupControlNumber | Inga | String | Kontrollnummer för den ursprungliga funktionella gruppen |
| svaraFunktionellgroupId | Inga | String | Kartor till AK101 i bekräftelsefunktionella grupp-ID |
| isMessageFailed | Boolean | Om X12-meddelandet misslyckades |
| statusKoda | Ja | Enum | Statuskod för bekräftelse med `Accepted`dessa `Rejected`tillåtna värden: , och`AcceptedWithErrors` |
| bearbetningStatus | Ja | Enum | Bearbetningsstatus för bekräftelsen med `Received`dessa `Generated`tillåtna värden: , och`Sent` |
| ak903 (på) | Inga | String | Antal mottagna transaktionsuppsättningar |
| ak904 (på) | Inga | String | Antal transaktionsuppsättningar som godkänts i den identifierade funktionsgruppen |
| ak9Segment | Inga | String | Huruvida den funktionella grupp som identifierats i AK1-segmentet accepteras eller avvisas och varför |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B-protokollspårningsscheman

Information om B2B-protokollspårningsscheman finns i:

* [AS2-spårningsscheman](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [B2B anpassade spårningsscheman](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Nästa steg

* [Övervaka B2B-meddelanden med Azure Monitor-loggar](../logic-apps/monitor-b2b-messages-log-analytics.md)