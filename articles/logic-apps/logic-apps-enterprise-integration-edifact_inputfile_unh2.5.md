---
title: Logic Apps B2B edifact avkoda lösa UNH2.5 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps B2B edifact avkoda lösa UNH2.5
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 3e04e7515a8ddfe512ba2efbbd45c3d1b571c1fd
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297955"
---
# <a name="how-to-handle-edifact-documents-having-unh25-segment"></a>Hantera EDIFACT-dokument med UNH2.5 segment
Om UNH2.5 finns i dokumentet EDIFACT används för schemat sökning. 

Exempel: Fältet UNH är **EAN008** i EDIFACT-meddelande  
UNH + SSDD1 + ORDER: D: 03B: FN:**EAN008**'  

Åtgärder för att hantera meddelandet 
1. Uppdatera schemat
2. Kontrollera inställningarna för avtal  

## <a name="update-the-schema"></a>Uppdatera schemat
Du behöver distribuera ett schema med UNH2.5 rotnodens namn för att bearbeta meddelandet.  För ett exempel är roten schemanamnet **EFACT_D03B_ORDERS_EAN008**  

För varje D03B_ORDERS med ett annat UNH2.5 segment, skulle du behöva distribuera en enskild schemat.  

## <a name="add-schema-to-the-edifact-agreement"></a>Lägga till schemat i EDIFACT-avtal
### <a name="edifact-decode"></a>EDIFACT avkoda
Konfigurera schemat för att avkoda det inkommande meddelandet i EDIFACT avtalet tar emot inställningar
1. Lägga till schemat integration-konto    
2. Konfigurera schemat i EDIFACT avtal får inställningar. 
3. Välj EDIFACT-avtal och klickar på **redigera som JSON**.  Lägg till UNH2.5 värde i avtalet får **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT koda
Konfigurera schemat för att koda det inkommande meddelandet i EDIFACT-avtal skicka inställningar
1. Lägga till schemat integration-konto    
2. Konfigurera schemat i EDIFACT-avtal skicka inställningar. 
3. Välj EDIFACT-avtal och klickar på **redigera som JSON**.  Lägg till UNH2.5 värde i avtalet skicka **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Nästa steg
* [Mer information om integration konto avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md "Lär dig mer om enterprise integration-avtal")  