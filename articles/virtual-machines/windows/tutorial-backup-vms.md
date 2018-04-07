---
title: Säkerhetskopiera virtuella Windows Azure-datorer | Microsoft Docs
description: Skydda Windows-datorer genom att säkerhetskopiera dem med Azure Backup.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 12859bf967cf8de1b57ab9dfd5c0bd080806f2eb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="back-up-windows-virtual-machines-in-azure"></a>Säkerhetskopiera Windows-datorer i Azure

Du kan skydda dina data genom att säkerhetskopiera med jämna mellanrum. Med Azure Backup skapas återställningspunkter som lagras i geo-redundanta återställningsvalv. När du återställer från en återställningspunkt kan du återställa hela den virtuella datorn eller bara specifika filer. Den här artikeln förklarar hur du återställer en fil till en virtuell dator som kör Windows Server och IIS. Om du inte redan har en virtuell dator ska användas, kan du skapa en med hjälp av den [Windows quickstart](quick-create-portal.md). I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en säkerhetskopia av en virtuell dator
> * Schemalägga en daglig säkerhetskopiering
> * Återställa en fil från en säkerhetskopia




## <a name="backup-overview"></a>Översikt över Backup

När tjänsten Azure Backup initierar ett säkerhetskopieringsjobb, utlöser reservanknytning för att ta en ögonblicksbild i tidpunkt. Azure Backup-tjänsten används den _VMSnapshot_ tillägg. Tillägget installeras under den första säkerhetskopieringen av den virtuella datorn om den virtuella datorn körs. Om den virtuella datorn inte körs tar Backup-tjänsten en ögonblicksbild av snapshot det underliggande lagringsutrymmet (eftersom ingen programskrivning medan den virtuella datorn stoppas).

När en ögonblicksbild av virtuella Windows-datorer, samordnar Backup-tjänsten med Volume Shadow Copy Service (VSS) att hämta en programkonsekvent ögonblicksbild av den virtuella datorns diskar. När Azure Backup-tjänsten har tagit ögonblicksbilden överförs data till valvet. För att maximera effektiviteten identifierar och överför tjänsten endast de datablock som har ändrats sedan föregående säkerhetskopia.

När dataöverföringen har slutförts tas ögonblicksbilden bort och en återställningspunkt skapas.


## <a name="create-a-backup"></a>Skapa en säkerhetskopia
Skapa en enkel schemalagd daglig säkerhetskopiering till ett Recovery Services-valv. 

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Virtuella datorer** på menyn till vänster. 
3. Välj en virtuell dator som du vill säkerhetskopiera i listan.
4. Klicka på **Säkerhetskopiering** i avsnittet **Inställningar** på bladet VM. Bladet **Aktivera säkerhetskopiering** öppnas.
5. I **Recovery Services-valv** klickar du på **Skapa nytt** och anger namnet för det nya valvet. Ett nytt valv skapas i samma resursgrupp och på samma plats som den virtuella datorn.
6. Klicka på **Säkerhetskopieringspolicy**. I det här exemplet behåller du standardvärdena och klickar på **OK**.
7. Klicka på **Aktivera säkerhetskopiering** på bladet **Aktivera säkerhetskopiering**. Då skapas en daglig säkerhetskopia baserat på standardschemat.
10. Om du vill skapa en första återställningspunkt går du till bladet **Säkerhetskopiera** och klickar på **Säkerhetskopiera nu**.
11. På bladet **Säkerhetskopiera nu** klickar du på kalenderikonen, använder kalenderkontrollen för att välja den sista dagen som den här återställningspunkten ska behållas och klickar sedan på **Säkerhetskopiera**.
12. På bladet **Säkerhetskopiera** för din virtuella dator ser du antalet återställningspunkter som är klara.

    ![Återställningspunkter](./media/tutorial-backup-vms/backup-complete.png)
    
Den första säkerhetskopieringen tar ungefär 20 minuter. Fortsätt till nästa del av den här självstudien när din säkerhetskopia är klar.

## <a name="recover-a-file"></a>Återställa en fil

Om du oavsiktligt råkar ta bort eller göra ändringar i en fil kan du använda Filåterställning för att återställa filen från ditt säkerhetskopieringsvalv. Filåterställning använder ett skript som körs på den virtuella datorn att montera återställningspunkten som lokal enhet. Enheterna förblir monterade i 12 timmar, så att du kan kopiera filer från återställningspunkten och återställa dem till den virtuella datorn.  

I det här exemplet visar vi hur du återställer avbildningsfilen som används i standardwebbsidan för IIS. 

1. Öppna en webbläsare och ansluta till IP-adressen för den virtuella datorn för att visa sidan med IIS.

    ![Standardwebbsidan för IIS](./media/tutorial-backup-vms/iis-working.png)

2. Ansluta till den virtuella datorn.
3. På den virtuella datorn, öppna **Utforskaren** och navigera till \inetpub\wwwroot och ta bort filen **iisstart.png**.
4. Uppdatera webbläsaren att bilden på sidan IIS är borta på den lokala datorn.

    ![Standardwebbsidan för IIS](./media/tutorial-backup-vms/iis-broken.png)

5. På den lokala datorn, öppna en ny flik och gå i den [Azure-portalen](https://portal.azure.com).
6. Välj på menyn till vänster **virtuella datorer** och välj den virtuella datorn från listan.
8. Klicka på **Säkerhetskopiering** i avsnittet **Inställningar** på bladet VM. Bladet **Säkerhetskopiera** öppnas. 
9. På menyn överst på bladet väljer du **Filåterställning**. Bladet **Filåterställning** öppnas.
10. I **Steg 1: Välj återställningspunkt** väljer du en återställningspunkt från listrutan.
11. I **Steg 2: Ladda ned skriptet för att söka och återställa filer** klickar du på knappen **Ladda ned körbar fil**. Spara filen på din **hämtar** mapp.
12. Öppna på den lokala datorn **Utforskaren** och navigera till din **hämtar** mappen och kopiera den nedladdade .exe-fil. Filnamnet ska föregås av VM-namn. 
13. Klistra in .exe-fil till skrivbordet på den virtuella datorn på den virtuella datorn (via RDP-anslutning). 
14. Navigera till skrivbordet på den virtuella datorn och dubbelklicka på .exe. Kommer att starta en kommandotolk och sedan montera återställningspunkten som en filresurs som du kan komma åt. När du är klar att skapa resursen, Skriv **q** att stänga Kommandotolken.
15. Öppna på den virtuella datorn **Utforskaren** och navigera till enhetsbeteckningen som användes för filresursen.
16. Navigera till \inetpub\wwwroot och kopiera **iisstart.png** från filen dela och klistra in den i \inetpub\wwwroot. Till exempel F:\inetpub\wwwroot\iisstart.png kopiera och klistra in den i c:\inetpub\wwwroot att återskapa filen.
17. Öppna fliken webbläsare där du är ansluten till IP-adressen för den virtuella datorn som visar sidan IIS på den lokala datorn. Tryck på CTRL + F5 för att uppdatera webbläsaren. Du bör nu se att avbildningen har återställts.
18. På din lokala dator går du tillbaka till webbläsarfliken för Azure-portalen i **Steg 3: Demontera diskarna efter återställning** klickar du på knappen **Demontera diskar**. Om du glömmer att göra det här steget är anslutningen till monteringspunkt Stäng automatiskt efter 12 timmar. Efter de 12 timmarna måste du ladda ned ett nytt skript för att skapa en ny monteringspunkt.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en säkerhetskopia av en virtuell dator
> * Schemalägga en daglig säkerhetskopiering
> * Återställa en fil från en säkerhetskopia

Gå vidare till nästa självstudie om du vill veta mer om övervakning av virtuella datorer.

> [!div class="nextstepaction"]
> [Styra virtuella datorer](tutorial-govern-resources.md)









