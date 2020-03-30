---
title: Hantera StorSimple-volymer (uppdatering 3)
description: I artikeln beskrivs hur du lägger till, ändrar, övervakar och tar bort StorSimple-volymer och hur du tar dem offline om det behövs.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: alkohli
ms.openlocfilehash: f32f8925bca33d90afa48071d0c0944ba63861cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254773"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>Använda Tjänsten StorSimple Device Manager för att hantera volymer (uppdatering 3 eller senare)

## <a name="overview"></a>Översikt

I den här självstudien beskrivs hur du använder Tjänsten StorSimple Device Manager för att skapa och hantera volymer på StorSimple 8000-serieenheter som kör uppdatering 3 och senare.

Tjänsten StorSimple Device Manager är ett tillägg i Azure-portalen som gör att du kan hantera din StorSimple-lösning från ett enda webbgränssnitt. Använd Azure-portalen för att hantera volymer på alla dina enheter. Du kan också skapa och hantera StorSimple-tjänster, hantera enheter, principer för säkerhetskopiering och säkerhetskopiering och visa aviseringar.

## <a name="volume-types"></a>Volymtyper

StorSimple volymer kan vara:

* **Lokalt fästa volymer**: Data i dessa volymer finns kvar på den lokala StorSimple-enheten hela tiden.
* **Nivåindelad volym**: Data i dessa volymer kan spilla till molnet.

En arkivvolym är en typ av nivåindelade volym. Den större dedupliceringssegmentstorleken som används för arkiveringsvolymer gör att enheten kan överföra större datasegment till molnet.

Om det behövs kan du ändra volymtypen från lokal till nivåindelad eller från nivåindelade till lokala. Mer information finns [i Ändra volymtyp](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Lokalt fixerade volymer

Lokalt fästa volymer är helt etablerade volymer som inte nivådata till molnet, vilket säkerställer lokala garantier för primära data, oberoende av molnanslutning. Data om lokalt fästa volymer är inte deduplicated och komprimerade; Ögonblicksbilder av lokalt fästa volymer dedupliceras dock. 

Lokalt fästa volymer är helt etablerade. Därför måste du ha tillräckligt med utrymme på enheten när du skapar dem. Du kan etablera lokalt fästa volymer upp till en maximal storlek på 8 TB på StorSimple 8100-enheten och 20 TB på 8600-enheten. StorSimple reserverar det återstående lokala utrymmet på enheten för ögonblicksbilder, metadata och databearbetning. Du kan öka storleken på en lokalt fäst volym till det maximala tillgängliga utrymmet, men du kan inte minska storleken på en volym när den har skapats.

När du skapar en lokalt fäst volym minskas det tillgängliga utrymmet för att skapa nivåindelade volymer. Det omvända är också sant: om du har befintliga nivåindelade volymer kommer det tillgängliga utrymmet för att skapa lokalt fästa volymer att vara lägre än de maximala gränser som anges ovan. Mer information om lokala volymer finns i vanliga [frågor om lokalt fästa volymer](storsimple-8000-local-volume-faq.md).

### <a name="tiered-volumes"></a>Nivåindelade volymer

Nivåindelad volymer är tunt etablerade volymer där data som används ofta förblir lokala på enheten och mindre frekvent använda data automatiskt nivåindelas till molnet. Tunn etablering är en virtualiseringsteknik där tillgängligt lagringsutrymme verkar överskrida fysiska resurser. I stället för att reservera tillräckligt med lagringsutrymme i förväg använder StorSimple tunn etablering för att allokera tillräckligt med utrymme för att uppfylla aktuella krav. Den elastiska karaktären hos molnlagring underlättar den här metoden eftersom StorSimple kan öka eller minska molnlagringen för att möta förändrade krav.

Om du använder den nivåindelad volymen för arkiveringsdata markerar du kryssrutan Använd den **här volymen för mindre ofta använda arkiveringsdata för** att ändra volymens dedupliceringssegmentstorlek för volymen till 512 kB. Om du inte väljer det här alternativet använder motsvarande nivåindelad volym en segmentstorlek på 64 kB. En större segmentstorlek för deduplicering låter enheten snabba på överföring av segmentstorleken tillåter enheten att påskynda överföringen av stora mängder arkiveringsdata till molnet.


### <a name="provisioned-capacity"></a>Etablerad kapacitet

Se följande tabell för maximal etablerad kapacitet för varje enhet och volymtyp. (Observera att lokalt fästa volymer inte är tillgängliga på en virtuell enhet.)

|  | Maximal volymstorlek på nivåer | Maximal lokalt fäst volymstorlek |
| --- | --- | --- |
| **Fysiska enheter** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Virtuella enheter** | | |
| 8010 |30 TB |Ej tillämpligt |
| 8020 |64 TB |Ej tillämpligt |

## <a name="the-volumes-blade"></a>Volymbladet

Med bladet **Volymer** kan du hantera de lagringsvolymer som etableras på Microsoft Azure StorSimple-enheten för dina initierare (servrar). Den visar en lista över volymer på StorSimple-enheter som är anslutna till din tjänst.

 ![Sidan Volymer](./media/storsimple-8000-manage-volumes-u2/volumeslist.png)

En volym består av en serie attribut:

* **Volymnamn** – Ett beskrivande namn som måste vara unikt och hjälper till att identifiera volymen. Det här namnet används också i övervakningsrapporter när du filtrerar på en viss volym. När volymen har skapats kan den inte byta namn.
* **Status** – Kan vara online eller offline. Om en volym är offline är den inte synlig för initierare (servrar) som har åtkomst att använda volymen.
* **Kapacitet** – anger den totala mängden data som kan lagras av initieraren (servern). Lokalt fästa volymer är helt etablerade och finns på StorSimple-enheten. Nivåindelad volymer etableras tunt och data dedupliceras. Med tunt etablerade volymer förallokerar enheten inte fysisk lagringskapacitet internt eller i molnet enligt konfigurerad volymkapacitet. Volymkapaciteten allokeras och förbrukas på begäran.
* **Typ** – Anger om volymen är **nivåindelade** (standard) eller **Lokalt fäst**.

Använd instruktionerna i den här självstudien för att utföra följande uppgifter:

* Lägga till en volym 
* Ändra en volym 
* Ändra volymtyp
* Ta bort en volym 
* Koppla från en volym 
* Övervaka en volym 

## <a name="add-a-volume"></a>Lägga till en volym

Du [skapade en volym](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) under distributionen av din StorSimple 8000-serieenhet. Att lägga till en volym är en liknande procedur.

#### <a name="to-add-a-volume"></a>Så här lägger du till en volym

1. Välj din enhet i tabellistan med enheter på bladet **Enheter**. Klicka på **+ Lägg till volymen**.

    ![Lägg till en ny volym](./media/storsimple-8000-manage-volumes-u2/step5createvol1.png)

2. På bladet **Lägg till en volym**:
   
    1. Fältet **Välj enhet** fylls i automatiskt med din aktuella enhet.

    2. I listrutan väljer du den volymcontainer som du vill lägga till en volym i.

    3.  Anger du ett **namn** för volymen. När volymen har skapats kan du inte byta namn på volymen.

    4. Välj **Typ** för volymen i listrutan. För arbetsbelastningar som kräver lokala garantier, låg latens och hög prestanda, väljer du en **lokalt fäst** volym. För all övrig data, väljer du en **nivåindelad** volym. Om du använder volymen för arkiveringsdata, markerar du **Använd volymen för arkiveringsdata med låg åtkomstfrekvens**.
      
       En nivåindelad volym är tunt etablerad och kan skapas snabbt. Om du väljer **Använd volymen för arkiveringsdata med låg åtkomstfrekvens** för nivåindelad volym avsedd för arkivdata så ändras segmentstorleken för deduplicering för din volym till 512 KB. Om fältet inte är markerat använder den motsvarande nivåindelade volymen en segmentstorlek på 64 kB. En större segmentstorlek för deduplicering låter enheten snabba på överföring av segmentstorleken tillåter enheten att påskynda överföringen av stora mängder arkiveringsdata till molnet.
       
       En lokalt fäst volym etableras tjockt, vilket försäkrar att primärdata på volymen är lokalt belägen för enheten och inte läcker över till molnet.  Om du skapar en lokalt fäst volym letar enheten efter ledigt utrymme på de lokala nivåerna för att etablera volymen med önskad storlek. Att skapa en lokalt fäst volym kan innebära att läcka befintlig data från enheten till molnet och volymen kan ta lång tid att skapa. Den totala tiden beror på den etablerade volymens storlek, tillgänglig nätverksbandbredd och den data som finns på din enhet.

    5. Ange **etableringskapacitet** för volymen. Anteckna kapaciteten som finns tillgänglig baserat på den volymtyp som valts. Den angivna volymstorleken får inte överskrida det tillgängliga utrymmet.
      
       Du kan etablera lokalt fästa volymer upp till 8,5 TB, eller nivåindelade volymer upp till 200 TB på 8100-enheten. På den större 8600-enheten kan du etablera lokalt fästa volymer upp till 22,5 TB, eller nivåindelade volymer upp till 500 TB. Eftersom det krävs lokalt utrymme på enheten för att hålla arbetsuppsättningen med nivåindelade volymer påverkar skapandet av fästa volymer mängden utrymme som finns tillgängligt för att etablera nivåindelade volymer. Därför minskar utrymmet som är tillgängligt för att skapa nivåindelade volymer om du skapar en lokalt fixerad volym. Likaså om du skapar en nivåindelad volym minskar det tillgängliga utrymmet för att skapa en lokalt fäst volym.
      
       Om du etablerar en lokalt fäst volym på 8,5 TB (största tillåtna storleken) på din 8100-enhet har du använt upp allt lokalt tillgängligt utrymme för enheten. I så fall kan du inte skapa fler nivåindelade volymer eftersom det inte finns något lokalt utrymme ledigt på enheten för att lagra arbetsuppsättningen för den nivåindelade volymen. Befintliga nivåindelade volymer påverkar också tillgängligt utrymme. Om du exempelvis har en 8100-enhet som redan har nivåindelade volymer på 106 TB finns det bara 4 TB utrymme tillgängligt för lokalt fästa volymer.

    6. Klicka på pilen i fältet **Anslutna värdar**. I bladet **Anslutna värdar** väljer du en befintlig ACR eller lägger till en ny ACR. Om du väljer en ny ACR anger du ett **namn** för acr:n och anger **IQN (IQN) iSCSI Qualified Name** (IQN). Om du inte har en IQN, går du till Hämta IQN för en Windows Server-värd. Klicka på **Skapa**. En volym skapas med de angivna inställningarna.

        ![Klicka på Skapa](./media/storsimple-8000-manage-volumes-u2/step5createvol3.png)

Din nya volym är nu klar att användas.

> [!NOTE]
> Om du skapar en lokalt fäst volym och sedan skapar en annan lokalt fäst volym direkt efteråt körs skapandejobb för volym sekventiellt. Det första jobbet för att skapa volym måste slutföras innan nästa jobb för att skapa volymer kan börja.

## <a name="modify-a-volume"></a>Ändra en volym

Ändra en volym när du behöver expandera den eller ändra de värdar som kommer åt volymen.

> [!IMPORTANT]
> * Om du ändrar volymstorleken på enheten måste volymstorleken också ändras på värden.
> * De steg på värdsidan som beskrivs här är för Windows Server 2012 (2012R2). Procedurerna för Linux eller andra värdoperativsystem kommer att vara annorlunda. Se värdoperativsystemets instruktioner när du ändrar volymen på en värd som kör ett annat operativsystem.

#### <a name="to-modify-a-volume"></a>Så här ändrar du en volym

1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **Enheter**. Välj den enhet som har den volym som du tänker ändra i tabelllistan för enheterna. Klicka på **Inställningar > volymer**.

    ![Gå till bladet Volymer](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

2. I tabelllistan över volymer väljer du volymen och högerklickar för att anropa snabbmenyn. Välj **Koppla från** om du vill ta volymen som du vill ändra offline.

    ![Markera och koppla från volymen](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. Granska effekten av att ta volymen offline i bladet **Ta offline** och markera motsvarande kryssruta. Kontrollera att motsvarande volym på värden är offline först. Information om hur du kopplar från en volym på värdservern som är ansluten till StorSimple finns i operativsystemspecifika instruktioner. Klicka på **Koppla från**.

    ![Granska effekten av att ta volymen offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

4. När volymen är offline (som visas av volymstatusen) markerar du volymen och högerklickar för att anropa snabbmenyn. Välj **Ändra volym**.

    ![Välj ändra volym](./media/storsimple-8000-manage-volumes-u2/modifyvol9.png)


5. I **bladet Ändra volym** kan du göra följande ändringar:
   
   1. Det går inte att redigera **volymnamnet.**
   2. Konvertera **typen** från lokalt fäst till nivåindelade eller från nivåindelade till lokalt fästa (se [Ändra volymtyp](#change-the-volume-type) för mer information).
   3. Öka den **etablerade kapaciteten**. Den **etablerade kapaciteten** kan bara ökas. Du kan inte krympa en volym när den har skapats.
   4. Under **Anslutna värdar**kan du ändra ACR. Om du vill ändra en ACR måste volymen vara offline.

       ![Granska effekten av att ta volymen offline](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

5. Klicka på **Spara** om du vill spara ändringarna. Klicka på **Ja** när du uppmanas att bekräfta åtgärden. Azure-portalen visar ett uppdateringsvolymmeddelande. Meddelandet kommer att visas när volymen har uppdaterats.

    ![Granska effekten av att ta volymen offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

7. Om du expanderar en volym utför du följande steg på värddatorn för Windows:
   
   1. Gå till**Diskhantering för** **datorhantering** ->.
   2. Högerklicka på **Diskhantering** och välj **Omsökningsdiskar**.
   3. Markera volymen som du uppdaterade i listan över diskar, högerklicka och välj sedan **Utöka volymen**. Guiden Utöka volym startar. Klicka på **Nästa**.
   4. Slutför guiden och acceptera standardvärdena. När guiden är klar ska volymen visa den ökade storleken.
      
      > [!NOTE]
      > Om du expanderar en lokalt fäst volym och sedan expanderar en annan lokalt fäst volym omedelbart efteråt körs jobben för volymexpansion sekventiellt. Det första volymexpansionsjobbet måste slutföras innan nästa volymexpansionsjobb kan påbörjas.
      

## <a name="change-the-volume-type"></a>Ändra volymtyp

Du kan ändra volymtypen från nivåindelade till lokalt fästa eller lokalt fästa till nivåindelad. Denna konvertering bör dock inte vara en frekvent förekomst.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Nivåindelad till lokala volymkonverteringsöverväganden

Några orsaker till att en volym konverteras från nivåindelade till lokalt fästa är:

* Lokala garantier för datatillgänglighet och prestanda
* Eliminering av molndyn-kort och molnanslutningsproblem.

Vanligtvis är det små befintliga volymer som du vill komma åt ofta. En lokalt fäst volym etableras fullständigt när den skapas. 

Om du konverterar en nivåindelad volym till en lokalt fäst volym kontrollerar StorSimple att du har tillräckligt med utrymme på enheten innan konverteringen startas. Om du inte har tillräckligt med utrymme får du ett felmeddelande och åtgärden avbryts. 

> [!NOTE]
> Innan du påbörjar en konvertering från nivåindelade till lokalt fästa, se till att du tar hänsyn till utrymmeskraven för dina andra arbetsbelastningar. 

Konvertering från en nivåindelad till en lokalt fäst volym kan påverka enhetens prestanda negativt. Dessutom kan följande faktorer öka den tid det tar att slutföra konverteringen:

* Det finns otillräcklig bandbredd.
* Det finns ingen aktuell säkerhetskopia.

Så här minimerar du de effekter som dessa faktorer kan ha:

* Granska dina principer för bandbreddsbegränsning och se till att det finns en dedikerad bandbredd på 40 Mbit/s.
* Schemalägg konverteringen för lågtrafik.
* Ta en ögonblicksbild av molnet innan du startar konverteringen.

Om du konverterar flera volymer (stöder olika arbetsbelastningar) bör du prioritera volymkonverteringen så att volymer med högre prioritet konverteras först. Du bör till exempel konvertera volymer som är värd för virtuella datorer (VMs) eller volymer med SQL-arbetsbelastningar innan du konverterar volymer med filresursarbetsbelastningar.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Lokala till nivåindelad volymkonverteringsöverväganden

Du kanske vill ändra en lokalt fäst volym till en nivåindelad volym om du behöver ytterligare utrymme för att etablera andra volymer. När du konverterar den lokalt fästa volymen till nivåindelade ökar den tillgängliga kapaciteten på enheten med storleken på den frisläppta kapaciteten. Om anslutningsproblem förhindrar konvertering av en volym från den lokala typen till den nivåindelad typen, visar den lokala volymen egenskaper för en nivåindelad volym tills konverteringen är klar. Detta beror på att vissa data kan ha spillts till molnet. Den här utspillda data fortsätter att uppta lokalt utrymme på enheten som inte kan frigöras förrän åtgärden startas om och slutförs.

> [!NOTE]
> Det kan ta lite tid att konvertera en volym och du kan inte avbryta en konvertering när den har startats. Volymen är online under konverteringen och du kan ta säkerhetskopior, men du kan inte expandera eller återställa volymen medan konverteringen sker.


#### <a name="to-change-the-volume-type"></a>Så här ändrar du volymtypen

1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **Enheter**. Välj den enhet som har den volym som du tänker ändra i tabelllistan för enheterna. Klicka på **Inställningar > volymer**.

    ![Gå till bladet Volymer](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. I tabelllistan över volymer väljer du volymen och högerklickar för att anropa snabbmenyn. Välj **Ändra**.

    ![Välj ändra från snabbmenyn](./media/storsimple-8000-manage-volumes-u2/changevoltype2.png)

4. Ändra volymtypen på bladet **Ändra volym** genom att välja den nya typen i listrutan **Typ.**
   
   * Om du ändrar typen till **Lokalt fäst**kontrollerar StorSimple om det finns tillräcklig kapacitet.
   * Om du ändrar typen till **Nivåindelade** och den här volymen ska användas för arkiveringsdata markerar du kryssrutan Använd den **här volymen för mindre frekventa arkiveringsdata.**
   * Om du konfigurerar en lokalt fäst volym som nivåindelad eller _vice versa_visas följande meddelande.
   
     ![Ändra meddelande om volymtyp](./media/storsimple-8000-manage-volumes-u2/changevoltype3.png)

7. Klicka på **Spara** för att spara ändringarna. När du uppmanas att bekräfta klickar du på **Ja** för att starta konverteringsprocessen. 

    ![Spara och bekräfta](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

8. Azure-portalen visar ett meddelande för att skapa jobb som skulle uppdatera volymen. Klicka på meddelandet för att övervaka status för volymkonverteringsjobbet.

    ![Jobb för volymkonvertering](./media/storsimple-8000-manage-volumes-u2/changevoltype5.png)

## <a name="take-a-volume-offline"></a>Koppla från en volym

Du kan behöva koppla från en volym när du planerar att ändra eller ta bort volymen. När en volym är offline är den inte tillgänglig för läs- och skrivåtkomst. Du måste koppla från volymen på värden och enheten.

#### <a name="to-take-a-volume-offline"></a>Så här kopplar du från en volym

1. Kontrollera att volymen i fråga inte används innan du kopplar från den.
2. Ta volymen offline på värden först. Detta eliminerar alla potentiella risker för data korruption på volymen. Mer information om hur du gör det finns i instruktionerna för värdoperativsystemet.
3. När värden är offline tar du volymen på enheten offline genom att utföra följande steg:
   
    1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **Enheter**. Välj den enhet som har den volym som du tänker ändra i tabelllistan för enheterna. Klicka på **Inställningar > volymer**.

        ![Gå till bladet Volymer](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

    2. I tabelllistan över volymer väljer du volymen och högerklickar för att anropa snabbmenyn. Välj **Koppla från** om du vill ta volymen som du vill ändra offline.

        ![Markera och koppla från volymen](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. Granska effekten av att ta volymen offline i bladet **Ta offline** och markera motsvarande kryssruta. Klicka på **Koppla från**. 

    ![Granska effekten av att ta volymen offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)
      
      Du meddelas när volymen är offline. Volymstatusen uppdateras också till Offline.
      
4. När en volym är offline blir alternativet Koppla **online** tillgängligt på snabbmenyn om du väljer volym och högerklickar.

> [!NOTE]
> Kommandot **Ta offline** skickar en begäran till enheten om att koppla från volymen. Om värdar fortfarande använder volymen resulterar detta i brutna anslutningar, men om volymen kopplas från misslyckas den inte.

## <a name="delete-a-volume"></a>Ta bort en volym

> [!IMPORTANT]
> Du kan bara ta bort en volym om den är offline.

Slutför följande steg för att ta bort en volym.

#### <a name="to-delete-a-volume"></a>Så här tar du bort en volym

1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **Enheter**. Välj den enhet som har den volym som du tänker ändra i tabelllistan för enheterna. Klicka på **Inställningar > volymer**.

    ![Gå till bladet Volymer](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Kontrollera status för den volym som du vill ta bort. Om volymen som du vill ta bort inte är offline tar du den offline först. Följ stegen i [Koppla från volymen](#take-a-volume-offline).
4. När volymen är offline markerar du volymen, högerklickar för att anropa snabbmenyn och väljer sedan **Ta bort**.

    ![Välj ta bort från snabbmenyn](./media/storsimple-8000-manage-volumes-u2/deletevol1.png)

5. Granska och markera kryssrutan mot effekten av att ta bort en volym i bladet **Ta bort.** När du tar bort en volym går alla data som finns på volymen förlorade. 

    ![Spara och bekräfta ändringar](./media/storsimple-8000-manage-volumes-u2/deletevol2.png)

6. När volymen har tagits bort uppdateras tabelllistan med volymer för att ange borttagningen.

    ![Uppdaterad volymlista](./media/storsimple-8000-manage-volumes-u2/deletevol3.png)
   
   > [!NOTE]
   > Om du tar bort en lokalt fäst volym kanske det tillgängliga utrymmet för nya volymer inte uppdateras omedelbart. StorSimple Device Manager-tjänsten uppdaterar det lokala utrymme som är tillgängligt med jämna mellanrum. Vi föreslår att du väntar några minuter innan du försöker skapa den nya volymen.
   >
   > Om du tar bort en lokalt fäst volym och sedan tar bort en annan lokalt fäst volym direkt efteråt körs dessutom jobben för borttagning av volymer sekventiellt. Det första jobbet för borttagning av volymen måste slutföras innan nästa jobb för borttagning av volymen kan påbörjas.

## <a name="monitor-a-volume"></a>Övervaka en volym

Volymövervakning gör att du kan samla in I/O-relaterad statistik för en volym. Övervakning är aktiverat som standard för de första 32 volymerna som du skapar. Övervakning av ytterligare volymer är inaktiverad som standard. 

> [!NOTE]
> Övervakning av klonade volymer är inaktiverad som standard.


Utför följande steg för att aktivera eller inaktivera övervakning för en volym.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Så här aktiverar eller inaktiverar du volymövervakning

1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **Enheter**. Välj den enhet som har den volym som du tänker ändra i tabelllistan för enheterna. Klicka på **Inställningar > volymer**.
2. I tabelllistan över volymer väljer du volymen och högerklickar för att anropa snabbmenyn. Välj **Ändra**.
3. Välj **Aktivera** eller **Inaktivera** för övervakning i **bladet Ändra volym** för **övervakning** för att aktivera eller inaktivera övervakning.

    ![Inaktivera övervakning](./media/storsimple-8000-manage-volumes-u2/monitorvol1.png) 

4. Klicka på **Spara** och klicka på **Ja**när du uppmanas att bekräfta. Azure-portalen visar ett meddelande för att uppdatera volymen och sedan ett meddelande om lyckade försök, när volymen har uppdaterats.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [klonar en StorSimple-volym](storsimple-8000-clone-volume-u2.md).
* Lär dig hur du [använder Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

