---
title: Testa logikappar med testdata
description: Ställ in statiska resultat för att testa logikappar med utkastdata utan att påverka produktionsmiljöer
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: b71aae91f4a065b70537a300aa0bd7016edfd4b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790274"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Testa logikappar med utkastdata genom att ställa in statiska resultat

När du testar logikapparna kanske du inte är redo att anropa eller komma åt appar, tjänster och system av olika anledningar. Vanligtvis i dessa scenarier kan du behöva köra olika villkorssökvägar, tvinga fel, tillhandahålla specifika meddelandesvarsorgan eller till och med prova att hoppa över några steg. Genom att ställa in statiska resultat för en åtgärd i logikappen kan du håna utdata från den åtgärden. Om du aktiverar statiska resultat för en åtgärd körs inte åtgärden, men utkastdata returneras i stället.

Om du till exempel ställer in statiska resultat för åtgärden Skicka e-post i Outlook 365 returnerar logic apps-motorn bara de utkastdata som du har angett som statiska resultat i stället för att anropa Outlook och skicka ett e-postmeddelande.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill ställa in statiska resultat

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Ställ in statiska resultat

1. Om du inte redan har öppnat logikappen i Logic Apps Designer i [Azure-portalen.](https://portal.azure.com)

1. Gör så här om du vill konfigurera statiska resultat: 

   1. I åtgärdens övre högra hörn väljer du ellipserna (*...*) och väljer **Statiskt resultat**, till exempel:

      ![Välj "Statiskt resultat" > "Aktivera statiskt resultat"](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Välj **Aktivera statiskt resultat**. För de obligatoriska (*) egenskaperna anger du de modellutdatavärden som du vill returnera för åtgärdens svar.

      Här är till exempel de egenskaper som krävs för HTTP-åtgärden:

      | Egenskap | Beskrivning |
      |----------|-------------|
      | **Status** | Åtgärdens status för att returnera |
      | **Statuskod** | Den specifika statuskoden som ska returneras |
      | **Rubriker** | Rubrikinnehållet som ska returneras |
      |||

      ![Välj "Aktivera statiskt resultat"](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Om du vill ange utkastsdata i JSON-format (JavaScript Object![Notation) väljer du](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)Växla **till JSON-läge** ( Välj "Växla till JSON-läge").

   1. Öppna listan Välj **valfria fält** för valfria egenskaper och välj de egenskaper som du vill håna.

      ![Välj valfria egenskaper](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. När du är redo att spara väljer du **Klar**.

   I åtgärdens övre högra hörn visar namnlisten nu en![testbägareikon](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)( Ikon för statiska resultat ), vilket indikerar att du har aktiverat statiska resultat.

   ![Ikon som visar aktiverade statiska resultat](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   Information om hur du hittar tidigare körningar som använder utkastdata finns i [Sök körs som använder statiska resultat](#find-runs-mock-data) senare i det här avsnittet.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Återanvända tidigare utdata

Om logikappen har en tidigare körning med utdata som du kan återanvända som utkast, kan du kopiera och klistra in utdata från den körningen.

1. Om du inte redan har öppnat logikappen i Logic Apps Designer i [Azure-portalen.](https://portal.azure.com)

1. På logikappens huvudmeny väljer du **Översikt**.

1. I avsnittet **Kör historik** väljer du den logikappkörning du vill använda.

1. Leta reda på och expandera den åtgärd som har de utdata du vill ha i logikappens arbetsflöde.

1. Välj länken **Visa råutgångar.**

1. Kopiera antingen det fullständiga JSON-objektet (JavaScript Object Notation) eller det specifika underavsnitt som du vill använda, till exempel avsnittet utdata eller bara rubrikavsnittet.

1. Följ stegen för att öppna rutan **Statiskt resultat** för din åtgärd i [Konfigurera statiska resultat](#set-up-static-results).

1. När rutan **Statiskt resultat** har öppnats väljer du något avstegen:

   * Om du vill klistra in ett komplett JSON-objekt väljer du](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)Växla till **JSON-läge** (![Välj "Växla till JSON-läge"):

     ![Välj "Växla till JSON-läge" för komplett objekt](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Om du bara vill klistra in ett JSON-avsnitt väljer du **Växla till JSON-läge bredvid** avsnittets etikett, till exempel:

     ![Välj "Växla till JSON-läge" för utgångar](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. Klistra in din tidigare kopierade JSON i JSON-redigeraren.

   ![JSON-läge](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. När du är klar väljer du **Klar**. Om du vill återgå till designern![väljer du Växla](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png) **redigeringsläge** ( Välj "Switch Editor Mode").

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Hitta körningar som använder statiska resultat

Logikappens körhistorik identifierar de körningar där åtgärderna använder statiska resultat. Så här hittar du följande körningar:

1. På logikappens huvudmeny väljer du **Översikt**. 

1. Leta reda på kolumnen **Statiska resultat** under **Historik**för körs i den högra rutan. 

   Alla lömrar som innehåller åtgärder med resultat har kolumnen **Statiska resultat** inställd **på Aktiverad,** till exempel:

   ![Körhistorik - statisk resultatkolumn](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Om du vill visa åtgärder som använder statiska resultat markerar du den körning du vill använda där kolumnen **Statiska resultat** är inställd **på Aktiverad**.

   Åtgärder som använder statiska resultat![visar ikonen](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)testbägare ( Ikon för statiska resultat ), till exempel:

   ![Kör historik - åtgärder som använder statiska resultat](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Inaktivera statiska resultat

Om du inaktiverar statiska resultat kastas inte värdena från den senaste konfigurationen bort. Så när du aktiverar statiska resultat nästa gång kan du fortsätta använda dina tidigare värden.

1. Leta reda på den åtgärd där du vill inaktivera statiska utdata. I åtgärdens övre högra hörn väljer du ikonen![för testbägaren ( Ikon för statiska resultat](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)).

   ![Inaktivera statiska resultat](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Välj **Inaktivera statiskt resultat** > **gjort**.

   ![Inaktivera statiska resultat](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>Referens

Mer information om den här inställningen i de underliggande arbetsflödesdefinitionerna finns i [Statiska resultat - Schemareferens för arbetsflödesdefinitionsspråk](../logic-apps/logic-apps-workflow-definition-language.md#static-results) och [runtimeConfiguration.staticResult - Runtime-konfigurationsinställningar](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Logic Apps](../logic-apps/logic-apps-overview.md)