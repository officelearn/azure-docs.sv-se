---
title: Flytta ett Azure Site Recovery-valv till en annan region
description: Beskriver hur du flyttar ett Recovery Services-valv (Azure Site Recovery) till en annan Azure-region
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 32dff9a165125ab1949560ce36438ae266cd3036
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74090303"
---
# <a name="move-a-recovery-services-vault-and-azure-site-recovery-configuration-to-another-azure-region"></a>Flytta en återställningstjänstvalv och Azure Site Recovery-konfiguration till en annan Azure-region

Det finns olika scenarier där du kanske vill flytta dina befintliga Azure-resurser från en region till en annan. Exempel är för hanterbarhet, styrning skäl, eller på grund av företagsfusioner och förvärv. En av de relaterade resurser som du kanske vill flytta när du flyttar dina virtuella Azure-datorer är konfigurationen för haveriberedskap. 

Det finns inget förstklassigt sätt att flytta en befintlig konfiguration för haveriberedskap från en region till en annan. Detta beror på att du har konfigurerat målregionen baserat på din källa VM-region. När du bestämmer dig för att ändra källregionen kan de tidigare befintliga konfigurationerna för målregionen inte återanvändas och måste återställas. Den här artikeln definierar steg-för-steg-processen för att konfigurera om inställningen för haveriberedskap och flytta den till en annan region.

I det här dokumentet kommer du att:

> [!div class="checklist"]
> * Kontrollera förutsättningar för flytten.
> * Identifiera de resurser som användes av Azure Site Recovery.
> * Inaktivera replikering.
> * Ta bort resurserna.
> * Ställ in Platsåterställning baserat på den nya källregionen för de virtuella datorerna.

> [!IMPORTANT]
> För närvarande finns det inget förstklassigt sätt att flytta ett Recovery Services-valv och katastrofåterställningskonfigurationen som är till en annan region. Den här artikeln guidar dig genom processen att inaktivera replikering och konfigurera den i den nya regionen.

## <a name="prerequisites"></a>Krav

- Se till att du tar bort och tar bort konfigurationen för haveriberedskap innan du försöker flytta virtuella Azure-datorer till en annan region. 

  > [!NOTE]
  > Om din nya målregion för Azure VM är samma som målregionen för haveriberedskap, kan du använda din befintliga replikeringskonfiguration och flytta den. Följ stegen i [Flytta virtuella Azure IaaS-datorer till en annan Azure-region](azure-to-azure-tutorial-migrate.md).

- Se till att du fattar ett välgrundat beslut och att intressenterna informeras. Den virtuella datorn skyddas inte mot katastrofer förrän flytten av den virtuella datorn är klar.

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Identifiera de resurser som användes av Azure Site Recovery
Vi rekommenderar att du gör det här steget innan du går vidare till nästa. Det är enklare att identifiera relevanta resurser medan de virtuella datorerna replikeras.

För varje Virtuell Azure-dator som replikeras går du till**egenskaper** **för replikerade objekt för skyddade** > **objekt** > och identifierar följande resurser:

- Målgrupp för målresurs
- Cachelagringskonto
- Mållagringskonto (vid ohanterat diskbaserat Virtuellt virtuell dator) 
- Målnätverk


## <a name="disable-the-existing-disaster-recovery-configuration"></a>Inaktivera den befintliga konfigurationen för haveriberedskap

1. Gå till valvet för återställningstjänster.
2. Högerklicka på datorn **i Replikerade objekt för skyddade** > **objekt**och välj **Inaktivera replikering**.
3. Upprepa det här steget för alla virtuella datorer som du vill flytta.

> [!NOTE]
> Mobilitetstjänsten avinstalleras inte från de skyddade servrarna. Du måste avinstallera den manuellt. Om du planerar att skydda servern igen kan du hoppa över att avinstallera mobilitetstjänsten.

## <a name="delete-the-resources"></a>Ta bort resurserna

1. Gå till valvet för återställningstjänster.
2. Välj **Ta bort**.
3. Ta bort alla andra resurser som du [tidigare har identifierat](#identify-the-resources-that-were-used-by-azure-site-recovery).
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Flytta virtuella Azure-datorer till den nya målregionen

Följ stegen i dessa artiklar baserat på ditt krav på att flytta virtuella Azure-datorer till målregionen:

- [Flytta virtuella Azure-datorer till en annan region](azure-to-azure-tutorial-migrate.md)
- [Flytta virtuella Azure-datorer till tillgänglighetszoner](move-azure-VMs-AVset-Azone.md)

## <a name="set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>Konfigurera platsåterställning baserat på den nya källregionen för de virtuella datorerna

Konfigurera haveriberedskap för virtuella Azure-datorer som har flyttats till den nya regionen genom att följa stegen i [Konfigurera haveriberedskap för virtuella Azure-datorer](azure-to-azure-tutorial-enable-replication.md).
