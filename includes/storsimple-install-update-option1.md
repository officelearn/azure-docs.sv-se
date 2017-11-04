<!--author=SharS last changed: 03/17/2016-->

#### <a name="to-download-hotfixes"></a>Ladda ned snabbkorrigerar
Utför följande steg för att hämta programuppdateringen.

1. Starta Internet Explorer och gå till [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).
2. Om det här är första gången du använder Microsoft Update Catalog på den här datorn klickar du på **Installera** när du uppmanas att installera tillägget för Microsoft Update Catalog.
    ![Installera katalog](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)
3. I sökrutan i Microsoft Update-katalogen ange Knowledge Base (KB) antalet den snabbkorrigering som du vill hämta, till exempel **3063418**, och klicka sedan på **Sök**.
4. Du ser den **StorSimple uppdatering 1.2 enhet uppdatering** paket. Klicka på **Lägg till**. Uppdateringen läggs till korgen.
5. Sök efter eventuella ytterligare snabbkorrigeringar som anges i tabellen ovan (**3043005** och **3063416**), och Lägg till varje korgen.
6. Klicka på **View Basket** (Visa korg).
   
    ![Visa korg](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)
7. Klicka på **Hämta**. Ange eller **Bläddra** till en lokal plats där du vill att nedladdningarna ska läggas. Uppdateringarna hämtas till den angivna platsen och placeras i en undermapp med samma namn som uppdateringen. Mappen kan också kopieras till en nätverksresurs som kan nås från enheten.

> [!NOTE]
> Snabbkorrigeringarna måste vara tillgänglig från båda domänkontrollanter för att identifiera eventuella felmeddelanden från peer-domänkontrollant.
> 
> 

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>Installera och verifiera snabbkorrigeringar i normalläge
Utför följande steg för att installera och verifiera snabbkorrigeringarna regular-läge. Om du redan har installerat dem med hjälp av Azure Portal, gå vidare till [installera och underhåll läge snabbkorrigeringar](#to-install-and-verify-maintenance-mode-hotfixes).

1. Öppna Windows PowerShell-gränssnittet på din StorSimple enhetens seriekonsol för att installera programuppdateringen. Följ de detaljerade instruktionerna i [Använd PuTTY för att ansluta till enhetens seriekonsol](../articles/storsimple/storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console). Tryck på **Retur** i kommandotolken.
2. Välj **Alternativ 1** för att logga in på enheten med fullständig åtkomst.
3. Om du vill installera uppdateringspaketet i Kommandotolken, skriver du:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Använd IP i stället för DNS i resurssökvägen i kommandot ovan. Parametern för autentiseringsuppgifter används bara om du ansluter till en autentiserad resurs.
   
    Vi rekommenderar att du använder parametern för autentiseringsuppgifter för att få åtkomst till resurser. Även resurser som är öppna för ”alla” är vanligtvis inte öppna för icke-autentiserade användare.
   
    Ett exempel på utdata visas nedan.
   
    ```
    Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
    \hcsmdssoftwareupdate.exe -Credential contoso\John

    Confirm

    This operation starts the hotfix installation and could reboot one or
    both of the controllers. If the device is serving I/Os, these will not
    be disrupted. Are you sure you want to continue?
    [Y] Yes [N] No [?] Help (default is "Y"): Y
    ```

4. Skriv **Y** när du uppmanas att bekräfta installationen av snabbkorrigeringen.
5. Övervaka uppdateringen med hjälp av `Get-HcsUpdateStatus`-cmdlet.
   
    Följande exempel på utdata visar att uppdateringen pågår. `RunInprogress` kommer att vara `True` när uppdateringen pågår.
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp : 9/02/2015 10:36:13 PM
    LastUpdateTimestamp : 9/02/2015 10:35:25 PM
    Controller0Events   :
    Controller1Events   :
    ```
   
     Följande exempel på utdata visar att uppdateringen är färdig. `RunInProgress` kommer att vara `False` när uppdateringen är färdig.

    ```
    Controller1>Get-HcsUpdateStatus

    RunInprogress       : False
    LastHotfixTimestamp : 9/02/2015 10:56:13 PM
    LastUpdateTimestamp : 9/02/2015 10:35:25 PM
    Controller0Events   :
    Controller1Events   :
    ```
   
   > [!NOTE]
   > I vissa fall rapporterar cmdlet `False` när uppdateringen fortfarande pågår. Om du vill kontrollera att snabbkorrigeringen har slutförts väntar du några minuter och sedan kör du det här kommandot igen och kontrollerar att `RunInProgress` är `False`. Om det har det har snabbkorrigeringen slutförts.
   > 
   > 
6. När programuppdateringen har slutförts kan du kontrollera systemprogramversionerna. Ange följande kommando:
   
    `Get-HcsSystem`
   
    Du bör se följande versioner:
   
   * HcsSoftwareVersion: 6.3.9600.17584
   * CisAgentVersion: 1.0.9049.0
   * MdsAgentVersion: 26.0.4696.1433
     
     Om versionsnumren inte ändras efter att uppdateringen, visar att snabbkorrigeringen har kunde inte tillämpas. Kontakta [Microsoft Support](../articles/storsimple/storsimple-contact-microsoft-support.md) för ytterligare hjälp om du ser det här.
7. Upprepa steg 3-5 om du vill installera snabbkorrigeringen för återstående regular-läge (KB3043005).

#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>Installera och verifiera snabbkorrigeringar i underhållsläge
Använd KB3063416 att installera uppdateringar av inbyggd disk. Dessa är störande uppdateringar och ta runt 30-45 minuter för att slutföra. Du kan välja att installera dem i ett planerat underhållsfönster genom att ansluta till enhetens seriekonsol.

Följ anvisningarna nedan om du vill installera uppdateringarna för den inbyggda programvaran för disken.

1. Placera enheten i underhållsläge. Observera att du inte bör använda Windows PowerShell-fjärrkommunikation när du ansluter till en enhet i underhållsläge. Du måste köra denna cmdlet på styrenheten för enheten när du är ansluten till enhetens seriekonsol. Ange:
   
    `Enter-HcsMaintenanceMode`
   
    Ett exempel på utdata visas nedan.
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: Update1-8100-SHG0997879L76YD
        Software Version: 6.3.9600.17584
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Passive
        ---------------------------------------------------------------
        Serial Console Menu
        [1] Log in with full access
        [2] Log into peer controller with full access
        [3] Connect with limited access
        [4] Change language
        Please enter your choice>
   
    Båda domänkontrollanterna starta sedan om i underhållsläge.
2. Ange följande för att installera uppdateringen av inbyggd programvara för disk:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Ett exempel på utdata visas nedan.
   
        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
        Enter Password:
        WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
        Confirm
        This operation starts a hotfix installation and could reboot one or both of the controllers. Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. Övervaka installationsförloppet med `Get-HcsUpdateStatus`-kommandot. Uppdateringen är slutförd när `RunInProgress` ändras till `False`.
4. När installationen är klar startas den domänkontrollant som Underhåll läge snabbkorrigeringen har installerats om. Logga in som alternativ 1 med fullständig åtkomst och verifiera versionen för den inbyggda programvaran för disken. Ange:
   
   `Get-HcsFirmwareVersion`
   
   De förväntade versionerna för den inbyggda programvaran för disken är:
   
   `XMGG, XGEE, KZ50, F6C2, VR08`
   
   Kör `Get-HcsFirmwareVersion`-kommandot på den andra styrenheten för att verifiera att programvaruversionen har uppdaterats. Du kan sedan avsluta underhållsläget. Skriv följande kommando för varje enhet domänkontrollant:
   
   `Exit-HcsMaintenanceMode`
5. Domänkontrollanterna starta om när du avslutar underhållsläge. Efter att uppdateringarna för den inbyggda programvaran för disken har tillämpats och enheten har avslutat underhållsläget kan du gå tillbaka till den klassiska Azure-portalen. Observera att portalen inte kan se till att du installerat Underhåll läge uppdateringarna i 24 timmar.

