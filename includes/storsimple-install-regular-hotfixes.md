<!--author=SharS last changed: 9/17/15-->

#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Installera reguljära snabbkorrigeringar via Windows PowerShell för StorSimple
1. Ansluta till enhetens seriekonsol. Mer information finns i [steg 1: ansluta till seriekonsolen](../articles/storsimple/storsimple-update-device.md#step1).
2. Välj alternativ 1, i menyn för seriekonsolen **logga in med fullständig åtkomst**. Ange lösenordet. Standardlösenordet är **Password1**.
3. Skriv följande vid kommandotolken:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > Det här kommandot gäller enbart för vanliga snabbkorrigeringar. Du kör det här kommandot på en enda domänkontrollant, men både domänkontrollanter kommer att uppdateras.
    > Märker du kanske en domänkontrollant och växling vid fel under uppdateringen; växling vid fel påverkar inte filsystemets tillgänglighet eller åtgärden.

4. När du uppmanas, ange sökvägen till den delade nätverksmappen som innehåller snabbkorrigeringsfilerna.
5. Du uppmanas att bekräfta. Typen **Y** att fortsätta med installationen av snabbkorrigeringen.

