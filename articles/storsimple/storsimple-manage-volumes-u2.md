---
title: Hantera din StorSimple-volymer (U2) | Microsoft Docs
description: "Beskriver hur du lägger till, ändra, övervaka och ta bort StorSimple-volymer och hur du kopplar dem om det behövs."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 57896932-0aa5-4805-970c-d13403ae7551
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/28/2016
ms.author: alkohli
ms.openlocfilehash: a61c57cd74a0df8363648dd8df40e433b0e6489d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-volumes-update-2"></a>Använda StorSimple Manager-tjänsten för att hantera volymer (uppdatering 2)
[!INCLUDE [storsimple-version-selector-manage-volumes](../../includes/storsimple-version-selector-manage-volumes.md)]

## <a name="overview"></a>Översikt
Den här självstudiekursen beskrivs hur du använda StorSimple Manager-tjänsten för att skapa och hantera volymer på StorSimple-enheten och virtuella StorSimple-enheten med uppdatering 2.

StorSimple Manager-tjänsten är ett tillägg i den klassiska Azure-portalen där du kan hantera din StorSimple-lösning från ett enda webbgränssnitt. Utöver att hantera volymer, kan du använda StorSimple Manager-tjänsten för att skapa och hantera StorSimple-tjänster, visa och hantera enheter, Visa aviseringar, och visa och hantera principer för säkerhetskopiering och katalogen för säkerhetskopieringen.

## <a name="volume-types"></a>Volymtyper
StorSimple-volymer kan vara:

* **Lokalt fästa volymer**: Data i dessa volymer finns kvar på den lokala StorSimple-enheten vid alla tidpunkter.
* **Nivåindelade volymer**: Data i dessa volymer kan läcker över till molnet.

En arkivering volym är en typ av nivåindelad volym. Segmentstorleken för större deduplicering som används för arkivering volymer tillåter enheten att överföra större datasegment till molnet. 

Om nödvändigt, kan du ändra volymen typen från lokal till nivåer eller från nivåer i lokala. Mer information finns på [ändra volymtypen av](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Lokalt fästa volymer
Lokalt fästa volymer är helt allokerade volymer som gör nivå inte data till molnet, garantier och säkerställer härigenom lokala för primära data, oberoende av molnet anslutningen. Data på lokalt fästa volymer är inte deduplicerad och komprimerade. dock dedupliceras ögonblicksbilder av lokalt fästa volymer. 

Lokalt fästa volymer är helt etablerad; Du måste därför ha tillräckligt med utrymme på enheten när de skapas. Du kan etablera lokalt fästa volymer upp till en maximal storlek på 8 TB på 8100 StorSimple-enheten och 20 TB på 8600-enhet. StorSimple reserverar återstående lokalt utrymme på enheten för ögonblicksbilder, metadata och databearbetning. Du kan öka storleken på en lokalt Fäst volym till maximalt tillgängligt utrymme, men du kan inte minska storleken på en volym skapas en gång.

När du skapar en lokalt Fäst volym reduceras det tillgängliga utrymmet för att skapa nivåindelade volymer. Den gäller även omvänt: Om du har befintliga nivåindelade volymer diskutrymme för att skapa lokalt fästa volymer blir lägre än de maximala gränsen som anges ovan. Mer information om lokala volymer som avser den [vanliga frågor och svar på lokalt fästa volymer](storsimple-local-volume-faq.md).   

### <a name="tiered-volumes"></a>Nivåindelade volymer
Nivåindelade volymer är tunt allokerade volymer där data som används ofta är lokalt på enheten och mindre ofta använda data automatiskt nivåer till molnet. Tunn allokering är en virtualiseringsteknik som tillgängligt lagringsutrymme visas överskrider fysiska resurser. I stället för att reservera tillräckligt med lagringsutrymme i förväg använder StorSimple tunn allokering för att allokera bara tillräckligt med utrymme för att uppfylla krav som ställs. Elastisk komponenternas egenskaper molnlagring underlättar den här metoden eftersom StorSimple kan öka eller minska lagringsutrymmet i molnet för att möta föränderliga behov.

Om du använder den nivåindelade volymen för arkiveringsdata, väljer du den **Använd volymen för mindre ofta använda arkiveringsdata** kryssrutan ändras segmentstorleken för deduplicering för din volym till 512 KB. Om du inte väljer det här alternativet använder den motsvarande nivåindelade volymen en segmentstorlek på 64 KB. En större segmentstorlek för deduplicering låter enheten snabba på överföring av segmentstorleken tillåter enheten att påskynda överföringen av stora mängder arkiveringsdata till molnet.

> [!NOTE]
> Arkivering volymer som har skapats med en före uppdateringen 2 version av StorSimple kommer att importeras som har delats med arkivering kryssrutan är markerad.
> 
> 

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

## <a name="the-volumes-page"></a>Sidan volymer
Den **volymer** sidan kan du hantera lagringsvolymer som etablerats på Microsoft Azure StorSimple-enheten för dina initierarna (servrarna). Den visar en lista över volymer på StorSimple-enheten.

 ![Volymsidan](./media/storsimple-manage-volumes-u2/VolumePage.png)

En volym som består av en serie med attribut:

* **Volymnamn** – ett beskrivande namn som måste vara unikt och hjälper till att identifiera volymen. Det här namnet används också övervaka rapporter när du filtrerar på en viss volym.
* **Status för** – kan vara online eller offline. Om en volym är offline är den inte synlig för initierarna (servrarna) som har behörighet att använda volymen.
* **Kapacitet** – anger den totala mängden data som kan lagras av initieraren (server). Lokalt fästa volymer finns på StorSimple-enheten och är helt etablerad. Nivåindelade volymer är tunt etablerad och data är deduplicerad. Med tunt allokerade volymer allokera inte enheten före fysiska lagringskapacitet internt eller i molnet enligt konfigurerade volymens kapacitet. Volymens kapacitet är allokerade och används på begäran.
* **Typen** – anger om volymen är **skiktindelade** (standard) eller **lokalt Fäst**.
* **Säkerhetskopiering** – anger om det finns en standardprincip för säkerhetskopiering för volymen.
* **Åtkomst** – anger initierarna (servrarna) som har åtkomst till den här volymen. Initierare som inte är medlemmar i åtkomstkontrollpost (ACR) som är kopplad till volymen kan inte se volymen.
* **Övervaka** – anger huruvida en volym som övervakas. En volym har övervakning aktiverad som standard när den skapas. Övervakning kommer dock, inaktiveras för en volym kloning. Om du vill aktivera övervakning av en volym, följ instruktionerna i [övervaka en volym](#monitor-a-volume). 

Följ instruktionerna i den här självstudiekursen för att utföra följande uppgifter:

* Lägg till en volym 
* Ändra en volym 
* Ändra volymtypen av
* Ta bort en volym 
* Koppla från en volym 
* Övervaka en volym 

## <a name="add-a-volume"></a>Lägg till en volym
Du [har skapat en volym](storsimple-deployment-walkthrough-u2.md#step-6-create-a-volume) under distributionen av din StorSimple-lösning. Lägger till en volym är en liknande procedur.

#### <a name="to-add-a-volume"></a>Att lägga till en volym
1. På den **enheter** sidan, Välj enheten, dubbelklicka på den och klicka sedan på den **Volymbehållare** fliken.
2. Välj en volymbehållare i listan och dubbelklicka på den för att komma åt de volymer som är kopplade till behållaren.
3. Klicka på **Lägg till** längst ned på sidan. Lägg till en volym startas.
   
     ![Lägg till volym slutförs grundläggande inställningar](./media/storsimple-manage-volumes-u2/TieredVolEx.png)
4. I guiden Lägg till en volym, i **Grundläggande inställningar**, gör du följande:
   
   1. Ange ett **namn** för volymen.
   2. Välj en **användningstyp** från den nedrullningsbara listan. Arbetsbelastningar som kräver att data ska vara tillgängliga lokalt på enheten när som helst, Välj **lokalt Fäst**. Alla andra typer av data, Välj **skiktindelade**. (**Skiktindelade** är standard.)
   3. Om du har valt **skiktindelade** i steg 2, kan du välja den **Använd volymen för mindre ofta använda arkiveringsdata** kryssrutan för att konfigurera en Arkiverings-volym.
   4. Ange den **Etableringskapacitet** för volymen i GB eller TB. Se [etablerad kapacitet](#provisioned-capacity) för maximal storlek för varje typ av enhet och volymen. Titta på den **tillgänglig kapacitet** att avgöra hur mycket lagringsutrymme som är tillgängligt på enheten.
5. Klicka på pilikonen![Pilikonen](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png). Om du konfigurerar en lokalt Fäst volym, visas följande meddelande.
   
    ![Ändra volym typen meddelande](./media/storsimple-manage-volumes-u2/LocalVolEx.png)
6. Klicka på pilikonen ![pilikonen](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png)igen för att gå till den **ytterligare inställningar** sidan.
   
    ![Lägg till ytterligare inställningar för volym-guiden](./media/storsimple-manage-volumes-u2/AddVolume2.png)<br>
7. Under **ytterligare inställningar**, lägga till en ny åtkomstkontrollpost (ACR):
   
   1. Välj en åtkomstkontrollpost (ACR) från den nedrullningsbara listan. Alternativt kan du lägga till en ny ACR. ACRs avgör vilka värdar som kan komma åt dina volymer genom att matcha värden IQN med som anges i posten. Om du inte anger en ACR visas följande meddelande.
      
        ![Ange ACR](./media/storsimple-manage-volumes-u2/SpecifyACR.png)
   2. Vi rekommenderar att du väljer den **aktiverar en standard säkerhetskopiering för den här volymen** kryssrutan.
   3. Klicka på kryssikonen ![Kryssikon](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) att skapa volymen med de angivna inställningarna.

Den nya volymen är nu redo att användas.

> [!NOTE]
> Om du skapar en lokalt Fäst volym och skapa sedan en annan lokalt Fäst volym omedelbart efteråt volym skapas jobb körs sekventiellt. Det första volym skapas jobbet måste avslutas innan nästa volym skapas jobb kan börja.
> 
> 

## <a name="modify-a-volume"></a>Ändra en volym
Ändra en volym när du vill expandera den eller ändra de värdar som har åtkomst till volymen.

> [!IMPORTANT]
> * Om du ändrar volymens storlek på enheten måste ändras på värden samt volymens storlek. 
> * Värden på klientsidan steg som beskrivs här är för Windows Server 2012 (2012R2). Procedurer för Linux eller andra värdoperativsystem är olika. Läsa dina värden operativsystemet anvisningarna när du ändrar volymen på en värd som kör ett annat operativsystem. 
> 
> 

#### <a name="to-modify-a-volume"></a>Så här ändrar du en volym
1. På den **enheter** sidan, Välj enheten, dubbelklicka på den och klicka sedan på den **Volymbehållare** fliken.
2. Välj en volymbehållare i listan och dubbelklicka på den om du vill visa de volymer som är kopplade till behållaren.
3. Välj en volym och längst ned på sidan klickar du på **ändra**. Guiden Ändra volym startas.
4. I guiden Ändra volym under **grundläggande inställningar**, kan du göra följande:
   
   * Redigera den **namn**.
   * Omvandla den **användningstyp** från lokalt Fäst till nivåer eller från nivåer att lokalt Fäst (se [ändra volymtypen av](#change-the-volume-type) mer information).
   * Öka den **etablerad kapacitet**. Den **Etableringskapacitet** kan bara ökas. Det går inte att komprimera en volym när den har skapats.
5. Under **ytterligare inställningar**, du kan ändra ACR, förutsatt att volymen är offline. Om volymen är online, behöver du kopplar från den först. Följer du stegen i [kopplar från en volym](#take-a-volume-offline) innan du ändrar ACR.
   
   > [!NOTE]
   > Du kan inte ändra den **aktiverar en standard säkerhetskopiering** alternativet för volymen.
   > 
   > 
6. Spara dina ändringar genom att klicka på kryssikonen ![kryssikon](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png). Den klassiska Azure-portalen visas ett meddelande om uppdatering volym. Ett meddelande visas när volymen har uppdaterats.
7. Om du expanderar en volym, gör du följande på värddatorn för Windows:
   
   1. Gå till **Datorhantering** ->**Diskhantering**.
   2. Högerklicka på **Diskhantering** och välj **Skanna diskar**.
   3. I listan över diskar, väljer du den volym som du har uppdaterat, högerklicka och välj sedan **Utöka volym**. Guiden Utöka volym startas. Klicka på **Nästa**.
   4. Slutför guiden, Godkänn standardvärdena. När guiden har slutförts kan ska volymen visa den ökade storleken.
      
      > [!NOTE]
      > Om du expanderar en lokalt Fäst volym och sedan Fäst en annan lokalt volym omedelbart efteråt volym expansion jobb körs sekventiellt. Det första volym expansion jobbet måste avslutas innan nästa volym expansion jobb kan börja.
      > 
      > 

![Video tillgänglig](./media/storsimple-manage-volumes-u2/Video_icon.png) **Video tillgänglig**

Om du vill se en video som visar hur du expanderar en volym, klickar du på [här](https://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume/).

## <a name="change-the-volume-type"></a>Ändra volymtypen av
Du kan ändra volymtypen från nivåer att lokalt Fäst eller från Fäst lokalt till nivåindelade. Den här konverteringen får inte vara en frekventa förekomst. Några skäl för att konvertera en volym från är nivåer att lokalt Fäst:

* Lokala garantier avseende datatillgänglighet och prestanda
* Eliminering av molnet svarstiderna och molnet anslutningsproblem.

Dessa är oftast små befintliga volymer som du vill komma åt ofta. En lokalt Fäst volym etableras fullständigt när den skapas. Om du konverterar en nivåindelad volym till en lokalt Fäst volym, kontrollerar StorSimple att du har tillräckligt med utrymme på enheten innan den startar konverteringen. Om du har inte tillräckligt med utrymme du får ett felmeddelande och åtgärden avbryts. 

> [!NOTE]
> Innan du börjar konvertering från nivåer att lokalt Fäst, kontrollera att kraven på andra arbetsbelastningar du. 
> 
> 

Du kanske vill ändra en lokalt Fäst volym till en nivåindelad volym om du behöver ytterligare utrymme för att etablera andra volymer. När du konverterar lokalt Fäst volym att nivåer, tillgänglig kapacitet på enheten ökar av storleken på den utgivna kapaciteten. Om problem med nätverksanslutningen hindrar konverteringen av en volym från den lokala typen till typen nivåindelade, lokal volym kommer att ha egenskaperna för en nivåindelad volym tills konverteringen har slutförts. Det beror på att vissa data kan ha hamnat i molnet. Den här spilled data fortsätter att uppta lokalt utrymme på enheten som inte går att frigöra förrän åtgärden har startats om och slutförts.

> [!NOTE]
> Konvertera en volym kan ta lite tid och du kan inte avbryta en konvertering när den har startat. Volymen är online under konverteringen, och du kan göra säkerhetskopior, men du kan inte expandera eller återställa volymen när konverteringen sker.  
> 
> 

Konvertering från en skiktad till en lokalt Fäst volym kan Enhetsprestanda försämras. Dessutom kan kan följande faktorer öka den tid det tar för att slutföra konverteringen:

* Det finns inte tillräckligt bandbredd.
* Det finns ingen aktuell säkerhetskopia.

Minimera effekterna som faktorerna kan ha:

* Granska dina bandbreddsbegränsning principer och se till att det finns en dedikerad 40 Mbit/s bandbredd.
* Schemalägga konvertering för låg belastning.
* Ta en ögonblicksbild i molnet innan du startar konverteringen.

Om du konverterar flera volymer (stöd för olika arbetsbelastningar) bör du prioritera konverteringen volym så att den högre prioritet volymer konverteras först. Du bör till exempel konvertera volymer som är värdar för virtuella datorer (VM) eller volymer med SQL arbetsbelastningar innan du konverterar volymer med filen resursen arbetsbelastningar.

#### <a name="to-change-the-volume-type"></a>Att ändra volymtypen av
1. På den **enheter** sidan, Välj enheten, dubbelklicka på den och klicka sedan på den **Volymbehållare** fliken.
2. Välj en volymbehållare i listan och dubbelklicka på den om du vill visa de volymer som är kopplade till behållaren.
3. Välj en volym och längst ned på sidan klickar du på **ändra**. Guiden Ändra volym startas.
4. På den **grundläggande inställningar** ändrar användningstyp genom att välja den nya typen från den **användningstyp** listrutan.
   
   * Om du ändrar typ som **lokalt Fäst**, StorSimple ska kontrollera om det finns tillräckligt med kapacitet.
   * Om du ändrar typ som **skiktindelade** och kommer att användas volymen för arkiveringsdata, markerar den **Använd volymen för mindre ofta använda arkiveringsdata** kryssrutan.
     
       ![Kryssruta för arkivering](./media/storsimple-manage-volumes-u2/ModifyTieredVolEx.png)
5. Klicka på pilikonen ![pilikonen](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) att gå till den **ytterligare inställningar** sidan. Om du konfigurerar en lokalt Fäst volym, visas följande meddelande.
   
    ![Ändra volym typen meddelande](./media/storsimple-manage-volumes-u2/ModifyLocalVolEx.png)
6. Klicka på pilikonen ![pilikon](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) igen för att fortsätta.
7. Klicka på kryssikonen ![Kryssikon](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) starta konverteringsprocessen. Azure portal visas en uppdatering volym-meddelande. Ett meddelande visas när volymen har uppdaterats.

## <a name="take-a-volume-offline"></a>Koppla från en volym
Du kan behöva koppla från en volym när du planerar att ändra eller ta bort den. När en volym är offline, är den inte tillgänglig för skrivskyddad åtkomst. Du måste ta volymen offline på värden samt på enheten. 

#### <a name="to-take-a-volume-offline"></a>Att koppla från en volym
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

#### <a name="to-delete-a-volume"></a>Ta bort en volym
1. På den **enheter** sidan, Välj enheten, dubbelklicka på den och klicka sedan på den **Volymbehållare** fliken.
2. Välj volymbehållaren som innehåller volymen som du vill ta bort. Klicka på volymbehållare att komma åt den **volymer** sidan.
3. Alla volymer som är associerade med den här behållaren visas i tabellformat. Kontrollera status för den volym som du vill ta bort. Om du vill ta bort volymen inte är offline, kopplar från den först följa stegen i [kopplar från en volym](#take-a-volume-offline).
4. När volymen är offline, klickar du på **ta bort** längst ned på sidan.
5. Klicka på **Ja** när du uppmanas att bekräfta åtgärden. Volymen tas bort och **volymer** sidan visas en uppdaterad lista över volymer i behållaren.
   
   > [!NOTE]
   > Om du tar bort en lokalt Fäst volym kan det tillgängliga utrymmet för nya volymer inte uppdateras omedelbart. StorSimple Manager-tjänsten uppdaterar regelbundet lokalt tillgängligt utrymme. Vi rekommenderar att du vänta några minuter innan du försöker skapa den nya volymen.<br> Dessutom om du tar bort en lokalt Fäst volym och ta bort en annan lokalt Fäst volym omedelbart efteråt volym borttagningen jobb körs sekventiellt. Det första volym tas bort jobbet måste avslutas innan nästa volym borttagning jobb kan börja.
   > 
   > 

## <a name="monitor-a-volume"></a>Övervaka en volym
Övervakning av volym kan du samla in I/O-relaterade statistik för en volym. Övervakning är aktiverad som standard för första 32 volymer som du skapar. Övervakning av ytterligare volymer är inaktiverat som standard. Övervakning av klonade volymer är också inaktiverat som standard.

Utför följande steg om du vill aktivera eller inaktivera övervakning för en volym.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Aktivera eller inaktivera övervakning av volym
1. På den **enheter** sidan, Välj enheten, dubbelklicka på den och klicka sedan på den **Volymbehållare** fliken.
2. Välj volymbehållaren som volymen finns och klicka sedan på volymbehållare att komma åt den **volymer** sidan.
3. Alla volymer som är associerade med den här behållaren visas i tabellformat visas. Klicka och välj volymen eller volym kloning.
4. Längst ned på sidan klickar du på **ändra**.
5. I guiden Ändra volym under **grundläggande inställningar**väljer **aktivera** eller **inaktivera** från den **övervakning** listrutan.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [klona en StorSimple-volym](storsimple-clone-volume.md).
* Lär dig hur du [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

