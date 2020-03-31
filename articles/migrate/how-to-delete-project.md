---
title: Ta bort ett Azure Migrate-projekt
description: Beskriver hur du skapar ett Azure Migrate-projekt och lägger till ett utvärderings-/migreringsverktyg.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/22/2019
ms.author: raynew
ms.openlocfilehash: 55842d36cddb2a7851ff5bd7002c20e9873158f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73512735"
---
# <a name="delete-an-azure-migrate-project"></a>Ta bort ett Azure Migrate-projekt

I den här artikeln beskrivs hur du tar bort ett [Azure Migrate-projekt.](migrate-overview.md)


## <a name="before-you-start"></a>Innan du börjar

Innan du tar bort ett projekt:

- När du tar bort ett projekt tas metadata för projektet och identifierade datorn bort.
- Om du har anslutit en Log Analytics-arbetsyta till serverbedömningsverktyget för beroendeanalys bestämmer du om du vill ta bort arbetsytan. 
    - Arbetsytan tas inte bort automatiskt. Ta bort den manuellt.
    - Kontrollera vad en arbetsyta används för innan du tar bort den. Samma Log Analytics-arbetsyta kan användas för flera scenarier.
    - Innan du tar bort projektet kan du hitta en länk till arbetsytan i **Azure Migrate - Servers** > **Azure Migrate - Server Assessment**, under **OMS Workspace**.
    - Om du vill ta bort en arbetsyta när du har tagit bort ett projekt hittar du arbetsytan i den relevanta resursgruppen och följer [dessa instruktioner](../azure-monitor/platform/delete-workspace.md).


## <a name="delete-a-project"></a>Ta bort ett projekt


1. Öppna resursgruppen där projektet skapades i Azure-portalen.
2. På resursgruppssidan väljer du **Visa dolda typer**.
3. Markera projektet och de associerade resurser som du vill ta bort.
    - Resurstypen för Azure Migrate-projekt är **Microsoft.Migrate/migrateprojects**.
    - I nästa avsnitt granskar du de resurser som skapats för identifiering, utvärdering och migrering i ett Azure Migrate-projekt.
    - Om resursgruppen bara innehåller Azure Migrate-projektet kan du ta bort hela resursgruppen.
    - Om du vill ta bort ett projekt från den tidigare versionen av Azure Migrate är stegen desamma. Resurstypen för dessa projekt är **Migreringsprojekt**.


## <a name="created-resources"></a>Skapade resurser

Dessa tabeller sammanfattar de resurser som skapats för identifiering, utvärdering och migrering i ett Azure Migrate-projekt.

> [!NOTE]
> Ta bort nyckelvalvet med försiktighet eftersom det kan innehålla säkerhetsnycklar.

### <a name="vmwarephysical-server"></a>VMware/fysisk server

**Resurs** | **Typ**
--- | ---
"Apparatnamn"kv | Nyckelvalv
Webbplats för "Apparatnamn" | Microsoft.offAzure/VMwareSites Microsoft.offAzure/VMwareSites Microsoft.offAzure/VMwareSites Microsoft.
"Projektnamn" | Microsoft.Migrera/migrera projekt
Projektet "ProjectName" | Microsoft.Migrera/bedöma projekt
"ProjectName"rsvault | Recovery Services-valv
"ProjectName"-MigreraVault-* | Recovery Services-valv
migreratillämpningsbarawsa* | Lagringskonto
migreraapplilsa* | Lagringskonto
migreratilläms* | Lagringskonto
migreraapplikv* | Nyckelvalv
migrerar till 16041 | Service Bus-namnområde

### <a name="hyper-v-vm"></a>Hyper-V VM 

**Resurs** | **Typ**
--- | ---
"Projektnamn" | Microsoft.Migrera/migrera projekt
Projektet "ProjectName" | Microsoft.Migrera/bedöma projekt
HyperV*kv | Nyckelvalv
HyperV*-webbplats | Microsoft.offAzure/HyperVSites Microsoft.OffAzure/HyperVSites Microsoft.offAzure/HyperVSites Microsoft.
"ProjectName"-MigreraVault-* | Recovery Services-valv


## <a name="next-steps"></a>Nästa steg

Läs om hur du lägger till ytterligare [bedömnings-](how-to-assess.md) och [migreringsverktyg.](how-to-migrate.md) 
