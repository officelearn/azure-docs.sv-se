---
title: 'Snabbstart: Skapa en kunskapsbas – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Du kan skapa en QnA Maker-kunskapsbas (KB) av eget innehåll, till exempel vanliga frågor eller produkthandböcker. QnA Maker-kunskapsbasen i det här exemplet skapas med en enkel webbsida för vanliga frågor för att besvara frågor om återställning av BitLocker-nycklar.
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 09/12/2018
ms.author: diberry
ms.openlocfilehash: 85b4630492d5f5b3fb71d563b0947d8a72ae4e1d
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886590"
---
# <a name="quickstart-create-train-and-publish-your-knowledge-base"></a>Snabbstart: Skapa, öva och publicera en egen kunskapsbas

Du kan skapa en QnA Maker-kunskapsbas (KB) av eget innehåll, till exempel vanliga frågor eller produkthandböcker. QnA Maker-kunskapsbasen i det här exemplet skapas med en enkel webbsida för vanliga frågor för att besvara frågor om återställning av BitLocker-nycklar.

## <a name="prerequisite"></a>Krav

> [!div class="checklist"]
> * Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-qna-maker-knowledge-base"></a>Skapa en QnA Maker-kunskapsbas

1. Logga in på QnAMaker.ai med dina autentiseringsuppgifter för Azure.

2. På webbplatsen för QnA Maker väljer du **Create a knowledge base** (skapa en kunskapsbas).

   ![Skapa ny kunskapsbas](../media/qna-maker-create-kb.png)

3. På sidan **skapa** i steg 1 väljer du **Create a QnA service** (skapa en tjänst för frågor och svar). Du dirigeras till [Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) för att konfigurera en QnA Maker-tjänst i din prenumeration. Om tidsgränsen för Azure-portalen passeras väljer du **Försök igen** på webbplatsen. När du har anslutit visas instrumentpanelen i Azure.

4. När du har skapat en ny QnA Maker-tjänst i Azure kan du gå tillbaka till qnamaker.ai/create. Välj din frågor och svar-tjänst från listrutorna i steg 2. Om du har skapat en ny QnA-tjänst måste du uppdatera sidan.

   ![Välj en QnA-tjänstkunskapsbas](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

5. I steg 3 ska du tilldela din kunskapsbas namnet **My Sample QnA KB**.

6. Välj tre typer av datakällor om du vill lägga till innehåll i din kunskapsbas. Under **Populate your KB** (fyll i kunskapsbasen) i steg 4 lägger du till webbadressen för [BitLocker Recovery FAQ](https://docs.microsoft.com/en-us/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) i rutan **URL**.

   ![Välj en QnA-tjänstkunskapsbas](../media/qnamaker-quickstart-kb/add-datasources.png)

7. Välj **Create your KB** (skapa kunskapsbasen) i steg 5.

8. Ett popup-fönster visas medan kunskapsbasen skapas. Det tar några minuter för extraheringsprocessen att läsa HTML-sidan och identifiera frågor och svar.

9. När kunskapsbasen har skapats öppnas sidan **Kunskapsbas**. På den här sidan kan du redigera innehållet i kunskapsbasen.

10. Välj **Add QnA pair** (lägg till par med fråga och svar) högst upp till höger för att lägga till en ny rad i avsnittet **Redaktionellt** i kunskapsbasen. Skriv **Hi** under **Fråga.** Skriv **Hello** under **Svar. Ställ bitlocker-frågor till mig.**

   ![Lägga till par med fråga och svar](../media/qnamaker-quickstart-kb/add-qna-pair.png)

11. Välj **Spara och öva** i det övre högra hörnet för att spara dina ändringar och öva med QnA Maker-modellen. Redigeringarna försvinner om de inte sparas.

   ![Spara och öva](../media/qnamaker-quickstart-kb/add-qna-pair2.png)

12. Välj **Testa** i det övre högra hörnet för att testa att de ändringar du gjort trädde i kraft. Skriv **hi there** i rutan och tryck på RETUR. Du bör nu se svaret du skapade som ett svar.

13. Välj **Granska** för att undersöka svaret i detalj. Testfönstret används för att testa dina ändringar med kunskapsbasen innan de publiceras.

   ![Testpanel](../media/qnamaker-quickstart-kb/inspect-panel.png)

14. Välj **Test** igen för att dölja popup-fönstret **Test**.

15. På menyn bredvid **Redigera**väljer du **Publicera**. Bekräfta sedan genom att välja **Publicera** på sidan.

16. QnA Maker-tjänsten har nu publicerats. Du kan använda slutpunkten i ditt program eller din robotkod.

   ![Publicera](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en kunskapsbas](../How-To/create-knowledge-base.md)
