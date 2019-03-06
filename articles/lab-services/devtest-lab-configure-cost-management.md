---
title: Visa månatliga uppskattade labbkostnadstrend i Azure DevTest Labs | Microsoft Docs
description: Läs mer om Azure DevTest Labs-trenddiagram för månatliga uppskattade kostnaden.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: d3a02a850059dc96e4e4ba306fed7cded2f7dd7d
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454195"
---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>Visa månatliga uppskattade labbkostnadstrend i Azure DevTest Labs
Funktionen kostnadshantering i DevTest Labs kan du spåra kostnaden för ditt labb. Den här artikeln beskrivs hur du använder den **månatliga uppskattade kostnaden Trend** diagram om du vill visa den aktuella kalendermånaden uppskattad kostnad hittills och kostnadsprognoser i slutet av månaden för den aktuella kalendermånaden. Den här artikeln visar också hur du hanterar bättre labbkostnader genom att ange utgiftsgränsen mål och tröskelvärden som, när uppnåtts utlösaren DevTest Labs och rapporterar resultaten till dig.

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>Visa diagrammet månatliga uppskattade kostnaden Trend
Följ dessa steg om du vill visa månatliga uppskattade kostnaden Trend diagrammet: 

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Om det behövs väljer **alla tjänster**, och välj sedan **DevTest Labs** i listan. (Labbet kanske redan visas på instrumentpanelen under **alla resurser**).
1. Listan över labbar, Välj önskade labbet.  
1. På testmiljön **översikt** Välj **konfiguration och principer**.   
1. Till vänster under **KOSTNADSUPPFÖLJNING**väljer **kostnaden trend**.

   Följande skärmbild visar ett exempel på ett diagram med kostnaden. 
   
    ![Kostnad diagram](./media/devtest-lab-configure-cost-management/graph.png)

Den **uppskattad kostnad** värdet är den aktuella kalendermånaden uppskattad kostnad hittills. Den **beräknad kostnad** är den uppskattade kostnaden för den hela aktuella kalendermånaden, beräknas med hjälp av lab kostnaden för de senaste fem dagarna.

Kostnadsbelopp avrundas uppåt till närmaste heltal. Exempel: 

* 5.01 Avrundar upp till 6 
* 5.50 Avrundar upp till 6
* 5.99 Avrundar upp till 6

Kostnaderna som visas som standard i diagrammet är som det säger ovanför diagrammet *uppskattade* kostnader med hjälp av [användningsbaserad](https://azure.microsoft.com/offers/ms-azr-0003p/) erbjuder priser. Du kan också ange egna utgifter mål som visas i diagrammen av [hantera kostnaden mål för övningen.](#managing-cost-targets-for-your-lab)

Dessutom kan följande är *inte* ingår i beräkningen:

* CSP och Dreamspark-prenumerationer stöds inte för tillfället eftersom Azure DevTest Labs använder den [Azure fakturering API: er](../billing/billing-usage-rate-card-overview.md) att beräkna labbet kostnad, som inte stöder CSP eller Dreamspark-prenumerationer.
* Priserna för ditt erbjudande. För närvarande kan använda du inte erbjudandet priserna (visas i din prenumeration) att du har förhandlas med Microsoft eller Microsoft partner. Endast för användningsbaserad betalning används.
* Din skatter
* Slevy
* Faktureringsvalutan. För närvarande visas lab kostnaden endast i USD valuta.

## <a name="managing-cost-targets-for-your-lab"></a>Hantera kostnaden mål för övningen
DevTest Labs kan du bättre hantera kostnaderna i labbet genom att ange en utgiftsgräns mål som du kan visa i diagrammet månatliga uppskattade kostnaden Trend. DevTest Labs kan också skicka ett meddelande när den angivna målsökvägen utgifter eller tröskelvärdet har uppnåtts. 

1. På ditt labb **översikt** väljer **konfiguration och principer**.
1. Till vänster under **KOSTNADSUPPFÖLJNING**väljer **kostnaden trend**.

    ![Hantera målknappen](./media/devtest-lab-configure-cost-management/cost-trend.png)

1. I den **kostnaden trend** väljer **hantera target**.

    ![Hantera målknappen](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)

1. Ange önskat utgiftsgränsen mål och tröskelvärden i fönstret hantera mål. Du kan också ange om varje valda tröskelvärde rapporteras på trenddiagram kostnaden eller via ett webhook-meddelande.

    ![Hantera mål-fönstret](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Välj en tidsperiod under vilken du vill att kostnaden mål som spåras.
      - **Månatliga**: kostnaden mål spåras per månad.
      - **Fast**: kostnaden mål spåras för datumintervall som du anger i Start- och slut-datumfält. Detta kan vanligtvis motsvarar hur länge ditt projekt är schemalagd att köras.
   - Ange en **rikta kostnaden**. Det kanske till exempel hur mycket du planerar att spendera på den här övningen under den tidsperioden som du har definierat.
   - Välj om du vill aktivera eller inaktivera eventuella tröskelvärde du vill rapporteras – i steg om 25% – upp till 125% av dina angivna **rikta kostnaden**.
      - **Meddela**: När det här tröskelvärdet har uppnåtts, visas ett meddelande med en webhook-URL som du anger.
      - **Vykreslit v grafu**: När den här tröskeln uppfylls resultatet ritas på kostnaden trend diagrammet som du kan visa, enligt beskrivningen i [visa diagrammet månatliga uppskattade kostnaden Trend](#viewing-the-monthly-estimated-cost-trend-chart).
   - Om du väljer att **meddela** när tröskelvärdet är uppfyllt, måste du ange en webhook-URL. I området kostnaden integreringar väljer **Klicka här för att lägga till en integration**.

      Ange en Webhook-URL i meddelandefönstret konfigurera och välj sedan **OK**.

       ![Konfigurera meddelandefönstret](./media/devtest-lab-configure-cost-management/configure-notification.png)

      - Om du anger **meddela**, måste du definiera en webhook-URL.
      - På samma sätt, om du definierar en webhook-URL måste du ange **meddelande** till **på** i fönstret kostnaden tröskelvärdet.
      - Du måste skapa en webhook innan den här.  

      Läs mer om webhooks [skapa en webhook eller API Azure-funktion](../azure-functions/functions-create-a-web-hook-or-api-function.md). 
 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Relaterade blogginlägg
* [Två saker att hålla dina kostnader på i DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [Varför Kostnadströsklar?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>Nästa steg
Här följer några saker du kan prova sedan:

* [Definiera labbprinciper](devtest-lab-set-lab-policy.md) – Lär dig att ange olika principer används för att styra hur ditt labb och dess virtuella datorer används. 
* [Skapa en anpassad avbildning](devtest-lab-create-template.md) – när du skapar en virtuell dator kan du ange en bastabell som kan vara en anpassad avbildning eller en Marketplace-avbildning. Den här artikeln visar hur du skapar en anpassad avbildning från en VHD-fil.
* [Konfigurera Marketplace-avbildningar](devtest-lab-configure-marketplace-images.md) – DevTest Labs stöder skapandet av virtuella datorer baserat på Azure Marketplace-avbildningar. Den här artikeln visar hur du kan ange vilka eventuella Azure Marketplace-avbildningar kan vara används när du skapar virtuella datorer i ett labb.
* [Skapa en virtuell dator i ett labb](devtest-lab-add-vm.md) -illustrerar hur du skapar en virtuell dator från en grundläggande avbildning (antingen anpassad eller Marketplace), och hur du arbetar med artefakter i den virtuella datorn.

