---
title: Publicera uppdateringshantering, ändringsspårning och lager lösningar från en Azure VM
description: Lär dig hur att publicera en virtuell Azure-dator med uppdateringshantering, ändringsspårning och lager-lösningar som ingår i Azure Automation.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: f270b2ccea51e83bc6475051b8667bf73d7fd717
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221520"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Publicera uppdateringshantering, ändringsspårning och lager lösningar från en virtuell Azure-dator

Azure Automation ger lösningar som hjälper dig att hantera operativsystemet säkerhetsuppdateringar, spåra ändringar och inventera vad som är installerat på datorerna. Det finns flera sätt att publicera datorer. Du kan publicera lösningar från en virtuell dator, [från ditt Automation-konto](automation-onboard-solutions-from-automation-account.md), [från flera datorer-surfning](automation-onboard-solutions-from-browse.md), eller genom att använda en [runbook](automation-onboard-solutions.md). Den här artikeln beskriver onboarding dessa lösningar från en virtuell Azure-dator.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="enable-the-solutions"></a>Aktivera lösningarna

Gå till en befintlig virtuell dator. Under **OPERATIONS**väljer **uppdateringshantering**, **inventering**, eller **ändringsspårning**.

Se till att om du vill aktivera lösningen för den virtuella datorn endast **aktivera för den här virtuella datorn** är markerad. Om du vill publicera flera datorer i lösningen väljer **aktivera för virtuella datorer i den här prenumerationen**, och välj sedan **klickar du på datorer för att aktivera**. Mer information hur att publicera flera datorer samtidigt, finns [publicera uppdateringshantering, ändringsspårning och lager lösningar](automation-onboard-solutions-from-automation-account.md).

Välj Azure logganalys-arbetsytan och Automation-konto och välj sedan **aktivera** att aktivera lösningen. Det tar upp till 15 minuter att aktivera lösningen.

![Publicera lösning för hantering av uppdateringar](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Gå till de andra lösningarna och välj sedan **aktivera**. De logganalys och automatisering konto listrutorna har inaktiverats eftersom dessa lösningar använder samma arbetsyta och Automation-konto som den tidigare aktiverade lösningen.

> [!NOTE]
> **Ändringsspårning** och **inventering** använder samma lösning. När någon av följande lösningar är aktiverad, aktiveras den andra också.

## <a name="scope-configuration"></a>Konfiguration av scope

Varje lösning använder en scope-konfiguration på arbetsytan för att fokusera på de datorer som hämta lösningen. Konfigurationen av är en grupp med en eller flera sparade sökningar som används för att begränsa omfattningen av lösningen på specifika datorer. Åtkomst till scope-konfigurationerna i ditt Automation-konto under **relaterade resurser**väljer **arbetsytan**. I arbetsytan under **ARBETSYTAN DATAKÄLLOR**väljer **omfång konfigurationer**.

Om den valda arbetsytan inte redan har lösningar för hantering av uppdateringar eller ändringsspårning, skapas följande scope konfigurationer:

* **MicrosoftDefaultScopeConfig ChangeTracking**

* **MicrosoftDefaultScopeConfig uppdateringar**

Om den valda arbetsytan har redan lösningen, lösningen omdistribueras inte och konfigurationen av läggas till inte.

Välj ellipserna (**...** ) på någon av de konfigurationer och välj sedan **redigera**. I den **redigera konfigurationen** väljer **Välj datorgrupper**. Den **datorgrupper** visar sparade sökningar som används för att skapa scope-konfiguration.

## <a name="saved-searches"></a>Sparade sökningar

När en dator läggs till uppdateringshantering, ändringsspårning eller lösningar för inventering, läggs datorn till en av två sparade sökningar på arbetsytan. Sparade sökningar är frågor som innehåller de datorer som är mål för dessa lösningar.

Gå till din arbetsyta. Under **allmänna**väljer **sparade sökningar**. Två sparade sökningar som används av dessa lösningar visas i följande tabell:

|Namn     |Kategori  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Uppdateringar        | Updates__MicrosoftDefaultComputerGroup         |

Välj något av de sparade sökningarna kan du se frågan som används för att fylla i gruppen. Följande bild visar frågan och dess resultat:

![Sparade sökningar](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="next-steps"></a>Nästa steg

Fortsätta att självstudier för lösningar att lära dig hur du använder dem:

* [Självstudiekurs – hantera uppdateringar för den virtuella datorn](automation-tutorial-update-management.md)
* [Självstudiekurs – identifiera programvara på en virtuell dator](automation-tutorial-installed-software.md)
* [Kursen - felsöka ändringar på en virtuell dator](automation-tutorial-troubleshoot-changes.md)
