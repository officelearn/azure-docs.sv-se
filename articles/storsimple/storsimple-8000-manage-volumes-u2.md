---
title: Hantera StorSimple-volymer (uppdatering 3) | Microsoft Docs
description: "Beskriver hur du lägger till, ändra, övervaka och ta bort StorSimple-volymer och hur du kopplar dem om det behövs."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/19/2017
ms.author: alkohli
ms.openlocfilehash: 09f4de79ab9b0cdfafd10c7c7c29b0f8e6304f14
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>Använda Enhetshanteraren för StorSimple-tjänsten för att hantera volymer (uppdatering 3 eller senare)

## <a name="overview"></a>Översikt

Den här självstudiekursen beskrivs hur du använda Enhetshanteraren för StorSimple-tjänsten för att skapa och hantera volymer på StorSimple 8000-serien-enheter med uppdatering 3 och senare.

Tjänsten StorSimple Device Manager är ett tillägg i Azure-portalen där du kan hantera din StorSimple-lösning från ett enda webbgränssnitt. Använda Azure-portalen för att hantera volymer på alla dina enheter. Du kan också skapa och hantera StorSimple-tjänster, hantera enheter, principer för säkerhetskopiering och säkerhetskopieringskatalogen och visa aviseringar.

## <a name="volume-types"></a>Volymtyper

StorSimple-volymer kan vara:

* **Lokalt fästa volymer**: Data i dessa volymer finns kvar på den lokala StorSimple-enheten vid alla tidpunkter.
* **Nivåindelade volymer**: Data i dessa volymer kan läcker över till molnet.

En arkivering volym är en typ av nivåindelad volym. Segmentstorleken för större deduplicering som används för arkivering volymer tillåter enheten att överföra större datasegment till molnet.

Om nödvändigt, kan du ändra volymen typen från lokal till nivåer eller från nivåer i lokala. Mer information finns på [ändra volymtypen av](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Lokalt fästa volymer

Lokalt fästa volymer är helt allokerade volymer som gör nivå inte data till molnet, garantier och säkerställer härigenom lokala för primära data, oberoende av molnet anslutningen. Data på lokalt fästa volymer är inte deduplicerad och komprimerade. dock dedupliceras ögonblicksbilder av lokalt fästa volymer. 

Lokalt fästa volymer är helt etablerad; Du måste därför ha tillräckligt med utrymme på enheten när de skapas. Du kan etablera lokalt fästa volymer upp till en maximal storlek på 8 TB på 8100 StorSimple-enheten och 20 TB på 8600-enhet. StorSimple reserverar återstående lokalt utrymme på enheten för ögonblicksbilder, metadata och databearbetning. Du kan öka storleken på en lokalt Fäst volym till maximalt tillgängligt utrymme, men du kan inte minska storleken på en volym skapas en gång.

När du skapar en lokalt Fäst volym reduceras det tillgängliga utrymmet för att skapa nivåindelade volymer. Den gäller även omvänt: Om du har befintliga nivåindelade volymer diskutrymme för att skapa lokalt fästa volymer blir lägre än de maximala gränsen som anges ovan. Mer information om lokala volymer som avser den [vanliga frågor och svar på lokalt fästa volymer](storsimple-8000-local-volume-faq.md).

### <a name="tiered-volumes"></a>Nivåindelade volymer

Nivåindelade volymer är tunt allokerade volymer där data som används ofta är lokalt på enheten och mindre ofta använda data automatiskt nivåer till molnet. Tunn allokering är en virtualiseringsteknik som tillgängligt lagringsutrymme visas överskrider fysiska resurser. I stället för att reservera tillräckligt med lagringsutrymme i förväg använder StorSimple tunn allokering för att allokera bara tillräckligt med utrymme för att uppfylla krav som ställs. Elastisk komponenternas egenskaper molnlagring underlättar den här metoden eftersom StorSimple kan öka eller minska lagringsutrymmet i molnet för att möta föränderliga behov.

Om du använder den nivåindelade volymen för arkiveringsdata, markerar du den **Använd volymen för mindre ofta använda arkiveringsdata** kryssrutan om du vill ändra segmentstorleken för deduplicering för din volym till 512 KB. Om du inte väljer det här alternativet använder den motsvarande nivåindelade volymen en segmentstorlek på 64 KB. En större segmentstorlek för deduplicering låter enheten snabba på överföring av segmentstorleken tillåter enheten att påskynda överföringen av stora mängder arkiveringsdata till molnet.


### <a name="provisioned-capacity"></a>Etablerad kapacitet

Se följande tabell för maximal etablerad kapacitet för varje typ av enhet och volymen. (Observera att lokalt fästa volymer inte är tillgängliga på en virtuell enhet.)

|  | Maximal nivåindelad volymstorlek | Maximalt lokalt Fäst volymens storlek |
| --- | --- | --- |
| **Fysiska enheter** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Virtuella enheter** | | |
| 8010 |30 TB |Saknas |
| 8020 |64 TB |Saknas |

## <a name="the-volumes-blade"></a>Bladet volymer

Den **volymer** bladet kan du hantera lagringsvolymer som etablerats på Microsoft Azure StorSimple-enheten för dina initierarna (servrarna). Den visar en lista över volymer på StorSimple-enheter som är anslutna till din tjänst.

 ![Volymsidan](./media/storsimple-8000-manage-volumes-u2/volumeslist.png)

En volym som består av en serie med attribut:

* **Volymnamn** – ett beskrivande namn som måste vara unikt och hjälper till att identifiera volymen. Det här namnet används också övervaka rapporter när du filtrerar på en viss volym. När volymen har skapats går inte att byta namn.
* **Status för** – kan vara online eller offline. Om en volym är offline är den inte synlig för initierarna (servrarna) som har behörighet att använda volymen.
* **Kapacitet** – anger den totala mängden data som kan lagras av initieraren (server). Lokalt fästa volymer finns på StorSimple-enheten och är helt etablerad. Nivåindelade volymer är tunt etablerad och data är deduplicerad. Med tunt allokerade volymer allokera inte enheten före fysiska lagringskapacitet internt eller i molnet enligt konfigurerade volymens kapacitet. Volymens kapacitet är allokerade och används på begäran.
* **Typen** – anger om volymen är **skiktindelade** (standard) eller **lokalt Fäst**.

Följ instruktionerna i den här självstudiekursen för att utföra följande uppgifter:

* Lägg till en volym 
* Ändra en volym 
* Ändra volymtypen av
* Ta bort en volym 
* Koppla från en volym 
* Övervaka en volym 

## <a name="add-a-volume"></a>Lägg till en volym

Du [har skapat en volym](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) under distributionen av enheten StorSimple 8000-serien. Lägger till en volym är en liknande procedur.

#### <a name="to-add-a-volume"></a>Att lägga till en volym

1. Välj din enhet i tabellistan med enheter på bladet **Enheter**. Klicka på **+ Lägg till volymen**.

    ![Lägg till en ny volym](./media/storsimple-8000-manage-volumes-u2/step5createvol1.png)

2. På bladet **Lägg till en volym**:
   
    1. Fältet **Välj enhet** fylls i automatiskt med din aktuella enhet.

    2. I listrutan väljer du den volymbehållare som du vill lägga till en volym i.

    3.  Anger du ett **namn** för volymen. När volymen har skapats kan du byta namn på volymen.

    4. Välj **Typ** för volymen i listrutan. För arbetsbelastningar som kräver lokala garantier, låg latens och hög prestanda, väljer du en **lokalt fäst** volym. För all övrig data, väljer du en **nivåindelad** volym. Om du använder volymen för arkiveringsdata, markerar du **Använd volymen för arkiveringsdata med låg åtkomstfrekvens**.
      
       En nivåindelad volym är tunt etablerad och kan skapas snabbt. Om du väljer **Använd volymen för arkiveringsdata med låg åtkomstfrekvens** för nivåindelad volym avsedd för arkivdata så ändras segmentstorleken för deduplicering för din volym till 512 KB. Om fältet inte är markerat använder den motsvarande nivåindelade volymen en segmentstorlek på 64 kB. En större segmentstorlek för deduplicering låter enheten snabba på överföring av segmentstorleken tillåter enheten att påskynda överföringen av stora mängder arkiveringsdata till molnet.
       
       En lokalt fäst volym etableras tjockt, vilket försäkrar att primärdata på volymen är lokalt belägen för enheten och inte läcker över till molnet.  Om du skapar en lokalt fäst volym letar enheten efter ledigt utrymme på de lokala nivåerna för att etablera volymen med önskad storlek. Att skapa en lokalt fäst volym kan innebära att läcka befintlig data från enheten till molnet och volymen kan ta lång tid att skapa. Den totala tiden beror på den etablerade volymens storlek, tillgänglig nätverksbandbredd och den data som finns på din enhet.

    5. Ange **etableringskapacitet** för volymen. Anteckna kapaciteten som finns tillgänglig baserat på den volymtyp som valts. Den angivna volymstorleken får inte överskrida det tillgängliga utrymmet.
      
       Du kan etablera lokalt fästa volymer upp till 8,5 TB, eller nivåindelade volymer upp till 200 TB på 8100-enheten. På den större 8600-enheten kan du etablera lokalt fästa volymer upp till 22,5 TB, eller nivåindelade volymer upp till 500 TB. Eftersom det krävs lokalt utrymme på enheten för att hålla arbetsuppsättningen med nivåindelade volymer påverkar skapandet av fästa volymer mängden utrymme som finns tillgängligt för att etablera nivåindelade volymer. Därför minskar utrymmet som är tillgängligt för att skapa nivåindelade volymer om du skapar en lokalt fixerad volym. Likaså om du skapar en nivåindelad volym minskar det tillgängliga utrymmet för att skapa en lokalt fäst volym.
      
       Om du etablerar en lokalt fäst volym på 8,5 TB (största tillåtna storleken) på din 8100-enhet har du använt upp allt lokalt tillgängligt utrymme för enheten. I så fall kan du inte skapa fler nivåindelade volymer eftersom det inte finns något lokalt utrymme ledigt på enheten för att lagra arbetsuppsättningen för den nivåindelade volymen. Befintliga nivåindelade volymer påverkar också tillgängligt utrymme. Om du exempelvis har en 8100-enhet som redan har nivåindelade volymer på 106 TB finns det bara 4 TB utrymme tillgängligt för lokalt fästa volymer.

    6. Klicka på pilen i fältet **Anslutna värdar**. 

        ![Anslutna värdar](./media/storsimple-8000-manage-volumes-u2/step5createvol2.png)

    7. I den **anslutna värdar** bladet Välj en befintlig ACR eller Lägg till en ny ACR. Om du väljer en ny ACR anger en **namn** för din ACR anger du den **iSCSI Qualified Name** (IQN) för Windows-värd. Om du inte har en IQN, går du till [Hämta IQN för en Windows Server-värd](#get-the-iqn-of-a-windows-server-host). Klicka på **Skapa**. En volym skapas med de angivna inställningarna.

        ![Klicka på Skapa](./media/storsimple-8000-manage-volumes-u2/step5createvol3.png)

Den nya volymen är nu redo att användas.

> [!NOTE]
> Om du skapar en lokalt Fäst volym och skapa sedan en annan lokalt Fäst volym omedelbart efteråt volym skapas jobb körs sekventiellt. Det första volym skapas jobbet måste avslutas innan nästa volym skapas jobb kan börja.

## <a name="modify-a-volume"></a>Ändra en volym

Ändra en volym när du vill expandera den eller ändra de värdar som har åtkomst till volymen.

> [!IMPORTANT]
> * Om du ändrar volymens storlek på enheten måste ändras på värden samt volymens storlek.
> * Värden på klientsidan steg som beskrivs här är för Windows Server 2012 (2012R2). Procedurer för Linux eller andra värdoperativsystem är olika. Läsa dina värden operativsystemet anvisningarna när du ändrar volymen på en värd som kör ett annat operativsystem.

#### <a name="to-modify-a-volume"></a>Så här ändrar du en volym

1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **Enheter**. Välj den enhet som har den volym som du vill ändra tabular listan över enheter. Klicka på **Inställningar > volymer**.

    ![Gå till bladet för volymer](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

2. Välj volymen tabular lista över volymer, och högerklicka om du vill anropa snabbmenyn. Välj **ta offline** att koppla från den volym som du vill ändra.

    ![Välj och kopplar från volymen](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. I den **ta offline** bladet Granska effekten av att volymen tas offline och välj motsvarande kryssruta. Se till att motsvarande volymen på värden är offline först. Information om hur du utför en volym som är offline på värdservern ansluten till StorSimple finns specifika anvisningar för operativsystemet. Klicka på **ta offline**.

    ![Granska effekten av att volym tas offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

4. När volymen är offline (som visas av volymstatusen), väljer du volymen och högerklicka om du vill anropa på snabbmenyn. Välj **ändra volym**.

    ![Välj Ändra volym](./media/storsimple-8000-manage-volumes-u2/modifyvol9.png)


5. I den **ändra volym** bladet kan du göra följande ändringar:
   
   1. Volymen **namn** kan inte redigeras.
   2. Konvertera den **typen** från lokalt Fäst till nivåer eller från nivåer att lokalt Fäst (se [ändra volymtypen av](#change-the-volume-type) mer information).
   3. Öka den **etablerad kapacitet**. Den **Etableringskapacitet** kan bara ökas. Det går inte att komprimera en volym när den har skapats.
   4. Under **anslutna värdar**, du kan ändra ACR. Om du vill ändra en ACR måste volymen vara offline.

       ![Granska effekten av att volym tas offline](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

5. Klicka på **spara** att spara ändringarna. Klicka på **Ja** när du uppmanas att bekräfta åtgärden. Azure portal visas en uppdatering volym-meddelande. Ett meddelande visas när volymen har uppdaterats.

    ![Granska effekten av att volym tas offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

7. Om du expanderar en volym, gör du följande på värddatorn för Windows:
   
   1. Gå till **Datorhantering** ->**Diskhantering**.
   2. Högerklicka på **Diskhantering** och välj **Skanna diskar**.
   3. I listan över diskar, väljer du den volym som du har uppdaterat, högerklicka och välj sedan **Utöka volym**. Guiden Utöka volym startas. Klicka på **Nästa**.
   4. Slutför guiden, Godkänn standardvärdena. När guiden har slutförts kan ska volymen visa den ökade storleken.
      
      > [!NOTE]
      > Om du expanderar en lokalt Fäst volym och sedan Fäst en annan lokalt volym omedelbart efteråt volym expansion jobb körs sekventiellt. Det första volym expansion jobbet måste avslutas innan nästa volym expansion jobb kan börja.
      

## <a name="change-the-volume-type"></a>Ändra volymtypen av

Du kan ändra volymtypen från nivåer att lokalt Fäst eller från Fäst lokalt till nivåindelade. Den här konverteringen får inte vara en frekventa förekomst.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Nivåer lokal volym konvertering överväganden

Några skäl för att konvertera en volym från är nivåer att lokalt Fäst:

* Lokala garantier avseende datatillgänglighet och prestanda
* Eliminering av molnet svarstiderna och molnet anslutningsproblem.

Dessa är oftast små befintliga volymer som du vill komma åt ofta. En lokalt Fäst volym etableras fullständigt när den skapas. 

Om du konverterar en nivåindelad volym till en lokalt Fäst volym, kontrollerar StorSimple att du har tillräckligt med utrymme på enheten innan den startar konverteringen. Om du har inte tillräckligt med utrymme du får ett felmeddelande och åtgärden avbryts. 

> [!NOTE]
> Innan du börjar konvertering från nivåer att lokalt Fäst, kontrollera att kraven på andra arbetsbelastningar du. 

Konvertering från en skiktad till en lokalt Fäst volym kan Enhetsprestanda försämras. Dessutom kan kan följande faktorer öka den tid det tar för att slutföra konverteringen:

* Det finns inte tillräckligt bandbredd.
* Det finns ingen aktuell säkerhetskopia.

Minimera effekterna som faktorerna kan ha:

* Granska dina bandbreddsbegränsning principer och se till att det finns en dedikerad 40 Mbit/s bandbredd.
* Schemalägga konvertering för låg belastning.
* Ta en ögonblicksbild i molnet innan du startar konverteringen.

Om du konverterar flera volymer (stöd för olika arbetsbelastningar) bör du prioritera konverteringen volym så att den högre prioritet volymer konverteras först. Du bör till exempel konvertera volymer som är värdar för virtuella datorer (VM) eller volymer med SQL arbetsbelastningar innan du konverterar volymer med filen resursen arbetsbelastningar.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Lokala nivåindelad volym konvertering överväganden

Du kanske vill ändra en lokalt Fäst volym till en nivåindelad volym om du behöver ytterligare utrymme för att etablera andra volymer. När du konverterar lokalt Fäst volym att nivåer, tillgänglig kapacitet på enheten ökar av storleken på den utgivna kapaciteten. Om problem med nätverksanslutningen hindrar konverteringen av en volym från den lokala typen till typen nivåindelade, lokal volym kommer att ha egenskaperna för en nivåindelad volym tills konverteringen har slutförts. Det beror på att vissa data kan ha hamnat i molnet. Den här spilled data fortsätter att uppta lokalt utrymme på enheten som inte går att frigöra förrän åtgärden har startats om och slutförts.

> [!NOTE]
> Konvertera en volym kan ta lite tid och du kan inte avbryta en konvertering när den har startat. Volymen är online under konverteringen, och du kan göra säkerhetskopior, men du kan inte expandera eller återställa volymen när konverteringen sker.


#### <a name="to-change-the-volume-type"></a>Att ändra volymtypen av

1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **Enheter**. Välj den enhet som har den volym som du vill ändra tabular listan över enheter. Klicka på **Inställningar > volymer**.

    ![Gå till bladet för volymer](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Välj volymen tabular lista över volymer, och högerklicka om du vill anropa snabbmenyn. Välj **ändra**.

    ![Välj Ändra snabbmenyn](./media/storsimple-8000-manage-volumes-u2/changevoltype2.png)

4. På den **ändra volym** bladet ändra volymtypen av genom att välja den nya typen från den **typen** listrutan.
   
   * Om du ändrar typ som **lokalt Fäst**, StorSimple ska kontrollera om det finns tillräckligt med kapacitet.
   * Om du ändrar typ som **skiktindelade** och kommer att användas volymen för arkiveringsdata, markerar den **Använd volymen för mindre ofta använda arkiveringsdata** kryssrutan.
   * Om du konfigurerar en lokalt Fäst volym som nivåer eller _vice versa_, visas följande meddelande.
   
    ![Ändra volym typen meddelande](./media/storsimple-8000-manage-volumes-u2/changevoltype3.png)

7. Klicka på **Spara** för att spara ändringarna. När du uppmanas att bekräfta, klickar du på **Ja** att starta konverteringsprocessen. 

    ![Spara och bekräfta](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

8. Azure-portalen visar ett meddelande för att skapa jobb som skulle uppdatera volymen. Klicka på meddelandet för att övervaka status för volymen konverteringsjobbet.

    ![Jobb för Volymkonvertering](./media/storsimple-8000-manage-volumes-u2/changevoltype5.png)

## <a name="take-a-volume-offline"></a>Koppla från en volym

Du kan behöva koppla från en volym när du planerar att ändra eller ta bort volymen. När en volym är offline, är den inte tillgänglig för skrivskyddad åtkomst. Du måste koppla volymen offline på värden och enheten.

#### <a name="to-take-a-volume-offline"></a>Att koppla från en volym

1. Kontrollera att den aktuella volymen inte används innan den tas offline.
2. Ta volymen offline på värden första. Detta tar bort alla potentiella risken för korrupta data på volymen. Specifikt steg finns anvisningar för operativsystemet för värden.
3. När värden är offline, vidta volymen på enheten offline genom att utföra följande steg:
   
    1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **Enheter**. Välj den enhet som har den volym som du vill ändra tabular listan över enheter. Klicka på **Inställningar > volymer**.

        ![Gå till bladet för volymer](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

    2. Välj volymen tabular lista över volymer, och högerklicka om du vill anropa snabbmenyn. Välj **ta offline** att koppla från den volym som du vill ändra.

        ![Välj och kopplar från volymen](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. I den **ta offline** bladet Granska effekten av att volymen tas offline och välj motsvarande kryssruta. Klicka på **ta offline**. 

    ![Granska effekten av att volym tas offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)
      
      Du meddelas när volymen är offline. Volymstatusen uppdateras även till Offline.
      
4. När en volym är offline, om du väljer volym och högerklicka, **Anslut** alternativet blir tillgängligt i snabbmenyn.

> [!NOTE]
> Den **ta Offline** kommandot skickar en begäran till enheten för att koppla från volymen. Om värdar fortfarande använder volymen, detta resulterar i avbrutna anslutningar, men inte koppla volymen misslyckas.

## <a name="delete-a-volume"></a>Ta bort en volym

> [!IMPORTANT]
> Du kan ta bort en volym endast om den är offline.

Utför följande steg för att ta bort en volym.

#### <a name="to-delete-a-volume"></a>Ta bort en volym

1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **Enheter**. Välj den enhet som har den volym som du vill ändra tabular listan över enheter. Klicka på **Inställningar > volymer**.

    ![Gå till bladet för volymer](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Kontrollera status för den volym som du vill ta bort. Om du vill ta bort volymen inte är offline, koppla från den först. Följ stegen i [kopplar från en volym](#take-a-volume-offline).
4. Efter att volymen är offline, väljer du volymen, högerklicka om du vill anropa snabbmenyn och välj sedan **ta bort**.

    ![Välj Ta bort från snabbmenyn](./media/storsimple-8000-manage-volumes-u2/deletevol1.png)

5. I den **ta bort** bladet, granska och markera kryssrutan mot effekten av att ta bort en volym. När du tar bort en volym, förloras alla data som finns på volymen. 

    ![Spara och bekräfta ändringar](./media/storsimple-8000-manage-volumes-u2/deletevol2.png)

6. När volymen tas bort, uppdaterar tabular lista över volymer som indikerar att tas bort.

    ![Uppdaterade Volymlista](./media/storsimple-8000-manage-volumes-u2/deletevol3.png)
   
   > [!NOTE]
   > Om du tar bort en lokalt Fäst volym kan det tillgängliga utrymmet för nya volymer inte uppdateras omedelbart. Tjänsten StorSimple Device Manager uppdaterar regelbundet lokalt tillgängligt utrymme. Vi rekommenderar att du vänta några minuter innan du försöker skapa den nya volymen.
   >
   > Dessutom om du tar bort en lokalt Fäst volym och ta bort en annan lokalt Fäst volym omedelbart efteråt volym borttagningen jobb körs sekventiellt. Det första volym tas bort jobbet måste avslutas innan nästa volym borttagning jobb kan börja.

## <a name="monitor-a-volume"></a>Övervaka en volym

Övervakning av volym kan du samla in I/O-relaterade statistik för en volym. Övervakning är aktiverad som standard för första 32 volymer som du skapar. Övervakning av ytterligare volymer är inaktiverat som standard. 

> [!NOTE]
> Övervakning av klonade volymer är inaktiverat som standard.


Utför följande steg om du vill aktivera eller inaktivera övervakning för en volym.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Aktivera eller inaktivera övervakning av volym

1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **Enheter**. Välj den enhet som har den volym som du vill ändra tabular listan över enheter. Klicka på **Inställningar > volymer**.
2. Välj volymen tabular lista över volymer, och högerklicka om du vill anropa snabbmenyn. Välj **ändra**.
3. I den **ändra volym** bladet för **övervakning** Välj **aktivera** eller **inaktivera** att aktivera eller inaktivera övervakning.

    ![Inaktivera övervakning](./media/storsimple-8000-manage-volumes-u2/monitorvol1.png) 

4. Klicka på **spara** och när du uppmanas att bekräfta på **Ja**. Azure-portalen visar ett meddelande för att uppdatera volymen och ett meddelande när volymen har uppdaterats.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [klona en StorSimple-volym](storsimple-8000-clone-volume-u2.md).
* Lär dig hur du [använda Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

