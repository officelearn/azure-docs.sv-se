
1. I den **hybridanslutningar** bladet, klickar du på hybridanslutning som du just skapat, klicka **lyssnare installationsprogrammet**.
   
    ![Klicka på lyssnaren installationsprogrammet](./media/app-service-hybrid-connections-manager-install/D04ClickListenerSetup.png)
2. Den **Hybrid anslutningsegenskaper** blad öppnas. Under **på lokal Hybridanslutningshanterare**, Välj **hämta och konfigurera manuellt**, spara det Hämta paketet HybridConnectionManager.msi och kopiera anslutningssträngen gateway.
   
    ![Klicka här om du vill installera](./media/app-service-hybrid-connections-manager-install/D05ClickToInstallHCM.png)
3. Skriv följande kommando för att starta installationsprogrammet från en administratörskommandotolk:
   
        start HybridConnectionManager.msi
4. När installationsprogrammet körs, klickar du på **inte**, bläddra till mappen %ProgramFiles%\Microsoft\HybridConnectionManager kör HCMConfigWizard.exe och klickar på **Ja** i den **användarkonto Kontrollen** dialogrutan.
5. Klistra in anslutningssträngen hybrid som du kopierade tidigare och klicka på **OK**. 
   
    ![Installera](./media/app-service-hybrid-connections-manager-install/D08aHCMInstallManual.png)
6. När installationen är klar klickar du på **Stäng**.
   
    ![Klicka på Stäng](./media/app-service-hybrid-connections-manager-install/D09HCMInstallComplete.png)
   
    På den **hybridanslutningar** bladet den **Status** kolumnen visar nu **ansluten**. 
   
    ![Anslutna Status](./media/app-service-hybrid-connections-manager-install/D10HCStatusConnected.png)

