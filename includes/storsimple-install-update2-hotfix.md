<!--author=alkohli last changed: 03/17/16-->

#### <a name="to-download-hotfixes"></a>Ladda ned snabbkorrigerar
Utför följande steg för att hämta programuppdateringen från Microsoft Update Catalog.

1. Starta Internet Explorer och gå till [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).
2. Om det här är första gången du använder Microsoft Update Catalog på den här datorn klickar du på **Installera** när du uppmanas att installera tillägget för Microsoft Update Catalog.
    ![Installera katalog](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)
3. I sökrutan i Microsoft Update-katalogen ange Knowledge Base (KB) antalet den snabbkorrigering som du vill hämta, till exempel **3121901**, och klicka sedan på **Sök**.
   
    Snabbkorrigeringen listan visas till exempel **kumulativa programvara paket Update 2.0 för StorSimple 8000-serien**.
   
    ![Sökkatalog](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)
4. Klicka på **Lägg till**. Uppdateringen läggs till i korgen.
5. Sök efter eventuella ytterligare snabbkorrigeringar som anges i tabellen ovan (**3121900**, **3080728**, **3090322**, och **3121899**), och lägga till den korg.
6. Klicka på **View Basket** (Visa korg).
7. Klicka på **Hämta**. Ange eller **Bläddra** till en lokal plats där du vill att nedladdningarna ska läggas. Uppdateringarna hämtas till den angivna platsen och placeras i en undermapp med samma namn som uppdateringen. Mappen kan också kopieras till en nätverksresurs som kan nås från enheten.

> [!NOTE]
> Snabbkorrigeringarna måste vara tillgänglig från båda domänkontrollanter för att identifiera eventuella felmeddelanden från peer-domänkontrollant.
> 
> 

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>Installera och verifiera snabbkorrigeringar i normalläge
Utför följande steg för att installera och verifiera snabbkorrigeringar i normalläge. Om du redan har installerat dem med hjälp av Azure Portal, gå vidare till [installera och underhåll läge snabbkorrigeringar](#to-install-and-verify-maintenance-mode-hotfixes).

1. Gå in i Windows PowerShell-gränssnittet på din StorSimple-enhets seriekonsol för att installera snabbkorrigerarna. Följ de detaljerade instruktionerna i [Använd PuTTY för att ansluta till enhetens seriekonsol](../articles/storsimple/storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console). Tryck på **Retur** i kommandotolken.
2. Välj **Alternativ 1** för att logga in på enheten med fullständig åtkomst.
3. Ange följande i kommandotolken för att installera snabbkorrigeringen:
   
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
    LastHotfixTimestamp : 12/21/2015 10:36:13 PM
    LastUpdateTimestamp : 12/21/2015 10:35:25 PM
    Controller0Events   :
    Controller1Events   :
    ```
   
     Följande exempel på utdata visar att uppdateringen är färdig. `RunInProgress` kommer att vara `False` när uppdateringen är färdig.
   
    ```
    Controller1>Get-HcsUpdateStatus

    RunInprogress       : False
    LastHotfixTimestamp : 12/21/2015 10:59:13 PM
    LastUpdateTimestamp : 12/21/2015 10:35:25 PM
    Controller0Events   :
    Controller1Events   :
    ```
   
   > [!NOTE]
   > I vissa fall rapporterar cmdlet `False` när uppdateringen fortfarande pågår. Om du vill kontrollera att snabbkorrigeringen har slutförts väntar du några minuter och sedan kör du det här kommandot igen och kontrollerar att `RunInProgress` är `False`. Om det har det har snabbkorrigeringen slutförts.

6. När programuppdateringen har slutförts, Upprepa steg 3-5 för att installera och övervaka SaaS-agenten och MDS-agenten. Se till att `all-hcsmdssoftwareupdate_0b438ddf0d5b686aada2378b754fac8c7f2160e9.exe` är installerad före `all-cismdsagentupdatebundle_f98e62f4d56c79e2a6644d027af7a2393a93827a.exe`.
7. Kontrollera programvaruversioner system. Ange:
   
    `Get-HcsSystem`
   
    Du bör se följande versioner:
   
   * HcsSoftwareVersion: 6.3.9600.17673
   * CisAgentVersion: 1.0.9150.0
   * MdsAgentVersion: 30.0.4698.13
     
     Om versionsnumren inte ändras efter att uppdateringen, visar att snabbkorrigeringen har kunde inte tillämpas. Kontakta [Microsoft Support](../articles/storsimple/storsimple-contact-microsoft-support.md) för ytterligare hjälp om du ser det här.
8. Upprepa steg 3-5 om du vill installera snabbkorrigeringar återstående regular-läge.
   
   * Drivrutinens LSI - KB3121900
   * Uppdatering för Storport - KB3080728
   * Uppdateringen Spaceport - KB3090322

#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>Installera och verifiera snabbkorrigeringar i underhållsläge
Använd KB3121899 att installera uppdateringar av inbyggd disk. Det här är störande uppdateringar och tar cirka 30 minuter för att slutföra. Du kan välja att installera dem i ett planerat underhållsfönster genom att ansluta till enhetens seriekonsol.

Observera att om den inbyggda programvaran för disken redan är uppdaterad så behöver du inte installera uppdateringarna. Kör `Get-HcsUpdateAvailability`-cmdleten från enhetens seriekonsol för att kontrollera om det finns tillgängliga uppdateringar och om uppdateringarna är störande (underhållsläge) eller avbrottsfria (standardläget).

Följ anvisningarna nedan om du vill installera uppdateringarna för den inbyggda programvaran för disken.

1. Placera enheten i underhållsläge. Observera att du inte bör använda Windows PowerShell-fjärrkommunikation när du ansluter till en enhet i underhållsläge. I stället köra denna cmdlet på styrenheten för enheten när du är ansluten till enhetens seriekonsol. Ange:
   
    `Enter-HcsMaintenanceMode`
   
    Ett exempel på utdata visas nedan.
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: Update2-8100-SHG0997879L76YD
        Software Version: 6.3.9600.17664
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
        This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. Övervaka installationsförloppet med `Get-HcsUpdateStatus`-kommandot. Uppdateringen är slutförd när `RunInProgress` ändras till `False`.
4. När installationen är färdig startas styrenheten som snabbkorrigeringen i underhållsläge installerades på om. Logga in som alternativ 1 med fullständig åtkomst och verifiera versionen för den inbyggda programvaran för disken. Ange:
   
   `Get-HcsFirmwareVersion`
   
   De förväntade versionerna för den inbyggda programvaran för disken är:
   
   `XMGG, XGEG, KZ50, F6C2, VR08`
   
   Ett exempel på utdata visas nedan.
   
       -----------------------MAINTENANCE MODE------------------------
       Microsoft Azure StorSimple Appliance Model 8100
       Name: Update2-8100-SHG0997879L76YD
       Software Version: 6.3.9600.17664
       Copyright (C) 2014 Microsoft Corporation. All rights reserved.
       You are connected to Controller1
       ---------------------------------------------------------------
   
       Controller1>Get-HcsFirmwareVersion
   
       Controller0 : TalladegaFirmware
         ActiveBIOS:0.45.0006
         BackupBIOS:0.45.0008
         MainCPLD:17.0.0005
         ActiveBMCRoot:2.0.000E
         BackupBMCRoot:2.0.000E
         BMCBoot:2.0.0001
         LsiFirmware:19.00.00.00
         LsiBios:07.37.00.00
         Battery1Firmware:06.29
         Battery2Firmware:06.29
         DomFirmware:X231600
         CanisterFirmware:3.5.0.32
         CanisterBootloader:5.03
         CanisterConfigCRC:0xD1B030A4
         CanisterVPDStructure:0x06
         CanisterGEMCPLD:0x17
         CanisterVPDCRC:0xEE3504B4
         MidplaneVPDStructure:0x0C
         MidplaneVPDCRC:0xA6BD4F64
         MidplaneCPLD:0x10
         PCM1Firmware:1.00|1.05
         PCM1VPDStructure:0x05
         PCM1VPDCRC:0x41BEF99C
         PCM2Firmware:1.00|1.05
         PCM2VPDStructure:0x05
         PCM2VPDCRC:0x41BEF99C
   
         DisksFirmware
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST400FM0073:XGEG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
         SEAGATE:ST4000NM0023:XMGG
   
    Kör `Get-HcsFirmwareVersion`-kommandot på den andra styrenheten för att verifiera att programvaruversionen har uppdaterats. Du kan sedan avsluta underhållsläget. För att göra det anger du följande kommando för varje enhetsstyrenhet:
   
   `Exit-HcsMaintenanceMode`
5. Domänkontrollanterna starta om när du avslutar underhållsläge. Efter att uppdateringarna för den inbyggda programvaran för disken har tillämpats och enheten har avslutat underhållsläget kan du gå tillbaka till den klassiska Azure-portalen. Observera att portalen inte kan se till att du installerat Underhåll läge uppdateringarna i 24 timmar.

