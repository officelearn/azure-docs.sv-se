---
title: "Säkerhetskopiera Windows Server till Azure | Microsoft Docs"
description: "Den här självstudiekursen information säkerhetskopiera lokala Windows-servrar till Recovery Services-valvet."
services: back up
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
keywords: "Windows server säkerhetskopiera; Säkerhetskopiera windows server. säkerhetskopiering och katastrofåterställning"
ms.assetid: 
ms.service: back up
ms.workload: storage-back up-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/23/2017
ms.author: saurabhsensharma;markgal;
ms.custom: mvc
ms.openlocfilehash: 0dbf3850c7fcccb1a02e70a19b498522a4ce0e79
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="back-up-windows-server-to-azure"></a>Säkerhetskopiera Windows Server till Azure


Du kan använda Azure Backup för att skydda Windows Server från skador, attacker och katastrofer. Azure-säkerhetskopiering ger ett enkelt verktyg som kallas Microsoft Azure Recovery Services MARS-agenten. MARS-agenten är installerad på en Windows Server kan skydda filer och mappar och konfigurationsinformation för server via Windows Server System tillstånd. Den här självstudiekursen beskrivs hur du kan använda MARS-agenten för att säkerhetskopiera Windows Server till Azure. I den här självstudiekursen får du lära du dig att: 


> [!div class="checklist"]
> * Hämta och konfigurera MARS-agenten
> * Konfigurera säkerhetskopiera gånger och bevarandeschema för din server säkerhetskopieras
> * Utföra en ad hoc-säkerhetskopiering


## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på http://portal.azure.com.

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

Innan du kan säkerhetskopiera Windows Server, måste du skapa en plats för säkerhetskopiorna eller återställningspunkter som ska lagras. En [Recovery Services-valvet](backup-azure-recovery-services-vault-overview.md) är en behållare i Azure som lagrar säkerhetskopior från Windows Server. Följ stegen nedan för att skapa ett Recovery Services-valv i Azure-portalen. 

1. På den vänstra menyn väljer **alla tjänster** och Skriv i listan över tjänster **återställningstjänster**. Klicka på **Recovery Services-valv**.

   ![Öppna Recovery Services-valvet](./media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)

2.  På menyn **Recovery Services-valv** klickar du på **Lägg till**.

   ![Ange information för valvet](./media/tutorial-backup-windows-server-to-azure/provide-vault-detail-2.png)

3.  I den **Recovery Services-valvet** -menyn

    - Typen *myRecoveryServicesVault* i **namn**.
    - Det aktuella prenumerations-ID visas i **prenumeration**.
    - För **resursgruppen**väljer **använda befintliga** och välj *myResourceGroup*. Om *myResourceGroup* inte finns, Välj **Skapa nytt** och skriv *myResourceGroup*. 
    - Från den **plats** nedrullningsbara menyn, Välj *Västeuropa*.
    - Klicka på **skapa** att skapa Recovery Services-valvet.
 
När valvet har skapats visas det i listan över Recovery Services-valv.

## <a name="download-recovery-services-agent"></a>Hämta Recovery Services-agenten

Microsoft Azure Recovery Services MARS-agenten skapar en association mellan Windows Server och Recovery Services-valvet. Följande procedur beskrivs hur du hämtar agenten till servern.

1.  Välj i listan över Recovery Services-valv **myRecoveryServicesVault** att öppna dess instrumentpanel.

   ![Ange information för valvet](./media/tutorial-backup-windows-server-to-azure/open-vault-from-list.png)

2.  Klicka på menyn valvet instrumentpanelen **säkerhetskopiering**.

3.  På den **säkerhetskopiering målet** menyn:

    - för **var körs din arbetsbelastning?**väljer**lokalt**, 
    - för **vad vill du säkerhetskopiera?**väljer **filer och mappar** och **systemtillstånd** 

    ![Ange information för valvet](./media/tutorial-backup-windows-server-to-azure/backup-goal.png)
    
4.  Klicka på **Förbered infrastrukturen** att öppna den **Förbered infrastruktur** menyn.
5.  På den **Förbered infrastruktur** -menyn klickar du på **ladda ned Agent för Windows Server och Windows-klient** att ladda ned den *MARSAgentInstaller.exe*. 

    ![förbereda infrastrukturen](./media/tutorial-backup-windows-server-to-azure/prepare-infrastructure.png)

    Installationsprogrammet öppnar en separat webbläsare och hämtar **MARSAgentInstaller.exe**.
 
6.  Innan du kör den hämta filen, klickar du på den **hämta** knappen på bladet Förbered infrastrukturen för att hämta och spara den **Valvautentiseringsuppgifter** fil. Den här filen krävs för att ansluta MARS-agenten med Recovery Services-valvet.

    ![förbereda infrastrukturen](./media/tutorial-backup-windows-server-to-azure/download-vault-credentials.png)
 
## <a name="install-and-register-the-agent"></a>Installera och registrera agenten

1. Leta upp och dubbelklicka på den hämtade **MARSagentinstaller.exe**.
2. Den **installationsguiden för Microsoft Azure Recovery Services-agenten** visas. När du går igenom guiden anger du följande information när du uppmanas och klickar på **registrera**.
    - Plats för installationen och cache-mappen.
    - Proxy-serverinformation om du använder en proxyserver för att ansluta till internet.
    - Ditt namn och lösenord användarinformation om du använder en autentiserad proxyserver.

    ![förbereda infrastrukturen](./media/tutorial-backup-windows-server-to-azure/mars-installer.png) 

3. I slutet av guiden klickar du på **fortsätta till registreringen** och ange den **Valvautentiseringsuppgifter** filen som du hämtade i föregående procedur.
 
4. När du uppmanas ange krypteringslösenfrasen för att kryptera säkerhetskopiorna från Windows Server. Spara lösenfrasen på en säker plats som Microsoft inte kan återställa lösenfrasen förlorade.

5. Klicka på **Slutför**. 

## <a name="configure-backup-and-retention"></a>Konfigurera säkerhetskopiering och kvarhållning

Du kan använda Microsoft Azure Recovery Services-agenten för att schemalägga när säkerhetskopiering till Azure sker på Windows Server. Utför följande steg på den server där du hämtade agenten.

1. Öppna Microsoft Azure Recovery Services-agenten. Du hittar den genom att söka efter **Microsoft Azure Backup** på datorn.

2.  Recovery Services agent-konsolen, klicka på **schemalägga säkerhetskopiering** under den **åtgärdsfönstret**.

    ![förbereda infrastrukturen](./media/tutorial-backup-windows-server-to-azure/mars-schedule-backup.png)

3. Klicka på **nästa** att navigera till den **markerar objekt att tillbaka in** sidan.

4. Klicka på **Lägg till objekt** och i dialogrutan som öppnas väljer du **systemtillstånd** och filer eller mappar som du vill säkerhetskopiera. Klicka sedan på **OK**.

5. Klicka på **Nästa**.

6. På den **Ange schema för säkerhetskopiering (systemtillstånd)** anger tid på dagen eller veckan när säkerhetskopieringar måste aktiveras för systemtillstånd och klicka på **nästa** 

7.  På den **Välj bevarandeprincip (systemtillstånd)** väljer bevarandeprincipen för säkerhetskopia för systemtillstånd och klicka på **nästa**
8. På liknande sätt, Välj säkerhetskopieringsprincip för schema och lagring för valda filer och mappar. 
8.  På den **väljer första säkerhetskopiering typen** lämnar alternativet **automatiskt via nätverket** markerad och klicka sedan på **nästa**.
9.  På den **bekräftelse** sidan Granska informationen och klicka sedan på **Slutför**.
10. När guiden har skapat säkerhetskopieringsschemat klickar du på **Stäng**.

## <a name="perform-an-ad-hoc-back-up"></a>Utföra en ad hoc-säkerhetskopiering

Du har skapat schemat när säkerhetskopieringsjobb kör. Dock har du inte säkerhetskopiera servern. Det är disaster recovery bäst att köra en säkerhetskopiering på begäran för att säkerställa dataåterhämtning för servern.

1.  Microsoft Azure Recovery Services agent-konsolen, klicka på **Säkerhetskopiera nu**.

    ![förbereda infrastrukturen](./media/tutorial-backup-windows-server-to-azure/backup-now.png)

2.  På den **Säkerhetskopiera nu** guiden Markera en från **filer och mappar** eller **systemtillstånd** som du vill säkerhetskopiera och klicka på **nästa** 
3. På den **bekräftelse** granskar du inställningarna som den **Säkerhetskopiera nu** guiden använder för att säkerhetskopiera servern. Klicka på **Säkerhetskopiera**.
4.  Stäng guiden genom att klicka på **Stäng**. Om du stänger guiden innan säkerhetskopierade processen är klar, fortsätter guiden i bakgrunden.
4.  När den första säkerhetskopieringen har slutförts **jobbet slutfört** status visas i **jobb** rutan i konsolen för MARS-agenten.


## <a name="next-steps"></a>Nästa steg

I den här kursen används Azure portal: 
 
> [!div class="checklist"] 
> * Skapa ett Recovery Services-valv 
> * Ladda ned Microsoft Azure Recovery Services-agent 
> * Installera agenten 
> * Konfigurera säkerhetskopiering för Windows Server 
> * Utföra en säkerhetskopiering på begäran 

Fortsätt till nästa guiden för att återställa filer från Azure till Windows Server

> [!div class="nextstepaction"] 
> [Återställa filer från Azure till Windows Server](./tutorial-backup-restore-files-windows-server.md) 

