<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Så här installerar du underhållsläge uppdateringar via Windows PowerShell för StorSimple
1. Om du inte redan gjort det, åtkomst till enhetens seriekonsol och välj alternativ 1, **logga in med fullständig åtkomst**. 
2. Ange lösenordet. Standardlösenordet är **Password1**.
3. Skriv följande vid kommandotolken:
   
     `Get-HcsUpdateAvailability` 
4. Du meddelas om det finns uppdateringar och om uppdateringarna är störande eller utan avbrott. Om du vill använda störande uppdateringar måste du placera enheten i underhållsläge. Se [steg 2: Ange underhållsläge](../articles/storsimple/storsimple-update-device.md#step2) anvisningar.
5. När enheten är i underhållsläge, i Kommandotolken skriver du:`Start-HcsUpdate`
6. Du uppmanas att bekräfta. När du har bekräftat uppdateringarna kommer att installeras på en domänkontrollant som du använder. När uppdateringarna har installerats startas styrenheten. 
7. Övervaka statusen för uppdateringar. Ange:
   
    `Get-HcsUpdateStatus`
   
    Om den `RunInProgress` är `True`, uppdateringen pågår fortfarande. Om `RunInProgress` är `False`, indikerar det att uppdateringen har slutförts.  
8. När uppdateringen är installerad på den aktuella domänkontrollanten och den har startats om, ansluta till den andra styrenheten och utföra steg 1 till 6.
9. Avsluta underhållsläget när båda domänkontrollanter har uppdaterats. Se [steg 4: avsluta underhållsläget](../articles/storsimple/storsimple-update-device.md#step4) anvisningar.

