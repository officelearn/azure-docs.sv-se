---
title: Självstudie – Hantera kostnader med Azure Cost Management | Microsoft Docs
description: I den här självstudien lär du dig att hantera kostnader med hjälp av kostnadsallokering, showback- och chargeback-rapporter.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: ''
manager: dougeby
ms.openlocfilehash: 16f86eace9b5848f263e0d0772db441a123f21ae
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-manage-costs-by-using-azure-cost-management"></a>Självstudie: Hantera kostnader med Azure Cost Management

Du hanterar kostnader och skapar showback-rapporter i Azure Cost Management genom att allokera kostnader baserat på taggar. Vid kostnadsallokeringen tilldelas kostnader till dina förbrukade molnresurser. Kostnaderna är helt allokerade när alla resurser är kategoriserade med taggar. När kostnaderna har allokerats kan du visa showback- eller chargeback-information för användarna på instrumentpaneler och i rapporter. Många resurser kanske däremot inte är taggade eller inte kan taggas när du börjar använda Cost Management.

Till exempel kanske du vill få ersättning för ingenjörskostnader. Du måste kunna visa ingenjörsteamet att du behöver ett specifikt belopp baserat på resurskostnader. Du kan visa en rapport med alla förbrukade resurser som är taggade med *engineering*.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Använder anpassade taggar för allokering av kostnader.
> * Skapar showback- och chargeback-rapporter.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Du måste ha ett Azure-konto.
- Du måste antingen ha en utvärderingsregistrering eller en betald prenumeration för Azure Cost Management.

## <a name="use-custom-tags-to-allocate-costs"></a>Använda anpassade taggar för allokering av kostnader

När du börjar allokera kostnader ska du först definiera omfattningen med hjälp av en kostnadsmodell. Kostnadsmodellen ändrar inte kostnaderna utan fördelar dem. När du skapar en kostnadsmodell delar du upp data efter kostnadsenhet, konto eller prenumeration, samt med flera olika taggar. Vanliga exempel på taggar kan vara en faktureringskod, ett kostnadsställe eller ett gruppnamn. Taggarna hjälper dig även med showback eller chargeback till andra delar av organisationen.

Om du vill skapa en anpassad kostnadsallokeringsmodell väljer du **Cost** (Kostnad) &gt; **Cost Management** (Kostnadshantering) &gt; **Cost Allocation 360°** (Kostnadsallokering 360°) i rapportmenyn.

![Kostnadsallokering 360° valt](./media/tutorial-manage-costs/cost-allocation-360.png)

På sidan **Cost Allocation 360°** (Kostnadsallokering 360°) väljer du **Add** (Lägg till) och ange sedan ett namn och en beskrivning för kostnadsmodellen. Välj antingen alla konton eller enskilda konton. Om du vill använda enskilda konton kan du välja flera konton från flera molntjänstproviders. Klicka sedan på **Categorization** (Kategorisering) för att välja vilka identifierade taggar som kategoriserar dina kostnadsdata. Välj de taggar (kategorier) som du vill ta med i modellen. I följande exempel visas taggen **Unit**.

![Exempel på kategorisering i kostnadsmodell](./media/tutorial-manage-costs/cost-model01.png)



Exemplet visar att 14 444 USD inte är kategoriserade (saknar taggar).

Välj sedan **Uncategorized Resources** (Okategoriserade resurser) och välj de tjänster som har kostnader som inte allokerats. Definiera sedan regler för allokering av kostnaderna.

Till exempel kanske du vill ta Azure Storage-kostnaderna och fördela dem lika mellan de virtuella Azure-datorerna. Om du vill göra det väljer du tjänsten **Azure/Storage**, **Proportional to Categorized** (Proportionellt mot kategoriserade) och sedan **Azure/VM**. Välj sedan **Create** (Skapa).

![Exempel på allokeringsregel för jämn fördelning i kostnadsmodell](./media/tutorial-manage-costs/cost-model02.png)



I ett annat exempel kanske du vill allokera alla Azure-nätverkskostnader till en specifik affärsenhet i organisationen. Om du vill göra det väljer du tjänsten **Azure/Network** och sedan **Explicit Distribution** (Explicit fördelning). Ange sedan fördelningsvärdet till 100 procent och välj affärsenheten – **G&amp;A** i följande bild:

![Exempel på allokeringsregel för en viss affärsenhet i kostnadsmodell](./media/tutorial-manage-costs/cost-model03.png)



Skapa ytterligare allokeringsregler för återstående okategoriserade resurser.

Om du har några lediga reserverade AWS-instanser (Amazon Web Services) kan du tilldela dem till taggade kategorier med **Reserved Instances** (Reserverade instanser).

Om du vill visa information om de val du gjort i allokeringen av kostnader väljer du **Summary** (Sammanfattning). Om du vill spara informationen och fortsätta arbeta med reglerna senare väljer du **Save As Draft** (Spara som utkast). Om du vill spara informationen och låta Cloudyn börja bearbeta modellen för kostnadsallokering väljer du **Save and Activate** (Spara och aktivera).

Du ser den nya kostnadsmodellen i listan med modeller, med statusen **Processing** (Bearbetas). Det kan ta lite tid innan Cloudyn-databasen uppdateras med din kostnadsmodell. När bearbetningen är klar uppdateras statusen till **Completed** (Slutförd). Du kan sedan visa data från kostnadsmodellen i rapporten Cost Analysis under **Extended Filters** (Utökade filter) &gt; **Cost Model** (Kostnadsmodell).

### <a name="category-manager"></a>Category Manager

Category Manager är ett verktyg för datarensning som hjälper dig att koppla värden från flera kategorier (taggar) för att skapa nya. Det är ett enkelt och regelbaserat verktyg där du väljer en kategori och skapar regler för att koppla befintliga värden. Du kanske till exempel har de befintliga kategorierna **R&amp;D** och **dev**, där både avser utvecklingsgruppen.

Klicka på kugghjulssymbolen uppe till höger i Cloudyn-portalen och välj **Category Manager**. Välj plustecknet (**+**) om du vill skapa en ny kategori. Ange ett namn för kategori och ange sedan de kategorinycklar du vill ta med i den nya kategorin under **Keys** (Nycklar).

När du definierar en regel kan du lägga till flera värden med ett OR-villkor. Du kan också utföra vissa grundläggande strängåtgärder. I båda fallen klickar du på symbolen med tre punkter (**...** ) till höger om **Rule** (Regel).

Om du vill definiera en ny regel skapar du den i området **Rules** (Regler). Ange till exempel **dev** under **Rules** och sedan **R&amp;D** under **Actions** (Åtgärder). När du är färdig sparar du den nya kategorin.

Följande bild visar ett exempel på regler som har skapats för en ny kategori som heter **Work-Load**:

![Exempelkategori](./media/tutorial-manage-costs/category01.png)

### <a name="tag-sources-and-reports"></a>Taggkällor och rapporter

De taggdata du ser i Cloudyn-rapporter har sitt ursprung på tre platser:

- Molnproviderns resurs-API:er
- Molnproviderns fakturerings-API:er
- Manuellt skapade taggar från följande källor:
    - Cloudyn-entitetstaggar – användardefinierade metadata som tillämpas på Cloudyn-entiteter
    - Category Manager – ett verktyg för datarensning som skapar nya taggar baserat på regler som tillämpas på befintliga taggar

Om du vill visa molnproviderns taggar i Cloudyn-kostnadsrapporter måste du skapa en anpassad kostnadsallokeringsmodell med Cost Allocation 360. Det gör du genom att gå till **Cost** > **Cost Management** > **Cost Allocation 360**, välja önskade taggar och definiera regler för hantering av otaggade kostnader. Skapa sedan en ny kostnadsmodell. Sedan kan du visa rapporter i Cost Allocation Analysis och du kan visa, filtrera och sortera efter dina Azure-resurstaggar.

Azure-resurstaggar visas bara i **Cost Allocation Analysis**-rapporter.

Molnproviderns faktureringstaggar visas i alla kostnadsrapporter.

Cloudyn-entitetstaggar och taggar du skapar manuellt visas i alla kostnadsrapporter.


## <a name="create-showback-and-chargeback-reports"></a>Skapa showback- och chargeback-rapporter

Vilka metoder som organisationer använder för showback och chargeback varierar mycket. Du kan dock använda alla instrumentpaneler och rapporter i Cloudyn-portal som grund för båda ändamålen. Du kan ge åtkomst till valfri användare i organisationen så att de kan visa instrumentpaneler och rapporter på begäran. Alla Cost Analysis-rapporter har stöd för showback, eftersom de visar användarna vilka resurser de har förbrukat. Användare kan även visa detaljerad information om kostnader och data som är specifik för deras grupp i organisationen.

Om du vill visa resultatet av en kostnadsallokering öppnar du Cost Analysis-rapporten och väljer den kostnadsmodell du skapade. Lägg sedan till en gruppering enligt en eller flera av taggarna som valts i kostnadsmodellen.

![Cost Analysis-rapport](./media/tutorial-manage-costs/cost-analysis.png)

Du kan enkelt skapa och spara rapporter som fokuserar på specifika tjänster som förbrukats av specifika grupper. Du kan till exempel ha en avdelning som använder många virtuella Azure-datorer. Du kan skapa en rapport som har filtrerats på virtuella Azure-datorer för att visa förbrukning och kostnader.

Om du behöver visa ögonblicksbilddata för andra grupper kan du exportera rapporter i PDF- eller CSV-format.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Använder anpassade taggar för allokering av kostnader.
> * Skapar showback- och chargeback-rapporter.



Gå vidare till nästa självstudie om du vill lära dig att styra åtkomsten till data.

> [!div class="nextstepaction"]
> [Styra åtkomsten till data](tutorial-user-access.md)
