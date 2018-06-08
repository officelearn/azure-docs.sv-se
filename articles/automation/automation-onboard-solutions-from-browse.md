---
title: Lär dig hur du publicera uppdateringshantering, ändringsspårning och lager lösningar för flera virtuella datorer i Azure Automation
description: Lär dig hur att publicera ett Azure-virtuella datorn med uppdateringshantering, ändringsspårning och lager-lösningar som ingår i Azure Automation
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 6d0109b9043b48fbbbeeaccea6c798eaa547a056
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839884"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Aktivera uppdateringshantering, ändringsspårning och lager lösningar på flera virtuella datorer

Azure Automation ger lösningar för att hantera uppdateringar av operativsystemet säkerhet, spåra ändringar och inventera vad som är installerat på datorerna. Det finns flera sätt att publicera datorer, kan du publicera lösningen [från en virtuell dator](automation-onboard-solutions-from-vm.md), från din [Automation-konto](automation-onboard-solutions-from-automation-account.md), när du bläddrar virtuella datorer eller genom att [runbook](automation-onboard-solutions.md). Den här artikeln beskriver onboarding dessa lösningar när du bläddrar virtuella datorer i Azure.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in till Azure på https://portal.azure.com

## <a name="enable-solutions"></a>Aktivera lösningar

I Azure-portalen går du till **virtuella datorer**.

Använd kryssrutorna och markera de virtuella datorerna som du vill publicera med ändringsspårning och inventering eller hantering av uppdateringar. Onboarding-processen är tillgängliga för upp till tre olika resursgrupper i taget.

![Lista över virtuella datorer](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Använda filterkontroller för att ändra listan över virtuella datorer och klicka sedan på den översta kryssrutan för att markera alla virtuella datorer i listan.

I kommandofältet klickar du på **Services** och välj antingen **ändringsspårning**, **inventering**, eller **uppdateringshantering**.

> [!NOTE]
> **Ändringsspårning** och **inventering** använder samma lösning när en aktiveras den andra är aktiverad samt.

Följande bild är för hantering av uppdateringar. Ändringsspårning och lager har samma layout och beteende.

Listan över virtuella datorer filtreras så att endast virtuella datorer i samma prenumeration och plats. Om dina virtuella datorer har fler än tre resursgrupper, markeras de första tre resursgrupperna.

Använd filterkontroller för att välja virtuella datorer från olika prenumerationer, platser och resursgrupper.

![Publicera uppdateringshantering](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Granska valen för Log analytics-arbetsyta och Automation-konto. En ny arbetsyta och Automation-konto är markerade som standard. Om du har en befintlig logganalys-arbetsytan och Automation-konto du vill använda klickar du på **ändra** att välja dem från den **Configuration** sidan. När du är klar klickar du på **Spara**.

![Välj arbetsytan och konto](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Avmarkera kryssrutan bredvid varje virtuell dator som du inte vill aktivera. Virtuella datorer som inte är redan avmarkerat.

Klicka på **aktivera** att aktivera lösningen. Det tar upp till 15 minuter att aktivera lösningen.

## <a name="troubleshooting"></a>Felsökning

När onboarding flera datorer kan det vara datorer som visas som **kan inte aktivera**. Det finns olika skäl varför vissa datorer är inte kanske aktiverat. Följande avsnitt visar möjliga orsaker till det **kan inte aktivera** tillstånd på en virtuell dator när du försöker publicera.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>VM-rapporter till en annan arbetsyta: '\<workspaceName\>'.  Ändra konfiguration som ska användas för att aktivera

**Orsak**: det här felet visas som den virtuella datorn som du försöker publicera rapporter till en annan arbetsyta.

**Lösningen**: arbetsytan som du riktar genom att klicka i infogad länk eller klicka på Ändra **ändra**. Du kan också använda följande skript för att ändra arbetsytan till det avsedda för varje dator.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>VM-rapporter till en arbetsyta som inte är tillgänglig i den här prenumerationen

**Orsak**: arbetsytan som den virtuella datorn rapporterar till:

* I en annan prenumeration eller
* Finns inte längre, eller
* Är i en resursgrupp som du inte har behörigheter för åtkomst till

**Lösningen**: hitta automation-kontot associerade med arbetsytan som den virtuella datorn rapporterar till och publicera den virtuella datorn genom att ändra konfigurationen av.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>VM operativsystemets version eller distribution stöds inte

**Orsak:** lösningen stöds inte för alla Linux-distributioner eller alla versioner av Windows.

**Lösning:** avser den [lista över klienter som stöds](automation-update-management.md#clients) för lösningen.

### <a name="classic-vms-cannot-be-enabled"></a>Det går inte att aktivera klassiska virtuella datorer

**Orsak**: virtuella datorer som använder den klassiska distributionsmodellen stöds inte.

**Lösningen**: migrera den virtuella datorn till resource manager-distributionsmodellen. Information om hur du gör detta finns [migrera klassisk distribution modellen resurser](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>VM har stoppats. (frigjord)

**Orsak**: den virtuella datorn i inte i en **kör** tillstånd.

**Lösningen**: för att publicera en virtuell dator till en lösning för den virtuella datorn måste köras. Klicka på den **starta VM** infogad länk för att starta den virtuella datorn utan att behöva lämna sidan.

## <a name="next-steps"></a>Nästa steg

Nu när lösningen har aktiverats för de virtuella datorerna, besök översiktsartikel uppdatera hantering om du vill veta hur du visar utvärdering av uppdateringar för dina datorer.

> [!div class="nextstepaction"]
> [Uppdatera hantering - vyn utvärdering av uppdateringar](./automation-update-management.md#viewing-update-assessments)

Ytterligare självstudier om lösningarna och hur du använder dem:

* [Självstudiekurs – hantera uppdateringar för den virtuella datorn](automation-tutorial-update-management.md)

* [Självstudiekurs – identifiera programvara på en virtuell dator](automation-tutorial-installed-software.md)

* [Kursen - felsöka ändringar på en virtuell dator](automation-tutorial-troubleshoot-changes.md)