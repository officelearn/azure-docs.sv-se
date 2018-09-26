

När du skapar ett webbprogramsprojekt för Azure kan etablera du en virtuell dator i Azure. Du kan sedan konfigurera den virtuella datorn med ytterligare programvara eller använda den virtuella datorn för diagnostik och felsökning.

Om du vill skapa en virtuell dator när du skapar ett webbprogram, Följ dessa steg:

1. I Visual Studio klickar du på **filen** > **New** > **projekt** > **Web**, och välj sedan **ASP.NET-webbprogram** (under den **Visual C#** eller **Visual Basic** noder).
2. I den **nytt ASP.NET-projekt** dialogrutan, Välj typ av webbprogram och i Azure i dialogrutan (i det nedre högra hörnet), se till att den **värd i molnet** kryssrutan valda (den här kryssrutan **skapa fjärransluten resurser** i vissa installationer).
   
    ![][0]
3. Det här exemplet, i listrutan under Microsoft Azure, Välj **VM (v1)**, och klicka sedan på den **OK** knappen.
4. Logga in på Azure om du uppmanas. Den **Skapa virtuell dator** dialogrutan visas.
   
    ![][2]
5. I den **DNS-namnet** anger du ett namn för den virtuella datorn. DNS-namnet måste vara unikt i Azure. Om namnet du angav är inte tillgängligt, visas ett rött utropstecken.
6. I den **bild** väljer du den avbildning du vill basera den virtuella datorn på. Du kan välja mellan standard Azure VM-bilder eller din avbildning som du har överfört till Azure.
7. Lämna den **aktivera IIS- och Web Deploy** Markera kryssrutan om du planerar att installera en annan server. Du kan inte publicera från Visual Studio om du inaktiverar Web Deploy. Du kan lägga till IIS och Web Deploy till någon av de paketerade Windows Server-avbildningar, inklusive dina egna anpassade avbildningar.
8. I den **storlek** väljer storleken på den virtuella datorn.
9. Ange autentiseringsuppgifterna för den virtuella datorn. Skriv ned dem, eftersom du behöver dem få tillgång till datorn via fjärrskrivbord.
10. I den **plats** väljer du regionen som värd för den virtuella datorn.
11. Klicka på den **OK** knappen för att börja skapa den virtuella datorn. Du kan följa förloppet för åtgärden i den **utdata** fönster.
    
    ![][3]
12. När den virtuella datorn har etablerats publicerade skript skapas i en **PublishScripts** nod i din lösning. Publicerade skriptet körs och som etablerar en virtuell dator i Azure. Den **utdata** status visas i fönstret. Skriptet utför följande åtgärder för att konfigurera den virtuella datorn:
    
    * Skapar den virtuella datorn om den inte redan finns.
    * Skapar ett lagringskonto med ett namn som börjar med `devtest`, men bara om det inte redan finns ett lagringskonto för den angivna regionen.
    * Skapar en molnbaserad tjänst som en behållare för den virtuella datorn och skapar en webbroll för webbprogrammet.
    * Konfigurerar webbdistribution på den virtuella datorn.
    * Konfigurerar IIS och ASP.NET på den virtuella datorn.
    
    ![][4]
13. (Valfritt) Du kan ansluta till den nya virtuella datorn. I **Server Explorer**, expandera den **virtuella datorer** nod, väljer du noden för den virtuella datorn som du skapade och på dess snabbmenyn, **Anslut med Remote Desktop**. Du kan också **Cloud Explorer** du **öppna i portalen** på snabbmenyn och ansluta till den virtuella datorn i det.
    
    ![][5]

## <a name="next-steps"></a>Nästa steg
Om du vill anpassa de publicerade skript som du har skapat kan du läsa mer detaljerad information på [med hjälp av Windows PowerShell-skript för publicera på utvecklings- och testmiljöer](http://msdn.microsoft.com/library/dn642480.aspx).

[0]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_NewProject.PNG
[1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SignIn.PNG
[2]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_CreateVM.PNG
[3]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_Provisioning.png
[4]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_SolutionExplorer.png
[5]: ./media/virtual-machines-common-classic-web-app-visual-studio/VS_Create_VM_Connect.png
