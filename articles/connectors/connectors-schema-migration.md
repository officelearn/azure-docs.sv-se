---
title: Migrera appar till det senaste schemat
description: Migrera json-definitioner för logikapparbetsflöde till den senaste schemaversionen för arbetsflödesdefinitionsspråk
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: cef0fcb990cd2c5c6583822d4dc4c6993c52eac2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666796"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>Migrera logikappar till den senaste schemaversionen

Så här flyttar du dina befintliga logikappar till det senaste schemat: 

1. Öppna logikappen i Logic App Designer i [Azure-portalen.](https://portal.azure.com)

2. På logikappens meny väljer du **Översikt**. Välj **Uppdatera schema i**verktygsfältet .

   > [!NOTE]
   > När du väljer **Uppdatera schema**kör Azure Logic Apps automatiskt migreringsstegen och tillhandahåller kodutdata för dig. Du kan använda den här utdata för att uppdatera logikappdefinitionen. Se dock till att du följer metodtips enligt beskrivningen i följande avsnitt **om metodtips.**

   ![Uppdatera schema](./media/connectors-schema-migration/update-schema.png)

   Sidan Uppdatera schema visas och visar en länk till ett dokument som beskriver förbättringarna i det nya schemat.

## <a name="best-practices"></a>Bästa praxis

Här är några metodtips för att migrera dina logikappar till den senaste schemaversionen:

* Kopiera det migrerade skriptet till en ny logikapp. Skriv inte över den gamla versionen förrän du har slutfört testningen och bekräfta att den migrerade appen fungerar som förväntat.

* Testa logikappen **innan** du sätter i produktion.

* När du har avslutat migreringen börjar du uppdatera logikapparna så att de [hanterade API:erna](../connectors/apis-list.md) används där det är möjligt. Börja till exempel använda Dropbox v2 överallt där du använder DropBox v1.

## <a name="next-steps"></a>Nästa steg

* Läs om hur du [migrerar dina Logic-appar manuellt](../logic-apps/logic-apps-schema-2015-08-01.md)
