---
title: Hantera logikappar i Visual Studio - Azure Logic Apps | Microsoft Docs
description: "Hantera logikappar och andra Azure tillgångar med Visual Studio Cloud Explorer"
author: klam
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 12/19/2016
ms.author: LADocs; klam
ms.openlocfilehash: a5bf24de1a7a2b6d4c1ae6416c95d83ef7506da3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-your-logic-apps-with-visual-studio-cloud-explorer"></a>Hantera dina logic apps med Visual Studio Cloud Explorer

Även om den [Azure-portalen](https://portal.azure.com/) erbjuder ett bra sätt att utforma och hantera Azure Logikappar kan du använda Visual Studio Cloud Explorer för att hantera många Azure tillgångar, inklusive logikappar. Visual Studio Cloud Explorer kan du bläddra, hantera, redigera och hämta publicerade logikappar. Hanteringsuppgifter omfattar aktivera, inaktivera och visa kör historik. 

Innan du kan komma åt och hantera dina logic apps i Visual Studio, installera och konfigurera de här Visual Studio-verktygen för Logikappar i Azure. 

## <a name="prerequisites"></a>Krav

* [Visual Studio 2015 eller Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [Den senaste Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 eller senare)
* [Visual Studio Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* Tillgång till Internet när du använder embedded designer

## <a name="install-visual-studio-tools-for-logic-apps"></a>Installera Visual Studio tools för Logic Apps

När du har installerat de nödvändiga förutsättningarna, hämta och installera Azure Logic Apps verktyg för Visual Studio.

1. Öppna Visual Studio. På den **verktyg** väljer du **tillägg och uppdateringar**.
2. Expandera den **Online** kategori så att du kan söka online i Visual Studio-galleriet.
3. Bläddra eller Sök efter **Logikappar** tills du hittar **Azure Logic Apps Tools för Visual Studio**.
4. Om du vill hämta och installera tillägget genom att klicka på **hämta**.
5. Starta om Visual Studio efter installationen.

> [!NOTE]
> Hämta Azure Logic Apps verktyg för Visual Studio direkt genom att gå till den [Visual Studio Marketplace](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9).

## <a name="browse-for-logic-apps-in-cloud-explorer"></a>Bläddra efter logikappar i Cloud Explorer

1.  Öppna Cloud Explorer, på den **visa** -menyn, Välj **Cloud Explorer**.
2.  Leta upp din logikapp genom resursgruppen eller resursen. 

    * Om du bläddrar efter resurstyp, Välj din Azure-prenumeration, expandera den **Logic Apps** avsnittet och väljer din logikapp. 
    * Om du bläddrar med resursgrupp, expandera den resursgrupp som har logikappen och välj din logikapp.

    Om du vill visa kommandon för din logikapp, antingen högerklicka på din logikapp eller längst ned i Cloud Explorer väljer den **åtgärder** menyn.

    ![Bläddra efter din logikapp](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-your-logic-app-with-logic-apps-designer"></a>Redigera din logikapp med Logic Apps Designer

Cloud Explorer kan du öppna en för tillfället distribuerade logikapp i samma designer som du använder i Azure-portalen. 

* Om du vill redigera din logikapp i Cloud Explorer, högerklicka på din logikapp och välj **öppna med logik App Editor**. 

* För att publicera dina uppdateringar till molnet, Välj **publicera**. 

* Om du vill starta ett nytt kör väljer **köra utlösaren**.

![Logic Apps Designer](./media/logic-apps-manage-from-vs/designer.png)

Från designer kan du också **hämta** en logikapp. Den här åtgärden automatiskt parameterizes logik app definition och sparar definitionen som en Distributionsmall av Azure Resource Manager. Du kan lägga till den här mallen för distribution till Azure-resursgrupp-projekt.

## <a name="browse-your-logic-app-run-history"></a>Bläddra logikappen kör historik

Om du vill visa körningshistorik för din logikapp, högerklicka på din logikapp och välj **öppna kör historik**. Om du vill ordna om historiken kör baserat på någon av egenskaperna som visas markerar du kolumnrubriken.

![Kör historik](media/logic-apps-manage-from-vs/runs.png)

Dubbelklicka på någon av instanserna kör om du vill visa körningshistorik för en instans så att du kan granska kör resultat, inklusive indata och utdata från varje steg.

![Kör historikresultat, indata och utdata från steg](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>Nästa steg

* [Skapa din första logiska app](logic-apps-create-a-logic-app.md)
* [Utforma, skapa och distribuera logikappar i Visual Studio](logic-apps-deploy-from-vs.md)
* [Visa vanliga exempel och scenarier](logic-apps-examples-and-scenarios.md)
* [Video: Automatisera affärsprocesser med Azure Logikappar](http://channel9.msdn.com/Events/Build/2016/T694)
* [Video: Integrera dina system med Azure Logikappar](http://channel9.msdn.com/Events/Build/2016/P462)
