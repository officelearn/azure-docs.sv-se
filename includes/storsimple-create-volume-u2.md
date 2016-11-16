<!--author=alkohli last changed: 08/16/2016-->

#### <a name="to-create-a-volume"></a>Skapa en volym
1. På enhetens **Snabbstart**-sida klickar du på **Lägg till volym** för att starta guiden Lägg till volym.
2. I guiden lägg till en volym, i **grundläggande inställningar**:
   
   1. Anger du ett **namn** för volymen.
   2. I listrutan väljer du **användningstyp** för volymen. För arbetsbelastningar som kräver lokala garantier, låg latens och hög prestanda, väljer du en **lokalt fäst** volym. För all övrig data, väljer du en **nivåindelad** volym. Om du använder volymen för arkiveringsdata, markerar du **Använd volymen för arkiveringsdata med låg åtkomstfrekvens**. 
      
       En lokalt fäst volym etableras tjockt, vilket försäkrar att primärdata på volymen är lokalt belägen för enheten och inte läcker över till molnet.  Om du skapar en lokalt fäst volym letar enheten efter ledigt utrymme på de lokala nivåerna för att etablera volymen med önskad storlek. Att skapa en lokalt fäst volym kan innebära att läcka befintlig data från enheten till molnet och volymen kan ta lång tid att skapa. Den totala tiden beror på den etablerade volymens storlek, tillgänglig nätverksbandbredd och den data som finns på din enhet. 
      
       En nivåindelad volym är tunt etablerad och kan skapas snabbt. Om du väljer **Använd volymen för arkiveringsdata med låg åtkomstfrekvens** för nivåindelad volym avsedd för arkivdata så ändras segmentstorleken för deduplicering för din volym till 512 KB. Om fältet inte är markerat använder den motsvarande nivåindelade volymen en segmentstorlek på 64 kB. En större segmentstorlek för deduplicering låter enheten snabba på överföring av segmentstorleken tillåter enheten att påskynda överföringen av stora mängder arkiveringsdata till molnet.
   3. Ange **etableringskapacitet** för volymen. Anteckna kapaciteten som finns tillgänglig baserat på den volymtyp som valts. Den angivna volymstorleken får inte överskrida det tillgängliga utrymmet.
      
       Du kan etablera lokalt fästa volymer upp till 8,5 TB, eller nivåindelade volymer upp till 200 TB på 8100-enheten. På den större 8600-enheten kan du etablera lokalt fästa volymer upp till 22,5 TB, eller nivåindelade volymer upp till 500 TB. Eftersom det krävs lokalt utrymme på enheten för att hålla arbetsuppsättningen med nivåindelade volymer påverkar skapandet av fästa volymer mängden utrymme som finns tillgängligt för att etablera nivåindelade volymer. Om du skapar en lokalt fäst volym, kommer därmed utrymmet som finns tillgängligt för att skapa nivåindelade volymer att minska. Likaså om du skapar en nivåindelad volym minskar det tillgängliga utrymmet för att skapa en lokalt fäst volym.
      
       Om du etablerar en lokalt fäst volym på 8,5 TB (största tillåtna storleken) på din 8100-enhet har du använt upp allt lokalt tillgängligt utrymme för enheten. Du kommer inte kunna skapa någon nivåindelad volym därefter, eftersom det inte finns något lokalt utrymme ledigt på enheten för att hålla arbetsuppsättningen för den nivåindelade volymen. Befintliga nivåindelade volymer påverkar också tillgängligt utrymme. Om du exempelvis har en 8100-enhet som redan har nivåindelade volymer på 106 TB finns det bara 4 TB utrymme tillgängligt för lokalt fästa volymer.
      
       Följande bild visar dialogrutan **Grundläggande inställningar** för en lokalt fäst volym.
      
        ![Lägg till lokal volym](./media/storsimple-create-volume-u2/add-local-volume-include.png)
      
       Följande bild visar dialogrutan **Grundläggande inställningar** för en nivåindelad volym.
      
        ![Lägg till lokal volym](./media/storsimple-create-volume-u2/add-tiered-volume-include.png)
   
   1. Klicka på pilikonen ![pilikon](./media/storsimple-create-volume-u2/HCS_ArrowIcon-include.png) för att gå till nästa sida.
3. I dialogrutan **Ytterligare inställningar**, lägger du till en ny åtkomstkontrollpost (ACR):
   
   1. Ange ett **namn** för din ACR.
   2. Som **iSCSI-initierarnamn**, anger du det iSCSI-kvalificerade namnet (IQN) för din Windows-värd. Om du inte har en IQN, går du till [Hämta IQN för en Windows Server-värd](#get-the-iqn-of-a-windows-server-host).
   3. Som **Standard säkerhetskopiering för den här volymen?**, markerar du kryssrutan **Aktivera**. Standardsäkerhetskopieringen skapar en princip som körs klockan 22.30 varje dag (enhetens tid) och skapar en ögonblicksbild i molnet av den här volymen.
      
      > [!NOTE]
      > När säkerhetskopieringen har aktiverats här, kan den inte återställas. Du behöver redigera volymen för att ändra inställningen.
      > 
      > 
      
      ![Lägg till volym](./media/storsimple-create-volume-u2/AddVolumeAdditionalSettings1.png)
4. Klicka på kryssikonen ![kryssikon](./media/storsimple-create-volume-u2/HCS_CheckIcon-include.png). En volym skapas med de angivna inställningarna.



<!--HONumber=Nov16_HO2-->


