---
title: Kör en spelbok i förhandsversionen av Azure Sentinel | Microsoft Docs
description: Den här artikeln beskriver hur du kör en spelbok i Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 5ce409b984ab9110a8ca44f47675045e4d80aed5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58088646"
---
# <a name="tutorial-set-up-automated-threat-responses-in-azure-sentinel-preview"></a>Självstudier: Konfigurera automatisk threat svar i Sentinel-förhandsversionen av Azure

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här kursen hjälper dig att använda strategiböcker för säkerhet i Azure Sentinel för att ange automatiserade threat svar på säkerhetsrelaterade problem som identifieras av Azure Sentinel.


> [!div class="checklist"]
> * Förstå spelböcker
> * Skapa en spelbok
> * Kör en spelbok


## <a name="what-is-a-security-playbook-in-azure-sentinel"></a>Vad är en säkerhetsspelbok i Azure Sentinel?

En säkerhetsspelbok är en samling procedurer som kan köras från Azure Sentinel som svar på en avisering. En säkerhetsspelbok kan kan automatisera och dirigera svaret, och köras manuellt eller inställd på att köras automatiskt när specifika aviseringar har utlösts. Strategiböcker för säkerhet i Azure Sentinel baseras på [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps), vilket innebär att du får all den kraft, anpassningsbarhet och inbyggda mallar för Logikappar. Varje spelboken har skapats för den specifika prenumerationen som du väljer, men när du tittar på sidan med Strategiböcker, visas alla spelböcker för alla valda prenumerationer.

> [!NOTE]
> Spelböcker utnyttja Azure Logic Apps, därför tillkommer avgifter. Besök prissidan för [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) om du vill ha mer information.

Om du oroar skadlig angripare som har åtkomst till nätverksresurserna, kan du ange en avisering som söker efter skadliga IP-adresser som kommer åt nätverket. Sedan skapar du en spelbok som gör följande:
1. Skapa ett ärende i ServiceNow eller andra IT som biljettsystem när aviseringen utlöses.
2. Skicka ett meddelande till din säkerhetskanal åtgärder i Microsoft Teams eller Slack för att kontrollera att din säkerhetsanalytiker är medvetna om incidenten.
3. Skicka all information i aviseringen till din senior administratörs- och säkerhetsanvändare nätverksadministratör. E-postmeddelandet innehåller också alternativknapparna för användaren **blockera** eller **Ignorera**.
4. Spelboken fortsätter att köras när ett svar tas emot från administratörer.
5. Om administratörer väljer **blockera**, IP-adressen är blockerad i brandväggen och användaren är inaktiverad i Azure AD.
6. Om administratörer väljer **Ignorera**, aviseringen stängs i Azure Sentinel och incidenten stängs i ServiceNow.

Strategiböcker för säkerhet kan köras manuellt eller automatiskt. Kör dem manuellt innebär att när du får en avisering kan du kör en spelbok på begäran som ett svar till den valda aviseringen. Köra dem automatiskt innebär att när du redigerar korrelation regeln du ställer in den för att automatiskt köra en eller flera spelböcker när aviseringen utlöses.


## <a name="create-a-security-playbook"></a>Skapa en säkerhetsspelbok

Följ dessa steg för att skapa en ny säkerhetsspelbok i Azure Sentinel:

1. Öppna den **Azure Sentinel** instrumentpanelen.
2. Under **Management**väljer **Spelböcker**.

   ![Logikapp](./media/tutorial-respond-threats-playbook/playbookimg.png)

3. I den **Sentinel-Azure - Spelböcker (förhandsgranskning)** klickar du på **Lägg till** knappen.

   ![Skapa en logikapp](./media/tutorial-respond-threats-playbook/create-playbook.png) 

4. I den **skapa en logikapp** anger den begärda informationen för att skapa den nya logikappen och klicka på **skapa**. 

5. I den [ **Logic App Designer**](../logic-apps/logic-apps-overview.md), Välj den mall som du vill använda. Om du väljer en mall som kräver autentiseringsuppgifter som behöver du ange dem. Du kan också skapa en ny tom spelbok från grunden. Välj **tom Logikapp**. 

   ![Designer för logikappar](./media/tutorial-respond-threats-playbook/playbook-template.png)

6. Du kommer till Logic App Designer där du kan skapa nya eller redigera mallen. Mer information om hur du skapar en spelbok med [Logikappar](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

7. Om du skapar en tom spelbok i den **Sök alla anslutningsappar och utlösare** skriver *Azure Sentinel*, och välj **när ett svar till en Azure Sentinel avisering är utlöst**. <br>När den har skapats, ny spelbok visas i den **Spelböcker** lista. Om den inte visas klickar du på **uppdatera**. 

7. Nu kan du definiera vad som händer när du utlöser spelboken. Du kan lägga till en åtgärd, logiskt villkor, förhållanden växeln eller slingor.

   ![Designer för logikappar](./media/tutorial-respond-threats-playbook/logic-app.png)

## <a name="how-to-run-a-security-playbook"></a>Så här kör du en säkerhetsspelbok

Du kan köra en spelbok på begäran.

Kör en spelbok på begäran:

1. I den **fall** väljer du ett ärende och klicka på **Visa fullständig information**.

2. I den **aviseringar** fliken, klickar på den avisering du vill köra spelboken och rulla längst till höger och klicka på **visa spelböcker** och välj en spelbok till **kör** från den lista över tillgängliga spelböcker för prenumerationen. 




## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du kör en spelbok i Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar: I den här självstudien har du lärt dig hur du kör en spelbok i Azure Sentinel. Fortsätta att den [hur proaktivt hitta hot](hunting.md) med hjälp av Azure Sentinel.
> [!div class="nextstepaction"]
> [Hitta hot](hunting.md) att hitta proaktivt hot i nätverket.

