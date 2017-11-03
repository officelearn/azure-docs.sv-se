---
title: "Visa de månatliga kostnadstrend beräknade labbkostnaden i Azure DevTest Labs | Microsoft Docs"
description: "Läs mer om Azure DevTest Labs månatliga uppskattade kostnaden trend diagrammet."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
ms.openlocfilehash: b3ad1ead522908d4b41b7cca98d20ac91664998e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>Visa de månatliga kostnadstrend beräknade labbkostnaden i Azure DevTest Labs
Funktionen hantering av kostnaden för DevTest Labs hjälper dig att spåra kostnaden för ditt labb. Den här artikeln beskrivs hur du använder den **månatlig uppskattad Kostnadstrend** diagram om du vill visa den aktuella kalendermånaden uppskattade kostnaden-till-date och planerade sista månad kostnaden för den aktuella kalendermånaden. I den här artikeln får lära du att visa diagrammet trend månatliga uppskattade kostnaden i Azure-portalen.

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>Visa diagrammet månatlig uppskattad Kostnadstrend
Följ dessa steg om du vill visa månatlig uppskattad Kostnadstrend diagrammet: 

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **fler tjänster**, och välj sedan **DevTest Labs** från listan.
3. Lista över labs, Välj önskade labbet.   
4. På den testmiljön bladet välj **kostnad inställningar**.
5. På testmiljön **kostnad inställningar** bladet väljer **Lab kostnadstrend**.
6. Följande skärmbild visar ett exempel på ett diagram med kostnaden. 
   
    ![Kostnad diagram](./media/devtest-lab-configure-cost-management/graph.png)

Den **uppskattade kostnaden** värdet är den aktuella kalendermånaden uppskattade kostnaden hittills. Den **planerad kostnad** är uppskattade kostnaden för hela aktuell kalendermånad, beräknas med hjälp av lab kostnaden för föregående fem dagar.

Kostnadsbelopp som avrundat till närmaste heltal. Exempel: 

* 5.01 Avrundar upp till 6 
* 5.50 Avrundar upp till 6
* 5.99 Avrundar upp till 6

Eftersom den anger ovanför diagrammet kostnaderna som du ser i diagrammet är *uppskattade* kostnader med [betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/) erbjuda priser.
Dessutom kan följande är *inte* med i beräkningen:

* CSP och Dreamspark-prenumerationer stöds inte för närvarande eftersom Azure DevTest Labs använder den [Azure fakturering API: er](../billing/billing-usage-rate-card-overview.md) att beräkna labbet kostnad, som inte stöder CSP eller Dreamspark-prenumerationer.
* Allteftersom-taxan. Vi är för närvarande inte kan använda din allteftersom-taxan (visas i din prenumeration) att du har förhandlats med Microsoft eller Microsoft partner. Vi använder betala per användning priser.
* Din skatter
* Din rabatt
* Valuta. För närvarande visas lab kostnaden bara i USD valuta.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Relaterade blogginlägg
* [Två saker att hålla dina kostnader på Spåra i DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [Varför kostnad tröskelvärden?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>Nästa steg
Här följer några saker att försöka:

* [Definiera principer för labbet](devtest-lab-set-lab-policy.md) – Lär dig att ange olika principer som används för att styra hur ditt labb och dess virtuella datorer används. 
* [Skapa den anpassade bilden](devtest-lab-create-template.md) – när du skapar en virtuell dator, anger du en bas som kan vara antingen en anpassad avbildning eller en Marketplace-avbildning. Den här artikeln beskrivs hur du skapar en anpassad avbildning från en VHD-fil.
* [Konfigurera Marketplace-bilder](devtest-lab-configure-marketplace-images.md) - DevTest Labs stöder skapandet av virtuella datorer baserat på Azure Marketplace-bilder. Den här artikeln visar hur du kan ange vilka eventuella Azure Marketplace-bilder kan användas när du skapar virtuella datorer i ett labb.
* [Skapa en virtuell dator i ett labb](devtest-lab-add-vm-with-artifacts.md) -illustrerar hur du skapar en virtuell dator från en grundläggande bild (antingen anpassad eller Marketplace), och hur du arbetar med artefakter i den virtuella datorn.

