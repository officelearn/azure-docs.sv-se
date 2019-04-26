---
title: Migrera appar till senaste schema – Azure Logic Apps | Microsoft Docs
description: Så här migrerar du logikappar till den senaste schemaversionen
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.assetid: 3e177e49-fd69-43e9-9b9b-218abb250c31
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: bf27739bd42106550c18e3bbc27a1ff8b3770747
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60447169"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>Så här migrerar du logikappar till senaste schemaversionen

Följ dessa steg om du vill flytta dina befintliga logikappar till det senaste schemat: 

1. I den [Azure-portalen](https://portal.azure.com), öppna logikappen i Logic App Designer.

2. På menyn för din logikapp, Välj **översikt**. I verktygsfältet, välja **uppdatera Schema**.

   > [!NOTE]
   > När du väljer **uppdatera Schema**, Azure Logic Apps som automatiskt kör migreringsstegen och ger dig kodens utdata för. Du kan använda dessa utdata för att uppdatera sina logikapp-definitioner. Se dock till att du följer bästa praxis som beskrivs i följande **bästa praxis** avsnittet.

   ![Uppdatera schema](./media/connectors-schema-migration/update-schema.png)

   Sidan uppdatera Schema visas och visar en länk till ett dokument som beskriver förbättringarna i det nya schemat.

## <a name="best-practices"></a>Bästa praxis

Här följer några rekommendationer för att migrera dina logikappar till den senaste schemaversionen:

* Kopiera det migrerade skriptet till en ny logikapp. Skriv inte över den gamla versionen tills du slutför testet och bekräfta att migrerade appen fungerar som förväntat.

* Testa din logikapp **innan** i produktion.

* När du har slutfört migreringen, börja uppdatera logikapparna och använda den [hanterade API: er](../connectors/apis-list.md) där det är möjligt. Till exempel börja använda Dropbox v2 var som helst att du använder DropBox v1.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [migrera dina logikappar manuellt](../logic-apps/logic-apps-schema-2015-08-01.md)
