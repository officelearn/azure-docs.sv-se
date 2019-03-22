---
title: Säkerhetskopiera Windows Server till Azure
description: Den här självstudiekursen förklarar hur du säkerhetskopierar lokala Windows-servrar till ett Recovery Services-valv.
services: backup
author: saurabhsensharma
manager: shivamg
keywords: windows server back up; back up windows server; back up and disaster recovery
ms.service: backup
ms.topic: tutorial
ms.date: 8/22/2018
ms.author: saurse
ms.custom: mvc
ms.openlocfilehash: f5cfa3de962c51ac7ff43d9b83bdea9f408f5bcf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58097431"
---
# <a name="back-up-windows-server-to-azure"></a>Säkerhetskopiera Windows Server till Azure


Du kan använda Azure Backup för att skydda Windows Server från skador, attacker och katastrofer. Azure Backup innehåller ett enkelt verktyg som kallas MARS-agenten (Microsoft Azure Recovery Services). MARS-agenten installeras på en Windows Server för att skydda filer och mappar samt information om serverkonfigurationen via Systemtillstånd för Windows Server. I den här självstudiekursen beskrivs hur du kan använda MARS-agenten för att säkerhetskopiera Windows Server till Azure. I den här självstudiekursen får du lära du dig att: 


> [!div class="checklist"]
> * Ladda ned och installera MARS-agenten
> * Konfigurera tidpunkter för säkerhetskopiering och kvarhållningsschema för serverns säkerhetskopior
> * Utföra en ad hoc-säkerhetskopiering


## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Innan du kan säkerhetskopiera Windows Server måste du skapa en plats för de säkerhetskopior, eller återställningspunkter, som ska lagras. Ett [Recovery Services-valv](backup-azure-recovery-services-vault-overview.md) är en container i Azure som lagrar säkerhetskopior från Windows Server. Följ stegen nedan för att skapa ett Recovery Services-valv i Azure-portalen. 

1. Välj **Alla tjänster** på menyn till vänster och skriv **Recovery Services** i tjänstlistan. Klicka på **Recovery Services-valv**.

   ![öppna Recovery Services-valv](./media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)

2. På menyn **Recovery Services-valv** klickar du på **Lägg till**.

   ![ange information för valvet](./media/tutorial-backup-windows-server-to-azure/provide-vault-detail-2.png)

3. På menyn **Recovery Services-valt**:

    - Skriv *myRecoveryServicesVault* i **Namn**.
    - Aktuellt prenumerations-ID visas i **Prenumeration**.
    - För **Resursgrupp** väljer du **Använd befintlig** och sedan *myResourceGroup*. Om *myResourceGroup* inte finns väljer du **Skapa ny** och skriver *myResourceGroup*. 
    - Från den nedrullningsbara menyn **Plats** väljer du *Europa, västra*.
    - Klicka på **Skapa** för att skapa ditt Recovery Services-valv.
 
När valvet har skapats visas det i listan över Recovery Services-valv.

## <a name="download-recovery-services-agent"></a>Ladda ned Recovery Services-agenten

MARS-agenten (Microsoft Azure Recovery Services) skapar en koppling mellan Windows Server och Recovery Services-valvet. Följande procedur beskriver hur du laddar ned agenten till servern.

1. Från listan med Recovery Services-valv väljer du **myRecoveryServicesVault** för att öppna valvets instrumentpanel.

   ![ange information för valvet](./media/tutorial-backup-windows-server-to-azure/open-vault-from-list.png)

2. Klicka på **Säkerhetskopiering** på valvets instrumentpanel.

3. På **Säkerhetskopieringsmål**-menyn:

   * för **Var körs din arbetsbelastning?**, välj **Lokalt**, 
   * för **Vad vill du säkerhetskopiera?**, välj **Filer och mappar** och **Systemtillstånd**.

   ![ange information för valvet](./media/tutorial-backup-windows-server-to-azure/backup-goal.png)

4. Klicka på **Förbered infrastrukturen** för att öppna **Förbered infrastruktur**-menyn.

5. På **Förbered infrastruktur**-menyn, klicka på **Ladda ned agent för Windows Server eller Windows Client** för att ladda ned *MARSAgentInstaller.exe*. 

    ![förbereda infrastrukturen](./media/tutorial-backup-windows-server-to-azure/prepare-infrastructure.png)

    Installationsprogrammet öppnar en separat webbläsare och laddar ned **MARSAgentInstaller.exe**.
 
6. Innan du kör den nedladdade filen klickar du på menyn Förbered infrastruktur, klickar på **Hämta** och sparar filen med **valvautentiseringsuppgifter**. Autentiseringsuppgifter krävs för att koppla MARS-agenten till Recovery Services-valvet.

    ![förbereda infrastrukturen](./media/tutorial-backup-windows-server-to-azure/download-vault-credentials.png)
 
## <a name="install-and-register-the-agent"></a>Installera och registrera agenten

1. Leta upp och dubbelklicka på **MARSagentinstaller.exe** som du laddade ned.
2. **Installationsguide för Microsoft Azure Recovery Services-agenten** visas. När du går igenom guiden anger du följande information när den efterfrågas och klickar på **Registrera**.
   - Plats för installationen och cachelagringsmappen.
   - Information om proxyservern om du använder en proxyserver för att ansluta till Internet.
   - Ditt användarnamn och lösenord om du använder en autentiserad proxyserver.

     ![förbereda infrastrukturen](./media/tutorial-backup-windows-server-to-azure/mars-installer.png) 

3. I slutet av guiden klickar du på **Fortsätt till registreringen** och tillhandahåller filen med **valvautentiseringsuppgifter** som du hämtade i föregående steg.
 
4. Ange vid uppmaningen en krypteringslösenfrasen för att kryptera säkerhetskopiorna från Windows Server. Spara lösenfrasen på en säker plats eftersom Microsoft inte kan återställa lösenfrasen om du förlorar den.

5. Klicka på **Slutför**. 

## <a name="configure-backup-and-retention"></a>Konfigurera säkerhetskopiering och kvarhållning

Du använder Microsoft Azure Recovery Services-agenten för att schemalägga när säkerhetskopiering till Azure sker på Windows Server. Utför följande steg på den server där du laddade ned agenten.

1. Öppna Microsoft Azure Recovery Services-agenten. Du hittar den genom att söka efter **Microsoft Azure Backup** på datorn.

2.  I konsolen för Recovery Services-agenten klickar du på **Schemalägg säkerhetskopiering** under **åtgärdsfönstret**.

    ![förbereda infrastrukturen](./media/tutorial-backup-windows-server-to-azure/mars-schedule-backup.png)

3. Klicka på **Nästa** för att gå till sidan där du **väljer objekt att säkerhetskopiera**.

4. Klicka på **Lägg till objekt**. I dialogrutan som öppnas markerar du **Systemtillstånd** och de filer eller mappar som du vill säkerhetskopiera. Klicka sedan på **OK**.

5. Klicka på **Nästa**.

6. På sidan **Ange schema för säkerhetskopiering (Systemtillstånd)** anger du tidpunkten på dagen eller veckan när säkerhetskopieringar ska utlösas för Systemtillstånd och klickar på **Nästa**.

7. På sidan **Välj kvarhållningsprincip (Systemtillstånd)**  väljer du en kvarhållningsprincip för säkerhetskopian för Systemtillstånd och klickar på **Nästa**.

8. Välj på samma sätt säkerhetskopieringsschema och kvarhållningsprincip för valda filer och mappar. 

9. Välj **Automatiskt över nätverket** på sidan **Välj typ för första säkerhetskopieringen** och klicka på **Nästa**.

10. Läs informationen på sidan **Bekräftelse** och klicka på **Slutför**.

11. När guiden har skapat säkerhetskopieringsschemat klickar du på **Stäng**.

## <a name="perform-an-ad-hoc-back-up"></a>Utföra en ad hoc-säkerhetskopiering

Du har skapat schemat för när säkerhetskopieringsjobb ska köras. Men du har inte säkerhetskopierat servern. Det är bästa praxis för haveriberedskap att köra en säkerhetskopiering på begäran för att säkerställa dataåterhämtning för servern.

1.  Öppna Microsoft Azure Recovery Services-agenten och klicka på **Säkerhetskopiera nu**.

    ![förbereda infrastrukturen](./media/tutorial-backup-windows-server-to-azure/backup-now.png)

2.  Guiden **Säkerhetskopiera nu** öppnas. Välj antingen **Filer och mappar** eller **Systemtillstånd** som du vill säkerhetskopiera och klicka på **Nästa** 
3. Gå igenom de inställningar på sidan **Bekräftelse** som guiden **Säkerhetskopiera nu** ska använda för att säkerhetskopiera servern. Klicka på **Säkerhetskopiera**.
4.  Stäng guiden genom att klicka på **Stäng**. Om du stänger guiden innan säkerhetskopieringen är klar fortsätter guiden att köras i bakgrunden.
4.  När den första säkerhetskopieringen har slutförts visas statusen **Jobbet har slutförts** i rutan **Jobb** i MARS-agentkonsolen.


## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du Azure Portal för att: 
 
> [!div class="checklist"] 
> * skapar ett Recovery Services-valv 
> * Ladda ned Microsoft Azure Recovery Services-agenten 
> * Installera agenten 
> * Konfigurera säkerhetskopiering för Windows Server 
> * Utföra en säkerhetskopiering på begäran 

Fortsätt till nästa självstudiekurs för att återställa filer från Azure till Windows Server

> [!div class="nextstepaction"] 
> [Återställa filer från Azure till Windows Server](./tutorial-backup-restore-files-windows-server.md) 

