---
title: "Säkerhetskopiera virtuella datorer i Azure | Microsoft Docs"
description: "Identifiera, registrera och säkerhetskopiera virtuella Azure-datorer till ett recovery services-valv."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "säkerhetskopiering av virtuella datorer Säkerhetskopiera virtuella; säkerhetskopiering och katastrofåterställning återställning. arm säkerhetskopiering"
ms.assetid: 5c68481d-7be3-4e68-b87c-0961c267053e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/15/2017
ms.author: trinadhk;jimpark;markgal;
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 40983a3de104238d09b976b5fcf2419da42c1bba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-azure-virtual-machines-to-a-recovery-services-vault"></a>Säkerhetskopiera virtuella Azure-datorer till ett Recovery Services-valv
> [!div class="op_single_selector"]
> * [Säkerhetskopiera virtuella datorer till Recovery Services-valvet](backup-azure-arm-vms.md)
> * [Säkerhetskopiera virtuella datorer till Backup-valvet](backup-azure-vms.md)
>
>

Den här artikeln beskrivs hur du säkerhetskopierar virtuella Azure-datorer (både distribuerade Resource Manager och klassisk distribuerade) till Recovery Services-valvet. Det mesta av arbetet för att säkerhetskopiera virtuella datorer är förberedelserna. Innan du kan säkerhetskopiera eller mellan en virtuell dator, måste du slutföra de [krav](backup-azure-arm-vms-prepare.md) att förbereda miljön för att skydda dina virtuella datorer. När du har slutfört förutsättningarna kan du starta säkerhetskopiering för att ta ögonblicksbilder av den virtuella datorn.


[!INCLUDE [learn about backup deployment models](../../includes/backup-deployment-models.md)]

Mer information finns i artiklar på [planerar din infrastruktur för VM-säkerhetskopiering i Azure](backup-azure-vms-introduction.md) och [virtuella Azure-datorer](https://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="triggering-the-backup-job"></a>Utlöser säkerhetskopieringsjobbet
Den säkerhetskopieringsprincip som är kopplade till Recovery Services-valvet definierar hur ofta och när Säkerhetskopieringsåtgärden körs. Som standard är den första säkerhetskopian i den första schemalagda säkerhetskopieringen. Innan den första säkerhetskopieringen har körts visas Status för senaste säkerhetskopiering på bladet **Säkerhetskopieringsjobb** som **Varning (första säkerhetskopiering väntar)**.

![Säkerhetskopiering väntar](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

Såvida inte den första säkerhetskopieringen kommer att börja snart, rekommenderar vi att du kör **Säkerhetskopiera nu**. Följande procedur börjar från instrumentpanelen valvet. Den här proceduren används för att köra det inledande säkerhetskopieringsjobbet när du har slutfört alla krav. Den här proceduren är inte tillgängligt om inledande säkerhetskopieringsjobbet redan har körts. Den associera säkerhetskopieringsprincipen anger nästa säkerhetskopieringsjobb.  

Så här kör du det första säkerhetskopieringsjobbet:

1. På instrumentpanelen för valvet klickar du på numret under **Säkerhetskopieringsobjekt**, eller på panelen **Säkerhetskopieringsobjekt**. <br/>
  ![Ikonen Inställningar](./media/backup-azure-vms-first-look-arm/rs-vault-config-vm-back-up-now-1.png)

  Bladet **Säkerhetskopieringsobjekt** öppnas.

  ![Säkerhetskopieringsobjekt](./media/backup-azure-vms-first-look-arm/back-up-items-list.png)

2. Välj objektet på bladet **Säkerhetskopieringsobjekt**.

  ![Ikonen Inställningar](./media/backup-azure-vms-first-look-arm/back-up-items-list-selected.png)

  Listan **Säkerhetskopieringsobjekt** öppnas. <br/>

  ![Säkerhetskopieringsjobbet utlöses](./media/backup-azure-vms-first-look-arm/backup-items-not-run.png)

3. Öppna snabbmenyn genom att klicka på ellipserna **...** i listan **Säkerhetskopieringsobjekt**.

  ![Snabbmeny](./media/backup-azure-vms-first-look-arm/context-menu.png)

  Snabbmenyn visas.

  ![Snabbmeny](./media/backup-azure-vms-first-look-arm/context-menu-small.png)

4. Klicka på **Säkerhetskopiera nu** på snabbmenyn.

  ![Snabbmeny](./media/backup-azure-vms-first-look-arm/context-menu-small-backup-now.png)

  Bladet Säkerhetskopiera nu öppnas.

  ![Visar bladet Säkerhetskopiera nu](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

5. På bladet Säkerhetskopiera nu klickar du på kalenderikonen, använder kalenderkontrollen för att välja den sista dagen som den här återställningspunkten ska behållas och klickar sedan på **Säkerhetskopiera**.

  ![Ange den sista dagen som återställningspunkten som skapas med Säkerhetskopiera nu ska behållas](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  Distributionsmeddelanden visas som anger att säkerhetskopieringsjobbet har initierats, och du kan övervaka förloppet för jobbet på sidan Säkerhetskopieringsjobb. Beroende på den virtuella datorns storlek kan det ta en stund att skapa den första säkerhetskopian.

6. Du kan visa eller spåra statusen för den första säkerhetskopieringen genom att klicka på **Pågår** på panelen **Säkerhetskopieringsjobb** på instrumentpanelen för valvet.

  ![Panelen Säkerhetskopieringsjobb](./media/backup-azure-vms-first-look-arm/open-backup-jobs-1.png)

  Bladet Säkerhetskopieringsjobb öppnas.

  ![Panelen Säkerhetskopieringsjobb](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view-1.png)

  Du kan se statusen för alla jobb på bladet **Säkerhetskopieringsjobb**. Kontrollera om säkerhetskopieringsjobbet för den virtuella datorn fortfarande körs, eller om det har slutförts. När säkerhetskopieringsjobbet är klart visas statusen *Slutfört*.

  > [!NOTE]
  > Som en del av säkerhetskopieringen skickar tjänsten Azure Backup ett kommando till säkerhetskopieringstillägget på varje virtuell dator som instruerar det att tömma alla skrivningar och använda en konsekvent ögonblicksbild.
  >
  >

## <a name="troubleshooting-errors"></a>Felsökning av fel
Om du stöter på problem när säkerhetskopiera den virtuella datorn finns på [VM felsökningsartikel](backup-azure-vms-troubleshoot.md) för hjälp.

## <a name="next-steps"></a>Nästa steg
Nu när du har skyddat den virtuella datorn finns i följande artiklar om VM hanteringsuppgifter och återställa virtuella datorer.

* [Hantera och övervaka dina virtuella datorer](backup-azure-manage-vms.md)
* [Återställa virtuella datorer](backup-azure-arm-restore-vms.md)
