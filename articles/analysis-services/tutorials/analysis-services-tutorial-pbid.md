---
title: Självstudie – Ansluta till Azure Analysis Services med Power BI Desktop | Microsoft Docs
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: ad97fd7569690d0d199b0ce1a82f31f8dfa64c26
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426140"
---
# <a name="tutorial-connect-with-power-bi-desktop"></a>Självstudie: Ansluta med Power BI Desktop

I den här självstudien använder du Power BI Desktop för att ansluta till exempelmodelldatabasen adventureworks på din server. Uppgifterna du utför simulerar en vanlig användaranslutning till modellen och skapar en enkel rapport från modelldata.

> [!div class="checklist"]
> * Hämta servernamnet från Portal
> * Anslut med hjälp av Power BI Desktop
> * Skapa en enkel rapport

## <a name="prerequisites"></a>Nödvändiga komponenter

- [Lägg till exempelmodelldatabasen adventureworks](../analysis-services-create-sample-model.md) på din server.
- Se till att du har [*läsbehörighet*](../analysis-services-server-admins.md) för exempelmodelldatabasen adventureworks.
- [Installera senaste versionen av Power BI Desktop](https://powerbi.microsoft.com/desktop).

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure-portalen
I den här självstudien loggar du in på Portal för att hämta servernamnet. Normalt skulle användarna få servernamnet från serveradministratören.

Logga in på [Portal](https://portal.azure.com/).

## <a name="get-server-name"></a>Hämta servernamnet
För att kunna ansluta till servern från Power BI Desktop behöver du först servernamnet. Du kan hämta servernamnet på Portal.

Välj **Azure Portal** > server > **Översikt** > **Servernamn** och kopiera servernamnet.
   
   ![Hämta servernamnet i Azure](./media/analysis-services-tutorial-pbid/aas-copy-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>Ansluta i Power BI Desktop

1. I Power BI Desktop klickar du på **Hämta data** > **Azure** > **Azure Analysis Services-databas**.

   ![Ansluta i Hämta data](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aasserver.png)

2. I **Server** klistrar du in servernamnet. I **Databas** skriver du sedan **adventureworks** och klickar på **OK**.

   ![Ange servernamn och modelldatabas](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aas-servername.png)

3. Ange dina inloggningsuppgifter när du uppmanas att göra det. Kontot du anger måste ha minst läsbehörighet för exempelmodelldatabasen adventureworks.

    Modellen adventureworks öppnas i Power BI Desktop med en tom rapport i rapportvyn. I listan **Fält** visas alla modellobjekt som inte är dolda. Anslutningsstatus visas i det nedre högra hörnet.

4. I **VISUALISERINGAR** väljer du **Grupperat liggande stapeldiagram**, klickar på **Format** (färgrollerikonen) och aktiverar **Data Labels** (Dataetiketter). 

   ![Visualiseringar](./media/analysis-services-tutorial-pbid/aas-pbid-visualizations-report.png)

5. I **FÄLT** > **Internet Sales**-tabellen (Internetförsäljning) väljer du måtten **Internet Sales Total** (Total Internetförsäljning) och **Margin** (Marginal). Välj **Product Category Name** (Namn på produktkategori) i tabellen **Product Category** (Produktkategori).

   ![Slutför rapporten](./media/analysis-services-tutorial-pbid/aas-pbid-complete-report.png)

    Ägna en stund åt att utforska exempelmodellen adventureworks genom att skapa olika visualiseringar och uppdelningar av data och mått. Glöm inte att spara när du är nöjd med rapporten.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte behöver rapporten längre behöver du inte spara den. Du kan också ta bort filen om du har sparat den.

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig att använda Power BI Desktop för att ansluta till en datamodell på en server och skapa en enkel rapport. Om du inte vet hur man skapar en datamodell kan du se [självstudien för Adventure Works-tabellobjektsmodell](aas-adventure-works-tutorial.md).