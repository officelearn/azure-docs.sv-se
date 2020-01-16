---
title: Lär dig att integrera Uppdateringshantering-, Ändringsspårning-och inventerings lösningar för flera virtuella datorer i Azure Automation
description: Lär dig att publicera en virtuell Azure-dator med Uppdateringshantering, Ändringsspårning och inventerings lösningar som ingår i Azure Automation
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: 385806dca7dcac9fd0aac4c1bf9e1072e7fe5ecb
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979476"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Aktivera Uppdateringshantering-, Ändringsspårning-och inventerings lösningar på flera virtuella datorer

Azure Automation innehåller lösningar för att hantera säkerhets uppdateringar av operativ systemet, spåra ändringar och inventering som är installerade på datorerna. Det finns flera sätt att publicera datorer på, du kan publicera lösningen [från en virtuell dator](automation-onboard-solutions-from-vm.md), från ditt [Automation-konto](automation-onboard-solutions-from-automation-account.md), när du söker i virtuella datorer eller i en [Runbook](automation-onboard-solutions.md). Den här artikeln beskriver hur du registrerar de här lösningarna när du söker i virtuella datorer i Azure.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in i Azure på https://portal.azure.com

## <a name="enable-solutions"></a>Aktivera lösningar

I Azure Portal navigerar du till **virtuella datorer**.

Använd kryss rutorna och välj de virtuella datorer som du vill publicera med Ändringsspårning och lager eller Uppdateringshantering. Onboarding är tillgängligt för upp till tre olika resurs grupper i taget. Virtuella Azure-datorer kan finnas i vilken region som helst, oavsett platsen för ditt Automation-konto.

![Lista över virtuella datorer](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Använd filter kontrollerna för att ändra listan över virtuella datorer och klicka sedan på den översta kryss rutan för att markera alla virtuella datorer i listan.

I kommando fältet klickar du på **tjänster** och väljer antingen **ändrings spårning**, **lager**eller **uppdateringshantering**.

> [!NOTE]
> **Ändrings spårning** och **inventering** använder samma lösning när en är aktive rad även den andra är aktive rad.

Följande bild är för Uppdateringshantering. Ändrings spårning och inventering har samma layout och beteende.

Listan över virtuella datorer är filtrerad för att endast visa de virtuella datorer som finns i samma prenumeration och plats. Om dina virtuella datorer finns i fler än tre resurs grupper väljs de första tre resurs grupperna.

### <a name="resource-group-limit"></a>Onboarding-begränsningar

Antalet resurs grupper som du kan använda för onboarding begränsas av [distributions gränserna för Resource Manager](../azure-resource-manager/templates/cross-resource-group-deployment.md). Resource Manager-distributioner, ska inte förväxlas med uppdaterings distributioner, är begränsade till 5 resurs grupper per distribution. För att säkerställa integriteten för onboarding är 2 av dessa resurs grupper reserverade för att konfigurera Log Analytics arbets yta, Automation-konto och relaterade resurser. Detta gör att du har 3 resurs grupper att välja för distribution. Den här gränsen gäller bara för samtidiga onboarding, inte antalet resurs grupper som kan hanteras av en Automation-lösning.

Du kan också använda en Runbook för onboarding. mer information finns i [Uppdatera och ändra spårnings lösningar till Azure Automation](automation-onboard-solutions.md).

Använd filter kontrollerna för att välja virtuella datorer från olika prenumerationer, platser och resurs grupper.

![Publicera hanterings lösning för uppdateringar](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Granska alternativen för Log Analytics arbets yta och Automation-konto. En befintlig arbets yta och ett Automation-konto är markerade som standard. Om du vill använda en annan Log Analytics arbets yta och ett Automation-konto klickar du på **anpassad** och väljer dem på sidan **Anpassad konfiguration** . När du väljer en Log Analytics arbets yta görs en kontroll för att avgöra om den är länkad till ett Automation-konto. Om ett länkat Automation-konto hittas visas följande skärm. När du är färdig klickar du på **OK**.

![Välj arbets yta och konto](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Om den valda arbets ytan inte är länkad till ett Automation-konto visas följande skärm. Välj ett Automation-konto och klicka på **OK** när du är klar.

![Ingen arbets yta](media/automation-onboard-solutions-from-browse/no-workspace.png)

> [!NOTE]
> När du aktiverar lösningar går det endast att länka en Log Analytics-arbetsyta och ett Automation-konto i vissa regioner.
>
> En lista över mappnings par som stöds finns i [region mappning för Automation-konto och Log Analytics-arbetsyta](how-to/region-mappings.md).

Avmarkera kryss rutan bredvid en virtuell dator som du inte vill aktivera. Virtuella datorer som inte kan aktive ras avmarkeras redan.

Aktivera lösningen genom att klicka på **Aktivera** . Det tar upp till 15 minuter att aktivera lösningen.

## <a name="unlink-workspace"></a>Ta bort länk till arbets yta

Följande lösningar är beroende av en Log Analytics arbets yta:

* [Hantering av uppdateringar](automation-update-management.md)
* [Spårning av ändringar](automation-change-tracking.md)
* [Starta/stoppa virtuella datorer när de inte används](automation-solution-vm-management.md)

Om du inte längre vill integrera ditt Automation-konto med en Log Analytics arbets yta kan du ta bort länken till ditt konto direkt från Azure Portal. Innan du fortsätter måste du först ta bort de lösningar som nämns ovan, annars kommer den här processen att förhindras från att fortsätta. Läs artikeln för den specifika lösning som du har importerat för att förstå de steg som krävs för att ta bort den.

När du har tagit bort dessa lösningar kan du utföra följande steg för att ta bort länken till ditt Automation-konto.

> [!NOTE]
> Vissa lösningar inklusive tidigare versioner av Azure SQL-övervaknings lösningen kan ha skapat Automation-till gångar och kan också behöva tas bort innan du tar bort länken till arbets ytan.

1. Öppna ditt Automation-konto från Azure Portal och välj **länkad arbets yta** under avsnittet **relaterade resurser** till vänster på sidan Automation-konto.

2. På sidan ta bort länk till arbets yta klickar du på **ta bort arbets ytan**.

   ![Sidan ta bort länk till arbets yta](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   Ett meddelande visas där du bekräftar att du vill fortsätta.

3. Medan Azure Automation försöker ta bort länken till kontot Log Analytics arbets ytan, kan du följa förloppet under **meddelanden** på menyn.

Om du använde Uppdateringshantering-lösningen kanske du vill ta bort följande objekt som inte längre behövs efter att du har tagit bort lösningen.

* Uppdaterings scheman-varje kommer att ha namn som matchar de uppdaterings distributioner som du har skapat)

* Hybrid Worker-grupper som har skapats för lösningen – var och en får samma namn som machine1. contoso. com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Om du använde Starta/stoppa virtuella datorer när de inte används-lösningen kanske du vill ta bort följande objekt som inte längre behövs efter att du har tagit bort lösningen.

* Starta och stoppa virtuella dator Runbook-scheman
* Starta och stoppa VM-Runbooks
* Variabler

Alternativt kan du också ta bort länken till arbets ytan från ditt Automation-konto från din Log Analytics-arbetsyta. På arbets ytan väljer du **Automation-konto** under **relaterade resurser**. På sidan Automation-konto väljer du **ta bort länk till konto**.

## <a name="troubleshooting"></a>Felsöka

När flera datorer onboardas kan det finnas datorer som visas som **inte kan aktivera**. Det finns olika orsaker till att vissa datorer kanske inte är aktiverade. I följande avsnitt visas möjliga orsaker till **att det inte går att aktivera** tillstånd på en virtuell dator vid försök att publicera.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>VM-rapporter till en annan arbets yta:\<workspaceName\>.  Ändra konfigurationen så att den används för att aktivera

**Orsak**: det här felet visar att den virtuella datorn som du försöker publicera rapporter till en annan arbets yta.

**Lösning**: Klicka på **Använd som konfiguration** för att ändra det riktade Automation-kontot och Log Analytics arbets ytan.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>VM-rapporter till en arbets yta som inte är tillgänglig i den här prenumerationen

**Orsak**: arbets ytan som den virtuella datorn rapporterar till:

* Är i en annan prenumeration, eller
* Finns inte längre eller
* Finns i en resurs grupp har du inte åtkomst behörighet till

**Lösning**: hitta det Automation-konto som är kopplat till arbets ytan som den virtuella datorn rapporterar till och publicera den virtuella datorn genom att ändra omfattnings konfigurationen.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>Version eller distribution av operativ system för virtuell dator stöds inte

**Orsak:** Lösningen stöds inte för alla Linux-distributioner eller alla versioner av Windows.

**Lösning:** Se [listan över klienter som stöds](automation-update-management.md#clients) för lösningen.

### <a name="classic-vms-cannot-be-enabled"></a>Det går inte att aktivera klassiska virtuella datorer

**Orsak**: virtuella datorer som använder den klassiska distributions modellen stöds inte.

**Lösning**: migrera den virtuella datorn till distributions modellen för Resource Manager. Information om hur du gör detta finns i [migrera klassiska distributions modell resurser](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>Den virtuella datorn har stoppats. frigjord

**Orsak**: den virtuella datorn är inte i ett **körnings** tillstånd.

**Lösning**: för att kunna publicera en virtuell dator till en lösning måste den virtuella datorn köras. Starta den virtuella datorn genom att klicka på länken **starta den virtuella datorn** i inlednings fönstret utan att gå bort från sidan.

## <a name="clean-up-resources"></a>Rensa resurser

Så här tar du bort en virtuell dator från Uppdateringshantering:

* I arbets ytan Log Analytics tar du bort den virtuella datorn från den sparade sökningen för omfattnings konfigurationen `MicrosoftDefaultScopeConfig-Updates`. Sparade sökningar hittar du under **Allmänt** på arbets ytan.
* Ta bort [Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) eller [Log Analytics agent för Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Nästa steg

Nu när lösningen är aktive rad för dina virtuella datorer går du till artikeln Uppdateringshantering översikt och lär dig hur du skapar en **uppdaterings distribution** för dina datorer.

> [!div class="nextstepaction"]
> [Uppdateringshantering-hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](./automation-tutorial-update-management.md)

Ytterligare självstudier om lösningar och hur de används:

* [Självstudie – hantera uppdateringar för din virtuella dator](automation-tutorial-update-management.md)

* [Självstudie – identifiera program vara på en virtuell dator](automation-tutorial-installed-software.md)

* [Självstudie – felsöka ändringar på en virtuell dator](automation-tutorial-troubleshoot-changes.md)
