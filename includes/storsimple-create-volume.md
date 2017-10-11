<!--author=SharS last changed: 02/04/2016-->

#### <a name="to-create-a-volume"></a>Skapa en volym
1. På enhetens **Snabbstart**-sida, klickar du på **Lägg till en volym**. Det startar guiden lägg till en volym.
2. I guiden Lägg till en volym, i **Grundläggande inställningar**, gör du följande:
   
   1. Ange ett **namn** för volymen.
   2. Ange **etablerad kapacitet** för volymen i GB eller TB. Volymens kapacitet måste vara mellan 1 GB och 64 TB för en fysisk enhet.
   3. I listrutan väljer du **användningstyp** för volymen. 
   4. Om du använder volymen för arkiveringsdata, markerar du **Använd volymen för arkiveringsdata med låg åtkomstfrekvens**. För alla andra användningsfall, väljer du helt enkelt **Nivåindelad volym**. (Nivåindelade volymer kallades förut för primära volymer).
      
        ![Lägg till volym](./media/storsimple-create-volume/ScreenshotUpdate1VolumeFlow.png)
      
      1. Klicka på pilikonen ![pilikon](./media/storsimple-create-volume/HCS_ArrowIcon-include.png) för att gå till nästa sida.
3. I dialogrutan **Ytterligare inställningar**, lägger du till en ny åtkomstkontrollpost (ACR):
   
   1. Ange ett **namn** för din ACR.
   2. Som **iSCSI-initierarnamn**, anger du det iSCSI-kvalificerade namnet (IQN) för din Windows-värd. Om du inte har en IQN, går du till [Hämta IQN för en Windows Server-värd](#get-the-iqn-of-a-windows-server-host).
   3. Vi rekommenderar att du aktiverar en standard säkerhetskopiering genom att markera kryssrutan **Aktivera en standard säkerhetskopiering för den här volymen**. Standard säkerhetskopieringen skapar en princip som körs klockan 22.30 varje dag (enhetens tid) och skapar en ögonblicksbild i molnet av den här volymen.
      
      > [!NOTE]
      > När säkerhetskopieringen har aktiverats här, kan den inte återställas. För att ändra inställningen, behöver du redigera volymen.
      > 
      > 
      
        ![Lägg till volym](./media/storsimple-create-volume/AddVolume2-include.png)
4. Klicka på kryssikonen ![kryssikon](./media/storsimple-create-volume/HCS_CheckIcon-include.png). En volym skapas med de angivna inställningarna.

![Video tillgänglig](./media/storsimple-create-volume/Video_icon.png) **Video tillgänglig**

Om du vill se en video som visar hur du skapar en StorSimple-volym klickar du [här](https://azure.microsoft.com/documentation/videos/create-a-storsimple-volume/).

