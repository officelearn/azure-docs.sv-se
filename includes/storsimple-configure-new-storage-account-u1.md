<!--author=alkohli last changed: 9/17/15-->

#### Så här lägger du till ett lagringskonto i StorSimple 8000 Series uppdatering 1.0
1. På StorSimple Manager-tjänstens startsida väljer du din tjänst och dubbelklickar på den. Då kommer du till **Snabbstart**-sidan. Välj fliken **Konfigurera**.
2. Klicka på **Lägg till/redigera lagringskonto**.
3. I dialogrutan **Lägg till/redigera lagringskonto**, klickar du på **Lägg till ny**.
4. I **Leverantör**-fältet väljer du lämplig molntjänstleverantör. De leverantörer som stöds är Azure, Amazon S3, Amazon S3 med RRS, HP och OpenStack. Ange autentiseringsuppgifter och plats som associeras med lagringskontot för dina molntjänstleverantörer. Fälten som visas för autentiseringsuppgifter kommer att skilja sig åt beroende på vilken molntjänstleverantör du angett. 
   
   * Om du har valt Azure som molntjänstleverantör, anger du **namn** och primär **åtkomstnyckel** för ditt Microsoft Azure-lagringskonto. För ett Azure-konto fylls platsen automatiskt i.
     
        ![Add Azure storage account](./media/storsimple-configure-new-storage-account-u1/AddAzureStorageaccount-include.png)
   * Om du har valt Amazon S3 eller Amazon S3 med RRS, anger du ett eget **namn på lagringskonto**, **åtkomstnyckel** och **hemlig nyckel**. För Amazon S3 och Amazon S3 med RRS, stöds följande platser:
     
     * US Standard
     * US West (Oregon)
     * US West (Northern California)
     * EU (Ireland)
     * Asia Pacific (Singapore)
     * Asia Pacific (Sydney)
     * Asia Pacific (Tokyo)
     * South America (Sao Paulo)
       
       ![Add Amazon storage account](./media/storsimple-configure-new-storage-account-u1/AddAmazonStorageaccount-include.png)
   * Om du valt HP som din molntjänstleverantör, anger du ett eget **namn på lagringskonto**, **klient-ID**, **användarnamn** och **lösenord**. Följande platser stöds för HP:
     
     * US East
     * US West
       
       ![Add HP storage account](./media/storsimple-configure-new-storage-account-u1/AddHPStorageaccount-include.png)
   * Om du har valt **Openstack** som din molntjänstleverantör, anger du **värdnamn**, **åtkomstnyckel** och **hemlig nyckel**.
     
     > [!NOTE]
     > Ett eget namn är tillåtet för alla molntjänstleverantörer förutom Azure. Du kan använda olika egna namn och skapa mer än ett lagringskonto med samma uppsättning autentiseringsuppgifter.
     > 
     > 
     
        ![Add Openstack storage account](./media/storsimple-configure-new-storage-account-u1/AddOpenstackStorageaccount-include.png)
5. Välj **Aktivera SSL-läge** om du vill skapa en säker kanal för nätverkskommunikation mellan din enhet och molnet. Avmarkera kryssrutan **Aktivera SSL-läge** endast om du arbetar inom ett privat moln.
   
   > [!NOTE]
   > Om du använder HP som leverantör är SSL alltid aktiverat.
   > 
   > 
6. Klicka på kryssikonen ![kryssikon](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png). Du meddelas när lagringskontot har skapats.
7. Det nyligen skapade lagringskontot kommer att visas på sidan **Konfigurera**, under **Lagringskonton**. Klicka på **spara** för att spara det nya lagringskontot. Klicka på **OK** när du uppmanas att bekräfta.

<!--HONumber=Sep16_HO3-->


