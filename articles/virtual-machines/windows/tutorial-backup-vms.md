---
title: "Säkerhetskopiera virtuella Windows Azure-datorer | Microsoft Docs"
description: "Skydda Windows-datorer genom att säkerhetskopiera dem med Azure Backup."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 8e58a2290e5034ef393f65cbcddb86e18cf4a6ec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-windows-virtual-machines-in-azure"></a>Säkerhetskopiera Windows-datorer i Azure

Du kan skydda dina data genom att säkerhetskopiera med jämna mellanrum. Azure-säkerhetskopiering skapar återställningspunkter som är lagrade i geo-redundant recovery-valv. När du återställer från en återställningspunkt kan du återställa hela VM eller bara vissa filer. Den här artikeln förklarar hur du återställer en fil till en virtuell dator som kör Windows Server och IIS. Om du inte redan har en virtuell dator ska användas, kan du skapa en med hjälp av den [Windows quickstart](quick-create-portal.md). I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en säkerhetskopia av en virtuell dator
> * Schemalägga en daglig säkerhetskopiering
> * Återställa en fil från en säkerhetskopia




## <a name="backup-overview"></a>Översikt över Backup

När tjänsten Azure Backup initierar ett säkerhetskopieringsjobb, utlöser reservanknytning för att ta en ögonblicksbild i tidpunkt. Azure Backup-tjänsten används den _VMSnapshot_ tillägg. Tillägget installeras under den första säkerhetskopieringen om den virtuella datorn körs. Om inte den virtuella datorn körs, tar en ögonblicksbild av det underliggande lagringsutrymmet i Backup-tjänsten (eftersom inga program skrivningar inträffar när den virtuella datorn stoppas).

När en ögonblicksbild av virtuella Windows-datorer, samordnar Backup-tjänsten med Volume Shadow Copy Service (VSS) att hämta en programkonsekvent ögonblicksbild av den virtuella datorns diskar. När Azure Backup-tjänsten tar ögonblicksbilden kan överföra data till valvet. För att maximera effektiviteten, tjänsten identifierar och överför endast block av data som har ändrats sedan den tidigare säkerhetskopian.

När dataöverföringen har slutförts ögonblicksbilden tas bort och skapa en återställningspunkt.


## <a name="create-a-backup"></a>Skapa en säkerhetskopia
Skapa en enkel schemalagd daglig säkerhetskopiering till ett Recovery Services-valv. 

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Välj **Virtuella datorer** på menyn till vänster. 
3. Välj en virtuell dator som du vill säkerhetskopiera i listan.
4. På VM-blad i den **inställningar** klickar du på **säkerhetskopiering**. Den **Aktivera säkerhetskopiering** blad öppnas.
5. I **Recovery Services-valvet**, klickar du på **Skapa nytt** och ange namn för det nya valvet. Ett nytt valv skapas i samma resursgrupp och plats som den virtuella datorn.
6. Klicka på **säkerhetskopiera princip**. I det här exemplet att behålla standardinställningarna och klickar på **OK**.
7. På den **Aktivera säkerhetskopiering** bladet, klickar du på **Aktivera säkerhetskopiering**. Detta skapar en daglig säkerhetskopiering baserat på standardschemat.
10. Skapa en första återställningspunkten på den **säkerhetskopiering** bladet och klickar på **Säkerhetskopiera nu**.
11. På den **säkerhetskopiering nu** bladet klickar du på kalenderikonen, Använd kalender för att välja den sista dagen i den här återställningspunkten behålls Klicka på **säkerhetskopiering**.
12. I den **säkerhetskopiering** bladet för den virtuella datorn, visas antalet återställningspunkter som är klar.

    ![Återställningspunkter](./media/tutorial-backup-vms/backup-complete.png)
    
Den första säkerhetskopieringen tar ungefär 20 minuter. När säkerhetskopieringen är klar fortsätter du till nästa del av den här kursen.

## <a name="recover-a-file"></a>Återställa en fil

Om du tar bort av misstag eller göra ändringar i en fil, kan du använda filåterställning för att återställa filen från din säkerhetskopieringsvalvet. Filåterställning använder ett skript som körs på den virtuella datorn att montera återställningspunkten som lokal enhet. Dessa enheter förblir monterade 12 timmar så att du kan kopiera filer från återställningspunkten och återställa dem till den virtuella datorn.  

I det här exemplet visar vi hur du återställer avbildningsfilen som används i standardwebbsidan för IIS. 

1. Öppna en webbläsare och ansluta till IP-adressen för den virtuella datorn för att visa sidan med IIS.

    ![Standardwebbsidan för IIS](./media/tutorial-backup-vms/iis-working.png)

2. Ansluta till den virtuella datorn.
3. På den virtuella datorn, öppna **Utforskaren** och navigera till \inetpub\wwwroot och ta bort filen **iisstart.png**.
4. Uppdatera webbläsaren att bilden på sidan IIS är borta på den lokala datorn.

    ![Standardwebbsidan för IIS](./media/tutorial-backup-vms/iis-broken.png)

5. På den lokala datorn, öppna en ny flik och gå i den [Azure-portalen](https://portal.azure.com).
6. Välj på menyn till vänster **virtuella datorer** och välj formuläret VM i listan.
8. På VM-blad i den **inställningar** klickar du på **säkerhetskopiering**. Den **säkerhetskopiering** blad öppnas. 
9. Välj på menyn längst upp på bladet **filåterställning**. Den **filåterställning** blad öppnas.
10. I **steg 1: Välj återställningspunkt**, Välj en återställningspunkt från listrutan.
11. I **steg 2: hämta skript för att bläddra och återställa filerna**, klicka på den **hämta körbara** knappen. Spara filen på din **hämtar** mapp.
12. Öppna på den lokala datorn **Utforskaren** och navigera till din **hämtar** mappen och kopiera den nedladdade .exe-fil. Filnamnet ska föregås av VM-namn. 
13. Klistra in .exe-fil till skrivbordet på den virtuella datorn på den virtuella datorn (via RDP-anslutning). 
14. Navigera till skrivbordet på den virtuella datorn och dubbelklicka på .exe. Kommer att starta en kommandotolk och sedan montera återställningspunkten som en filresurs som du kan komma åt. När du är klar att skapa resursen, Skriv **q** att stänga Kommandotolken.
15. Öppna på den virtuella datorn **Utforskaren** och navigera till enhetsbeteckningen som användes för filresursen.
16. Navigera till \inetpub\wwwroot och kopiera **iisstart.png** från filen dela och klistra in den i \inetpub\wwwroot. Till exempel F:\inetpub\wwwroot\iisstart.png kopiera och klistra in den i c:\inetpub\wwwroot att återskapa filen.
17. Öppna fliken webbläsare där du är ansluten till IP-adressen för den virtuella datorn som visar sidan IIS på den lokala datorn. Tryck på CTRL + F5 för att uppdatera sidan webbläsare. Du bör nu se att avbildningen har återställts.
18. På den lokala datorn, gå tillbaka till fliken för Azure-portalen och i **steg3: demontera diskarna efter återställningen** klickar du på den **demontera diskar** knappen. Om du glömmer att göra det här steget är anslutningen till monteringspunkt Stäng automatiskt efter 12 timmar. Du måste hämta ett nytt skript om du vill skapa en ny monteringspunkt efter dessa 12 timmar.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en säkerhetskopia av en virtuell dator
> * Schemalägga en daglig säkerhetskopiering
> * Återställa en fil från en säkerhetskopia

Gå vidare till nästa kurs mer information om övervakning av virtuella datorer.

> [!div class="nextstepaction"]
> [Övervaka virtuella datorer](tutorial-monitoring.md)









