1. Logga in på [Azure Portal](http://portal.azure.com).  
2. Klicka på **ny** > **Compute** > **finns alla** och skriv sedan **SUSE**.
   
3. Välj en avbildning av virtuell dator OpenSUSE och klicka sedan på pilen för att fortsätta.
5. Första **konfiguration av virtuell dator** sidan:
   
   * Ange en **virtuellt datornamn**, till exempel ”testlinuxvm”. Namnet måste innehålla mellan 3 och 15 tecken, kan innehålla endast bokstäver, siffror och bindestreck, och måste börja med en bokstav och sluta med en bokstav eller siffra.
   * Kontrollera den **nivå** och välj en **storlek**. Nivån anger storleken som du kan välja bland. Storlek påverkar kostnaden för att använda den, samt konfigurationsalternativ som hur många diskar du kan ansluta. Mer information finns i [storlekar för virtuella datorer](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
   * Ange en **nytt användarnamn**, eller acceptera standardinställningarna, **azureuser**. Det här namnet har lagts till i filen med listan Sudoers.
   * Bestämmer vilka typer av **autentisering** ska användas. För allmänna lösenordsriktlinjer finns [starka lösenord](http://msdn.microsoft.com/library/ms161962.aspx).
6. På nästa **konfiguration av virtuell dator** sidan:
   
   * Använd standard **skapa en ny molntjänst**.
   * I den **DNS-namnet** Skriv ett unikt DNS-namn ska användas som en del av adress, till exempel ”testlinuxvm”.
   * I den **Region/tillhörighet grupp/virtuellt nätverk** väljer du en region där den här virtuella bilden kommer att finnas.
   * Under **slutpunkter**, hålla SSH-slutpunkten. Du kan lägga till andra nu, eller lägga till, ändra eller ta bort dem när den virtuella datorn har skapats.
     
     > [!NOTE]
     > Om du vill att en virtuell dator att använda ett virtuellt nätverk du **måste** ange det virtuella nätverket när du skapar den virtuella datorn. Du kan inte lägga till en virtuell dator till ett virtuellt nätverk när du har skapat den virtuella datorn. Mer information finns i [översikt över virtuella nätverk](../articles/virtual-network/virtual-networks-overview.md).
     > 
     > 
7. På sist **konfiguration av virtuell dator** , behålla standardinställningarna och klickar sedan på bockmarkeringen för att avsluta.

Portalen visar den nya virtuella datorn under **virtuella datorer**. Medan status rapporteras som **(allokering)**, den virtuella datorn ställs in. När status rapporteras som **kör**, du kan gå vidare till nästa steg.

## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn
Du ska använda SSH eller PuTTY för att ansluta till den virtuella datorn, beroende på operativsystemet på den dator som du ska ansluta från:

* Använd SSH från en dator som kör Linux. Skriv följande vid kommandotolken:
  
    `$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180`
  
    Ange användarens lösenord.
* Använd PuTTY från en dator som kör Windows. Om du inte har installerats kan du hämta det från den [PuTTY-hämtningssida][PuTTYDownload].
  
    Spara **putty.exe** till en katalog på datorn. Öppna en kommandotolk, navigera till mappen och kör **putty.exe**.
  
    Skriv värdnamnet, till exempel ”testlinuxvm.cloudapp.net” och ”22” för den **Port**.
  
    ![PuTTY skärmen][Image6]  

## <a name="update-the-virtual-machine-optional"></a>Uppdatera den virtuella datorn (valfritt)
1. När du är ansluten till den virtuella datorn kan installera du alternativt uppdateringar och korrigeringsfiler. För att köra uppdateringen, skriver du:
   
    `$ sudo zypper update`
2. Välj **programvara**, sedan **Online-uppdatering** att visa tillgängliga uppdateringar. Välj **acceptera** och starta installationen som du kan tillämpa alla tillgängliga nya korrigeringsfiler (utom som är valfritt).
3. När installationen är klar väljer du **Slutför**.  Systemet är nu uppdaterad.

[PuTTYDownload]: http://www.puttyssh.org/download.html

[Image6]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png
