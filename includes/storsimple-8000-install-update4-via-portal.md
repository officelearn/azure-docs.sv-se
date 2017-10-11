<!--author=alkohli last changed: 07/07/17-->

#### <a name="to-install-an-update-from-the-azure-portal"></a>Installera en uppdatering från Azure Portal

1. Välj enheten på tjänstesidan StorSimple.

    ![Välj enhet](./media/storsimple-8000-install-update4-via-portal/update1.png)

2. Gå till **Enhetsinställningar** > **enhetsuppdateringar**.

    ![Klicka på uppdatering av enheter](./media/storsimple-8000-install-update4-via-portal/update2.png)

2. Ett meddelande visas om det finns nya uppdateringar. Du kan också i den **enhetsuppdateringar** bladet klickar du på **Sök efter uppdateringar**. Det skapas ett jobb för att söka efter tillgängliga uppdateringar. Du meddelas när jobbet har slutförts.

    ![Klicka på uppdatering av enheter](./media/storsimple-8000-install-update4-via-portal/update3.png)

3. Vi rekommenderar att du läser den viktiga informationen innan du installerar en uppdatering på enheten. Om du vill tillämpa uppdateringar, klickar du på **installera uppdateringar**. I den **bekräfta regelbundna uppdateringar** bladet granska förutsättningar för att kunna slutföra innan du installerar uppdateringar. Markera kryssrutan för att visa att du är redo att uppdatera enheten och klicka sedan på **installera**.

    ![Klicka på uppdatering av enheter](./media/storsimple-8000-install-update4-via-portal/update4.png)

6. En uppsättning nödvändiga kontrollerar startar. Dessa kontroller omfattar följande:
   
   * **Hälsokontroller av styrenheten** för att verifiera att båda styrenheterna är felfria och online.
   * **Hälsokontroller för maskinvarukomponenter** för att verifiera att alla maskinvarukomponenter på StorSimple-enheten är felfria.
   * **DATA 0-kontroller** för att verifiera att DATA 0 är aktiverat på enheten. Om inte det här gränssnittet är aktiverat måste du aktivera det och sedan försöka igen.

    Uppdateringen hämtas och installeras bara om alla kontroller har slutförts. Du får ett meddelande när kontrollerna pågår. Om prechecks misslyckas sedan anges du med orsakerna till felet. Lösa dessa problem och försök sedan igen. Du kan behöva kontakta Microsofts support om du inte kan lösa dessa problem själv.

7. När prechecks har slutförts, skapas ett uppdateringsjobb. Du får ett meddelande när uppdateringsjobbet har skapats.
   
    ![Uppdatera skapande av jobb](./media/storsimple-8000-install-update4-via-portal/update6.png)
   
    Uppdateringen tillämpas sedan på enheten.

9. Uppdateringen tar några timmar att slutföra. Markera uppdateringsjobbet och klicka på **Information** så kan du visa information om jobbet när som helst.

    ![Uppdatera skapande av jobb](./media/storsimple-8000-install-update4-via-portal/update8.png)

     Du kan också övervaka förloppet för Uppdateringsjobbet från **Enhetsinställningar > jobb**. På den **jobb** bladet du kan se förloppet för uppdatering.

     ![Uppdatera skapande av jobb](./media/storsimple-8000-install-update4-via-portal/update7.png)

10. När jobbet har slutförts, går du till den **Enhetsinställningar > enhetsuppdateringar**. Programvaruversionen ska nu uppdateras.

    ![Uppdatera skapande av jobb](./media/storsimple-8000-install-update4-via-portal/update9.png)

