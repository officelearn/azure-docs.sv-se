<!--author=SharS last changed: 11/18/16-->

#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>Så här installerar du regelbundna uppdateringar via Windows PowerShell för StorSimple
1. Öppna enhetens seriekonsol och välj alternativ 1, **logga in med fullständig åtkomst**. Ange lösenordet. Standardlösenordet är *Password1*. 
2. Skriv följande vid kommandotolken:
   
     `Get-HcsUpdateAvailability`
   
    Du meddelas om det finns uppdateringar och om uppdateringarna är störande eller utan avbrott.
3. Skriv följande vid kommandotolken:
   
     `Start-HcsUpdate`
   
    Uppdateringen startar.

> [!IMPORTANT]
> * Det här kommandot gäller enbart för regelbundna uppdateringar. Du kör det här kommandot på en enda domänkontrollant, men både domänkontrollanter kommer att uppdateras. 
> * Märker du kanske en domänkontrollant och växling vid fel under uppdateringen; växling vid fel påverkar inte filsystemets tillgänglighet eller åtgärden.
> 
> 

