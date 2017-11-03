---
title: "Migrera virtuella datorer från AWS till Azure | Microsoft Docs"
description: "Den här artikeln beskriver hur du migrerar virtuella datorer som körs i Amazon Web Services (AWS) till Azure med Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: bsiva
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/31/2017
ms.author: bsiva
ms.openlocfilehash: b3c0727a279649f4f7dae30d41027129ce5b04ee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Migrera virtuella datorer i Amazon Web Services (AWS) till Azure med Azure Site Recovery

Den här artikeln beskriver hur du migrerar AWS Windows instanser till Azure virtuella datorer med den [Azure Site Recovery](site-recovery-overview.md) service.

Migrering är en växling från AWS till Azure. Det går inte att återställning av datorer till AWS och det finns ingen pågående replikering. Den här artikeln beskriver stegen för migrering i Azure-portalen och baserat på instruktioner för [replikerar en fysisk dator till Azure](site-recovery-vmware-to-azure.md).

Skriv dina kommentarer eller frågor längst ned i den här artikeln eller på den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="supported-operating-systems"></a>Operativsystem som stöds

Site Recovery kan användas för att migrera EC2 instanser som kör något av följande operativsystem:

- Windows (endast 64-bitars)
    - Windows Server 2008 R2 SP1 + (Citrix NV drivrutiner eller AWS NV-drivrutiner. **Kör RedHat NV drivrutiner instanser stöds inte**) Windows Server 2012 Windows Server 2012 R2
- Linux (endast 64-bitars)
    - Red Hat Enterprise Linux 6.7 (endast HVM virtualiserade instanser)

## <a name="prerequisites"></a>Krav

Här är vad du behöver för den här distributionen:

* **Konfigurationsservern**: en Amazon EC2 virtuell dator som kör Windows Server 2012 R2 distribueras som konfigurationsservern. Övriga Azure Site Recovery-komponenter (processervern och huvudmålservern) installeras som standard när du distribuerar konfigurationsservern. Den här artikeln beskriver stegen för migrering i Azure-portalen och baserat på instruktioner för [Läs mer](site-recovery-components.md)

* **EC2 instanser**: det Amazon EC2 virtuella datorer instanser som du vill migrera.

## <a name="deployment-steps"></a>Distributionssteg

1. Skapa ett Recovery Services-valv.
2. Säkerhetsgruppen för dina EC2 instanser ska ha regler har konfigurerats för att tillåta kommunikation mellan EC2-instans som du vill migrera och den instans som du tänker distribuera konfigurationsservern.

3. På samma Amazon virtuella privata moln som EC2-instanser, distribuerar du en ASR konfigurationsservern. Finns VMware / fysiskt till Azure-kraven för konfiguration serverkrav för distribution.

    ![DeployCS](./media/site-recovery-migrate-aws-to-azure/migration_pic2.png)

4.  När din konfigurationsservern distribueras i AWS och registreras Recovery Services-valvet, bör du se konfigurationsservern och processervern under Site Recovery-infrastruktur som visas nedan:

    ![CSinVault](./media/site-recovery-migrate-aws-to-azure/migration_pic3.png)

5. När du har distribuerat konfigurationsservern (det kan ta upp till 15 minustes för att det ska visas), verifiera att den kan kommunicera med de virtuella datorer som du vill migrera.

6. [Konfigurera replikeringsinställningar](site-recovery-setup-replication-settings-vmware.md).

7. Aktivera replikering: Aktivera replikering för virtuella datorer du vill migrera. Du kan identifiera EC2-instanser som använder privata IP-adresser som du kan hämta från konsolen EC2.

    ![SelectVM](./media/site-recovery-migrate-aws-to-azure/migration_pic4.png)

8. När EC2-instanser som har skyddats och replikering till Azure är klar, [köra ett Redundanstest](site-recovery-test-failover-to-azure.md) att verifiera ditt programs prestanda i Azure.

    ![TFI](./media/site-recovery-migrate-aws-to-azure/migration_pic5.png)

9. Kör en växling vid fel från AWS till Azure för varje virtuell dator. Alternativt kan du skapa en återställningsplan kör en Redundansväxling för att migrera flera virtuella datorer från AWS till Azure [Lär dig mer](site-recovery-create-recovery-plans.md) om återställningsplaner.

10. För migrering behöver du inte utföra någon redundansväxling. I stället väljer du alternativet Fullständig migrering för varje dator som du vill migrera. Åtgärden slutföra migreringen är klar in migreringen, tar bort replikering för datorn och stoppar Site Recovery-faktureringen för datorn.

    ![Migrera](./media/site-recovery-migrate-aws-to-azure/migration_pic6.png)

## <a name="next-steps"></a>Nästa steg

- [Förbereda migrerade datorer att aktivera replikering](site-recovery-azure-to-azure-after-migration.md) till en annan region för katastrofåterställning.
- Börja skydda dina arbetsbelastningar genom att [replikera virtuella Azure datorer.](site-recovery-azure-to-azure.md)
