---
title: X12 spårningsscheman för B2B - meddelanden i Azure Logic Apps | Microsoft Docs
description: Skapa X12 spårningsscheman som övervakar B2B-meddelanden i integrationskonton för Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.date: 01/27/2017
ms.openlocfilehash: 1db324006e1e6332b5fdd8afd28ebed8a32ac707
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195191"
---
# <a name="create-schemas-for-tracking-x12-messages-in-integration-accounts-for-azure-logic-apps"></a>Skapa scheman för att spåra X12 meddelanden i integrationskonton för Azure Logic Apps

För att hjälpa dig att övervaka lyckades, fel och meddelandeegenskaper för business-to-business (B2B) transaktioner, du kan använda dessa X12 spårningsscheman i ditt integrationskonto:

* X12 transaktionsuppsättnings-spårningsschema
* X12 transaktionsuppsättnings-bekräftelse spårningsschema
* X12 interchange-spårningsschema
* X12 interchange bekräftelse spårningsschema
* X12 funktionell grupp-spårningsschema
* X12 funktionell grupp bekräftelse spårningsschema

## <a name="x12-transaction-set-tracking-schema"></a>X12 transaktionsuppsättnings-spårningsschema

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

| Egenskap  | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | String | X12 meddelande avsändarens Partnernamn. (Valfritt) |
| receiverPartnerName | String | X12 meddelande mottagarens Partnernamn. (Valfritt) |
| senderQualifier | String | Skicka partner kvalificerare. (Obligatorisk) |
| senderIdentifier | String | Skicka ID för partner. (Obligatorisk) |
| receiverQualifier | String | Ta emot partner kvalificerare. (Obligatorisk) |
| receiverIdentifier | String | Ta emot partner-ID. (Obligatorisk) |
| agreementName | String | Namnet på X12 avtal som meddelanden har åtgärdats. (Valfritt) |
| riktning | Enum | Riktning för meddelande-flödet tar emot eller skickar. (Obligatorisk) |
| interchangeControlNumber | String | Interchange-kontrollnummer. (Valfritt) |
| functionalGroupControlNumber | String | Funktionella kontrollnummer. (Valfritt) |
| transactionSetControlNumber | String | Kontrollnumret för transaktionsuppsättningen. (Valfritt) |
| CorrelationMessageId | String | Korrelations-meddelande-ID. En kombination av {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber}. (Valfritt) |
| messageType | String | Transaktionen ange eller dokumenttyp. (Valfritt) |
| isMessageFailed | Boolesk | Om X12 meddelandet misslyckades. (Obligatorisk) |
| isTechnicalAcknowledgmentExpected | Boolesk | Om teknisk bekräftelse har konfigurerats i X12 avtal. (Obligatorisk) |
| isFunctionalAcknowledgmentExpected | Boolesk | Om den funktionella bekräftelsen har konfigurerats i X12 avtal. (Obligatorisk) |
| needAk2LoopForValidMessages | Boolesk | Om AK2 loopen krävs för ett giltigt meddelande. (Obligatorisk) |
| segmentsCount | Integer | Antalet segment i X12 transaktionsuppsättning. (Valfritt) |
||||

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>X12 transaktionsuppsättnings-bekräftelse spårningsschema

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

| Egenskap  | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | String | X12 meddelande avsändarens Partnernamn. (Valfritt) |
| receiverPartnerName | String | X12 meddelande mottagarens Partnernamn. (Valfritt) |
| senderQualifier | String | Skicka partner kvalificerare. (Obligatorisk) |
| senderIdentifier | String | Skicka ID för partner. (Obligatorisk) |
| receiverQualifier | String | Ta emot partner kvalificerare. (Obligatorisk) |
| receiverIdentifier | String | Ta emot partner-ID. (Obligatorisk) |
| agreementName | String | Namnet på X12 avtal som meddelanden har åtgärdats. (Valfritt) |
| riktning | Enum | Riktning för meddelande-flödet tar emot eller skickar. (Obligatorisk) |
| interchangeControlNumber | String | Interchange-kontrollnummer av funktionella bekräftelse. Värdet fylls bara för sändningssidan där funktionella bekräftelse tas emot för meddelanden som skickas till partner. (Valfritt) |
| functionalGroupControlNumber | String | Funktionell gruppkontrollnummer av funktionella bekräftelse. Värdet fylls bara för sändningssidan där funktionella bekräftelse tas emot för meddelanden som skickas till partner. (Valfritt) |
| isaSegment | String | ISA-segmentet för meddelandet. Värdet fylls bara för sändningssidan där funktionella bekräftelse tas emot för meddelanden som skickas till partner. (Valfritt) |
| gsSegment | String | GS-segment i meddelandet. Värdet fylls bara för sändningssidan där funktionella bekräftelse tas emot för meddelanden som skickas till partner. (Valfritt) |
| respondingfunctionalGroupControlNumber | String | Svara interchange-kontrollnummer. (Valfritt) |
| respondingFunctionalGroupId | String | Svara funktionell grupp-ID, som mappar till AK101 i godkännandet. (Valfritt) |
| respondingtransactionSetControlNumber | String | Svarar Kontrollnumret för transaktionsuppsättningen. (Valfritt) |
| respondingTransactionSetId | String | Svarar transaktionsuppsättnings-ID, som mappar till AK201 i godkännandet. (Valfritt) |
| statusCode | Boolesk | Transaktionsuppsättnings-bekräftelsestatuskod. (Obligatorisk) |
| segmentsCount | Enum | Bekräftelsestatuskod. Tillåtna värden är **godkända**, **Avvisad**, och **AcceptedWithErrors**. (Obligatorisk) |
| processingStatus | Enum | Bearbetningsstatus av bekräftelsen. Tillåtna värden är **mottagna**, **Generated**, och **skickade**. (Obligatorisk) |
| CorrelationMessageId | String | Korrelations-meddelande-ID. En kombination av {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber}. (Valfritt) |
| isMessageFailed | Boolesk | Om X12 meddelandet misslyckades. (Obligatorisk) |
| ak2Segment | String | Bekräftelse för en transaktion som anges i mottagna funktionsgrupp. (Valfritt) |
| ak3Segment | String | Rapporterar fel i en data-segmentet. (Valfritt) |
| ak5Segment | String | Rapporter om transaktionsuppsättningen identifierade i segmentet AK2 godkännas eller avvisas och varför. (Valfritt) |
||||

## <a name="x12-interchange-tracking-schema"></a>X12 interchange-spårningsschema

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

| Egenskap  | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | String | X12 meddelande avsändarens Partnernamn. (Valfritt) |
| receiverPartnerName | String | X12 meddelande mottagarens Partnernamn. (Valfritt) |
| senderQualifier | String | Skicka partner kvalificerare. (Obligatorisk) |
| senderIdentifier | String | Skicka ID för partner. (Obligatorisk) |
| receiverQualifier | String | Ta emot partner kvalificerare. (Obligatorisk) |
| receiverIdentifier | String | Ta emot partner-ID. (Obligatorisk) |
| agreementName | String | Namnet på X12 avtal som meddelanden har åtgärdats. (Valfritt) |
| riktning | Enum | Riktning för meddelande-flödet tar emot eller skickar. (Obligatorisk) |
| interchangeControlNumber | String | Interchange-kontrollnummer. (Valfritt) |
| isaSegment | String | Meddelandet ISA-segmentet. (Valfritt) |
| isTechnicalAcknowledgmentExpected | Boolesk | Om teknisk bekräftelse har konfigurerats i X12 avtal. (Obligatorisk) |
| isMessageFailed | Boolesk | Om X12 meddelandet misslyckades. (Obligatorisk) |
| isa09 | String | X12 dokumentera utbytet datum. (Valfritt) |
| isa10 | String | X12 dokumentera utbytet tid. (Valfritt) |
| isa11 | String | X12 interchange kontroll standarder identifierare. (Valfritt) |
| isa12 | String | X12 interchange kontroll versionsnumret. (Valfritt) |
| isa14 | String | X12 bekräftelse begärs. (Valfritt) |
| isa15 | String | Indikator för testning eller produktion. (Valfritt) |
| isa16 | String | Dataelement-avgränsare. (Valfritt) |
||||

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>X12 interchange bekräftelse spårningsschema

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

| Egenskap  | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | String | X12 meddelande avsändarens Partnernamn. (Valfritt) |
| receiverPartnerName | String | X12 meddelande mottagarens Partnernamn. (Valfritt) |
| senderQualifier | String | Skicka partner kvalificerare. (Obligatorisk) |
| senderIdentifier | String | Skicka ID för partner. (Obligatorisk) |
| receiverQualifier | String | Ta emot partner kvalificerare. (Obligatorisk) |
| receiverIdentifier | String | Ta emot partner-ID. (Obligatorisk) |
| agreementName | String | Namnet på X12 avtal som meddelanden har åtgärdats. (Valfritt) |
| riktning | Enum | Riktning för meddelande-flödet tar emot eller skickar. (Obligatorisk) |
| interchangeControlNumber | String | Interchange-kontrollnummer av teknisk bekräftelsen som tas emot från partner. (Valfritt) |
| isaSegment | String | ISA-segmentet för den tekniska bekräftelse som tas emot från partner. (Valfritt) |
| respondingInterchangeControlNumber |String | Interchange-kontrollnummer för den tekniska bekräftelse som tas emot från partner. (Valfritt) |
| isMessageFailed | Boolesk | Om X12 meddelandet misslyckades. (Obligatorisk) |
| statusCode | Enum | Interchange bekräftelsestatuskod. Tillåtna värden är **godkända**, **Avvisad**, och **AcceptedWithErrors**. (Obligatorisk) |
| processingStatus | Enum | Bekräftelsestatusen. Tillåtna värden är **mottagna**, **Generated**, och **skickade**. (Obligatorisk) |
| ta102 | String | Interchange datum. (Valfritt) |
| ta103 | String | Interchange tid. (Valfritt) |
| ta105 | String | Interchange Obs kod. (Valfritt) |
||||

## <a name="x12-functional-group-tracking-schema"></a>X12 funktionell grupp-spårningsschema

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

| Egenskap  | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | String | X12 meddelande avsändarens Partnernamn. (Valfritt) |
| receiverPartnerName | String | X12 meddelande mottagarens Partnernamn. (Valfritt) |
| senderQualifier | String | Skicka partner kvalificerare. (Obligatorisk) |
| senderIdentifier | String | Skicka ID för partner. (Obligatorisk) |
| receiverQualifier | String | Ta emot partner kvalificerare. (Obligatorisk) |
| receiverIdentifier | String | Ta emot partner-ID. (Obligatorisk) |
| agreementName | String | Namnet på X12 avtal som meddelanden har åtgärdats. (Valfritt) |
| riktning | Enum | Riktning för meddelande-flödet tar emot eller skickar. (Obligatorisk) |
| interchangeControlNumber | String | Interchange-kontrollnummer. (Valfritt) |
| functionalGroupControlNumber | String | Funktionella kontrollnummer. (Valfritt) |
| gsSegment | String | Meddelandet GS segment. (Valfritt) |
| isTechnicalAcknowledgmentExpected | Boolesk | Om teknisk bekräftelse har konfigurerats i X12 avtal. (Obligatorisk) |
| isFunctionalAcknowledgmentExpected | Boolesk | Om den funktionella bekräftelsen har konfigurerats i X12 avtal. (Obligatorisk) |
| isMessageFailed | Boolesk | Om X12 meddelandet misslyckades. (Obligatorisk)|
| gs01 | String | Funktionella identifierare kod. (Valfritt) |
| gs02 | String | Programmet avsändarens kod. (Valfritt) |
| gs03 | String | Programmet mottagare kod. (Valfritt) |
| gs04 | String | Funktionell grupp datum. (Valfritt) |
| gs05 | String | Funktionell grupp tid. (Valfritt) |
| gs07 | String | Ansvarig myndighet kod. (Valfritt) |
| gs08 | String | Versionen/versionen/bransch identifierare kod. (Valfritt) |
||||

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>X12 funktionell grupp bekräftelse spårningsschema

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

| Egenskap  | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | String | X12 meddelande avsändarens Partnernamn. (Valfritt) |
| receiverPartnerName | String | X12 meddelande mottagarens Partnernamn. (Valfritt) |
| senderQualifier | String | Skicka partner kvalificerare. (Obligatorisk) |
| senderIdentifier | String | Skicka ID för partner. (Obligatorisk) |
| receiverQualifier | String | Ta emot partner kvalificerare. (Obligatorisk) |
| receiverIdentifier | String | Ta emot partner-ID. (Obligatorisk) |
| agreementName | String | Namnet på X12 avtal som meddelanden har åtgärdats. (Valfritt) |
| riktning | Enum | Riktning för meddelande-flödet tar emot eller skickar. (Obligatorisk) |
| interchangeControlNumber | String | Interchange-kontrollnummer som fyller för sändningssidan när en teknisk bekräftelse tas emot från partner. (Valfritt) |
| functionalGroupControlNumber | String | Funktionell gruppkontrollnummer av den tekniska bekräftelse som fyller för sändningssidan när en teknisk bekräftelse tas emot från partner. (Valfritt) |
| isaSegment | String | Samma som utbytet styr många, men endast i vissa fall. (Valfritt) |
| gsSegment | String | Samma som funktionsgruppen styr många, men endast i vissa fall. (Valfritt) |
| respondingfunctionalGroupControlNumber | String | Kontrollnummer för den ursprungliga funktionell gruppen. (Valfritt) |
| respondingFunctionalGroupId | String | Mappar till AK101 i funktionsgruppen bekräftelse-ID. (Valfritt) |
| isMessageFailed | Boolesk | Om X12 meddelandet misslyckades. (Obligatorisk) |
| statusCode | Enum | Bekräftelsestatuskod. Tillåtna värden är **godkända**, **Avvisad**, och **AcceptedWithErrors**. (Obligatorisk) |
| processingStatus | Enum | Bearbetningsstatus av bekräftelsen. Tillåtna värden är **mottagna**, **Generated**, och **skickade**. (Obligatorisk) |
| ak903 | String | Antal transaktionsuppsättningar som tagits emot. (Valfritt) |
| ak904 | String | Antal transaktionsuppsättningar accepteras i identifierade funktionsgruppen. (Valfritt) |
| ak9Segment | String | Om funktionsgruppen som identifieras i segmentet AK1 godkännas eller avvisas och varför. (Valfritt) |
|||| 

## <a name="b2b-protocol-tracking-schemas"></a>B2B-protokollet-spårningsscheman

Information om B2B-protokollet spårningsscheman finns i:

* [AS2-spårningsscheman](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [B2B anpassade spårningsscheman](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [övervakning B2B-meddelanden](logic-apps-monitor-b2b-message.md).
* Lär dig mer om [spåra B2B-meddelanden i Azure Monitor-loggar](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
