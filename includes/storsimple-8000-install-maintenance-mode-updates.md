### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Installera Underhåll läge uppdateringar via Windows PowerShell för StorSimple

När du använder Underhåll läge uppdateringar på StorSimple-enhet har pausats alla i/o-begäranden. Tjänster, till exempel beständiga RAM-minne (NVRAM) eller klustertjänsten stoppas. Både domänkontrollanter startas om när du anger eller avsluta det här läget. När du lämnar det här läget återuppta alla tjänster och är felfria. (Detta kan ta några minuter.)

> [!IMPORTANT]
> * Kontrollera att båda styrenheter är felfri på Azure-portalen innan du anger underhållsläge. Om domänkontrollanten inte är felfri, [kontakta Microsoft Support](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) i nästa steg.
> * Du måste först uppdatera en domänkontrollant och den andra styrenheten när du är i underhållsläge.

1. Använd PuTTY för att ansluta till seriekonsol. Följ de detaljerade instruktionerna i [Använd PuTTY för att ansluta till enhetens seriekonsol](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). Tryck på **Retur** i kommandotolken. Välj alternativ 1 **logga in med fullständig åtkomst**.

2. Om du vill placera styrenheten i underhållsläge, skriver du:
    
    `Enter-HcsMaintenanceMode`

    Båda domänkontrollanterna starta om i underhållsläge.

3. Installera uppdateringar Underhåll läge. Ange:

    `Start-HcsUpdate`

    Du uppmanas att bekräfta. När du har bekräftat uppdateringarna är installerade på den domänkontrollant som du använder. När uppdateringarna har installerats på domänkontrollanten startar om.

4. Övervaka statusen för uppdateringar. Logga in till peer-styrenheten som den aktuella styrenheten uppdateras och det går inte att bearbeta andra kommandon. Ange:

    `Get-HcsUpdateStatus`

    Om den `RunInProgress` är `True`, uppdateringen pågår fortfarande. Om `RunInProgress` är `False`, indikerar det att uppdateringen har slutförts.

5. När disken firmware-uppdateringar har tillämpats och uppdaterade domänkontrollanten har startats om, kontrollerar du versionen av inbyggd programvara disk. På den uppdaterade styrenheten, skriver du:

    `Get-HcsFirmwareVersion`
   
    Förväntade disk firmware versioner är:  `XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. Avsluta underhållsläget. Skriv följande kommando för varje enhet domänkontrollant:

    `Exit-HcsMaintenanceMode`

    Styrenheterna startas om när du avslutar underhållsläget.

7. Gå tillbaka till Azure-portalen. Portalen kan inte visa att du installerat Underhåll läge uppdateringarna i 24 timmar.