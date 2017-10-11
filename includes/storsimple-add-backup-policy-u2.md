<!--author=v-sharos last changed: 11/06/15-->

#### <a name="to-add-a-storsimple-backup-policy"></a>Så här lägger du till en StorSimple-säkerhetskopieringsprincip
1. Klicka på fliken **Principer för säkerhetskopiering** på bladet **Snabbstart** för enheten. Sidan **Principer för säkerhetskopiering** öppnas.
2. Klicka på **Lägg till** längst ned på sidan för att starta guiden Add Backup Policy
(Lägg till princip för säkerhetskopiering).
   
    ![Lägga till en princip för säkerhetskopiering 1](./media/storsimple-add-backup-policy-u2/AddBackupPolicy1.png)
3. Gör följande under **Define your backup policy** (Definiera din princip för säkerhetskopiering) i dialogrutan **Add Backup Policy** (Lägg till princip för säkerhetskopiering):
   
   1. Ange ett namn på principen för säkerhetskopiering som innehåller mellan 3 och 150 tecken.
   2. Klicka på kryssrutan eller kryssrutorna för att koppla en eller flera volymer till den här principen för säkerhetskopiering. Observera att du inte kan välja volymer som använder olika molntjänstleverantörer. Om du använder flera molntjänstleverantörer visas bara, baserat på ditt första val, volymer som hör till den molntjänstleverantören. Detta gör att du kan gruppera volymer som hör till samma molntjänstleverantör i en ögonblicksbild.
   3. Klicka på pilikonen ![pilikon](./media/storsimple-add-backup-policy-u2/HCS_ArrowIcon-include.png) för att gå till nästa sida.
      
      ![Lägga till en princip för säkerhetskopiering 2](./media/storsimple-add-backup-policy-u2/AddBackupPolicy2.png)
4. Gör följande under **Define a schedule** (Definiera ett schema):
   
   1. I rutan **Typ av säkerhetskopiering** väljer du **Ögonblicksbild av molndata** eller **Lokal ögonblicksbild** i listrutan.
   2. Ange säkerhetskopieringsfrekvensen (ange ett tal och välj sedan **Dagar** eller **Veckor** i listrutan).
   3. Ange ett kvarhållningsschema.
   4. Ange ett datum och en tid då säkerhetskopieringsprincipen ska börja.  
   5. Klicka på kryssikonen ![kryssikon](./media/storsimple-add-backup-policy-u2/HCS_CheckIcon-include.png) för att spara principen.

Den nyligen tillagda principen visas i tabellvyn på sidan **Principer för säkerhetskopiering**.

