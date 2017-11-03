---
title: "Återställa filer från Azure till en Windows Server | Microsoft Docs"
description: "Den här självstudiekursen beskrivs återställa objekt från Azure till en Windows Server."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
keywords: "Windows server säkerhetskopiera, återställa filer windows server. säkerhetskopiering och katastrofåterställning"
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: saurabhsensharma;markgal;
ms.custom: 
ms.openlocfilehash: 28e0bc1414b0fea614f217dc3adf1484c1374018
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="recover-files-from-azure-to-a-windows-server"></a>Återställa filer från Azure till en Windows Server

Azure Backup kan återställning av enskilda objekt från säkerhetskopior av Windows Server. Återställa enskilda filer är användbart om du snabbt återställa filer som tas bort av misstag. Den här självstudiekursen beskrivs hur du kan använda Microsoft Azure Recovery Services Agent MARS-agenten för att återställa objekt från säkerhetskopior som du redan har genomfört i Azure. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Starta återställning av enskilda objekt 
> * Välj en återställningspunkt 
> * Återställa objekt från en återställningspunkt

Den här kursen förutsätter att du redan har utfört stegen för att [säkerhetskopiera en Windows Server till Azure](backup-configure-vault.md) och har minst en säkerhetskopia av Windows Server-filer i Azure.

## <a name="initiate-recovery-of-individual-items"></a>Starta återställning av enskilda objekt

En bra användaren guiden gränssnitt för heter Microsoft Azure Backup har installerats med Microsoft Azure Recovery Services MARS-agenten. Guiden Microsoft Azure Backup fungerar med Microsoft Azure Recovery Services MARS-agenten för att hämta säkerhetskopierade data från återställningspunkter som lagras i Azure. Använd guiden Microsoft Azure Backup för att identifiera filer eller mappar som du vill återställa till Windows Server. 

1. Öppna den **Microsoft Azure Backup** snapin-modulen. Du hittar den genom att söka efter **Microsoft Azure Backup** på datorn.

    ![Säkerhetskopiering väntar](./media/tutorial-backup-restore-files-windows-server/mars.png)

2. I guiden klickar du på **återställa Data** i den **åtgärdsfönstret** i administratörskonsolen för agenten att starta den **återställa Data** guiden.

    ![Säkerhetskopiering väntar](./media/tutorial-backup-restore-files-windows-server/mars-recover-data.png)

3. På den **komma igång** väljer **servern (server name)** och på **nästa**.

4. På den **Välj återställningsläge** väljer **enskilda filer och mappar** och klicka sedan på **nästa** ska börja återställningsprocessen punkt val.
 
5. På den **Välj volym och datum** väljer du den volym som innehåller de filer och mappar som du vill återställa och klicka på **montera**. Välj ett datum och välj en tid från den nedrullningsbara menyn som motsvarar en återställningspunkt. Datum i **fetstil** indikerar tillgängligheten för minst en återställningspunkt på den dagen.

    ![Säkerhetskopiering väntar](./media/tutorial-backup-restore-files-windows-server/mars-select-date.png)
 
    När du klickar på **montera**, Azure Backup gör den återställningspunkt som är tillgängliga som en disk. Bläddra och återställa filer från disken.

## <a name="restore-items-from-a-recovery-point"></a>Återställa objekt från en återställningspunkt

1. När återställningen volym är monterad, klickar du på **Bläddra** att öppna Utforskaren och hitta filer och mappar som du vill återställa. 

    ![Säkerhetskopiering väntar](./media/tutorial-backup-restore-files-windows-server/mars-browse-recover.png)

    Du kan öppna filerna direkt från återställning volym och verifiera filerna.

2. Kopiera filer och/eller mappar som du vill återställa och klistra in dem i valfri önskad plats på servern i Utforskaren.

    ![Säkerhetskopiering väntar](./media/tutorial-backup-restore-files-windows-server/mars-final.png)

3. När du är klar med att återställa filer och/eller mappar på den **Bläddra och återställningsfiler** sida av den **återställa Data** guiden, klickar du på **koppla från**. 

    ![Säkerhetskopiering väntar](./media/tutorial-backup-restore-files-windows-server/unmount-and-confirm.png)

4.  Klicka på **Ja** bekräfta att du vill att montera volymen.

    När ögonblicksbilden har demonterats, **jobbet slutförts** visas i den **jobb** rutan i konsolen agent.

## <a name="next-steps"></a>Nästa steg

Detta avslutar självstudier om säkerhetskopiering och återställning av Windows Server-data till Azure. Mer information om Azure Backup finns i PowerShell-exempel för att säkerhetskopiera den krypterade virtuella datorer.

> [!div class="nextstepaction"]
> [Säkerhetskopiera krypterade VM](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
