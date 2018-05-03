

När du skapar ett webbprojekt program för Azure kan etablera du en virtuell dator i Azure. Du kan sedan konfigurera den virtuella datorn med ytterligare programvara eller använda den virtuella datorn för diagnostik och felsökning.

Om du vill skapa en virtuell dator när du skapar ett webbprogram, så här:

1. I Visual Studio klickar du på **filen** > **ny** > **projekt** > **Web**, och välj sedan **ASP.NET-webbprogram** (under den **Visual C#** eller **Visual Basic** noder).
2. I den **nytt ASP.NET-projekt** dialogrutan, Välj typ av webbprogram och i avsnittet Azure i dialogrutan (i det nedre högra hörnet), se till att den **värd i molnet** kryssrutan är valda (den här kryssrutan **skapa fjärresurser** i vissa installationer).
   
    ![][0]
3. Det här exemplet i listrutan under Microsoft Azure, Välj **virtuell dator (v1)**, och klicka sedan på den **OK** knappen.
4. Logga in på Azure om du uppmanas. Den **Skapa virtuell dator** dialogrutan visas.
   
    ![][2]
5. I den **DNS-namnet** ange ett namn för den virtuella datorn. DNS-namnet måste vara unikt i Azure. Om det angivna namnet inte är tillgänglig, visas ett rött utropstecken.
6. I den **bild** Välj den avbildning du vill basera den virtuella datorn på. Du kan välja något av Azure-dator bilder eller bilden som du har överfört till Azure.
7. Lämna den **aktivera IIS och Web Deploy** Markera kryssrutan om du planerar att installera en annan webbserver. Du kan inte publicera från Visual Studio om du inaktiverar webbdistribution. Du kan lägga till IIS och Web Deploy någon av de paketerade Windows Server-avbildningar, inklusive dina egna anpassade avbildningar.
8. I den **storlek** Välj storleken på den virtuella datorn.
9. Ange autentiseringsuppgifterna för den virtuella datorn. Anteckna dem, eftersom du behöver dem åtkomst till datorn via fjärrskrivbord.
10. I den **plats** väljer du regionen som värd för den virtuella datorn.
11. Klicka på den **OK** för att starta den virtuella datorn. Du kan följa förloppet för åtgärden i den **utdata** fönster.
    
    ![][3]
12. När den virtuella datorn har etablerats publicerade skript skapas i en **PublishScripts** nod i din lösning. Publicerade skriptet körs och etablerar en virtuell dator i Azure. Den **utdata** fönster visar status. Skriptet utför följande åtgärder för att konfigurera den virtuella datorn:
    
    * Skapar den virtuella datorn om den inte redan finns.
    * Skapar ett lagringskonto med ett namn som börjar med `devtest`, men endast om det inte redan finns ett lagringskonto för den angivna regionen.
    * Skapar en molnbaserad tjänst som en behållare för den virtuella datorn och skapar en webbroll för webbprogrammet.
    * Konfigurerar webbdistribution på den virtuella datorn.
    * Konfigurerar IIS och ASP.NET på den virtuella datorn.
    
    ![][4]
13. (Valfritt) Du kan ansluta till den nya virtuella datorn. I **Server Explorer**, expandera den **virtuella datorer** noden, välj noden för den virtuella datorn som du skapade och dess snabbmenyn, Välj **Anslut med fjärrskrivbord**. Du kan också i **Cloud Explorer** du **öppna i portalen** på snabbmenyn och ansluta till virtuella.
    
    ![][5]

## <a name="next-steps"></a>Nästa steg
Om du vill anpassa de publicerade skript som du har skapat kan du läsa mer detaljerad information på [med hjälp av Windows PowerShell-skript för publicera utvecklings-och testmiljöer](http://msdn.microsoft.com/library/dn642480.aspx).

[0]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_NewProject.PNG
[1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SignIn.PNG
[2]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_CreateVM.PNG
[3]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_Provisioning.png
[4]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_SolutionExplorer.png
[5]: ./media/virtual-machines-common-classic-web-app-visual-studio/VS_Create_VM_Connect.png
