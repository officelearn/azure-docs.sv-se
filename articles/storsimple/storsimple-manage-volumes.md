---
title: Hantera din StorSimple-volymer | Microsoft Docs
description: "Beskriver hur du lägger till, ändra, övervaka och ta bort StorSimple-volymer och hur du kopplar dem om det behövs."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: ccabd859-590c-4568-a81d-6ff38f125be3
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 8e33f971610b01e9802e8fb12457d9ce88b51813
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-volumes"></a>Använda StorSimple Manager-tjänsten för att hantera volymer
> [!NOTE]
> Den klassiska portalen för StorSimple är föråldrad. Din StorSimple-enhetshanterare flyttas automatiskt till den nya Azure portalen enligt utfasningen schemat. Du får ett e-postmeddelande och portalmeddelandet för flyttningen. Det här dokumentet kommer också att dragits tillbaka snart. Frågor om flyttningen, se [vanliga frågor och svar: flyttar till Azure-portalen](storsimple-8000-move-azure-portal-faq.md).

[!INCLUDE [storsimple-version-selector-manage-volumes](../../includes/storsimple-version-selector-manage-volumes.md)]

## <a name="overview"></a>Översikt
Den här självstudiekursen beskrivs hur du använda StorSimple Manager-tjänsten för att skapa och hantera volymer på StorSimple-enheten och virtuella StorSimple-enheten.

StorSimple Manager-tjänsten är en utökning av den klassiska Azure-portalen där du kan hantera din StorSimple-lösning från ett enda webbgränssnitt. Utöver att hantera volymer, kan du använda StorSimple Manager-tjänsten för att skapa och hantera StorSimple-tjänster, visa och hantera enheter, Visa aviseringar, och visa och hantera principer för säkerhetskopiering och katalogen för säkerhetskopieringen.

> [!NOTE]
> Azure StorSimple skapa tunt allokerade volymer. Du kan inte skapa helt eller delvis etablerade volymer på ett Azure StorSimple-system.
> 
> Tunn allokering är en virtualiseringsteknik som tillgängligt lagringsutrymme visas överskrider fysiska resurser. I stället för att reservera tillräckligt med lagringsutrymme i förväg använder Azure StorSimple tunn allokering för att allokera bara tillräckligt med utrymme för att uppfylla krav som ställs. Elastisk komponenternas egenskaper molnlagring underlättar den här metoden eftersom Azure StorSimple kan öka eller minska lagringsutrymmet i molnet för att möta föränderliga behov.
> 
> 

## <a name="the-volumes-page"></a>Sidan volymer
Den **volymer** sidan kan du hantera lagringsvolymer som etablerats på Microsoft Azure StorSimple-enheten för dina initierarna (servrarna). Den visar en lista över volymer på StorSimple-enheten.

 ![Volymsidan](./media/storsimple-manage-volumes/HCS_VolumesPage.png)

En volym som består av en serie med attribut:

* **Namnet** – ett beskrivande namn som måste vara unikt och hjälper till att identifiera volymen. Det här namnet används också övervaka rapporter när du filtrerar på en viss volym.
* **Status för** – kan vara online eller offline. Om en volym om offline, visas det inte för initierarna (servrarna) som har behörighet att använda volymen.
* **Kapacitet** – anger hur stor volymen är anser vara av initieraren (server). Kapacitet anger den totala mängden data som kan lagras av initieraren (server). Volymer är tunt etablerad och data är deduplicerad. Detta innebär att enheten inte allokera fysiska lagringskapacitet före internt eller i molnet enligt konfigurerade volymens kapacitet. Volymens kapacitet är allokerade och används på begäran.
* **Typen** – volymtypen som kan vara nivåindelade eller arkivering (undertyp av skikt)
* **Åtkomst** – anger initierarna (servrarna) som har åtkomst till den här volymen. Initierare som inte är medlemmar i åtkomstkontrollpost (ACR) som är kopplad till volymen kan inte se volymen.
* **Övervaka** – anger huruvida en volym som övervakas. En volym har övervakning aktiverad som standard när den skapas. Övervakning kommer dock, inaktiveras för en volym kloning. Om du vill aktivera övervakning av en volym, följer du anvisningarna i övervakaren en volym.

De vanligaste uppgifterna som är kopplade till en volym är:

* Lägg till en volym
* Ändra en volym
* Ta bort en volym
* Koppla från en volym
* Övervaka en volym

## <a name="add-a-volume"></a>Lägg till en volym
Du [har skapat en volym](storsimple-deployment-walkthrough-u1.md#step-6-create-a-volume) under distributionen av din StorSimple-lösning. Lägger till en volym är en liknande procedur.

### <a name="to-add-a-volume"></a>Att lägga till en volym
1. På den **enheter** sidan, Välj enheten, dubbelklicka på den och klicka sedan på den **Volymbehållare** fliken.
2. Välj en volymbehållare och klicka på pilen i motsvarande rad få åtkomst till volymerna som är kopplade till behållaren.
3. Klicka på **Lägg till** längst ned på sidan. Lägg till en volym startas.
   
     ![Lägg till volym slutförs grundläggande inställningar](./media/storsimple-manage-volumes/AddVolume1.png)
4. I guiden Lägg till en volym, i **Grundläggande inställningar**, gör du följande:
   
   1. Ange ett **namn** för volymen.
   2. Ange **etablerad kapacitet** för volymen i GB eller TB. Kapaciteten måste vara mellan 1 GB och 64 TB för en fysisk enhet. Maximal kapacitet som kan tillhandahållas för en volym på en virtuell StorSimple-enhet är 30 TB.
   3. Välj den **användningstyp** för volymen. Om du använder den nivåindelade volymen för arkiveringsdata, väljer du den **Använd volymen för mindre ofta använda arkiveringsdata** kryssrutan ändras segmentstorleken för deduplicering för din volym till 512 KB. Om du inte väljer det här alternativet använder den motsvarande nivåindelade volymen en segmentstorlek på 64 KB. En större segmentstorlek för deduplicering tillåter enheten att påskynda överföringen av stora mängder arkiveringsdata till molnet. (Nivåindelade volymer kallades primära volymer.)
   4. Klicka på pilikonen ![pilikonen](./media/storsimple-manage-volumes/HCS_ArrowIcon.png)att gå till den **ytterligare inställningar** sidan.
      
        ![Lägg till ytterligare inställningar för volym-guiden](./media/storsimple-manage-volumes/AddVolume2.png)
5. Under **ytterligare inställningar**, lägga till en ny åtkomstkontrollpost (ACR):
   
   1. Välj en åtkomstkontrollpost (ACR) från den nedrullningsbara listan. Alternativt kan du lägga till en ny ACR. ACRs avgör vilka värdar som kan komma åt dina volymer genom att matcha värden IQN med som anges i posten.
   2. Vi rekommenderar att du aktiverar en standard säkerhetskopiering genom att markera kryssrutan **Aktivera en standard säkerhetskopiering för den här volymen**.
   3. Klicka på kryssikonen ![Kryssikon](./media/storsimple-manage-volumes/HCS_CheckIcon.png) att skapa volymen med de angivna inställningarna.

Den nya volymen är nu redo att användas.

## <a name="modify-a-volume"></a>Ändra en volym
Ändra en volym när du vill expandera den eller ändra de värdar som har åtkomst till volymen.

> [!IMPORTANT]
> * Om du ändrar volymens storlek på enheten måste ändras på värden samt volymens storlek.
> * Värden på klientsidan steg som beskrivs här är för Windows Server 2012 (2012R2). Procedurer för Linux eller andra värdoperativsystem är olika. Läsa dina värden operativsystemet anvisningarna när du ändrar volymen på en värd som kör ett annat operativsystem.
> 
> 

### <a name="to-modify-a-volume"></a>Så här ändrar du en volym
1. På den **enheter** sidan, Välj enheten, dubbelklicka på den och klicka sedan på den **Volymbehållare** fliken. Den här sidan visas i tabellformat alla volymbehållare som är kopplade till enheten.
2. Välj en volymbehållare och klicka på den om du vill visa en lista över alla volymer i behållaren.
3. På den **volymer** , Välj en volym och klickar på **ändra**.
4. I guiden Ändra volym under **grundläggande inställningar**, kan du göra följande:
   
   * Redigera den **namn** och **typen** om du vill ändra en nivåindelad volym till en arkivering volym genom att välja den **Använd volymen för mindre ofta använda arkiveringsdata** kryssruta Ändra segmentstorleken för deduplicering för din volym till 512 KB.
   * Öka den **etablerad kapacitet**. Den **Etableringskapacitet** kan bara ökas. Det går inte att komprimera en volym när den har skapats.
     
     > [!NOTE]
     > Du kan inte ändra volymbehållaren när den är tilldelad till en volym.
     > 
     > 
5. Under **ytterligare inställningar**, kan du göra följande:
   
   * Ändra ACRs, förutsatt att volymen är offline. Om volymen är online, behöver du kopplar från den först. Följer du stegen i [kopplar från en volym](#take-a-volume-offline) innan du ändrar ACR.
   * Ändra listan över ACRs efter att volymen är offline.
     
     > [!NOTE]
     > Du kan inte ändra den **aktiverar en standard säkerhetskopiering för den här volymen** alternativet för volymen.
     > 
     > 
6. Spara dina ändringar genom att klicka på kryssikonen ![kryssikon](./media/storsimple-manage-volumes/HCS_CheckIcon.png). Den klassiska Azure-portalen visas ett meddelande om uppdatering volym. Ett meddelande visas när volymen har uppdaterats.
7. Om du expanderar en volym, gör du följande på värddatorn för Windows:
   
   1. Gå till **Datorhantering** ->**Diskhantering**.
   2. Högerklicka på **Diskhantering** och välj **Skanna diskar**.
   3. I listan över diskar, väljer du den volym som du har uppdaterat, högerklicka och välj sedan **Utöka volym**. Guiden Utöka volym startas. Klicka på **Nästa**.
   4. Slutför guiden, Godkänn standardvärdena. När guiden har slutförts kan ska volymen visa den ökade storleken.

![Video tillgänglig](./media/storsimple-manage-volumes/Video_icon.png) **Video tillgänglig**

Om du vill se en video som visar hur du expanderar en volym, klickar du på [här](https://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume/).

## <a name="take-a-volume-offline"></a>Koppla från en volym
Du kan behöva koppla från en volym när du planerar att ändra eller ta bort den. När en volym är offline, är den inte tillgänglig för skrivskyddad åtkomst. Du måste ta volymen offline på värden samt på enheten. Utför följande steg om du vill koppla från en volym.

### <a name="to-take-a-volume-offline"></a>Att koppla från en volym
1. Kontrollera att den aktuella volymen inte används innan den tas offline.
2. Ta volymen offline på värden första. Detta tar bort alla potentiella risken för korrupta data på volymen. Specifikt steg finns anvisningar för operativsystemet för värden.
3. När värden är offline, vidta volymen på enheten offline genom att utföra följande steg:
   
   1. På den **enheter** sidan, Välj enheten, dubbelklicka på den och klicka sedan på den **Volymbehållare** fliken. Den **Volymbehållare** fliken visas i tabellformat alla volymbehållare som är kopplade till enheten.
   2. Välj en volymbehållare och klicka på den om du vill visa en lista över alla volymer i behållaren.
   3. Välj en volym och klicka på **ta offline**.
   4. Klicka på **Ja** när du uppmanas att bekräfta åtgärden. Nu bör volymen offline.
      
      När en volym är offline, de **Anslut** alternativet blir tillgängligt.

> [!NOTE]
> Den **ta Offline** kommandot skickar en begäran till enheten för att koppla från volymen. Om värdar fortfarande använder volymen, detta resulterar i avbrutna anslutningar, men inte koppla volymen misslyckas.
> 
> 

## <a name="delete-a-volume"></a>Ta bort en volym
> [!IMPORTANT]
> Du kan ta bort en volym endast om den är offline.
> 
> 

Utför följande steg för att ta bort en volym.

### <a name="to-delete-a-volume"></a>Ta bort en volym
1. På den **enheter** sidan, Välj enheten, dubbelklicka på den och klicka sedan på den **Volymbehållare** fliken.
2. Välj volymbehållaren som innehåller volymen som du vill ta bort. Klicka på volymbehållare att komma åt den **volymer** sidan.
3. Alla volymer som är associerade med den här behållaren visas i tabellformat. Kontrollera status för den volym som du vill ta bort. Om du vill ta bort volymen inte är offline, kopplar från den först följa stegen i [kopplar från en volym](#take-a-volume-offline).
4. När volymen är offline, klickar du på **ta bort** längst ned på sidan.
5. Klicka på **Ja** när du uppmanas att bekräfta åtgärden. Volymen tas bort och **volymer** sidan visas en uppdaterad lista över volymer i behållaren.

## <a name="monitor-a-volume"></a>Övervaka en volym
Övervakning av volym kan du samla in I/O-relaterade statistik för en volym. Övervakning är aktiverad som standard för första 32 volymer som du skapar. Övervakning av ytterligare volymer är inaktiverat som standard. Övervakning av klonade volymer är också inaktiverat som standard.

Utför följande steg om du vill aktivera eller inaktivera övervakning för en volym.

### <a name="to-enable-or-disable-volume-monitoring"></a>Aktivera eller inaktivera övervakning av volym
1. På den **enheter** sidan, Välj enheten, dubbelklicka på den och klicka sedan på den **Volymbehållare** fliken.
2. Välj volymbehållaren som volymen finns och klicka sedan på volymbehållare att komma åt den **volymer** sidan.
3. Alla volymer som är associerade med den här behållaren visas i tabellformat visas. Klicka och välj volymen eller volym kloning.
4. Längst ned på sidan klickar du på **ändra**.
5. I guiden Ändra volym under **grundläggande inställningar**väljer **aktivera** eller **inaktivera** från den **övervakning** listrutan.
   
    ![Ändra en volym grundläggande inställningar](./media/storsimple-manage-volumes/HCS_MonitorVolumeM.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [klona en StorSimple-volym](storsimple-clone-volume.md).
* Lär dig hur du [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

