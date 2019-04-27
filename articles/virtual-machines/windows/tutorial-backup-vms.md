---
title: Självstudier – Säkerhetskopiera virtuella Windows-datorer på Azure Portal | Microsoft Docs
description: I den här självstudiekursen lär du dig hur du använder Azure Portal för att skydda dina virtuella Windows-datorer med Azure Backup.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 7c6f5e199041af7d0ecd829ace2b56f5789f4955
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60785358"
---
# <a name="tutorial-back-up-and-restore-files-for-windows-virtual-machines-in-azure"></a>Självstudier: Säkerhetskopiera och återställa filer för Windows-datorer i Azure

Du kan skydda dina data genom att säkerhetskopiera med jämna mellanrum. Med Azure Backup skapas återställningspunkter som lagras i geo-redundanta återställningsvalv. När du återställer från en återställningspunkt kan du återställa hela den virtuella datorn eller specifika filer. Den här artikeln beskriver hur du återställer en enskild fil till en virtuell dator som kör Windows Server och IIS. Om du inte redan har en virtuell dator kan du skapa en genom att följa anvisningarna i [Windows-snabbstarten](quick-create-portal.md). I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en säkerhetskopia av en virtuell dator
> * Schemalägga en daglig säkerhetskopiering
> * Återställa en fil från en säkerhetskopia

## <a name="backup-overview"></a>Översikt över Backup

När Azure Backup-tjänsten initierar ett säkerhetskopieringsjobb instruerar den säkerhetskopieringstillägget att ta en ögonblicksbild. Azure Backup-tjänsten använder tillägget _VMSnapshot_. Tillägget installeras under den första säkerhetskopieringen av den virtuella datorn om den virtuella datorn körs. Om den virtuella datorn inte körs tar Backup-tjänsten en ögonblicksbild av snapshot det underliggande lagringsutrymmet (eftersom ingen programskrivning medan den virtuella datorn stoppas).

När Backup-tjänsten tar en ögonblicksbild av virtuella Windows-datorer kontaktar den VSS-tjänsten (Volume Shadow Copy) för att få en konsekvent ögonblicksbild av den virtuella datorns diskar. När Azure Backup-tjänsten har tagit ögonblicksbilden överförs data till valvet. För att maximera effektiviteten identifierar och överför tjänsten endast de datablock som har ändrats sedan föregående säkerhetskopia.

När dataöverföringen har slutförts tas ögonblicksbilden bort och en återställningspunkt skapas.


## <a name="create-a-backup"></a>Skapa en säkerhetskopia
Skapa en enkel schemalagd daglig säkerhetskopiering till ett Recovery Services-valv. 

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Virtuella datorer** på menyn till vänster. 
3. Välj en virtuell dator som du vill säkerhetskopiera i listan.
4. På VM-bladet går du till avsnittet **Åtgärder** och klickar på **Säkerhetskopiering**. Bladet **Aktivera säkerhetskopiering** öppnas.
5. I **Recovery Services-valv** klickar du på **Skapa nytt** och anger namnet för det nya valvet. Ett nytt valv skapas i samma resursgrupp och på samma plats som den virtuella datorn.
6. Klicka på **Säkerhetskopieringspolicy**. I det här exemplet behåller du standardvärdena och klickar på **OK**.
7. Klicka på **Aktivera säkerhetskopiering** på bladet **Aktivera säkerhetskopiering**. Då skapas en daglig säkerhetskopia baserat på standardschemat.
10. Om du vill skapa en första återställningspunkt går du till bladet **Säkerhetskopiera** och klickar på **Säkerhetskopiera nu**.
11. På bladet **Säkerhetskopiera nu** klickar du på kalenderikonen, använder kalenderkontrollen för att välja den sista dagen som den här återställningspunkten ska behållas och klickar sedan på **Säkerhetskopiera**.
12. På bladet **Säkerhetskopiera** för din virtuella dator ser du antalet återställningspunkter som är klara.

    ![Återställningspunkter](./media/tutorial-backup-vms/backup-complete.png)
    
Den första säkerhetskopieringen tar ungefär 20 minuter. Fortsätt till nästa del av den här självstudien när din säkerhetskopia är klar.

## <a name="recover-a-file"></a>Återställa en fil

Om du oavsiktligt råkar ta bort eller göra ändringar i en fil kan du använda Filåterställning för att återställa filen från ditt säkerhetskopieringsvalv. Filåterställning använder ett skript som körs på den virtuella datorn för att montera återställningspunkten som en lokal enhet. Enheterna förblir monterade i 12 timmar, så att du kan kopiera filer från återställningspunkten och återställa dem till den virtuella datorn.  

I det här exemplet beskriver vi hur du återställer bildfilen som används på standardwebbsidan för IIS. 

1. Visa IIS-standardsidan genom att öppna en webbläsare och ansluta till IP-adressen för den virtuella datorn.

    ![Standardwebbsidan för IIS](./media/tutorial-backup-vms/iis-working.png)

2. Anslut till den virtuella datorn.
3. Öppna **Utforskaren** på den virtuella datorn, navigera till \inetpub\wwwroot och ta bort filen **iisstart.png**.
4. Uppdatera webbläsaren på den lokala datorn så ser du att bilden på IIS-standardsidan är borta.

    ![Standardwebbsidan för IIS](./media/tutorial-backup-vms/iis-broken.png)

5. Öppna en ny flik på den lokala datorn och gå till [Azure Portal](https://portal.azure.com).
6. Välj **Virtuella datorer** på menyn till vänster och välj den virtuella datorn i listan.
8. Klicka på **Säkerhetskopiering** i avsnittet **Inställningar** på bladet VM. Bladet **Säkerhetskopiera** öppnas. 
9. På menyn överst på bladet väljer du **Filåterställning**. Bladet **Filåterställning** öppnas.
10. I **steg 1: Välj återställningspunkt**, Välj en återställningspunkt från listrutan.
11. I **steg 2: Ladda ned skript för att söka och återställa filer**, klickar du på den **ladda ned körbar fil** knappen. Spara filen i mappen **Hämtningsbara filer**.
12. Öppna **Utforskaren** på den lokala datorn, navigera till mappen **Hämtningsbara filer** och kopiera den nedladdade EXE-filen. Filnamnet inleds med namnet på den virtuella datorn. 
13. På den virtuella datorn (via RDP-anslutningen) klistrar du in EXE-filen på den virtuella datorns skrivbord. 
14. Navigera till skrivbordet på den virtuella datorn och dubbelklicka på EXE-filen. När du gör det startar en kommandotolk och återställningspunkten monteras sedan som en filresurs som du kan komma åt. När resursen har skapats stänger du kommandotolken genom att skriva **q**.
15. Öppna **Utforskaren** på den virtuella datorn och navigera till enhetsbeteckningen som användes för filresursen.
16. Navigera till \inetpub\wwwroot, kopiera **iisstart.png** från filresursen och klistra in filen i \inetpub\wwwroot. Kopiera exempelvis F:\inetpub\wwwroot\iisstart.png och klistra in filen i c:\inetpub\wwwroot för att återställa filen.
17. På din lokala dator öppnar du webbläsarfliken där du är ansluten till den virtuella datorns IP-adress som visar IIS-standardwebbsidan. Tryck på CTRL + F5 för att uppdatera webbläsaren. Nu bör du se att bilden har återställts.
18. På den lokala datorn, gå tillbaka till webbläsarfliken för Azure-portalen och i **steg3: Demontera diskarna efter återställning** klickar du på den **demontera diskar** knappen. Om du glömmer att utföra det här steget stängs anslutningen till monteringspunkten automatiskt efter 12 timmar. Efter de 12 timmarna måste du ladda ned ett nytt skript för att skapa en ny monteringspunkt.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en säkerhetskopia av en virtuell dator
> * Schemalägga en daglig säkerhetskopiering
> * Återställa en fil från en säkerhetskopia

Gå vidare till nästa självstudie om du vill veta mer om övervakning av virtuella datorer.

> [!div class="nextstepaction"]
> [Styra virtuella datorer](tutorial-govern-resources.md)









