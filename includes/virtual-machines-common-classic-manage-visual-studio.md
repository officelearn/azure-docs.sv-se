Du kan skapa virtuella datorer i Azure med hjälp av Server Explorer i Visual Studio.

## <a name="create-an-azure-virtual-machine-in-server-explorer"></a>Skapa en virtuell Azure-dator i Server Explorer
Du kan skapa en virtuell dator i den [Azure-hanteringsportalen](http://go.microsoft.com/fwlink/?LinkID=253103), du kan också skapa en virtuell dator i Azure med hjälp av kommandon i Server Explorer. Virtuella datorer kan användas, till exempel att tillhandahålla en klientdel bakom en gemensam offentlig belastningsutjämnade-slutpunkt.

### <a name="to-create-a-new-virtual-machine"></a>Skapa en ny virtuell dator
1. I Server Explorer öppnar den **Azure** och klickar på **virtuella datorer**.
2. Klicka på snabbmenyn **Skapa virtuell dator**.
   
    Den **skapa en ny virtuell dator** guiden visas.
   
    ![Kommandot Skapa virtuell dator](./media/virtual-machines-common-classic-create-manage-visual-studio/IC718342.png)
3. På den **Välj en prenumeration** , välja en prenumeration att använda när du skapar den virtuella datorn och klickar sedan på **nästa**.
   
    Om du inte är inloggad på Azure, klickar du på **logga In** att logga in. Markera din Azure-prenumeration i listrutan om den inte redan är markerad.
4. På den **markerar du en avbildning av virtuell dator** väljer du en bildtyp i den **bildtypen** nedrullningsbar listruta och välj sedan en avbildningar av virtuella datorer i den **avbildningsnamn** listruta . När du är klar klickar du på **nästa**.
   
    ![Välj en virtuell dator sida](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744137.png)
   
    Du kan välja följande bildtyper.
   
   * **Offentliga bilder** visar virtuella avbildningar av operativsystem och serverprogram, till exempel Windows Server och SQL Server.
   * **MSDN-bilder** visar virtuella avbildningar av program som är tillgängliga för MSDN-prenumeranter som Visual Studio och Microsoft Dynamics.
   * **Privat bilder** listor specialiserade och generaliserad avbildningar av virtuella datorer som du har skapat.
     
     Mer information om särskilda och generaliserad virtuella datorer, se [VM-avbildning](https://azure.microsoft.com/blog/2014/04/14/vm-image-blog-post/). Se [så här skapar du en virtuell dator i Windows till mall](https://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/) för information om hur du aktiverar en virtuell dator till en mall som du kan använda för att snabbt skapa nya förkonfigurerade virtuella datorer.
     
     Du kan klicka på en virtuell dator avbildningen kan se information om bilden till höger på sidan.
     
     > [!NOTE]
     > Du kan inte lägga till avbildningar av virtuella datorer till den **offentliga bilder** eller **MSDN-bilder** visas eftersom de är skrivskyddad. Alla virtuella datorer som du skapar läggs till i **privata bilder** lista.
     > 
     > 
     
     Om du är en MSDN-prenumerant med en prenumeration på Visual Studio-nivån, kan du skapa en förskapad Azure virtuell dator som innehåller Visual Studio, samt andra bilder. Mer information finns i [skapar en virtuell dator i Visual Studio av avbildningen med hjälp av avbildningar Visual Studio 2013-galleriet för MSDN-prenumeranter](http://visualstudio2013msdngalleryimage.azurewebsites.net) och [MSDN prenumerationer](https://www.visualstudio.com/products/msdn-subscriptions-vs). |
5. På den **grundläggande inställningar för virtuell dator** anger du ett namn för datorn och Lägg sedan till specifikationerna för den virtuella datorn, inklusive storlek, och ett användarnamn och lösenord. När du är klar klickar du på **nästa**.
   
    Du ska använda nytt namn och lösenord för att logga in på datorn med hjälp av fjärrskrivbord, så det är en bra idé att skriva ned om du skulle glömma. När du har skapat en virtuell Azure-dator i Visual Studio kan du ändra dess storlek och andra inställningar i den [Azure-hanteringsportalen](http://go.microsoft.com/fwlink/?LinkID=253103).
   
   > [!NOTE]
   > Om du väljer större storlek för den virtuella datorn, kan extra avgifter tillkomma. Se [prisinformation för virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/) för mer information.
   > 
   > 
6. Virtuella datorer som skapats i Visual Studio kräver en tjänst i molnet. På den **moln tjänstinställningar** väljer du en tjänst i molnet för den virtuella datorn, eller klicka på **< Skapa nytt >** i den nedrullningsbara listan om du inte redan har ett moln tjänst eller använda en ny. Ett lagringskonto krävs också, så väljer något lagringskonto (eller skapa ett nytt lagringskonto) i den **lagringskonto** listrutan. Se [introduktion till Microsoft Azure Storage](../articles/storage/common/storage-introduction.md) för mer information.
7. Om du vill ange ett virtuellt nätverk (som är valfri) markerar du den i listrutorna för listrutan virtuellt nätverk och undernät.
   
    Virtuella datorer som är medlemmar i en tillgänglighetsuppsättning har distribuerats till olika feldomäner. Se [Azure Virtual Network](https://azure.microsoft.com/services/virtual-network/) för mer information.
8. Om du vill att den virtuella datorn ska tillhöra en tillgänglighetsuppsättning (även valfritt) Välj den **ange en tillgänglighetsuppsättning** kryssrutan och välj sedan en tillgänglighetsuppsättning i den nedrullningsbara listrutan. När du är klar väljer du den **nästa** knappen.
   
    Lägger till den virtuella datorn i en tillgänglighetsuppsättning hjälper ditt program vara tillgängliga under nätverksfel, lokal disk maskinvarufel och planerade driftavbrott. Du måste använda den [Azure-hanteringsportalen](http://go.microsoft.com/fwlink/?LinkID=253103) att skapa virtuella nätverk, undernät och tillgänglighet. Se [hantera tillgängligheten för Virtual Machines](https://azure.microsoft.com/documentation/articles/manage-availability-virtual-machines/) för mer information.
9. På den **slutpunkter** anger de offentliga slutpunkterna som finns tillgängliga för användare av den virtuella datorn. Du kan till exempel välja att aktivera HTTP (Port 80) förutom fjärrskrivbord och PowerShell-slutpunkter som är aktiverade som standard. Om du vill lägga till en slutpunkt kan du välja det i den **portnamn** nedrullningsbara listan listruta och välj sedan den **Lägg till** knappen. Ta bort en slutpunkt, välja röda **X** bredvid namnet i listan över slutpunkter.
   
    ![Sidan slutpunkter i guiden för virtuella datorer.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC718351.png)
   
    Slutpunkter som är tillgängliga beror på den molntjänst som du valt för den virtuella datorn. Se [Azure Tjänsteslutpunkter](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/) för mer information.
   
   > [!NOTE]
   > Aktivera offentliga slutpunkter tillgängliggör services på den virtuella datorn till internet. Se till att installera och konfigurera slutpunkter och tjänster på den virtuella datorn som inställningen åtkomstkontrollistor (ACL) för slutpunkter. Se [så ange slutpunkter till en virtuell dator](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/) för mer information.
   > 
   > 
10. När du är klar väljer du konfigurerar inställningarna för virtuella datorn, den **skapa** för att skapa den virtuella datorn.
    
     Eftersom Azure skapar den virtuella datorn, den **Azure-aktivitetsloggen** visar förloppet för åtgärden för att skapa virtuella datorn.
    
     ![Virtuella aktivitetsloggen - pågår.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744138.png)
    
     Välj för att visa information om virtuella datorn endast den **virtuella datorer** fliken i den **Azure-aktivitetsloggen**.
    
     ![Virtuella aktivitetsloggen - slutfördes.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744139.png)
    
     Om åtgärden är klar visas den nya virtuella datorn under den **virtuella datorer** nod i Server Explorer. Du kan logga in på den genom att klicka på den **ansluta med hjälp av fjärrskrivbord** genväg.
    
     ![Den virtuella datorn som visas i Server Explorer.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744140.png)

## <a name="manage-your-virtual-machines"></a>Hantera dina virtuella datorer
Du kan också visa eller ändra inställningarna för den virtuella datorn på konfigurationssidan virtuella förutom avslutas, ansluta, uppdatera och lägga till kontrollpunkter till den valda virtuella datorn. Du kan:

* Ändra storleken på virtuella datorn.
* Välj tillgänglighetsuppsättning för att använda med den virtuella datorn.
* Lägga till, ta bort eller ändra inställningar för offentliga slutpunkter.
* Lägga till, ta bort eller konfigurera tillägg för virtuell dator.
* Visa information om de diskar som är kopplade till den virtuella datorn.

### <a name="view-or-change-virtual-machine-settings"></a>Visa eller ändra inställningar för virtuell dator
1. I Server Explorer, väljer du den virtuella datorn i den **Azure Virtual Machines** nod.
2. Välj på snabbmenyn **konfigurera** att visa konfigurationssidan för virtuell dator.
   
    ![Konfigurationssidan för virtuell Azure-dator](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744141.png)
3. Visa informationen om virtuella datorn eller ändra den.

### <a name="save-or-restore-the-status-of-your-virtual-machine"></a>Spara eller återställa status för den virtuella datorn
När du konfigurera den virtuella datorn och installera programvara på den är en bra idé att regelbundet spara ditt arbete genom att skapa kontrollpunkter för virtuell dator. En kontrollpunkt är en ögonblicksbild eller avbildning av det aktuella tillståndet för den virtuella datorn. Om något går fel med den virtuella datorn eller om du vill konfigurera om den virtuella datorn, kan du spara tid genom att återställa den till ett tidigare tillstånd kontrollpunkt i stället för att börja om från början.

### <a name="to-create-a-virtual-machine-checkpoint"></a>Skapa en kontrollpunkt för virtuell dator
1. I Server Explorer, väljer du den virtuella datorn i den **Azure Virtual Machines** nod.
2. Välj på snabbmenyn **konfigurera** att visa konfigurationssidan för virtuell dator.
3. På konfigurationssidan, väljer du den **Skapa avbildning** knappen.
   
    ![Konfigurationen av Azure sidan klipp ut](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744142.png)
   
    Den **avbilda virtuella** visas.
   
    ![Dialogrutan för Azure avbilda virtuell dator](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744143.png)
4. Ange en avbildningsetiketten och en beskrivning. En standardetikett och en beskrivning finns, men du kan skriva över dem med din egen om du vill.
5. Om du redan har kört Sysprep på den virtuella datorn, väljer du den **jag har kört Sysprep på den virtuella datorn** rutan.
   
    Sysprep är ett verktyg som bland annat tar bort system-specifika data från den virtuella datorns version av Windows, vilket gör den mall som andra kan använda. Se [så här skapar du en virtuell dator i Windows till mall](https://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/) för mer information. Säkerhetskopiera den virtuella datorn innan du kör Sysprep.
6. När du är klar väljer du konfigurera inställningar för avbildning, den **avbilda** för att skapa kontrollpunkten.
   
    Eftersom Azure skapar kontrollpunkt, den **Azure-aktivitetsloggen** visar status för åtgärden.
   
    ![Skapa en kontrollpunkt för virtuell dator](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744144.png)
   
    När kontrollpunkt har slutförts, visas den i den **Azure-aktivitetsloggen**.
   
    ![CheckPoint-åtgärden slutfördes](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744145.png)

## <a name="to-manage-virtual-machine-checkpoints"></a>Att hantera kontrollpunkter för virtuell dator
### <a name="to-restore-a-virtual-machine-to-a-previously-saved-state"></a>Återställa en virtuell dator till ett tidigare sparat läge
* Följ stegen som beskrivs i [stegvisa: utföra molnet återställer av Microsoft Azure virtuella datorer med hjälp av PowerShell - del 2](http://blogs.technet.com/b/keithmayer/archive/2014/02/04/step-by-step-perform-cloud-restores-of-windows-azure-virtual-machines-using-powershell-part-2.aspx).

### <a name="to-delete-a-checkpoint"></a>Ta bort en kontrollpunkt
1. Gå till den [Azure-hanteringsportalen](http://go.microsoft.com/fwlink/?LinkID=253103).
2. På sidan för konfiguration av virtuell dator väljer du den **bilder** högst upp på sidan.
3. Välj den kontrollpunkt som du vill ta bort och välj sedan den **ta bort** längst ned på sidan.

## <a name="shut-down-your-virtual-machine"></a>Stäng av den virtuella datorn
1. I Server Explorer, väljer du den virtuella datorn som du vill stänga av i den **Azure Virtual Machines** nod.
2. På snabbmenyn, väljer du antingen den **avstängning** kommandot, eller välj **konfigurera** att visa konfigurationssidan för virtuell dator och välj sedan den **avstängning** knappen.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du skapar virtuella datorer finns [skapa en virtuell dator kör Linux](../articles/virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) och [skapa en virtuell dator som kör Windows i Azure preview portal](../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

