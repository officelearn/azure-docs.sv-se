


## <a name="attach-an-empty-disk"></a>Ansluta en tom disk
Ansluta en tom disk är ett enkelt sätt att lägga till en datadisk, eftersom Azure skapar VHD-filen för dig och lagrar den i lagringskontot.

1. Klicka på **virtuella datorer (klassiska)**, och välj sedan den lämpliga Virtuellt enheten.

2. I menyn inställningar klickar du på **diskar**.

   ![Koppla en ny tom disk](./media/howto-attach-disk-windows-linux/menudisksattachnew.png)

3. I kommandofältet klickar du på **bifoga som ny**.  
    Den **bifoga ny disk** dialogrutan visas.

    ![Koppla en ny disk](./media/howto-attach-disk-windows-linux/newdiskdetail.png)

    Fyll i följande information:
    - I **filnamn**, använd standardnamnet eller ange en ny för VHD-filen. Datadisken använder ett automatgenererat namn, även om du anger ett annat namn för VHD-filen.
    - Välj den **typ** av datadisken. Alla virtuella datorer stöder standarddiskar. Många virtuella datorer har också stöd för premiumdiskar.
    - Välj den **storlek (GB)** av datadisken.
    - För **vara värd för cachelagring**, Välj none eller skrivskyddad.
    - Klicka på OK för att slutföra.

4. När datadisken har skapats och ansluten, visas den i avsnittet diskar för den virtuella datorn.

   ![Nya och tom datadisk har bifogats](./media/howto-attach-disk-windows-linux/newdiskemptysuccessful.png)

> [!NOTE]
> När du lägger till en datadisk, måste du logga in på den virtuella datorn och initiera disken så att den kan användas.

## <a name="how-to-attach-an-existing-disk"></a>Så här: Koppla en befintlig disk
För att kunna ansluta en befintlig disk måste du ha en VHD-fil tillgänglig i ett lagringskonto. Använd den [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx) cmdlet för att ladda upp VHD-filen till lagringskontot. När du har skapat och överfört VHD-filen, kan du ansluta den till en virtuell dator.

1. Klicka på **virtuella datorer (klassiska)**, och välj sedan lämplig virtuell dator.

2. I menyn inställningar klickar du på **diskar**.

3. I kommandofältet klickar du på **bifoga befintlig**.

    ![Anslut en datadisk](./media/howto-attach-disk-windows-linux/menudisksattachexisting.png)

4. Klicka på **plats**. Visa tillgängliga storage-konton. Välj sedan ett lämpligt storage-konto från de som anges.

    ![Ange disk storage-konto](./media/howto-attach-disk-windows-linux/existdiskstorageaccounts.png)

5. En **lagringskonto** innehåller en eller flera behållare som innehåller hårddiskar (VHD). Välj så att rätt behållare än de som listas.

    ![Ange behållare för virtuella datorer-windows](./media/howto-attach-disk-windows-linux/existdiskcontainers.png)

6. Den **virtuella hårddiskar** panelen visar en lista över de diskenheter som lagras i behållaren. Klicka på någon av diskarna och klicka sedan på Välj.

    ![Ange diskavbildning för virtuella datorer-windows](./media/howto-attach-disk-windows-linux/existdiskvhds.png)

7. Den **bifoga befintlig disk** panelen visas igen, med den plats där lagringskontot, behållaren och valda hårddisken (vhd) för att lägga till den virtuella datorn.

  Ange **vara värd för cachelagring** NONE eller Läs, klicka på OK.

    ![Datadisk har bifogats](./media/howto-attach-disk-windows-linux/exisitingdisksuccessful.png)
