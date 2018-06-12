---
title: AS2 spårning scheman för övervakning av B2B - Azure Logic Apps | Microsoft Docs
description: AS2 spårning scheman används för att övervaka B2B-meddelanden från transaktioner i ditt Azure-konto för integrering.
author: padmavc
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 48e39fd20716e962c4a3e367fdff18e0b4fba32d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300889"
---
# <a name="start-or-enable-tracking-of-as2-messages-and-mdns-to-monitor-success-errors-and-message-properties"></a>Starta eller aktivera spårning av AS2-meddelanden och MDNs till övervakaren lyckade, fel och meddelandeegenskaper
Du kan använda dessa scheman för AS2-spårning i ditt konto i Azure-integrering för att övervaka business-to-business (B2B) transaktioner:

* AS2 meddelandet spårning av schemat
* AS2 MDN spårning av schemat

## <a name="as2-message-tracking-schema"></a>AS2 meddelandet spårning av schemat
````java

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
````

| Egenskap  | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | Sträng | AS2 meddelandet partner avsändare. (Valfritt) |
| receiverPartnerName | Sträng | Mottagaren AS2-meddelande Partnernamn. (Valfritt) |
| as2To | Sträng | AS2 meddelandet mottagarens namn från meddelandet AS2-huvuden. (Obligatoriskt) |
| as2From | Sträng | AS2 meddelandet avsändare, från meddelandet AS2-huvuden. (Obligatoriskt) |
| agreementName | Sträng | Namnet på AS2-avtal som meddelanden har åtgärdats. (Valfritt) |
| riktning | Sträng | Flödesriktning i meddelandet, ta emot och skicka. (Obligatoriskt) |
| messageId | Sträng | AS2 meddelande-ID från huvuden i AS2-meddelande (valfritt) |
| dispositionType |Sträng | Meddelandet Disposition meddelande (MDN) disposition TYPVÄRDE. (Valfritt) |
| fileName | Sträng | Namnet på filen från meddelandet AS2-rubriken. (Valfritt) |
| isMessageFailed |Boolesk | Om AS2-meddelandet misslyckades. (Obligatoriskt) |
| isMessageSigned | Boolesk | Om AS2-meddelandet signerades. (Obligatoriskt) |
| isMessageEncrypted | Boolesk | Anger om meddelandet AS2 krypterades. (Obligatoriskt) |
| isMessageCompressed |Boolesk | Om AS2-meddelandet har komprimerats. (Obligatoriskt) |
| correlationMessageId | Sträng | AS2 meddelande-ID, att korrelera meddelanden med MDNs. (Valfritt) |
| incomingHeaders |Ordlista för JToken | AS2-huvud detaljer för inkommande meddelanden. (Valfritt) |
| outgoingHeaders |Ordlista för JToken | Utgående detaljer om AS2-huvud. (Valfritt) |
| isNrrEnabled | Boolesk | Använd standardvärdet om värdet inte är känd. (Obligatoriskt) |
| isMdnExpected | Boolesk | Använd standardvärdet om värdet inte är känd. (Obligatoriskt) |
| mdnType | Enum | Tillåtna värden är **NotConfigured**, **Sync**, och **asynkrona**. (Obligatoriskt) |

## <a name="as2-mdn-tracking-schema"></a>AS2 MDN spårning av schemat
````java

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
````

| Egenskap  | Typ | Beskrivning |
| --- | --- | --- |
| senderPartnerName | Sträng | AS2 meddelandet partner avsändare. (Valfritt) |
| receiverPartnerName | Sträng | Mottagaren AS2-meddelande Partnernamn. (Valfritt) |
| as2To | Sträng | Partnernamn som tar emot AS2-meddelandet. (Obligatoriskt) |
| as2From | Sträng | Partnernamn som skickar AS2-meddelandet. (Obligatoriskt) |
| agreementName | Sträng | Namnet på AS2-avtal som meddelanden har åtgärdats. (Valfritt) |
| riktning |Sträng | Flödesriktning i meddelandet, ta emot och skicka. (Obligatoriskt) |
| messageId | Sträng | AS2-meddelande-ID. (Valfritt) |
| originalMessageId |Sträng | AS2 ursprungliga meddelande-ID. (Valfritt) |
| dispositionType | Sträng | MDN disposition TYPVÄRDE. (Valfritt) |
| isMessageFailed |Boolesk | Om AS2-meddelandet misslyckades. (Obligatoriskt) |
| isMessageSigned |Boolesk | Om AS2-meddelandet signerades. (Obligatoriskt) |
| isNrrEnabled | Boolesk | Använd standardvärdet om värdet inte är känd. (Obligatoriskt) |
| statuskod | Enum | Tillåtna värden är **godkända**, **Avvisad**, och **AcceptedWithErrors**. (Obligatoriskt) |
| micVerificationStatus | Enum | Tillåtna värden är **NotApplicable**, **lyckades**, och **misslyckades**. (Obligatoriskt) |
| correlationMessageId | Sträng | Korrelations-ID. Ursprungligt postmeddelandet ID (meddelande-ID för meddelandet som MDN är konfigurerat). (Valfritt) |
| incomingHeaders | Ordlista för JToken | Anger sidhuvudet detaljer för inkommande meddelanden. (Valfritt) |
| outgoingHeaders |Ordlista för JToken | Anger utgående meddelande sidhuvud information. (Valfritt) |

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om den [Enterprise-Integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md).    
* Lär dig mer om [övervakning B2B-meddelanden](logic-apps-monitor-b2b-message.md).   
* Lär dig mer om [B2B anpassade spårning scheman](logic-apps-track-integration-account-custom-tracking-schema.md).   
* Lär dig mer om [X12 spårning scheman](logic-apps-track-integration-account-x12-tracking-schema.md).   
* Lär dig mer om [spåra B2B-meddelanden i logganalys](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
