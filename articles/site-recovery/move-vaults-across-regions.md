---
title: Flytta en Azure Site Recovery-konfiguration till en annan Azure-region | Microsoft Docs
description: Vägledning för att flytta en Site Recovery-konfiguration till en annan Azure-region
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 2cf06a0c4e35d22cbad260201183516db2f07436
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013468"
---
# <a name="move-a-recovery-services-vault-and-azure-site-recovery-configuration-to-another-azure-region"></a>Flytta ett Recovery Services valv och Azure Site Recovery konfigurationen till en annan Azure-region

Det finns olika scenarier där du kanske vill flytta dina befintliga Azure-resurser från en region till en annan. Exempel är för hanterbarhet, styrnings orsaker eller på grund av företagets fusioner och förvärv. En av de relaterade resurserna som du kanske vill flytta när du flyttar dina virtuella Azure-datorer är haveri beredskaps konfigurationen. 

Det finns inget första klass sätt att flytta en befintlig haveri beredskaps konfiguration från en region till en annan. Detta beror på att du konfigurerade mål regionen baserat på din käll-VM-region. När du bestämmer dig för att ändra käll regionen kan tidigare befintliga konfigurationer av mål regionen inte återanvändas och måste återställas. I den här artikeln beskrivs steg för steg hur du omkonfigurerar konfigurationen av haveri beredskap och flyttar den till en annan region.

I det här dokumentet kommer du att:

> [!div class="checklist"]
> * Verifiera krav för flytten.
> * Identifiera de resurser som användes av Azure Site Recovery.
> * Inaktivera replikering.
> * Ta bort resurserna.
> * Konfigurera Site Recovery baserat på det nya käll området för de virtuella datorerna.

> [!IMPORTANT]
> För närvarande finns det inget första klass sätt att flytta ett Recovery Services valv och haveri beredskaps konfigurationen som är till en annan region. Den här artikeln vägleder dig genom processen att inaktivera replikering och konfigurera den i den nya regionen.

## <a name="prerequisites"></a>Förutsättningar

- Se till att du tar bort och tar bort konfigurationen för haveri beredskap innan du försöker flytta de virtuella Azure-datorerna till en annan region. 

  > [!NOTE]
  > Om din nya mål region för den virtuella Azure-datorn är samma som mål området för haveri beredskap, kan du använda din befintliga replikeringskonfiguration och flytta den. Följ stegen i [flytta virtuella Azure IaaS-datorer till en annan Azure-region](azure-to-azure-tutorial-migrate.md).

- Se till att du fattar ett välgrundat beslut och att intressenterna informeras. Din virtuella dator skyddas inte mot katastrofer förrän flyttningen av den virtuella datorn har slutförts.

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Identifiera de resurser som användes av Azure Site Recovery
Vi rekommenderar att du gör det här innan du fortsätter till nästa steg. Det är enklare att identifiera relevanta resurser medan de virtuella datorerna replikeras.

För varje virtuell Azure-dator som replikeras går du till**Egenskaper** för **skyddade objekt** > **replikerade objekt** > och identifierar följande resurser:

- Målresursgrupp
- Cachelagringskonto
- Mål lagrings konto (i händelse av en ohanterad diskbaserad virtuell Azure-dator) 
- Målnätverk


## <a name="disable-the-existing-disaster-recovery-configuration"></a>Inaktivera den befintliga haveri återställnings konfigurationen

1. Gå till Recovery Services valvet.
2. I **skyddade objekt** > **replikerade objekt**högerklickar du på datorn och väljer **inaktivera replikering**.
3. Upprepa det här steget för alla virtuella datorer som du vill flytta.

> [!NOTE]
> Mobilitets tjänsten avinstalleras inte från de skyddade servrarna. Du måste avinstallera den manuellt. Om du planerar att skydda servern igen kan du hoppa över avinstallationen av mobilitets tjänsten.

## <a name="delete-the-resources"></a>Ta bort resurser

1. Gå till Recovery Services valvet.
2. Välj **Ta bort**.
3. Ta bort alla andra resurser som du [tidigare har identifierat](#identify-the-resources-that-were-used-by-azure-site-recovery).
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Flytta virtuella Azure-datorer till den nya mål regionen

Följ stegen i de här artiklarna, baserat på ditt krav för att flytta virtuella Azure-datorer till mål regionen:

- [Flytta virtuella Azure-datorer till en annan region](azure-to-azure-tutorial-migrate.md)
- [Flytta virtuella Azure-datorer till tillgänglighetszoner](move-azure-VMs-AVset-Azone.md)

## <a name="set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>Konfigurera Site Recovery baserat på det nya käll området för de virtuella datorerna

Konfigurera haveri beredskap för virtuella Azure-datorer som flyttats till den nya regionen genom att följa stegen i [Konfigurera haveri beredskap för virtuella Azure-datorer](azure-to-azure-tutorial-enable-replication.md).
