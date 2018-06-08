---
title: Lär dig hur du publicera lösningar för uppdateringshantering, ändringsspårning och lager från en virtuell dator i Azure
description: Lär dig hur att publicera ett Azure-virtuella datorn med uppdateringshantering, ändringsspårning och lager-lösningar som ingår i Azure Automation
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 675aebf35a6bee6e4cc4fd884204edb5bae4b848
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830569"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Publicera lösningar för uppdateringshantering, ändringsspårning och lager från en virtuell dator i Azure

Azure Automation ger lösningar för att hantera uppdateringar av operativsystemet säkerhet, spåra ändringar och inventera vad som är installerat på datorerna. Det finns flera sätt att publicera datorer, kan du publicera lösningar från en virtuell dator, [från ditt Automation-konto](automation-onboard-solutions-from-automation-account.md), [från flera datorer-surfning](automation-onboard-solutions-from-browse.md), eller av [runbook](automation-onboard-solutions.md). Den här artikeln beskriver onboarding dessa lösningar från en virtuell dator i Azure.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in till Azure på https://portal.azure.com

## <a name="enable-the-solutions"></a>Aktivera lösningarna

Navigera till en befintlig virtuell dator och välj antingen **uppdateringshantering**, **inventering**, eller **ändringsspårning** under **OPERATIONS**.

För att aktivera lösning för den virtuella datorn bara se till att den **aktivera för den här virtuella datorn** alternativknappen är markerad, om du vill publicera flera datorer för lösningen väljer **aktivera för virtuella datorer i den här prenumerationen** och klicka på **Klickar du på datorer för att aktivera**. Se, [publicera uppdateringshantering, ändringsspårning och lager lösningar](automation-onboard-solutions-from-automation-account.md) för att visa stegen för hur du hanterar flera datorer samtidigt.

Välj Log Analytics-arbetsytan och Automation-kontot och klicka på **Aktivera** för att aktivera lösningen. Det tar upp till 15 minuter att aktivera lösningen.

![Publicera lösning för uppdateringshantering](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Navigera till de andra lösningarna och klicka på **aktivera**, logganalys och Automation-konto listrutorna har inaktiverats eftersom de använder samma konto för arbetsytan och automatisering som tidigare aktiverade lösning.

> [!NOTE]
> **Ändringsspårning** och **inventering** använder samma lösning när en aktiveras den andra är aktiverad samt.

## <a name="scope-configuration"></a>Omfattningskonfiguration

Varje lösning använder en Scope-konfiguration på arbetsytan för att fokusera på de datorer som hämta lösningen. Konfigurationen av är en grupp med en eller flera sparade sökningar som används för att begränsa omfattningen av lösningen på specifika datorer. Åtkomst till Scope-konfigurationerna i ditt Automation-konto under **relaterade resurser**väljer **arbetsytan** i arbetsytan under **ARBETSYTAN DATAKÄLLOR**, Välj **omfång konfigurationer**.

Om den valda arbetsytan inte har skapa lösningar för hantering av uppdateringar eller ändringsspårning har följande omfång konfigurationer:

* **MicrosoftDefaultScopeConfig ChangeTracking**

* **MicrosoftDefaultScopeConfig uppdateringar**

Om den valda arbetsytan har redan lösningen. Lösningen distribueras inte igen och konfigurationen av läggs inte till den.

Klicka på ellipserna (...) på någon av de konfigurationer och välj **redigera**. På den **redigera konfigurationen** väljer **Välj datorgrupper** att öppna den **datorgrupper** sidan. Den här sidan visar sparade sökningar som används för att skapa Scope-konfiguration.

## <a name="saved-searches"></a>Sparade sökningar

När en dator läggs till i uppdateringshantering eller lösningar för spårning av ändringar och inventering, läggs de till en av två sparade sökningar på arbetsytan. Dessa sparade sökningar är frågor som innehåller de datorer som är mål för dessa lösningar.

Gå till arbetsytan och välj **sparade sökningar** under **allmänna**. Två sparade sökningar som används av dessa lösningar kan ses i följande tabell:

|Namn     |Kategori  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Uppdateringar        | Updates__MicrosoftDefaultComputerGroup         |

Välj antingen sparad sökning kan du se frågan som används för att fylla i gruppen. Följande bild visar frågan och dess resultat.

![Sparade sökningar](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="next-steps"></a>Nästa steg

Fortsätta att självstudier i lösningar för att lära sig hur de används.

* [Självstudiekurs – hantera uppdateringar för den virtuella datorn](automation-tutorial-update-management.md)

* [Självstudiekurs – identifiera programvara på en virtuell dator](automation-tutorial-installed-software.md)

* [Kursen - felsöka ändringar på en virtuell dator](automation-tutorial-troubleshoot-changes.md)
