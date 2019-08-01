---
title: Flytta Azure Site Recovery konfiguration till en annan Azure-region | Microsoft Docs
description: Vägledning för att flytta Site Recovery konfiguration till en annan Azure-region
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: ba0e2577d6fb8bac66322917936fe06825af0d96
ms.sourcegitcommit: 6ad03fa28a0f60cb6dce6144f728c2ceb56ff6e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68708300"
---
# <a name="moving-recovery-services-vault-and--azure-site-recovery-configuration-to-another-azure-region"></a>Flytta Recovery Services valv och Azure Site Recovery konfiguration till en annan Azure-region

Det finns olika scenarier där du vill flytta dina befintliga Azure-resurser från en region till en annan hanterbarhet, styrnings orsaker eller till följd av företagets sammanslagningar/förvärv osv. En av de relaterade resurserna som du kanske vill flytta när du flyttar dig till dina virtuella Azure-datorer är haveri återställnings konfigurationen för samma. Det finns inget första klass sätt att flytta en befintlig haveri beredskaps konfiguration från en region till en annan. Detta beror på att du skulle ha konfigurerat mål regionen baserat på din käll-VM-region och när du bestämmer dig för att ändra det, kan tidigare befintliga konfigurationer av mål region inte återanvändas och måste återställas. I den här artikeln beskrivs steg för steg hur du konfigurerar om haveri beredskap och flyttar till en annan region.

I det här dokumentet kommer du att:

> [!div class="checklist"]
> * Verifiera krav för flytten
> * Identifiera de resurser som användes av Azure Site Recovery 
> * Inaktivera replikering
> * Ta bort resurser 
> * Konfigurera om Site Recovery utifrån det nya käll området för de virtuella datorerna.

> [!IMPORTANT]
> För närvarande finns det inget första klass sätt att flytta ett Recovery Services valv och DR-konfigurationen som är till en annan region. det här dokumentet vägleder kunden genom processen att inaktivera replikeringen och återställa den i den nya regionen.

## <a name="prerequisites"></a>Förutsättningar

- Se till att du tar bort och tar bort konfigurationen för haveri beredskap innan du försöker flytta de virtuella Azure-datorerna till en annan region. 

> [!NOTE]
> Om din nya mål region för den virtuella Azure-datorn är samma som mål regionen för Disaster Recovery-målet, kan du använda din befintliga replikeringskonfiguration och flytta utifrån de steg som beskrivs [här](azure-to-azure-tutorial-migrate.md) 

- Se till att du fattar ett välgrundat beslut och att intressenterna informeras om att din virtuella dator inte skyddas mot katastrofer till flytten av den virtuella datorn är klar. 

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Identifiera de resurser som användes av Azure Site Recovery
Vi rekommenderar att du utför det här steget innan du fortsätter till nästa, eftersom det blir lättare att identifiera relevanta resurser medan de virtuella datorerna fortfarande replikeras

För var och en av de virtuella Azure-datorer som replikeras navigerar du till**Egenskaper** för **skyddade objekt** > **replikerade objekt**>och identifierar följande resurser

- Målresursgrupp
- Cachelagringskonto
- Mål lagrings konto (i händelse av ohanterad diskbaserad virtuell Azure-dator) 
- Målnätverk


## <a name="disable-existing-disaster-recovery-configuration"></a>Inaktivera befintlig katastrof återställnings konfiguration

1. Navigera till **Recovery Services valvet** 
2.  I **skyddade objekt** > **replikerade objekt**högerklickar du på datorn > **inaktiverar replikering**.
3. Upprepa detta för alla de virtuella datorer som du vill flytta.
> [!NOTE]
> mobilitets tjänsten kommer inte att avinstalleras från de skyddade servrarna. du måste avinstallera den manuellt. Om du planerar att skydda servern igen kan du hoppa över avinstallationen av mobilitets tjänsten.

## <a name="delete-the-resources"></a>Ta bort resurser

1. Gå till **Recovery Services valvet**
2. Klicka på **ta bort**
3. Fortsätt att ta bort alla andra resurser som identifierats i[ föregående steg](#identify-the-resources-that-were-used-by-azure-site-recovery)
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Flytta virtuella Azure-datorer till den nya mål regionen

Följ de steg som beskrivs nedan baserat på ditt krav för att flytta virtuella Azure-datorer till mål regionen.

- [Flytta virtuella Azure-datorer till en annan region](azure-to-azure-tutorial-migrate.md)
- [Flytta virtuella Azure-datorer till tillgänglighetszoner](move-azure-VMs-AVset-Azone.md)

## <a name="re-set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>Konfigurera Site Recovery på nytt baserat på den nya käll regionen för de virtuella datorerna

Konfigurera haveri beredskap för virtuella Azure-datorer som har flyttats till den nya regionen med hjälp av de steg som beskrivs [här](azure-to-azure-tutorial-enable-replication.md)
