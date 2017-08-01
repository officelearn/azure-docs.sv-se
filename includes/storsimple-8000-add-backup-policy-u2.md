<!--author=alkohli last changed: 02/10/17-->

#### <a name="to-add-a-storsimple-backup-policy"></a>Så här lägger du till en StorSimple-säkerhetskopieringsprincip

1. Gå till din StorSimple-enhet och klicka på **Säkerhetskopieringspolicy**.

2. Klicka på **+ Lägg till princip** i kommandofältet på bladet **Säkerhetskopieringspolicy**.
   
    ![Lägga till en säkerhetskopieringspolicy](./media/storsimple-8000-add-backup-policy-u2/addbupol1.png)

3. Utför följande steg på bladet **Skapa säkerhetskopieringspolicy**:
   
   1. **Välj enhet** fylls i automatiskt baserat på den enhet du valt.
   
   2. Ange ett **namn på principen** för säkerhetskopiering som innehåller mellan 3 och 150 tecken. När principen har skapats kan du inte byta namn på den.
       
   3. Du kan tilldela volymer till säkerhetskopieringspolicyn genom att välja **Lägg till volymer** och sedan klicka på kryssrutan eller kryssrutorna i tabellistan över volymer för att koppla en eller flera volymer till säkerhetskopieringspolicyn.

       ![Lägga till en säkerhetskopieringspolicy](./media/storsimple-8000-add-backup-policy-u2/addbupol2.png)

   4. Du kan definiera ett schema för säkerhetskopieringspolicyn genom att klicka på **Första schemat** och sedan ändra följande parametrar:

       ![Lägga till en säkerhetskopieringspolicy](./media/storsimple-8000-add-backup-policy-u2/addbupol3.png)

       1. För **Typ av ögonblicksbild** väljer du **Moln** eller **Lokal**.

       2. Ange säkerhetskopieringsfrekvensen (ange ett tal och välj sedan **Dagar** eller **Veckor** i listrutan).

       3. Ange ett kvarhållningsschema.

       4. Ange ett datum och en tid då säkerhetskopieringsprincipen ska börja.

       5. Klicka på **OK** för att definiera schemat.

   5. Klicka på **Skapa** för att skapa en säkerhetskopieringspolicy.

       ![Lägga till en säkerhetskopieringspolicy](./media/storsimple-8000-add-backup-policy-u2/addbupol4.png)
   
   6. Du meddelas när säkerhetskopieringspolicyn har skapats. Den nya principen visas i tabellvyn på bladet **Säkerhetskopieringspolicy**.

       ![Lägga till en säkerhetskopieringspolicy](./media/storsimple-8000-add-backup-policy-u2/addbupol7.png)

