---
title: Lär dig hur du publicera lösningar för uppdateringshantering, ändringsspårning och inventering för flera virtuella datorer i Azure Automation
description: Lär dig hur att publicera en Azure-virtuella datorn med lösningar för uppdateringshantering, ändringsspårning och inventering som ingår i Azure Automation
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/11/2019
ms.topic: article
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: df59342bebae3ac0f6e80e5b58f429fedf3c3336
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578500"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Aktivera uppdateringshantering, ändringsspårning och inventering lösningar på flera virtuella datorer

Azure Automation tillhandahåller lösningar för att hantera säkerhet för operativsystemuppdateringar, spåra ändringar och inventering vad som är installerat på datorerna. Det finns flera sätt att registrera datorer, du kan registrera lösningen [från en virtuell dator](automation-onboard-solutions-from-vm.md), från din [automatiseringskontot](automation-onboard-solutions-from-automation-account.md), när du bläddrar efter virtuella datorer eller genom att [runbook](automation-onboard-solutions.md). Den här artikeln beskrivs registrering dessa lösningar när du bläddrar efter virtuella datorer i Azure.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in till Azure på https://portal.azure.com

## <a name="enable-solutions"></a>Aktivera lösningar

I Azure-portalen går du till **virtuella datorer**.

Använd kryssrutorna och markera de virtuella datorerna som du vill publicera med ändringsspårning och inventering eller hantering av uppdateringar. Onboarding-processen är tillgängliga för upp till tre olika resursgrupper i taget. Virtuella Azure-datorer kan finnas i valfri region oavsett platsen för ditt Automation-konto.

![Lista över virtuella datorer](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Använd filtret kontrollerna för att ändra listan över virtuella datorer och klicka sedan på den översta kryssrutan för att välja alla virtuella datorer i listan.

I kommandofältet klickar du på **Services** och välj antingen **ändringsspårning**, **inventering**, eller **uppdateringshantering**.

> [!NOTE]
> **Ändringsspårning** och **inventering** använder samma lösning, när ett är aktiverat för den andra är aktiverat.

Följande bild är för uppdateringshantering. Ändringsspårning och inventering har samma layout och beteende.

Listan över virtuella datorer är filtrerad och visar endast virtuella datorer i samma prenumeration och plats. Om dina virtuella datorer finns i fler än tre resursgrupper, markeras de första tre resursgrupperna.

### <a name="resource-group-limit"></a> Onboarding-begränsningar

Antalet resursgrupper som du kan använda för registrering är begränsat av den [Resource Manager distribution gränser](../azure-resource-manager/resource-manager-cross-resource-group-deployment.md). Resource Manager-distributioner, ska inte förväxlas med distributioner, är begränsad till 5 resursgrupper per distribution. 2 av befintliga resursgrupper är reserverade för att konfigurera Log Analytics-arbetsyta, Automation-konto och relaterade resurser för att säkerställa integriteten för onboarding. Det innebär att du med 3 resursgrupper för att välja för distribution.

Använd filterkontrollerna för att välja virtuella datorer från olika prenumerationer, platser och resursgrupper.

![Publicera lösning för uppdateringshantering](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Granska valen för Log Analytics-arbetsytan och Automation-kontot. En befintlig arbetsyta och Automation-kontot är markerade som standard. Om du vill använda en annan Log Analytics-arbetsytan och Automation-kontot klickar du på **anpassade** att välja dem från den **anpassad konfiguration** sidan. När du väljer en Log Analytics-arbetsyta, görs en kontroll för att avgöra om den är kopplad till ett Automation-konto. Om ett länkat Automation-konto hittas visas följande skärmbild. När du är klar klickar du på **OK**.

![Välj en arbetsyta och konto](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Om arbetsytan som valts inte är länkad till ett Automation-konto, visas följande skärmbild. Välj ett Automation-konto och klicka på **OK** när du är klar.

![Ingen arbetsyta](media/automation-onboard-solutions-from-browse/no-workspace.png)

När du aktiverar lösningar stöds endast i vissa regioner för att länka en Log Analytics-arbetsyta och ett Automation-konto.

I följande tabell visas mappningarna som stöds:

|**Log Analytics arbetsytans Region**|**Azure Automation Region**|
|---|---|
|Sydöstra Australien|Sydöstra Australien|
|CanadaCentral|CanadaCentral|
|Indiencentrala|Indiencentrala|
|EastUS<sup>1</sup>|EastUS2|
|JapanEast|JapanEast|
|SoutheastAsia|SoutheastAsia|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|Västeuropa|Västeuropa|
|Södrastorbritannien|Södrastorbritannien|
|USGovVirginia|USGovVirginia|
|EastUS2EUAP<sup>1</sup>|CentralUSEUAP|

<sup>1</sup> EastUS2EUAP och östra USA mappningar för Log Analytics-arbetsytor till Automation-konton är inte en exakt mappning för olika regioner, men är korrekt mappning.

<sup>2</sup> på grund av begränsningar i kapaciteten regionen är inte tillgänglig när du skapar nya resurser. Detta inkluderar Automation-konton och Log Analytics-arbetsytor. Redan befintliga länkade resurser i regionen bör dock fortsätta att fungera.

Avmarkera kryssrutan bredvid en virtuell dator som du inte vill aktivera. Virtuella datorer som inte kan aktiveras är redan avmarkerat.

Klicka på **aktivera** att aktivera lösningen. Det tar upp till 15 minuter att aktivera lösningen.

## <a name="unlink-workspace"></a>Ta bort arbetsytans länk

Följande lösningar är beroende av en Log Analytics-arbetsyta:

* [Hantering av uppdateringar](automation-update-management.md)
* [Spårning av ändringar](automation-change-tracking.md)
* [Starta/Stoppa VM under kontorstid](automation-solution-vm-management.md)

Om du inte längre vill integrera ditt Automation-konto med en arbetsyta för Log Analytics kan du kan ta bort länken till ditt konto direkt från Azure-portalen. Innan du fortsätter måste du först ta bort lösningar som tidigare nämnts, annars den här processen kommer inte att kunna fortsätta. Läsa artikeln för den lösning du har importerat för att förstå de steg som krävs för att ta bort den.

När du tar bort dessa lösningar kan utföra du följande steg om du vill ta bort länken till ditt Automation-konto.

> [!NOTE]
> Vissa lösningar, inklusive tidigare versioner av Azure SQL-övervakningslösning kan ha skapat automation-tillgångar och kan också behöva tas bort innan du arbetsytans länk.

1. Öppna ditt Automation-konto från Azure-portalen och på Automation-konto väljer du sidan **länkade arbetsytan** under avsnittet **relaterade resurser** till vänster.

2. På sidan Avlänka från arbetsytan **ta bort arbetsytans länk**.

   ![Avlänka arbetsytssidan](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   Ett meddelande visas där du bekräftar att du vill fortsätta.

3. Medan Azure Automation försöker ta bort länken till konton som Log Analytics-arbetsytan, kan du följa förloppet under **meddelanden** på menyn.

Om du använder lösningen för uppdateringshantering, kan om du vill du ta bort följande objekt som inte längre behövs när du har tagit bort lösningen.

* Uppdatera schemalägger, var och en har namn som matchar de uppdateringsdistributioner du skapat)

* Hybrid worker-grupper som skapats för lösningen – var och en namnges på samma sätt till av typen machine1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Om du har använt Starta/stoppa virtuella datorer vid låg belastning på nätverket lösning kan om du vill du ta bort följande objekt som inte längre behövs när du har tagit bort lösningen.

* Starta och stoppa scheman för VM-runbook
* Starta och stoppa Virtuella runbooks
* Variabler

## <a name="troubleshooting"></a>Felsökning

När onboarding flera datorer och det kan vara datorer som visas som **kan inte aktivera**. Det finns olika orsaker varför vissa datorer inte kanske är aktiverad. I följande avsnitt visas möjliga orsaker till det **kan inte aktivera** tillstånd på en virtuell dator när du försöker publicera.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>Virtuella datorer som rapporterar till en annan arbetsyta: '\<workspaceName\>”.  Ändra konfiguration som ska användas för att aktivera

**Orsak**: Det här felet visas som den virtuella datorn som du försöker att publicera rapporter till en annan arbetsyta.

**Lösningen**: Klicka på **som konfiguration** att ändra den aktuella Automation-kontot och Log Analytics-arbetsytan.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>VM-rapporter till en arbetsyta som inte är tillgänglig i den här prenumerationen

**Orsak**: Den arbetsyta som den virtuella datorn rapporterar till:

* Är i en annan prenumeration eller
* Finns inte längre, eller
* Är i en resursgrupp som du inte har åtkomstbehörighet till

**Lösningen**: Hitta automation-konto associerat med den arbetsyta som den virtuella datorn rapporterar till och publicera den virtuella datorn genom att ändra omfattningskonfigurationen.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>Operativsystemversionen för virtuell dator eller distribution stöds inte

**Orsak:** Lösningen stöds inte för alla Linux-distributioner eller alla versioner av Windows.

**Lösning:** Referera till den [lista över klienter som stöds](automation-update-management.md#clients) för lösningen.

### <a name="classic-vms-cannot-be-enabled"></a>Det går inte att aktivera klassiska virtuella datorer

**Orsak**: Virtuella datorer som använder den klassiska distributionsmodellen stöds inte.

**Lösningen**: Migrera den virtuella datorn till resource manager-distributionsmodellen. Information om hur du gör detta finns i [migrera klassiska distributionsresurserna för modellen](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>Virtuell dator har stoppats. (frigjord)

**Orsak**: Den virtuella datorn i inte i en **kör** tillstånd.

**Lösningen**: För att måste publicera en virtuell dator till en lösning för den virtuella datorn köras. Klicka på den **starta VM** infogad länk för att starta den virtuella datorn utan att navigera bort från sidan.

## <a name="next-steps"></a>Nästa steg

Nu när lösningen har aktiverats för dina virtuella datorer, besök uppdateringshantering översiktsartikeln om du vill veta hur du visar kontroll av uppdateringar för dina datorer.

> [!div class="nextstepaction"]
> [Uppdatera hantering – Visa kontroll av uppdateringar](./automation-update-management.md#viewing-update-assessments)

Ytterligare självstudier om lösningarna och hur du använder dem:

* [Självstudie – hantera uppdateringar för din virtuella dator](automation-tutorial-update-management.md)

* [Självstudie – identifiera programvara på en virtuell dator](automation-tutorial-installed-software.md)

* [Självstudie – Felsök ändringar på en virtuell dator](automation-tutorial-troubleshoot-changes.md)
