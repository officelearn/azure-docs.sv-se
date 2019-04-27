---
title: Hantera StorSimple-volymer (Aktualizace 3) | Microsoft Docs
description: Förklarar hur du lägger till, ändra, övervaka och ta bort StorSimple-volymer och hur du kopplar dem om det behövs.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/08/2017
ms.author: alkohli
ms.openlocfilehash: f7bfe41b4cdc9989c6b949011bc240275886b6f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60634904"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>Använda StorSimple Device Manager-tjänsten för att hantera volymer (uppdatering 3 eller senare)

## <a name="overview"></a>Översikt

Den här självstudien beskrivs hur du använder StorSimple Device Manager-tjänsten för att skapa och hantera volymer på StorSimple 8000 series-enheter som kör uppdatering 3 och senare.

StorSimple Device Manager-tjänsten är ett tillägg i Azure-portalen som låter dig hantera din StorSimple-lösning från ett enda webbgränssnitt. Använda Azure-portalen för att hantera volymer på alla dina enheter. Du kan också skapa och hantera StorSimple-tjänster, hantera enheter, säkerhetskopieringsprinciper och säkerhetskopieringskatalogen och visa aviseringar.

## <a name="volume-types"></a>Volymtyper

StorSimple-volymer kan vara:

* **Lokalt fixerade volymer**: Data i dessa volymer finns kvar på den lokala StorSimple-enheten vid alla tidpunkter.
* **Nivåindelade volymer**: Data i dessa volymer kan läcker över till molnet.

En arkivering volym är en typ av nivåindelad volym. Den större segmentstorleken för deduplicering används för arkivering volymer tillåter enheten att överföra större segment av data till molnet.

Om nödvändigt, kan du ändra volymen från lokal till nivåindelad eller från nivåindelad till lokal. Mer information går du till [ändra volymtyp](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Lokalt fixerade volymer

Lokalt fixerade volymer är helt allokerade volymer som gör inte Datacenter till molnet, garanterar och säkerställer härigenom lokala för primära data, oberoende av molnanslutning. Data på lokalt fixerade volymer som inte är deduplicerad och komprimerade. dock ska ögonblicksbilder av lokalt fixerade volymer dedupliceras. 

Lokalt fixerade volymer är helt etablerad; Därför måste du ha tillräckligt med utrymme på enheten när de skapas. Du kan etablera lokalt fästa volymer upp till en maximal storlek på 8 TB på StorSimple 8100-enheten och 20 TB på 8600-enheten. StorSimple reserverar återstående lokalt utrymme på enheten för ögonblicksbilder, metadata och databearbetning. Du kan öka storleken på en lokalt Fäst volym att maximalt tillgängligt utrymme, men du kan inte minska storleken på en volym skapas en gång.

När du skapar en lokalt Fäst volym minskar det tillgängliga utrymmet för att skapa nivåindelade volymer. Det gäller även omvänt: Om du har befintliga nivåindelade volymer, utrymme för att skapa lokalt fixerade volymer ska vara lägre än de maximala gränserna som anges ovan. Mer information om hur på lokala volymer i den [vanliga frågor om lokalt fixerade volymer](storsimple-8000-local-volume-faq.md).

### <a name="tiered-volumes"></a>Nivåindelade volymer

Nivåindelade volymer är tunt allokerade volymer där data som används ofta är lokalt på enheten och nivåindelade mindre ofta använda data automatiskt till molnet. Tunn allokering är en virtualiseringsteknik som tillgängligt lagringsutrymme visas att överskrida fysiska resurser. I stället för att reservera tillräckligt med utrymme i förväg, använder StorSimple tunn allokering för att allokera tillräckligt utrymme för att uppfylla krav som ställs. Elastiska natur molnlagring underlättar den här metoden eftersom StorSimple kan öka eller minska lagring i molnet för att möta föränderliga behov.

Om du använder den nivåindelade volymen för arkiveringsdata, markerar den **Använd volymen för arkivdata mindre ofta** kryssrutan för att ändra segmentstorleken för deduplicering för din volym till 512 KB. Om du inte väljer det här alternativet använder den motsvarande nivåindelade volymen en segmentstorlek på 64 KB. En större segmentstorlek för deduplicering låter enheten snabba på överföring av segmentstorleken tillåter enheten att påskynda överföringen av stora mängder arkiveringsdata till molnet.


### <a name="provisioned-capacity"></a>Etablerad kapacitet

Se tabellen nedan för högsta etablerade kapaciteten för varje typ av enhet och volym. (Observera att lokalt fixerade volymer är inte tillgängliga på en virtuell enhet.)

|  | Maximal nivåindelad volymstorlek | Maximalt lokalt Fäst volymstorlek |
| --- | --- | --- |
| **Fysiska enheter** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Virtuella enheter** | | |
| 8010 |30 TB |Gäller inte |
| 8020 |64 TB |Gäller inte |

## <a name="the-volumes-blade"></a>Bladet volymer

Den **volymer** bladet kan du hantera lagringsvolymer som har etablerats på Microsoft Azure StorSimple-enheten för dina initierarna (servrarna). Den visar en lista över volymer på StorSimple-enheter som är anslutna till din tjänst.

 ![Volymsidan](./media/storsimple-8000-manage-volumes-u2/volumeslist.png)

En volym består av en serie med attribut:

* **Volymnamn** – ett beskrivande namn som måste vara unikt och hjälper dig att identifiera volymen. Det här namnet används också i övervakning rapporter när du filtrerar på en viss volym. När volymen har skapats, kan inte byta namn.
* **Status för** – kan vara online eller offline. Om en volym är offline, är det inte synliga för initierarna (servrarna) som har åtkomst till att använda volymen.
* **Kapacitet** – anger den totala mängden data som kan lagras av initieraren (server). Lokalt fixerade volymer är helt etablerad och för StorSimple-enheten. Nivåindelade volymer är tunt etablerad och data är deduplicerad. Med tunt allokerade volymer allokera inte enheten förväg fysiska kapacitet internt eller i molnet enligt konfigurerade volymens kapacitet. Volymens kapacitet är allokerade och beroende på begäran.
* **Typ** – anger om volymen är **Nivåindelad** (standard) eller **lokalt fixerade**.

Följ instruktionerna i den här självstudien för att utföra följande uppgifter:

* Lägg till en volym 
* Ändra en volym 
* Ändra volymtyp
* Ta bort en volym 
* Koppla från en volym 
* Övervaka en volym 

## <a name="add-a-volume"></a>Lägg till en volym

Du [har skapat en volym](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) under distributionen av din enhet i StorSimple 8000-serien. Lägger till en volym är en liknande procedur.

#### <a name="to-add-a-volume"></a>Lägga till en volym

1. Välj din enhet i tabellistan med enheter på bladet **Enheter**. Klicka på **+ Lägg till volymen**.

    ![Lägg till en ny volym](./media/storsimple-8000-manage-volumes-u2/step5createvol1.png)

2. På bladet **Lägg till en volym**:
   
    1. Fältet **Välj enhet** fylls i automatiskt med din aktuella enhet.

    2. I listrutan väljer du den volymcontainer som du vill lägga till en volym i.

    3.  Anger du ett **namn** för volymen. När volymen har skapats kan du byta namn på volymen.

    4. Välj **Typ** för volymen i listrutan. För arbetsbelastningar som kräver lokala garantier, låg latens och hög prestanda, väljer du en **lokalt fäst** volym. För all övrig data, väljer du en **nivåindelad** volym. Om du använder volymen för arkiveringsdata, markerar du **Använd volymen för arkiveringsdata med låg åtkomstfrekvens**.
      
       En nivåindelad volym är tunt etablerad och kan skapas snabbt. Om du väljer **Använd volymen för arkiveringsdata med låg åtkomstfrekvens** för nivåindelad volym avsedd för arkivdata så ändras segmentstorleken för deduplicering för din volym till 512 KB. Om fältet inte är markerat använder den motsvarande nivåindelade volymen en segmentstorlek på 64 kB. En större segmentstorlek för deduplicering låter enheten snabba på överföring av segmentstorleken tillåter enheten att påskynda överföringen av stora mängder arkiveringsdata till molnet.
       
       En lokalt fäst volym etableras tjockt, vilket försäkrar att primärdata på volymen är lokalt belägen för enheten och inte läcker över till molnet.  Om du skapar en lokalt fäst volym letar enheten efter ledigt utrymme på de lokala nivåerna för att etablera volymen med önskad storlek. Att skapa en lokalt fäst volym kan innebära att läcka befintlig data från enheten till molnet och volymen kan ta lång tid att skapa. Den totala tiden beror på den etablerade volymens storlek, tillgänglig nätverksbandbredd och den data som finns på din enhet.

    5. Ange **etableringskapacitet** för volymen. Anteckna kapaciteten som finns tillgänglig baserat på den volymtyp som valts. Den angivna volymstorleken får inte överskrida det tillgängliga utrymmet.
      
       Du kan etablera lokalt fästa volymer upp till 8,5 TB, eller nivåindelade volymer upp till 200 TB på 8100-enheten. På den större 8600-enheten kan du etablera lokalt fästa volymer upp till 22,5 TB, eller nivåindelade volymer upp till 500 TB. Eftersom det krävs lokalt utrymme på enheten för att hålla arbetsuppsättningen med nivåindelade volymer påverkar skapandet av fästa volymer mängden utrymme som finns tillgängligt för att etablera nivåindelade volymer. Därför minskar utrymmet som är tillgängligt för att skapa nivåindelade volymer om du skapar en lokalt fixerad volym. Likaså om du skapar en nivåindelad volym minskar det tillgängliga utrymmet för att skapa en lokalt fäst volym.
      
       Om du etablerar en lokalt fäst volym på 8,5 TB (största tillåtna storleken) på din 8100-enhet har du använt upp allt lokalt tillgängligt utrymme för enheten. I så fall kan du inte skapa fler nivåindelade volymer eftersom det inte finns något lokalt utrymme ledigt på enheten för att lagra arbetsuppsättningen för den nivåindelade volymen. Befintliga nivåindelade volymer påverkar också tillgängligt utrymme. Om du exempelvis har en 8100-enhet som redan har nivåindelade volymer på 106 TB finns det bara 4 TB utrymme tillgängligt för lokalt fästa volymer.

    6. Klicka på pilen i fältet **Anslutna värdar**. I den **anslutna värdar** bladet Välj en befintlig ACR eller lägger till en ny ACR. Om du väljer en ny ACR, ange sedan en **namn** för din ACR, anger du den **iSCSI Qualified namnge** (IQN) för din Windows-värd. Om du inte har en IQN, går du till att hämta IQN för en Windows Server-värd. Klicka på **Skapa**. En volym skapas med de angivna inställningarna.

        ![Klicka på Skapa](./media/storsimple-8000-manage-volumes-u2/step5createvol3.png)

Ny volymen är nu redo att användas.

> [!NOTE]
> Om du skapar en lokalt Fäst volym och skapa sedan en annan lokalt fixerad volym omedelbart efteråt, skapa volymer som jobb körs sekventiellt. Det första volym skapa jobbet måste avslutas innan nästa jobb för skapande av volymen kan börja.

## <a name="modify-a-volume"></a>Ändra en volym

Ändra en volym när du vill expandera den eller ändra de värdar som har åtkomst till volymen.

> [!IMPORTANT]
> * Om du ändrar volymens storlek på enheten måste ändras på värden också volymens storlek.
> * Värd-sida stegen som beskrivs här är för Windows Server 2012 (2012 R2). Procedurer för Linux- eller andra värdoperativsystem är olika. Referera till värd operativsystemet instruktioner som förklarar när du ändrar volymen på en värd som kör ett annat operativsystem.

#### <a name="to-modify-a-volume"></a>Ändra en volym

1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **Enheter**. Välj den enhet som har den volym som du vill ändra i tabellistan med enheter. Klicka på **Inställningar > volymer**.

    ![Gå till bladet för volymer](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

2. I tabellistan över volymer väljer du volymen och högerklicka för att öppna snabbmenyn. Välj **ta offline** du koppla från volymen som du vill ändra.

    ![Välj och koppla från volymen](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. I den **ta offline** bladet läser du effekten av att koppla volymen och välj motsvarande kryssruta. Se till att den motsvarande volymen på värden är offline först. Information om hur du utför en volym som är offline på värdservern är anslutna till StorSimple finns specifika anvisningar för operativsystemet. Klicka på **ta offline**.

    ![Granska effekten av att koppla volym](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

4. När volymen är offline (som visas av volymstatusen), väljer du volymen och högerklicka för att öppna snabbmenyn. Välj **ändra volym**.

    ![Välj Ändra volym](./media/storsimple-8000-manage-volumes-u2/modifyvol9.png)


5. I den **ändra volym** bladet kan du göra följande ändringar:
   
   1. Volymen **namn** kan inte redigeras.
   2. Konvertera den **typ** från lokalt fixerade till nivåer eller från nivåindelad till lokalt fixerade (se [ändra volymtyp](#change-the-volume-type) för mer information).
   3. Öka den **etablerad kapacitet**. Den **Etableringskapacitet** kan bara ökas. Du kan inte minska en volym när den har skapats.
   4. Under **anslutna värdar**, du kan ändra i ACR. Om du vill ändra en ACR måste volymen vara offline.

       ![Granska effekten av att koppla volym](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

5. Klicka på **spara** att spara dina ändringar. Klicka på **Ja** när du uppmanas att bekräfta åtgärden. Azure-portalen visas en uppdatering volym-meddelande. Ett meddelande visas när volymen har uppdaterats.

    ![Granska effekten av att koppla volym](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

7. Om du expanderar en volym, gör du följande på din Windows-värddator:
   
   1. Gå till **Datorhantering** ->**Diskhantering**.
   2. Högerklicka på **Diskhantering** och välj **Skanna diskar**.
   3. I listan över diskar, väljer du den volym som du har uppdaterat, högerklicka och välj sedan **Utöka volym**. Guiden Utöka volym startas. Klicka på **Nästa**.
   4. Slutför guiden, Godkänn standardvärdena. När guiden har slutförts kan ska volymen visa ökade storlek.
      
      > [!NOTE]
      > Om du expanderar en lokalt Fäst volym och expandera sedan Fäst en annan lokalt volym omedelbart efteråt, volym expansion jobb körs sekventiellt. Det första volym expansion jobbet måste avslutas innan nästa volym expansion jobb kan börja.
      

## <a name="change-the-volume-type"></a>Ändra volymtyp

Du kan ändra volymtypen från nivåindelad till lokalt fixerade eller från fixerade lokalt till nivåindelad. Den här konverteringen får inte vara en frekventa förekomst.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Nivåindelad till lokal volym konvertering överväganden

Några skäl för att konvertera en volym från är nivåindelad till lokalt fixerade:

* Lokala garantier avseende tillgänglighet och prestanda
* Eliminering av molnet svarstider och molnet anslutningsproblem.

Detta är normalt små befintliga volymer som du vill komma åt ofta. En lokalt Fäst volym är helt etablerad när den skapas. 

Om du konverterar en nivåindelad volym till en lokalt Fäst volym, verifierar StorSimple att du har tillräckligt med utrymme på enheten innan den startar konverteringen. Om du har inte tillräckligt med utrymme, du får ett felmeddelande och åtgärden avbryts. 

> [!NOTE]
> Innan du påbörjar en konvertering från nivåindelad till lokalt fixerade, kontrollera att du överväger utrymmeskrav andra arbetsbelastningar. 

Konvertering från en nivåindelad till en lokalt Fäst volym kan påverka enhetens prestanda negativt. Dessutom kan kan följande faktorer öka den tid det tar för att slutföra konverteringen:

* Det finns inte tillräckligt med bandbredd.
* Det finns ingen aktuell säkerhetskopia.

Att minimera effekterna som de här faktorerna kan ha:

* Granska dina principer för bandbreddsbegränsning och se till att det finns en dedikerad 40 Mbit/s bandbredd.
* Schemalägga konverteringen för låg belastning.
* Ta en ögonblicksbild i molnet innan du startar konverteringen.

Om du konverterar flera volymer (stöder olika arbetsbelastningar), bör du prioritera konverteringen volym så att den högre prioritet volymer konverteras först. Du bör till exempel konvertera volymer som är värdar för virtuella datorer (VM) eller volymer med SQL-arbetsbelastningar innan du konverterar volymer med filen resurs arbetsbelastningar.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Lokal till nivåindelad volym konvertering överväganden

Du kanske vill ändra en lokalt Fäst volym till en nivåindelad volym om du behöver ytterligare utrymme för att etablera andra volymer. När du konverterar till nivåindelade, tillgänglig kapacitet på enheten ökar av storleken på den utgivna kapaciteten lokalt fixerad volym. Om problem med anslutningen hindrar konverteringen av en volym från typen lokal till nivåindelad typ., den lokala volymen kommer att ha egenskaperna för en nivåindelad volym tills konverteringen har slutförts. Det beror på att vissa data kan ha hamnat i molnet. Den här spilled data fortsätter att uppta lokalt utrymme på enheten som inte kan frigöras förrän åtgärden har startats om och har slutförts.

> [!NOTE]
> Konvertera en volym kan ta lite tid och du kan inte avbryta en konvertering när den har startat. Volymen är online under konverteringen, och du kan göra säkerhetskopior, men du kan inte expandera eller återställa volymen när konverteringen sker.


#### <a name="to-change-the-volume-type"></a>Ändra volym

1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **Enheter**. Välj den enhet som har den volym som du vill ändra i tabellistan med enheter. Klicka på **Inställningar > volymer**.

    ![Gå till bladet för volymer](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. I tabellistan över volymer väljer du volymen och högerklicka för att öppna snabbmenyn. Välj **ändra**.

    ![Välj Ändra snabbmenyn](./media/storsimple-8000-manage-volumes-u2/changevoltype2.png)

4. På den **ändra volym** bladet ändra volymtyp genom att välja den nya typen från den **typ** listrutan.
   
   * Om du ändrar typen till **lokalt fixerade**, StorSimple ska kontrollera om det finns tillräckligt med kapacitet.
   * Om du ändrar typen till **Nivåindelad** och används den här volymen för arkiveringsdata, markerar den **Använd volymen för arkivdata mindre ofta** markerar du kryssrutan.
   * Om du konfigurerar en lokalt Fäst volym som nivåindelade eller _vice versa_, visas följande meddelande.
   
     ![Ändra volym typ av meddelande](./media/storsimple-8000-manage-volumes-u2/changevoltype3.png)

7. Klicka på **Spara** för att spara ändringarna. När du uppmanas att bekräfta klickar du på **Ja** att starta processen. 

    ![Spara och bekräfta](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

8. Azure-portalen visar ett meddelande för att skapa för jobb som skulle uppdatera volymen. Klicka på meddelandet för att övervaka status för volymomvandlingen.

    ![Jobb för Volymkonvertering](./media/storsimple-8000-manage-volumes-u2/changevoltype5.png)

## <a name="take-a-volume-offline"></a>Koppla från en volym

Du kan behöva koppla från en volym när du planerar att ändra eller ta bort volymen. När en volym är offline, är det inte tillgänglig för läs-/ skrivåtkomst. Du måste koppla frånkoppla volymen på värden och på enheten.

#### <a name="to-take-a-volume-offline"></a>Att koppla från en volym

1. Kontrollera att volymen i fråga inte är i användning innan du koppla från den.
2. Ta frånkoppla volymen på värden första. Detta eliminerar alla potentiella risken för korrupta data på volymen. Detaljerade anvisningar om hur, läser du anvisningarna för din värdoperativsystemet.
3. När värden är offline kan du vidta volymen på enheten offline genom att utföra följande steg:
   
    1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **Enheter**. Välj den enhet som har den volym som du vill ändra i tabellistan med enheter. Klicka på **Inställningar > volymer**.

        ![Gå till bladet för volymer](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

    2. I tabellistan över volymer väljer du volymen och högerklicka för att öppna snabbmenyn. Välj **ta offline** du koppla från volymen som du vill ändra.

        ![Välj och koppla från volymen](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. I den **ta offline** bladet läser du effekten av att koppla volymen och välj motsvarande kryssruta. Klicka på **ta offline**. 

    ![Granska effekten av att koppla volym](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)
      
      Du meddelas när volymen är offline. Volymstatusen uppdateras även till Offline.
      
4. När en volym är offline, om du väljer volym och högerklicka, **Anslut** alternativet blir tillgängligt i snabbmenyn.

> [!NOTE]
> Den **ta Offline** kommando skickar en begäran till enheten för att koppla från volymen. Om värdar fortfarande använder volymen, detta resulterar i brutna anslutningar, men inte koppla från volymen misslyckas.

## <a name="delete-a-volume"></a>Ta bort en volym

> [!IMPORTANT]
> Du kan ta bort en volym endast om den är offline.

Utför följande steg för att ta bort en volym.

#### <a name="to-delete-a-volume"></a>Att ta bort en volym

1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **Enheter**. Välj den enhet som har den volym som du vill ändra i tabellistan med enheter. Klicka på **Inställningar > volymer**.

    ![Gå till bladet för volymer](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Kontrollera status på den volym som du vill ta bort. Om du vill ta bort volymen inte är offline, koppla från den först. Följ stegen i [koppla från en volym](#take-a-volume-offline).
4. När volymen är offline, väljer du volymen, högerklicka för att öppna snabbmenyn och välj sedan **ta bort**.

    ![Välj Ta bort från snabbmenyn](./media/storsimple-8000-manage-volumes-u2/deletevol1.png)

5. I den **ta bort** bladet, granska och markera kryssrutan med effekten av att ta bort en volym. När du tar bort en volym, förloras alla data som finns på volymen. 

    ![Spara och bekräfta ändringar](./media/storsimple-8000-manage-volumes-u2/deletevol2.png)

6. När volymen har tagits bort, uppdaterar listan med volymer som indikerar att borttagningen.

    ![Uppdaterade volymlistan](./media/storsimple-8000-manage-volumes-u2/deletevol3.png)
   
   > [!NOTE]
   > Om du tar bort en lokalt Fäst volym kan det tillgängliga utrymmet för nya volymer inte uppdateras direkt. StorSimple Device Manager-tjänsten uppdaterar regelbundet lokalt tillgängligt utrymme. Vi rekommenderar att du vänta ett par minuter innan du försöker skapa den nya volymen.
   >
   > Även om du tar bort en lokalt Fäst volym och ta bort en annan lokalt Fäst volym omedelbart därefter volym borttagningen jobb körs sekventiellt. Det första volym borttagning av jobbet måste avslutas innan nästa volym borttagning av jobb kan börja.

## <a name="monitor-a-volume"></a>Övervaka en volym

Övervakning av volymen kan du samla in I/O-statistik för en volym. Övervakning är aktiverat som standard för de första 32 installerade volymerna som du skapar. Övervakning av ytterligare volymer är inaktiverat som standard. 

> [!NOTE]
> Övervakning av klonade volymer är inaktiverat som standard.


Utför följande steg för att aktivera eller inaktivera övervakning för en volym.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Aktivera eller inaktivera övervakning av volymen

1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **Enheter**. Välj den enhet som har den volym som du vill ändra i tabellistan med enheter. Klicka på **Inställningar > volymer**.
2. I tabellistan över volymer väljer du volymen och högerklicka för att öppna snabbmenyn. Välj **ändra**.
3. I den **ändra volym** bladet för **övervakning** Välj **aktivera** eller **inaktivera** att aktivera eller inaktivera övervakning.

    ![Inaktivera övervakning](./media/storsimple-8000-manage-volumes-u2/monitorvol1.png) 

4. Klicka på **spara** och när du uppmanas att bekräfta klickar du på **Ja**. Azure-portalen visar ett meddelande för att uppdatera volymen och sedan ett meddelande när volymen har uppdaterats.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [klona en StorSimple-volym](storsimple-8000-clone-volume-u2.md).
* Lär dig hur du [använda StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

