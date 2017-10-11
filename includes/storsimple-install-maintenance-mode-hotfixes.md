<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-maintenance-mode-hotfixes-via-windows-powershell-for-storsimple"></a>Installera Underhåll läge snabbkorrigeringar via Windows PowerShell för StorSimple
> [!IMPORTANT]
> Du måste först installera snabbkorrigeringen på en domänkontrollant och sedan på den andra styrenheten i underhållsläge.
> 
> 

1. Placera enheten i underhållsläge. Se [steg 2: Ange underhållsläge](../articles/storsimple/storsimple-update-device.md#step2) instruktioner om hur du anger underhållsläge.
2. Om du vill installera snabbkorrigeringen, skriver du:
   
     `Start-HcsHotfix` 
3. När du uppmanas, ange sökvägen till den delade nätverksmappen som innehåller snabbkorrigeringsfilerna.
4. Du uppmanas att bekräfta. Typen **Y** att fortsätta med installationen av snabbkorrigeringen.
5. När du har installerat snabbkorrigeringen på en domänkontrollant, kan du logga in på den andra styrenheten. Installera snabbkorrigeringen som du skapade tidigare domänkontrollant.
6. Avsluta underhållsläget när snabbkorrigeringarna tillämpas. Se [steg 4: avsluta underhållsläget](../articles/storsimple/storsimple-update-device.md#step4) anvisningar.

