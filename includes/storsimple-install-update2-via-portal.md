<!--author=alkohli last changed: 02/06/17-->

#### <a name="to-install-an-update-from-the-azure-portal"></a>Installera en uppdatering från Azure Portal

1. Välj enheten på tjänstesidan StorSimple. Gå till **Enheter** > **Underhåll**.
2. Klicka på **Genomsök uppdateringar** längst ned på sidan. Det skapas ett jobb för att söka efter tillgängliga uppdateringar. Du meddelas när jobbet har slutförts.
3. I avsnittet **Programuppdateringar** på samma sida finns nya programuppdateringar tillgängliga. Vi rekommenderar att du läser den viktiga informationen innan du installerar en uppdatering på enheten.
4. Klicka på **Installera uppdateringar** längst ned på listan och klicka sedan på **OK**.
5. I dialogrutan **Installera uppdateringar** kontrollerar du att du har följt rekommendationerna och väljer sedan **Jag är införstådd med kravet ovan och är redo att uppgradera enheten** och klickar på knappen med en bock.
   
    ![Bekräftelsemeddelande](./media/storsimple-install-update2-via-portal/InstallUpdate12_2M.png)
6. En uppsättning nödvändiga kontrollerar startar. Dessa kontroller omfattar följande:
   
   * **Hälsokontroller av styrenheten** för att verifiera att båda styrenheterna är felfria och online.
   * **Hälsokontroller för maskinvarukomponenter** för att verifiera att alla maskinvarukomponenter på StorSimple-enheten är felfria.
   * **DATA 0-kontroller** för att verifiera att DATA 0 är aktiverat på enheten. Om inte det här gränssnittet är aktiverat måste du aktivera det och sedan försöka igen.
   * **DATA 2- och 3 DATA-kontroller** för att verifiera att DATA 2- och DATA 3-nätverksgränssnitten inte är aktiverade. Om dessa gränssnitt är aktiverade måste du inaktivera dem och sedan försöka uppdatera din enhet. Den här kontrollen utförs enbart om du uppdaterar från en enhet som kör GA-programvara. Enheter som kör versionerna 0.1, 0.2 eller 0.3 behöver inte den här kontrollen.
   * **Gatewaykontroll** på en enhet som kör en version före Uppdatering 1. Den här kontrollen utförs på alla enheter som kör föruppdatering 1-programvara, men inte på enheter som har en gateway konfigurerad för ett annat nätverksgränssnitt än DATA 0.
     
     Uppdateringen tillämpas om alla kontroller har slutförts. Du får ett meddelande när kontrollerna pågår.
     
     ![Förkontrollsmeddelande](./media/storsimple-install-update2-via-portal/InstallUpdate12_3M.png)
     
     Följande är ett exempel där kontrollerna misslyckades. Du måste kontrollera att båda styrenheterna är felfria och online. Du måste också kontrollera maskinvarukomponenternas hälsotillstånd. I det här exemplet behöver komponenterna i Styrenhet 0 och Styrenhet 1 åtgärdas. Du kan behöva kontakta Microsofts support om du inte kan lösa dessa problem själv.
     
       ![Kontrollerna misslyckades](./media/storsimple-install-update2-via-portal/HCS_PreUpgradeChecksFailed-include.png)
7. När kontrollerna är klara skapas ett uppdateringsjobb. Du får ett meddelande när uppdateringsjobbet har skapats.
   
    ![Uppdatera skapande av jobb](./media/storsimple-install-update2-via-portal/InstallUpdate12_44M.png)
   
    Uppdateringen tillämpas sedan på enheten.
    
8. Klicka på **Visa jobb** om du vill övervaka förloppet för uppdateringsjobbet. På sidan **Jobb** kan du följa uppdateringsförloppet.
9. Uppdateringen tar några timmar att slutföra. Markera uppdateringsjobbet och klicka på **Information** så kan du visa information om jobbet när som helst.
10. När jobbet har slutförts navigerar du till sidan **Underhåll** och rullar ned till **Programuppdateringar**.

