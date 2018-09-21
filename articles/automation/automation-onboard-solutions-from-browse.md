---
title: Lär dig hur du publicera lösningar för uppdateringshantering, ändringsspårning och inventering för flera virtuella datorer i Azure Automation
description: Lär dig hur att publicera en Azure-virtuella datorn med lösningar för uppdateringshantering, ändringsspårning och inventering som ingår i Azure Automation
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 83a768b82172b8736ea06bfed012309ac92734b7
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2018
ms.locfileid: "46497968"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Aktivera uppdateringshantering, ändringsspårning och inventering lösningar på flera virtuella datorer

Azure Automation tillhandahåller lösningar för att hantera säkerhet för operativsystemuppdateringar, spåra ändringar och inventering vad som är installerat på datorerna. Det finns flera sätt att registrera datorer, du kan registrera lösningen [från en virtuell dator](automation-onboard-solutions-from-vm.md), från din [automatiseringskontot](automation-onboard-solutions-from-automation-account.md), när du bläddrar efter virtuella datorer eller genom att [runbook](automation-onboard-solutions.md). Den här artikeln beskrivs registrering dessa lösningar när du bläddrar efter virtuella datorer i Azure.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in till Azure på https://portal.azure.com

## <a name="enable-solutions"></a>Aktivera lösningar

I Azure-portalen går du till **virtuella datorer**.

Använd kryssrutorna och markera de virtuella datorerna som du vill publicera med ändringsspårning och inventering eller hantering av uppdateringar. Onboarding-processen är tillgängliga för upp till tre olika resursgrupper i taget.

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

Granska valen för Log analytics-arbetsytan och Automation-kontot. En befintlig arbetsyta och Automation-kontot är markerade som standard. Om du vill använda en annan Log Analytics-arbetsytan och Automation-kontot klickar du på **anpassade** att välja dem från den **anpassad konfiguration** sidan. När du väljer en Log Analytics-arbetsyta, görs en kontroll för att avgöra om den är kopplad till ett Automation-konto. Om ett länkat Automation-konto hittas visas följande skärmbild. När du är klar klickar du på **OK**.

![Välj en arbetsyta och konto](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Om arbetsytan som valts inte är länkad till ett Automation-konto, visas följande skärmbild. Välj ett Automation-konto och klicka på **OK** när du är klar.

![Ingen arbetsyta](media/automation-onboard-solutions-from-browse/no-workspace.png)

Avmarkera kryssrutan bredvid en virtuell dator som du inte vill aktivera. Virtuella datorer som inte kan aktiveras är redan avmarkerat.

Klicka på **aktivera** att aktivera lösningen. Det tar upp till 15 minuter att aktivera lösningen.

## <a name="troubleshooting"></a>Felsökning

När onboarding flera datorer och det kan vara datorer som visas som **kan inte aktivera**. Det finns olika orsaker varför vissa datorer inte kanske är aktiverad. I följande avsnitt visas möjliga orsaker till det **kan inte aktivera** tillstånd på en virtuell dator när du försöker publicera.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>Virtuella datorer som rapporterar till en annan arbetsyta: '\<workspaceName\>”.  Ändra konfiguration som ska användas för att aktivera

**Orsak**: det här felet visar att den virtuella datorn som du försöker att publicera rapporter till en annan arbetsyta.

**Lösningen**: Klicka på **som konfiguration** att ändra den aktuella Automation-kontot och Log Analytics-arbetsytan.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>VM-rapporter till en arbetsyta som inte är tillgänglig i den här prenumerationen

**Orsak**: den arbetsyta som den virtuella datorn rapporterar till:

* Är i en annan prenumeration eller
* Finns inte längre, eller
* Är i en resursgrupp som du inte har åtkomstbehörighet till

**Lösningen**: hitta automation-konto associerat med den arbetsyta som den virtuella datorn rapporterar till och publicera den virtuella datorn genom att ändra omfattningskonfigurationen.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>Operativsystemversionen för virtuell dator eller distribution stöds inte

**Orsak:** lösningen stöds inte för alla Linux-distributioner eller alla versioner av Windows.

**Lösning:** avser den [lista över klienter som stöds](automation-update-management.md#clients) för lösningen.

### <a name="classic-vms-cannot-be-enabled"></a>Det går inte att aktivera klassiska virtuella datorer

**Orsak**: virtuella datorer som använder den klassiska distributionsmodellen stöds inte.

**Lösningen**: migrera den virtuella datorn till resource manager-distributionsmodellen. Information om hur du gör detta finns i [migrera klassiska distributionsresurserna för modellen](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>Virtuell dator har stoppats. (frigjord)

**Orsak**: den virtuella datorn i inte i en **kör** tillstånd.

**Lösningen**: för att publicera en virtuell dator till en lösning för den virtuella datorn måste köras. Klicka på den **starta VM** infogad länk för att starta den virtuella datorn utan att navigera bort från sidan.

## <a name="next-steps"></a>Nästa steg

Nu när lösningen har aktiverats för dina virtuella datorer, besök uppdateringshantering översiktsartikeln om du vill veta hur du visar kontroll av uppdateringar för dina datorer.

> [!div class="nextstepaction"]
> [Uppdatera hantering – Visa kontroll av uppdateringar](./automation-update-management.md#viewing-update-assessments)

Ytterligare självstudier om lösningarna och hur du använder dem:

* [Självstudie – hantera uppdateringar för din virtuella dator](automation-tutorial-update-management.md)

* [Självstudie – identifiera programvara på en virtuell dator](automation-tutorial-installed-software.md)

* [Självstudie – Felsök ändringar på en virtuell dator](automation-tutorial-troubleshoot-changes.md)