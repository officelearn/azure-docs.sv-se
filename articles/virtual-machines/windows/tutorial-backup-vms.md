---
title: Självstudiekurs - Säkerhetskopiera virtuella Windows-datorer i Azure-portalen
description: I den här självstudiekursen lär du dig hur du använder Azure Portal för att skydda dina virtuella Windows-datorer med Azure Backup.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/06/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 89ed0bad2729a9e0983d4ef7f8a53faa4f5426ac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79415641"
---
# <a name="tutorial-back-up-and-restore-files-for-windows-virtual-machines-in-azure"></a>Självstudier: Säkerhetskopiera och återställa filer för virtuella Windows-datorer i Azure

Du kan skydda dina data genom att säkerhetskopiera med jämna mellanrum. Med Azure Backup skapas återställningspunkter som lagras i geo-redundanta återställningsvalv. När du återställer från en återställningspunkt kan du återställa hela den virtuella datorn eller specifika filer. Den här artikeln beskriver hur du återställer en enskild fil till en virtuell dator som kör Windows Server och IIS. Om du inte redan har en virtuell dator kan du skapa en genom att följa anvisningarna i [Windows-snabbstarten](quick-create-portal.md). I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Skapa en säkerhetskopia av en virtuell dator
> * Schemalägga en daglig säkerhetskopiering
> * Återställa en fil från en säkerhetskopia

## <a name="backup-overview"></a>Översikt över Backup

När Azure Backup-tjänsten initierar ett säkerhetskopieringsjobb instruerar den säkerhetskopieringstillägget att ta en ögonblicksbild. Azure Backup-tjänsten använder [VMSnapshot-tillägget](https://docs.microsoft.com/azure/virtual-machines/extensions/vmsnapshot-windows). Tillägget installeras under den första säkerhetskopieringen av den virtuella datorn om den virtuella datorn körs. Om den virtuella datorn inte körs tar Backup-tjänsten en ögonblicksbild av snapshot det underliggande lagringsutrymmet (eftersom ingen programskrivning medan den virtuella datorn stoppas).

När Backup-tjänsten tar en ögonblicksbild av virtuella Windows-datorer kontaktar den VSS-tjänsten (Volume Shadow Copy) för att få en konsekvent ögonblicksbild av den virtuella datorns diskar. När Azure Backup-tjänsten har tagit ögonblicksbilden överförs data till valvet. För att maximera effektiviteten identifierar och överför tjänsten endast de datablock som har ändrats sedan föregående säkerhetskopia.

När dataöverföringen har slutförts tas ögonblicksbilden bort och en återställningspunkt skapas.

## <a name="create-a-backup"></a>Skapa en säkerhetskopia
Skapa en enkel schemalagd daglig säkerhetskopiering till ett Recovery Services-valv. 

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Välj **Virtuella datorer** på menyn till vänster. 
1. Välj en virtuell dator som du vill säkerhetskopiera i listan.
1. På VM-bladet går du till avsnittet **Åtgärder** och klickar på **Säkerhetskopiering**. Bladet **Aktivera säkerhetskopiering** öppnas.
1. I **Recovery Services-valv** klickar du på **Skapa nytt** och anger namnet för det nya valvet. Ett nytt valv skapas i samma resursgrupp och plats som den virtuella datorn.
1. Under **Välj principer för säkerhetskopiering**behåller du **standardprincipen (Ny) DailyPolicy**och klickar sedan på **Aktivera säkerhetskopiering**.
1. Om du vill skapa en första återställningspunkt går du till bladet **Säkerhetskopiera** och klickar på **Säkerhetskopiera nu**.
1. Klicka på kalenderikonen på bladet **Säkerhetskopiera nu,** använd kalenderkontrollen för att välja hur länge återställningspunkten ska behållas och klicka på **OK**.
1. I **bladet Säkerhetskopiering** för den virtuella datorn ser du antalet återställningspunkter som är slutförda.


    ![Återställningspunkter](./media/tutorial-backup-vms/backup-complete.png)
    
Den första säkerhetskopieringen tar ungefär 20 minuter. Fortsätt till nästa del av den här självstudien när din säkerhetskopia är klar.

## <a name="recover-a-file"></a>Återställa en fil

Om du oavsiktligt råkar ta bort eller göra ändringar i en fil kan du använda Filåterställning för att återställa filen från ditt säkerhetskopieringsvalv. Filåterställning använder ett skript som körs på den virtuella datorn för att montera återställningspunkten som en lokal enhet. Enheterna förblir monterade i 12 timmar, så att du kan kopiera filer från återställningspunkten och återställa dem till den virtuella datorn.  

I det här exemplet beskriver vi hur du återställer bildfilen som används på standardwebbsidan för IIS. 

1. Visa IIS-standardsidan genom att öppna en webbläsare och ansluta till IP-adressen för den virtuella datorn.

    ![Standardwebbsidan för IIS](./media/tutorial-backup-vms/iis-working.png)

1. Anslut till den virtuella datorn.
1. Öppna **Utforskaren** på den virtuella datorn, navigera till \inetpub\wwwroot och ta bort filen **iisstart.png**.
1. Uppdatera webbläsaren på den lokala datorn så ser du att bilden på IIS-standardsidan är borta.

    ![Standardwebbsidan för IIS](./media/tutorial-backup-vms/iis-broken.png)

1. Öppna en ny flik på den lokala datorn och gå till [Azure Portal](https://portal.azure.com).
1. Välj **Virtuella datorer** på menyn till vänster och välj den virtuella datorn i listan.
1. På VM-bladet går du till avsnittet **Åtgärder** och klickar på **Säkerhetskopiering**. Bladet **Säkerhetskopiera** öppnas. 
1. På menyn överst på bladet väljer du **Filåterställning**. Bladet **Filåterställning** öppnas.
1. I **Steg 1: Välj återställningspunkt** väljer du en återställningspunkt från listrutan.
1. I **Steg 2: Ladda ned skriptet för att söka och återställa filer** klickar du på knappen **Ladda ned körbar fil**. Kopiera lösenordet för filen och spara det någonstans säkert.
1. Öppna **Utforskaren** på den lokala datorn, navigera till mappen **Hämtningsbara filer** och kopiera den nedladdade EXE-filen. Filnamnet inleds med namnet på den virtuella datorn. 
1. Klistra in EXE-filen på den virtuella datorn (med RDP-anslutningen) på den virtuella datorn. 
1. Navigera till skrivbordet på den virtuella datorn och dubbelklicka på EXE-filen. En kommandotolk startar. Återställningspunkten monteras som en filresurs som du kan komma åt. När resursen har skapats stänger du kommandotolken genom att skriva **q**.
1. Öppna **Utforskaren** på den virtuella datorn och navigera till enhetsbeteckningen som användes för filresursen.
1. Navigera till \inetpub\wwwroot, kopiera **iisstart.png** från filresursen och klistra in filen i \inetpub\wwwroot. Kopiera exempelvis F:\inetpub\wwwroot\iisstart.png och klistra in filen i c:\inetpub\wwwroot för att återställa filen.
1. På din lokala dator öppnar du webbläsarfliken där du är ansluten till den virtuella datorns IP-adress som visar IIS-standardwebbsidan. Tryck på CTRL + F5 för att uppdatera webbläsaren. Nu bör du se att bilden har återställts.
1. På din lokala dator går du tillbaka till webbläsarfliken för Azure-portalen i **Steg 3: Demontera diskarna efter återställning** klickar du på knappen **Demontera diskar**. Om du glömmer att utföra det här steget stängs anslutningen till monteringspunkten automatiskt efter 12 timmar. Efter dessa 12 timmar måste du ladda ner ett nytt skript för att skapa en ny monteringspunkt.





## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en säkerhetskopia av en virtuell dator
> * Schemalägga en daglig säkerhetskopiering
> * Återställa en fil från en säkerhetskopia

Gå vidare till nästa självstudie om du vill veta mer om övervakning av virtuella datorer.

> [!div class="nextstepaction"]
> [Styra virtuella datorer](tutorial-govern-resources.md)









