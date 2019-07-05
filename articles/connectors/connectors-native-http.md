---
title: Ansluta till HTTP eller HTTPS-slutpunkter från Azure Logic Apps
description: Övervaka HTTP eller HTTPS-slutpunkterna i automatiserade uppgifter, processer och arbetsflöden med hjälp av Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: fa5fd3ef8b144826468f56ea2a14be592cef5dc1
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541249"
---
# <a name="call-http-or-https-endpoints-by-using-azure-logic-apps"></a>Anropa HTTP eller HTTPS-slutpunkter med hjälp av Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och den inbyggda HTTP-anslutningen kan du automatisera arbetsflöden som regelbundet anropa valfri HTTP eller HTTPS-slutpunkt genom att skapa logikappar. Du kan till exempel övervaka tjänstslutpunkten för din webbplats genom att kontrollera att slutpunkten enligt ett angivet schema. När en viss händelse inträffar vid den slutpunkten, till exempel din webbplats slutar fungera, händelsen utlöser logikappens arbetsflöde och kör de angivna åtgärderna.

Kontrollera eller *avsökning* en slutpunkt med jämna mellanrum, du kan använda HTTP-utlösaren som det första steget i arbetsflödet. För varje kontroll utlösaren skickar ett anrop eller *begäran* till slutpunkten. Slutpunktens svaret avgör om din logikapp arbetsflödet körs. Utlösaren skickar med innehåll från svaret till åtgärder i din logikapp.

Du kan använda HTTP-åtgärden som andra steg i arbetsflödet för att anropa slutpunkten när du vill. Slutpunktens svaret avgör hur din återstående arbetsflödesåtgärder köras.

Baserat på mål-slutpunkten funktionen stöder HTTP-anslutningen Transport Layer Security (TLS) version 1.0, 1.1 och 1.2. Logic Apps förhandlar med slutpunkten jämfört med den högsta versionen som stöds möjligt. Så, till exempel om slutpunkten stöder 1.2, anslutningsappen använder 1.2 först. Annars kan används anslutningen den nästa högsta versionen som stöds.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* URL-Adressen för den mål-slutpunkt som du vill anropa

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Logikapp från där du vill anropa mål-slutpunkten. Du kommer igång med HTTP-utlösaren [skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Starta din logikapp med en utlösare som du vill om du vill använda HTTP-åtgärden. Det här exemplet använder HTTP-utlösaren som det första steget.

## <a name="add-an-http-trigger"></a>Lägg till en HTTP-utlösare

Den här inbyggda utlösaren gör ett HTTP-anrop till den angivna URL: en för en slutpunkt och returnerar ett svar.

1. Logga in på [Azure Portal](https://portal.azure.com). Öppna din tom logikapp i Logic App Designer.

1. På designern i sökrutan anger du ”http” som filter. Från den **utlösare** väljer den **HTTP** utlösaren.

   ![Välj HTTP-utlösare](./media/connectors-native-http/select-http-trigger.png)

   Det här exemplet byter namn på utlösare till ”HTTP-utlösare” så att steget innehåller ett mer beskrivande namn. Dessutom exemplet lägger till senare en HTTP-åtgärd, och båda måste vara unika.

1. Ange värden för den [http-utlösaren parametrar](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger) som du vill ska ingå i anropet till mål-slutpunkten. Konfigurera upprepningen för hur ofta du vill att utlösaren för att kontrollera slutpunkten som mål.

   ![Ange parametrar för HTTP-utlösare](./media/connectors-native-http/http-trigger-parameters.png)

   Läs mer om typer av autentisering som är tillgängliga för HTTP, [autentisera HTTP-utlösare och åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Om du vill lägga till andra tillgängliga parametrar, öppna den **Lägg till ny parameter** och välj de parametrar som du vill.

1. Fortsätt att skapa din logikapp arbetsflöde med åtgärder som körs när den utlöses.

1. När du är klar, gjort, Kom ihåg att spara din logikapp. På verktygsfältet för appdesignern väljer **spara**.

## <a name="add-an-http-action"></a>Lägg till en HTTP-åtgärd

Den här inbyggda åtgärden ett HTTP-anrop till den angivna URL: en för en slutpunkt och returnerar ett svar.

1. Logga in på [Azure Portal](https://portal.azure.com). Öppna logikappen i Logic App Designer.

   Det här exemplet använder HTTP-utlösaren som det första steget.

1. Under det steget där du vill lägga till HTTP-åtgärden, väljer **nytt steg**.

   Om du vill lägga till en åtgärd mellan stegen, flyttar du pekaren över pilen mellan stegen. Klicka på plustecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. På designern i sökrutan anger du ”http” som filter. Från den **åtgärder** väljer den **HTTP** åtgärd.

   ![Välj HTTP-åtgärd](./media/connectors-native-http/select-http-action.png)

   Det här exemplet byter namn på åtgärden som ska ”HTTP-åtgärd” så att steget innehåller ett mer beskrivande namn.

1. Ange värden för den [HTTP åtgärdsparametrar](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action) som du vill ska ingå i anropet till mål-slutpunkten.

   ![Ange HTTP-åtgärdsparametrar](./media/connectors-native-http/http-action-parameters.png)

   Läs mer om typer av autentisering som är tillgängliga för HTTP, [autentisera HTTP-utlösare och åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Om du vill lägga till andra tillgängliga parametrar, öppna den **Lägg till ny parameter** och välj de parametrar som du vill.

1. När du är klar, Kom ihåg att spara din logikapp. På verktygsfältet för appdesignern väljer **spara**.

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer information om utlösare och åtgärd parametrar finns i följande avsnitt:

* [Parametrar för HTTP-utlösare](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)
* [HTTP-åtgärdsparametrar](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)

### <a name="output-details"></a>Utdatainformation

Här finns mer information om utdata från en HTTP-utlösare eller åtgärd som returnerar den här informationen:

| Egenskapsnamn | Typ | Beskrivning |
|---------------|------|-------------|
| Rubriker | object | Rubriker i begäran |
| body | object | JSON-objekt | Objektet med brödtext i begäran |
| Statuskod | int | Statuskoden i begäran |
|||

| Statuskod | Beskrivning |
|-------------|-------------|
| 200 | Ok |
| 202 | Accepterat |
| 400 | Felaktig förfrågan |
| 401 | Behörighet saknas |
| 403 | Förbjudna |
| 404 | Det gick inte att hitta |
| 500 | Internt serverfel. Okänt fel uppstod. |
|||

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
