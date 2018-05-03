


## <a name="attach-an-empty-disk"></a>Ansluta en tom disk
Bifoga en tom disk är ett enkelt sätt att lägga till en datadisk eftersom Azure skapas VHD-fil och lagrar den i lagringskontot.

1. Klicka på **virtuella datorer (klassisk)**, och väljer sedan lämplig VM.

2. Klicka på menyn inställningar **diskar**.

   ![Koppla en ny tom disk](./media/howto-attach-disk-windows-linux/menudisksattachnew.png)

3. Klicka på kommandofältet **bifoga nya**.  
    Den **bifoga den nya disken** dialogrutan visas.

    ![Koppla en ny disk](./media/howto-attach-disk-windows-linux/newdiskdetail.png)

    Fyll i följande information:
    - I **filnamn**, acceptera standardnamnet eller ange ett annat namn för VHD-filen. Datadisken använder ett automatiskt genererat namn, även om du anger ett annat namn för VHD-filen.
    - Välj den **typen** av datadisk. Alla virtuella datorer stöder standarddiskar. Många virtuella datorer har också stöd för premiumdiskar.
    - Välj den **storlek (GB)** av datadisk.
    - För **Värdcachelagring**, väljer du ingen eller skrivskyddad.
    - Klicka på OK om du vill avsluta.

4. När datadisken skapas och bifogas visas den i avsnittet diskar i den virtuella datorn.

   ![Nya och tom disk har kopplats](./media/howto-attach-disk-windows-linux/newdiskemptysuccessful.png)

> [!NOTE]
> När du lägger till en datadisk, måste du logga in på den virtuella datorn och initiera disken så att den kan användas.

## <a name="how-to-attach-an-existing-disk"></a>Så här: bifoga en befintlig disk
För att kunna ansluta en befintlig disk måste du ha en VHD-fil tillgänglig i ett lagringskonto. Använd den [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx) för att överföra VHD-filen till lagringskontot. När du har skapat och överföra VHD-filen, kan du koppla den till en virtuell dator.

1. Klicka på **virtuella datorer (klassisk)**, och väljer sedan lämplig virtuell dator.

2. Klicka på menyn inställningar **diskar**.

3. Klicka på kommandofältet **bifoga befintliga**.

    ![Anslut en datadisk](./media/howto-attach-disk-windows-linux/menudisksattachexisting.png)

4. Klicka på **plats**. Visa tillgängliga storage-konton. Välj sedan ett lämpligt storage-konto från dem i listan.

    ![Ange disk storage-konto](./media/howto-attach-disk-windows-linux/existdiskstorageaccounts.png)

5. En **lagringskonto** innehåller en eller flera behållare som innehåller hårddiskar (VHD). Välj lämpliga behållare från dem i listan.

    ![Ange behållare för virtuella datorer windows](./media/howto-attach-disk-windows-linux/existdiskcontainers.png)

6. Den **virtuella hårddiskar** panelen visas diskenheter som lagras i behållaren. Klicka på en av diskarna och klicka på Välj.

    ![Ange diskavbildning för virtuella datorer windows](./media/howto-attach-disk-windows-linux/existdiskvhds.png)

7. Den **bifoga den befintliga disken** panelen visas igen med den plats där lagringskonto, behållare och valda hårddisk (vhd) för att lägga till den virtuella datorn.

  Ange **Värdcachelagring** NONE eller Läs bara, klicka på OK.

    ![Datadisk har kopplats](./media/howto-attach-disk-windows-linux/exisitingdisksuccessful.png)
