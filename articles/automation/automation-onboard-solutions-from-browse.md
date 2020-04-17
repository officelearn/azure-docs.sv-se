---
title: Lär dig hur du rapporterar uppdateringshantering, ändringsspårning och lagerlösningar för flera virtuella datorer i Azure Automation
description: Lär dig hur du ar ombord på en virtuell Azure-dator med lösningar för uppdateringshantering, ändringsspårning och lager som ingår i Azure Automation
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: f5f18e9365b09f06c1bd4f25a8efe909cc308dad
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537023"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Aktivera lösningar för uppdateringshantering, ändringsspårning och lager på flera virtuella datorer

Azure Automation tillhandahåller lösningar för att hantera säkerhetsuppdateringar för operativsystemet, spåra ändringar och inventera vad som är installerat på dina datorer. Det finns flera sätt att gå ombord på datorer, du kan gå in på lösningen [från en virtuell dator,](automation-onboard-solutions-from-vm.md)från ditt [Automation-konto,](automation-onboard-solutions-from-automation-account.md)när du surfar på virtuella datorer eller genom [runbook](automation-onboard-solutions.md). Den här artikeln beskriver introduktion av dessa lösningar när du surfar på virtuella datorer i Azure.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in i Azure på https://portal.azure.com

## <a name="enable-solutions"></a>Aktivera lösningar

Navigera till virtuella datorer i **Azure-portalen**.

Med hjälp av kryssrutorna markerar du de virtuella datorer som du vill gå ombord på med ändringsspårning och lager- eller uppdateringshantering. Introduktion är tillgängligt för upp till tre olika resursgrupper åt gången. Virtuella Azure-datorer kan finnas i valfri region oavsett var ditt Automation-konto finns.

![Lista över virtuella datorer](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Använd filterkontrollerna för att ändra listan över virtuella datorer och klicka sedan på den övre kryssrutan för att markera alla virtuella datorer i listan.

Klicka på **Tjänster** i kommandofältet och välj antingen **Ändra spårning,** **Lager**eller **Uppdateringshantering**.

> [!NOTE]
> Ändringsspårning och lager använder samma lösning. När en är aktiverad är den andra också aktiverad.

Följande bild är för Uppdateringshantering. Ändringsspårning och lager har samma layout och beteende.

Listan över virtuella datorer filtreras så att endast de virtuella datorer som finns i samma prenumeration och plats visas. Om dina virtuella datorer finns i fler än tre resursgrupper väljs de tre första resursgrupperna.

### <a name="onboarding-limitations"></a><a name="resource-group-limit"></a>Begränsningar för introduktionsdepåbord

Antalet resursgrupper som du kan använda för introduktion begränsas av [resurshanterarens distributionsgränser](../azure-resource-manager/templates/cross-resource-group-deployment.md). Resource Manager-distributioner, som inte ska förväxlas med uppdateringsdistributioner, är begränsade till 5 resursgrupper per distribution. För att säkerställa integriteten för introduktion är två av dessa resursgrupper reserverade för att konfigurera log analytics-arbetsytan, automationskontot och relaterade resurser. Detta ger dig 3 resursgrupper att välja för distribution. Den här gränsen gäller endast samtidig introduktion, inte antalet resursgrupper som kan hanteras av en Automation-lösning.

Du kan också använda en runbook för introduktion, mer information, se [Lösningar för uppdatering och ändringsspårning på Azure Automation](automation-onboard-solutions.md).

Använd filterkontrollerna för att välja virtuella datorer från olika prenumerationer, platser och resursgrupper.

![Hanteringslösning för inbyggd uppdatering](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Läs alternativen för log analytics-arbetsytan och automationskontot. En befintlig arbetsyta och ett automatiseringskonto väljs som standard. Om du vill använda en annan log analytics-arbetsyta och ett annat automationskonto klickar du på **ANPASSAD** för att välja dem på sidan **Anpassad konfiguration.** När du väljer en Log Analytics-arbetsyta görs en kontroll för att avgöra om den är länkad till ett Automation-konto. Om ett länkat Automation-konto hittas visas följande skärm. När du är klar klickar du på **OK**.

![Välj arbetsyta och konto](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Om den valda arbetsytan inte är länkad till ett Automation-konto visas följande skärm. Välj ett automationskonto och klicka på **OK** när du är klar.

![Ingen arbetsyta](media/automation-onboard-solutions-from-browse/no-workspace.png)

> [!NOTE]
> När du aktiverar lösningar går det endast att länka en Log Analytics-arbetsyta och ett Automation-konto i vissa regioner.
>
> En lista över mappningspar som stöds finns i [Regionmappning för Automation-konto och Log Analytics-arbetsyta](how-to/region-mappings.md).

Avmarkera kryssrutan bredvid en virtuell dator som du inte vill aktivera. Virtuella datorer som inte kan aktiveras är redan avmarkerade.

Klicka på **Aktivera** om du vill aktivera lösningen. Det tar upp till 15 minuter att aktivera lösningen.

## <a name="unlink-workspace"></a>Ta bort arbetsytans länk

Följande lösningar är beroende av en Log Analytics-arbetsyta:

* [Uppdateringshantering](automation-update-management.md)
* [Spårning av ändringar](automation-change-tracking.md)
* [Start/stoppa virtuella datorer under ledig tid](automation-solution-vm-management.md)

Om du bestämmer dig för att du inte längre vill integrera ditt Automation-konto med en Log Analytics-arbetsyta kan du ta bort länken till ditt konto direkt från Azure-portalen. Innan du fortsätter måste du först ta bort de lösningar som nämndes tidigare, annars förhindras den här processen från att fortsätta. Läs artikeln för den specifika lösning som du har importerat för att förstå de steg som krävs för att ta bort den.

När du har tagit bort dessa lösningar kan du utföra följande steg för att ta bort länken till ditt Automation-konto.

> [!NOTE]
> Vissa lösningar, inklusive tidigare versioner av Azure SQL-övervakningslösningen, kan ha skapat automatiseringsresurser och kan också behöva tas bort innan arbetsytan tas bort.

1. Öppna ditt Automation-konto på sidan Automation-konto på sidan Automation-konto **under** avsnittet **Relaterade resurser** till vänster.

2. Klicka på **Ta bort länk arbetsyta**på sidan Ta bort länk.

   ![Ta bort länkning av arbetsyta](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   Ett meddelande visas där du bekräftar att du vill fortsätta.

3. Medan Azure Automation försöker ta bort länken till kontot din Log Analytics-arbetsyta kan du spåra förloppet under **Meddelanden** från menyn.

Om du använde lösningen Update Management kanske du vill ta bort följande objekt som inte längre behövs när du har tagit bort lösningen.

* Uppdateringsscheman - Var och en kommer att ha namn som matchar de uppdateringsdistributioner du skapade)

* Hybridarbetaregrupper som skapats för lösningen - Var och en namnges på samma sätt som machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Om du använde start-/stopp-virtuella datorer under ledig tid kanske du vill ta bort följande objekt som inte längre behövs när du har tagit bort lösningen.

* Starta och stoppa VM-runbookscheman
* Starta och stoppa VM-runbooks
* Variabler

Du kan också ta bort länken till arbetsytan från ditt Automation-konto från logganalysarbetsytan. På arbetsytan väljer du **Automation-konto** under **Relaterade resurser**. På sidan Automation-konto väljer du **Ta bort länkkonto**.

## <a name="troubleshooting"></a>Felsökning

När du ar in flera maskiner kan `Cannot enable`det finnas maskiner som visas som . Det finns olika orsaker till att vissa datorer kanske inte är aktiverade. Följande avsnitt visar möjliga orsaker `Cannot enable` till tillståndet på en virtuell dator när du försöker gå ombord.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>VM rapporterar till en\<annan arbetsyta: ' workspaceName\>'.  Ändra konfiguration för att använda den för att aktivera

**Orsak**: Det här felet visar att den virtuella datorn som du försöker gå in på rapporterar till en annan arbetsyta.

**Lösning**: Klicka på **Använd som konfiguration** för att ändra det riktade Automation-kontot och Log Analytics-arbetsytan.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>VM-rapporter till en arbetsyta som inte är tillgänglig i den här prenumerationen

**Orsak**: Arbetsytan som den virtuella datorn rapporterar till:

* Finns i en annan prenumeration, eller
* Inte längre existerar, eller
* Finns i en resursgrupp som du inte har åtkomstbehörighet till

**Lösning:** Hitta det automatiseringskonto som är associerat med arbetsytan som den virtuella datorn rapporterar till och ombord på den virtuella datorn genom att ändra scopekonfigurationen.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>Vm-operativsystemversion eller -distribution stöds inte

**Orsak:** Lösningen stöds inte för alla Linux-distributioner eller alla versioner av Windows.

**Lösning:** Se [listan över klienter som stöds](automation-update-management.md#clients) för lösningen.

### <a name="classic-vms-cannot-be-enabled"></a>Klassiska virtuella datorer kan inte aktiveras

**Orsak**: Virtuella datorer som använder den klassiska distributionsmodellen stöds inte.

**Lösning**: Migrera den virtuella datorn till resurshanterarens distributionsmodell. Mer information om hur du gör detta finns i [Migrera klassiska distributionsmodellresurser](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>Den virtuella datorn har stoppats. (deallocated)

**Orsak**: Den virtuella datorn **Running** i inte i körtillstånd.

**Lösning:** För att kunna gå ombord på en virtuell dator till en lösning måste den virtuella datorn köras. Klicka på länken **Start VM-infogad** linje om du vill starta den virtuella datorn utan att navigera bort från sidan.

## <a name="clean-up-resources"></a>Rensa resurser

Så här tar du bort en virtuell dator från uppdateringshantering:

* I logganalysarbetsytan tar du bort den virtuella datorn `MicrosoftDefaultScopeConfig-Updates`från den sparade sökningen efter scopekonfigurationen . Sparade sökningar hittar du under **Allmänt** på arbetsytan.
* Ta bort [Log Analytics-agenten för Windows](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) eller Log [Analytics-agenten för Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Nästa steg

Nu när lösningen är aktiverad för dina virtuella datorer kan du gå till översiktsartikeln Update Management om du vill veta hur du skapar en **uppdateringsdistribution** för dina datorer.

> [!div class="nextstepaction"]
> [Uppdateringshantering – Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](./automation-tutorial-update-management.md)

Tillägg tutorials på lösningar och hur man använder dem:

* [Självstudiekurs - Hantera uppdateringar för din virtuella dator](automation-tutorial-update-management.md)

* [Självstudiekurs - Identifiera programvara på en virtuell dator](automation-tutorial-installed-software.md)

* [Självstudiekurs - Felsöka ändringar på en virtuell dator](automation-tutorial-troubleshoot-changes.md)
