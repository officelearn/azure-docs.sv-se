---
title: Aktivera Azure Automation Uppdateringshantering från Azure Portal
description: Den här artikeln beskriver hur du aktiverar Uppdateringshantering från Azure Portal.
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: d0df98e76e8f0575f14794a53c6987e12a4d05d6
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2020
ms.locfileid: "84170706"
---
# <a name="enable-update-management-from-the-azure-portal"></a>Aktivera Uppdateringshantering i Azure-portalen

I den här artikeln beskrivs hur du kan aktivera den [uppdateringshantering](automation-update-management.md) funktionen för virtuella datorer genom att bläddra i Azure Portal. Om du vill aktivera virtuella Azure-datorer i stor skala måste du aktivera en befintlig virtuell dator med hjälp av Uppdateringshantering. 

Antalet resurs grupper som du kan använda för att hantera dina virtuella datorer begränsas av [distributions gränserna för Resource Manager](../azure-resource-manager/templates/cross-resource-group-deployment.md). Resource Manager-distributioner, ska inte förväxlas med uppdaterings distributioner, är begränsade till fem resurs grupper per distribution. Två av dessa resurs grupper är reserverade för att konfigurera Log Analytics arbets yta, Automation-konto och relaterade resurser. Detta gör att du har tre resurs grupper som du kan välja för hantering genom att Uppdateringshantering. Den här gränsen gäller endast för samtidig installation, inte antalet resurs grupper som kan hanteras av en automatiserings funktion.

> [!NOTE]
> När du aktiverar Uppdateringshantering, stöds bara vissa regioner för att länka en Log Analytics arbets yta och ett Automation-konto. En lista över mappnings par som stöds finns i [region mappning för Automation-konto och Log Analytics-arbetsyta](how-to/region-mappings.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-konto](automation-offering-get-started.md) för att hantera datorer.
* En [virtuell dator](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure på https://portal.azure.com .

## <a name="enable-update-management"></a>Aktivera uppdateringshantering

1. I Azure Portal navigerar du till **virtuella datorer**.

2. Använd kryss rutorna för att välja de virtuella datorer som ska läggas till Uppdateringshantering. Du kan lägga till datorer för upp till tre olika resurs grupper i taget. Virtuella Azure-datorer kan finnas i vilken region som helst, oavsett var ditt Automation-konto finns.

    ![Lista över virtuella datorer](media/automation-onboard-solutions-from-browse/vmlist.png)

    > [!TIP]
    > Använd filter kontrollerna för att välja virtuella datorer från olika prenumerationer, platser och resurs grupper. Du kan klicka på den översta kryss rutan för att välja alla virtuella datorer i en lista.

    ![Aktivera uppdateringshantering](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

3. Klicka på **tjänster** och välj **Uppdateringshantering** för funktionen uppdateringshantering. 

4. Listan över virtuella datorer är filtrerad för att endast visa de virtuella datorer som finns i samma prenumeration och plats. Om dina virtuella datorer finns i fler än tre resurs grupper väljs de första tre resurs grupperna.

5. En befintlig Log Analytics arbets yta och ett Automation-konto väljs som standard. Om du vill använda en annan Log Analytics arbets yta och ett Automation-konto klickar du på **anpassad** och väljer dem på sidan anpassad konfiguration. När du väljer en Log Analytics arbets yta görs en kontroll för att avgöra om den är länkad till ett Automation-konto. Om ett länkat Automation-konto hittas visas följande skärm. När du är färdig klickar du på **OK**.

    ![Välj arbets yta och konto](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

6. Om den valda arbets ytan inte är länkad till ett Automation-konto visas följande skärm. Välj ett Automation-konto och klicka på **OK** när du är färdig.

    ![Ingen arbets yta](media/automation-onboard-solutions-from-browse/no-workspace.png)

7. Avmarkera kryss rutan bredvid en virtuell dator som du inte vill aktivera. Virtuella datorer som inte kan aktive ras avmarkeras redan.

8. Klicka på **Aktivera** för att aktivera funktionen som du har valt. Det tar upp till 15 minuter att slutföra installationen.

## <a name="next-steps"></a>Nästa steg

* Om du vill använda Uppdateringshantering för virtuella datorer läser du [Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](automation-tutorial-update-management.md).
* Information om hur du felsöker allmänna Uppdateringshantering fel finns i [felsöka uppdateringshantering problem](troubleshoot/update-management.md).
* Information om hur du felsöker problem med Windows Update-agenten finns i [Felsöka problem med Windows Update-agenten](troubleshoot/update-agent-issues.md).
* Information om hur du felsöker problem med Linux Update-agenten finns i[Felsöka problem med Linux Update Agent](troubleshoot/update-agent-issues-linux.md).
