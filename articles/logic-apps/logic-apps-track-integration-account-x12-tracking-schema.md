---
title: "X12 upp scheman för B2B övervakning - Azure Logic Apps | Microsoft Docs"
description: "Använd X12 spårning scheman för att övervaka B2B-meddelanden från transaktioner i ditt Azure-konto för integrering."
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3782c0a76ea8728a146b3d73774f74c31187cbfd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="start-or-enable-tracking-of-x12-messages-to-monitor-success-errors-and-message-properties"></a>Start- eller aktivera spårning av X12 meddelanden till övervakaren lyckade, fel och meddelandeegenskaper
Du kan använda dessa X12 spårning scheman i ditt konto i Azure-integrering för att övervaka business-to-business (B2B) transaktioner:

* X12 transaktion ange spårning av schemat
* X12 transaktion ange bekräftelse spårning av schemat
* X12 interchange spårning av schemat
* X12 interchange bekräftelse spårning av schemat
* X12 funktionella gruppera spårning av schemat
* X12 funktionella gruppera bekräftelse spårning av schemat

## <a name="x12-transaction-set-tracking-schema"></a>X12 transaktion ange spårning av schemat
````java

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
````

| Egenskap | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | Sträng | X12 meddelande partner avsändare. (Valfritt) |
| receiverPartnerName | Sträng | X12 meddelande mottagarens namn. (Valfritt) |
| senderQualifier | Sträng | Skicka partner kvalificerare. (Obligatoriskt) |
| senderIdentifier | Sträng | Skicka partner-ID. (Obligatoriskt) |
| receiverQualifier | Sträng | Ta emot partner kvalificerare. (Obligatoriskt) |
| receiverIdentifier | Sträng | Ta emot partner identifierare. (Obligatoriskt) |
| agreementName | Sträng | Namnet på X12 avtal som meddelanden har åtgärdats. (Valfritt) |
| Riktning | Enum | Flödesriktning i meddelandet, ta emot och skicka. (Obligatoriskt) |
| interchangeControlNumber | Sträng | Interchange kontrollnummer. (Valfritt) |
| functionalGroupControlNumber | Sträng | Funktionella kontrollnummer. (Valfritt) |
| transactionSetControlNumber | Sträng | Transaktionen ange kontrollnumret. (Valfritt) |
| correlationMessageId | Sträng | Korrelation meddelande-ID. En kombination av {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber}. (Valfritt) |
| messageType | Sträng | Transaktionen ange eller dokumenttyp. (Valfritt) |
| isMessageFailed | Booleskt värde | Om X12 meddelandet misslyckades. (Obligatoriskt) |
| isTechnicalAcknowledgmentExpected | Booleskt värde | Om tekniska bekräftelse har konfigurerats i X12 avtal. (Obligatoriskt) |
| isFunctionalAcknowledgmentExpected | Booleskt värde | Om den funktionella bekräftelsen har konfigurerats i X12 avtal. (Obligatoriskt) |
| needAk2LoopForValidMessages | Booleskt värde | Om AK2 loop krävs för ett giltigt meddelande. (Obligatoriskt) |
| segmentsCount | Integer | Antalet segment i X12 transaktion uppsättningen. (Valfritt) |

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>X12 transaktion ange bekräftelse spårning av schemat
````java

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
                "CorrelationMessageId": ""
                "isMessageFailed": "",
                "ak2Segment": "",
                "ak3Segment": "",
                "ak5Segment": ""
            }
    }
````

| Egenskap | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | Sträng | X12 meddelande partner avsändare. (Valfritt) |
| receiverPartnerName | Sträng | X12 meddelande mottagarens namn. (Valfritt) |
| senderQualifier | Sträng | Skicka partner kvalificerare. (Obligatoriskt) |
| senderIdentifier | Sträng | Skicka partner-ID. (Obligatoriskt) |
| receiverQualifier | Sträng | Ta emot partner kvalificerare. (Obligatoriskt) |
| receiverIdentifier | Sträng | Ta emot partner identifierare. (Obligatoriskt) |
| agreementName | Sträng | Namnet på X12 avtal som meddelanden har åtgärdats. (Valfritt) |
| Riktning | Enum | Flödesriktning i meddelandet, ta emot och skicka. (Obligatoriskt) |
| interchangeControlNumber | Sträng | Interchange kontrollen Antal funktionella bekräftelse. Värdet fylls endast för sändningssidan där funktionella bekräftelse har tagits emot för meddelanden som skickas till partner. (Valfritt) |
| functionalGroupControlNumber | Sträng | Funktionell grupp kontrollen Antal funktionella bekräftelse. Värdet fylls endast för sändningssidan där funktionella bekräftelse har tagits emot för meddelanden som skickas till partner. (Valfritt) |
| isaSegment | Sträng | ISA-segment i meddelandet. Värdet fylls endast för sändningssidan där funktionella bekräftelse har tagits emot för meddelanden som skickas till partner. (Valfritt) |
| gsSegment | Sträng | GS segment i meddelandet. Värdet fylls endast för sändningssidan där funktionella bekräftelse har tagits emot för meddelanden som skickas till partner. (Valfritt) |
| respondingfunctionalGroupControlNumber | Sträng | Svara interchange kontrollnummer. (Valfritt) |
| respondingFunctionalGroupId | Sträng | Svara funktionell grupp-ID, som mappar till AK101 i godkännandet. (Valfritt) |
| respondingtransactionSetControlNumber | Sträng | Svarande transaktionen ange kontrollnumret. (Valfritt) |
| respondingTransactionSetId | Sträng | Svarande transaktionen Ange ID, som mappar till AK201 i godkännandet. (Valfritt) |
| statusCode | Booleskt värde | Transaktionen ange statuskoden för bekräftelse. (Obligatoriskt) |
| segmentsCount | Enum | Bekräftelse-statuskoden. Tillåtna värden är **godkända**, **Avvisad**, och **AcceptedWithErrors**. (Obligatoriskt) |
| processingStatus | Enum | Bearbetningsstatus för av bekräftelsen. Tillåtna värden är **mottagna**, **Generated**, och **skickade**. (Obligatoriskt) |
| correlationMessageId | Sträng | Korrelation meddelande-ID. En kombination av {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber}. (Valfritt) |
| isMessageFailed | Booleskt värde | Om X12 meddelandet misslyckades. (Obligatoriskt) |
| ak2Segment | Sträng | Bekräftelse för en transaktion i gruppen mottagna funktionella. (Valfritt) |
| ak3Segment | Sträng | Rapporterar fel i en data-segmentet. (Valfritt) |
| ak5Segment | Sträng | Rapporter om transaktionen ange identifierade i segmentet AK2 godkännas eller avvisas och varför. (Valfritt) |

## <a name="x12-interchange-tracking-schema"></a>X12 interchange spårning av schemat
````java

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
````

| Egenskap | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | Sträng | X12 meddelande partner avsändare. (Valfritt) |
| receiverPartnerName | Sträng | X12 meddelande mottagarens namn. (Valfritt) |
| senderQualifier | Sträng | Skicka partner kvalificerare. (Obligatoriskt) |
| senderIdentifier | Sträng | Skicka partner-ID. (Obligatoriskt) |
| receiverQualifier | Sträng | Ta emot partner kvalificerare. (Obligatoriskt) |
| receiverIdentifier | Sträng | Ta emot partner identifierare. (Obligatoriskt) |
| agreementName | Sträng | Namnet på X12 avtal som meddelanden har åtgärdats. (Valfritt) |
| Riktning | Enum | Flödesriktning i meddelandet, ta emot och skicka. (Obligatoriskt) |
| interchangeControlNumber | Sträng | Interchange kontrollnummer. (Valfritt) |
| isaSegment | Sträng | Meddelandet ISA segment. (Valfritt) |
| isTechnicalAcknowledgmentExpected | Booleskt värde | Om tekniska bekräftelse har konfigurerats i X12 avtal. (Obligatoriskt) |
| isMessageFailed | Booleskt värde | Om X12 meddelandet misslyckades. (Obligatoriskt) |
| isa09 | Sträng | X12 dokumentera interchange datum. (Valfritt) |
| isa10 | Sträng | X12 dokumentera interchange tid. (Valfritt) |
| isa11 | Sträng | X12 interchange kontrollen standarder identifierare. (Valfritt) |
| isa12 | Sträng | X12 interchange kontrollen versionsnumret. (Valfritt) |
| isa14 | Sträng | X12 bekräftelse begärs. (Valfritt) |
| isa15 | Sträng | Indikator för test- eller produktionsmiljö. (Valfritt) |
| isa16 | Sträng | Elementet avgränsare. (Valfritt) |

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>X12 interchange bekräftelse spårning av schemat
````java
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
````

| Egenskap | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | Sträng | X12 meddelande partner avsändare. (Valfritt) |
| receiverPartnerName | Sträng | X12 meddelande mottagarens namn. (Valfritt) |
| senderQualifier | Sträng | Skicka partner kvalificerare. (Obligatoriskt) |
| senderIdentifier | Sträng | Skicka partner-ID. (Obligatoriskt) |
| receiverQualifier | Sträng | Ta emot partner kvalificerare. (Obligatoriskt) |
| receiverIdentifier | Sträng | Ta emot partner identifierare. (Obligatoriskt) |
| agreementName | Sträng | Namnet på X12 avtal som meddelanden har åtgärdats. (Valfritt) |
| Riktning | Enum | Flödesriktning i meddelandet, ta emot och skicka. (Obligatoriskt) |
| interchangeControlNumber | Sträng | Interchange kontrollen antal tekniska bekräftelse som tas emot från partner. (Valfritt) |
| isaSegment | Sträng | ISA segment för tekniska bekräftelse som tas emot från partner. (Valfritt) |
| respondingInterchangeControlNumber |Sträng | Interchange Kontrollnumret för den tekniska bekräftelse som tas emot från partner. (Valfritt) |
| isMessageFailed | Booleskt värde | Om X12 meddelandet misslyckades. (Obligatoriskt) |
| statusCode | Enum | Interchange bekräftelse-statuskod. Tillåtna värden är **godkända**, **Avvisad**, och **AcceptedWithErrors**. (Obligatoriskt) |
| processingStatus | Enum | Status för bekräftelse. Tillåtna värden är **mottagna**, **Generated**, och **skickade**. (Obligatoriskt) |
| TA102 | Sträng | Interchange datum. (Valfritt) |
| ta103 | Sträng | Interchange tid. (Valfritt) |
| ta105 | Sträng | Interchange Obs kod. (Valfritt) |

## <a name="x12-functional-group-tracking-schema"></a>X12 funktionella gruppera spårning av schemat
````java

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
````

| Egenskap | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | Sträng | X12 meddelande partner avsändare. (Valfritt) |
| receiverPartnerName | Sträng | X12 meddelande mottagarens namn. (Valfritt) |
| senderQualifier | Sträng | Skicka partner kvalificerare. (Obligatoriskt) |
| senderIdentifier | Sträng | Skicka partner-ID. (Obligatoriskt) |
| receiverQualifier | Sträng | Ta emot partner kvalificerare. (Obligatoriskt) |
| receiverIdentifier | Sträng | Ta emot partner identifierare. (Obligatoriskt) |
| agreementName | Sträng | Namnet på X12 avtal som meddelanden har åtgärdats. (Valfritt) |
| Riktning | Enum | Flödesriktning i meddelandet, ta emot och skicka. (Obligatoriskt) |
| interchangeControlNumber | Sträng | Interchange kontrollnummer. (Valfritt) |
| functionalGroupControlNumber | Sträng | Funktionella kontrollnummer. (Valfritt) |
| gsSegment | Sträng | Meddelandet GS segment. (Valfritt) |
| isTechnicalAcknowledgmentExpected | Booleskt värde | Om tekniska bekräftelse har konfigurerats i X12 avtal. (Obligatoriskt) |
| isFunctionalAcknowledgmentExpected | Booleskt värde | Om den funktionella bekräftelsen har konfigurerats i X12 avtal. (Obligatoriskt) |
| isMessageFailed | Booleskt värde | Om X12 meddelandet misslyckades. (Obligatoriskt)|
| gs01 | Sträng | Funktionella identifierare kod. (Valfritt) |
| gs02 | Sträng | Programmet avsändarens kod. (Valfritt) |
| gs03 | Sträng | Programmet mottagaren kod. (Valfritt) |
| gs04 | Sträng | Funktionell grupp datum. (Valfritt) |
| gs05 | Sträng | Funktionell grupp tid. (Valfritt) |
| gs07 | Sträng | Ansvarig agency kod. (Valfritt) |
| gs08 | Sträng | Branschen-version/utgåva identifierare kod. (Valfritt) |

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>X12 funktionella gruppera bekräftelse spårning av schemat
````java
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
````

| Egenskap | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | Sträng | X12 meddelande partner avsändare. (Valfritt) |
| receiverPartnerName | Sträng | X12 meddelande mottagarens namn. (Valfritt) |
| senderQualifier | Sträng | Skicka partner kvalificerare. (Obligatoriskt) |
| senderIdentifier | Sträng | Skicka partner-ID. (Obligatoriskt) |
| receiverQualifier | Sträng | Ta emot partner kvalificerare. (Obligatoriskt) |
| receiverIdentifier | Sträng | Ta emot partner identifierare. (Obligatoriskt) |
| agreementName | Sträng | Namnet på X12 avtal som meddelanden har åtgärdats. (Valfritt) |
| Riktning | Enum | Flödesriktning i meddelandet, ta emot och skicka. (Obligatoriskt) |
| interchangeControlNumber | Sträng | Interchange kontrollnummer som fyller för sändningssidan när en teknisk bekräftelse tas emot från partner. (Valfritt) |
| functionalGroupControlNumber | Sträng | Funktionell grupp kontrollen antal tekniska bekräftelse, som fyller för sändningssidan när en teknisk bekräftelse tas emot från partner. (Valfritt) |
| isaSegment | Sträng | Samma som interchange styr många men ifyllda endast i vissa fall. (Valfritt) |
| gsSegment | Sträng | Samma som funktionell grupp styr många men ifyllda endast i vissa fall. (Valfritt) |
| respondingfunctionalGroupControlNumber | Sträng | Kontrollera numret på den ursprungliga funktionella-gruppen. (Valfritt) |
| respondingFunctionalGroupId | Sträng | Mappas till AK101 i gruppen funktionella bekräftelse-ID. (Valfritt) |
| isMessageFailed | Booleskt värde | Om X12 meddelandet misslyckades. (Obligatoriskt) |
| statusCode | Enum | Bekräftelse-statuskoden. Tillåtna värden är **godkända**, **Avvisad**, och **AcceptedWithErrors**. (Obligatoriskt) |
| processingStatus | Enum | Bearbetningsstatus för av bekräftelsen. Tillåtna värden är **mottagna**, **Generated**, och **skickade**. (Obligatoriskt) |
| ak903 | Sträng | Antal transaktion anger togs emot. (Valfritt) |
| ak904 | Sträng | Antal transaktioner accepteras i gruppen identifierade funktionella. (Valfritt) |
| ak9Segment | Sträng | Om gruppen funktionella identifieras i segmentet AK1 godkännas eller avvisas och varför. (Valfritt) |

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [övervakning B2B-meddelanden](logic-apps-monitor-b2b-message.md).
* Lär dig mer om [AS2 spårning scheman](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md).
* Lär dig mer om [B2B anpassade spårning scheman](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md).
* Lär dig mer om [spåra B2B-meddelanden i Operations Management Suite-portalen](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
* Lär dig mer om den [Enterprise-Integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md).  
