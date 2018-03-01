---
title: "Återställa filer från Azure till Windows Server | Microsoft Docs"
description: "I den här självstudien beskrivs hur du återställer objekt från Azure till Windows Server."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
keywords: windows server back up;restore files windows server; back up and disaster recovery
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 2/14/2018
ms.author: saurabhsensharma;markgal;
ms.custom: mvc
ms.openlocfilehash: 3bda261bae8155ccb48196a980b14afc56004da8
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="recover-files-from-azure-to-a-windows-server"></a>Återställa filer från Azure till Windows Server

Med Azure Backup kan du återställa enskilda objekt från säkerhetskopior av Windows Server. Det är praktiskt att återställa enskilda filer om du snabbt måste återställa filer som du har råkat ta bort av misstag. I den här självstudien beskrivs hur du kan använda MARS-agenten (Microsoft Azure Recovery Services Agent) för att återställa objekt från säkerhetskopior som du redan har skapat i Azure. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Initiera återställning av enskilda objekt 
> * Välja en återställningspunkt 
> * Återställa objekt från en återställningspunkt

Den här kursen förutsätter att du redan har utfört stegen för att [Säkerhetskopiera Windows Server till Azure](backup-configure-vault.md) och att du har minst en säkerhetskopia av dina Windows Server-filer i Azure.

## <a name="initiate-recovery-of-individual-items"></a>Initiera återställning av enskilda objekt

En praktisk användargränssnittsguide som heter Microsoft Azure Backup är installerad med MARS-agenten (Microsoft Azure Recovery Services). Microsoft Azure Backup-guiden fungerar tillsammans med MARS-agenten (Microsoft Azure Recovery Services) för att hämta säkerhetskopierade data från återställningspunkter som lagras i Azure. Använd Microsoft Azure Backup-guiden för att identifiera de filer eller mappar du vill återställa till Windows Server. 

1. Öppna snapin-modulen **Microsoft Azure Backup**. Du hittar den genom att söka efter **Microsoft Azure Backup** på datorn.

    ![Säkerhetskopiering väntar](./media/tutorial-backup-restore-files-windows-server/mars.png)

2. I guiden klickar du på **Återställ data** i **åtgärdsfönstret** i agentkonsolen för att starta guiden **Återställ data**.

    ![Säkerhetskopiering väntar](./media/tutorial-backup-restore-files-windows-server/mars-recover-data.png)

3. På sidan **Komma igång** väljer du **Den här servern (servernamn)** och klickar på **Nästa**.

4. På sidan **Välj återställningsläge** väljer du **Enskilda filer och mappar** och klickar sedan på **Nästa** för att påbörja urvalet av återställningspunkt.
 
5. På sidan **Välj volym och datum** väljer du volymen som innehåller filerna eller mapparna du vill återställa och klickar på **Montera**. Välj ett datum och en tid på den nedrullningsbara menyn som motsvarar en återställningspunkt. Datum i **fetstil** anger tillgängligheten för minst en återställningspunkt för den dagen.

    ![Säkerhetskopiering väntar](./media/tutorial-backup-restore-files-windows-server/mars-select-date.png)
 
    När du klickar på **Montera** gör Azure Backup återställningspunkten tillgänglig som en disk. Bläddra till och återställ filer från disken.

## <a name="restore-items-from-a-recovery-point"></a>Återställa objekt från en återställningspunkt

1. När återställningsvolymen är monterad klickar du på **Bläddra** för att öppna Utforskaren i Windows och söker rätt på filerna och mapparna du vill återställa. 

    ![Säkerhetskopiering väntar](./media/tutorial-backup-restore-files-windows-server/mars-browse-recover.png)

    Du kan öppna filerna direkt från återställningsvolymen och verifiera filerna.

2. I Utforskaren i Windows kopierar du filerna och/eller mapparna du vill återställa och klistrar in dem på önskad plats på servern.

    ![Säkerhetskopiering väntar](./media/tutorial-backup-restore-files-windows-server/mars-final.png)

3. När du har återställt filerna och/eller mapparna går du till sidan för att **söka efter och återställa filer** i guiden **Återställ data** och klickar på **Demontera**. 

    ![Säkerhetskopiering väntar](./media/tutorial-backup-restore-files-windows-server/unmount-and-confirm.png)

4.  Klicka på **Ja** för att bekräfta att du vill demontera volymen.

    När ögonblicksbilden är demonterad visas **Jobbet har slutförts** i rutan **Jobb** i agentkonsolen.

## <a name="next-steps"></a>Nästa steg

Det avslutar självstudien om at säkerhetskopiera och återställa Windows Server-data till Azure. Om du vill läsa mer om Azure Backup kan du ta en titt på PowerShell-exemplet för att säkerhetskopiera krypterade virtuella datorer.

> [!div class="nextstepaction"]
> [Säkerhetskopiera krypterade virtuella datorer](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
