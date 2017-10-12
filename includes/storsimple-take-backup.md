<!--author=alkohli last changed: 9/17/15-->

### <a name="to-take-a-backup"></a>Gör en säkerhetskopia
1. På enhetens **Snabbstart**-sida, klickar du på **Lägg till en princip för säkerhetskopiering**. Det startar guiden lägg till princip för säkerhetskopiering. 
2. På sidan **Definiera din princip för säkerhetskopiering**:
   
   1. Ange ett namn som innehåller mellan 3 och 150 tecken för din princip för säkerhetskopiering.
   2. Välj de volymer som ska säkerhetskopieras. Om du väljer fler än en volym, kommer volymerna att grupperas tillsammans för att skapa en kraschkonsekvent säkerhetskopia.
   3. Klicka på pilikonen ![pilikon](./media/storsimple-take-backup/HCS_ArrowIcon-include.png). 
      
      ![Lägg till princip för säkerhetskopiering](./media/storsimple-take-backup/HCS_AddBackupPolicyWizard1M-include.png)
3. På sidan **Definiera ett schema**:
   
   1. Välj typ av säkerhetskopia från listrutan. Välj **Lokal ögonblicksbild**, för snabbare återställningar. Välj **Ögonblicksbild i molnet**, för dataåterhämtning.
   2. Ange frekvensen för säkerhetskopiering i minuter, timmar, dagar eller veckor.
   3. Välj en kvarhållningstid. Kvarhållningsalternativen beror på frekvensen för säkerhetskopiering. För en daglig princip, kan kvarhållning anges i veckor, medan kvarhållningen för en månatlig princip är i månader.
   4. Välj starttiden och datum för principen för säkerhetskopiering.
   5. Markera kryssrutan **Aktivera**, för att aktivera principen för säkerhetskopiering. 
   6. Klicka på kryssikonen ![kryssikon](./media/storsimple-take-backup/HCS_CheckIcon-include.png) för att spara principen.
      
      ![Lägg till princip för säkerhetskopiering](./media/storsimple-take-backup/HCS_AddBackupPolicyWizard2M-include.png)
      
      Du har nu en princip för säkerhetskopiering som skapar schemalagda säkerhetskopieringar av din volymdata.

Du har slutfört enhetskonfigurationen. 

![Video tillgänglig](./media/storsimple-take-backup/Video_icon.png) **Video tillgänglig**

Om du vill se en video som visar hur du tar en StorSimple-säkerhetskopia, klickar du [här](https://azure.microsoft.com/documentation/videos/take-a-storsimple-backup/).

