## <a name="prerequisites"></a>Krav

### <a name="azure-subscription"></a>Azure-prenumeration
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

### <a name="azure-roles"></a>Azure-roller
Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna **deltagare** eller **ägare**, eller vara **administratör** för Azure-prenumerationen. I Azure-portalen klickar du på ditt **användarnamn** i det övre högra hörnet och väljer **Behörigheter** för att visa de behörigheter du har i prenumerationen. Om du har åtkomst till flera prenumerationer väljer du rätt prenumeration. För exempel på instruktioner om hur du lägger till en användare till en roll läser du artikeln [Lägg till roller](../articles/billing/billing-add-change-azure-subscription-administrator.md).

### <a name="azure-storage-account"></a>Azure Storage-konto
I den här snabbstarten använder du ett allmänt Azure Storage-konto (Blob Storage, för att vara specifik) som datalager för både **källa** och **destination**. Om du inte har något allmänt Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account). 

#### <a name="get-storage-account-name-and-account-key"></a>Hämta lagringskontots namn och åtkomstnyckel
Du använder namnet och nyckeln för Azure Storage-kontot i den här snabbstarten. Följande procedur innehåller steg för att få fram namnet och nyckeln för ditt lagringskonto. 

1. Öppna webbläsaren och gå till [Azure Portal](https://portal.azure.com). Logga in med ditt Azure-användarnamn och lösenord. 
2. Klicka på **Fler tjänster >** i den vänstra menyn, filtrera på nyckelordet **Lagring** och välj **Lagringskonton**.

    ![Sök efter lagringskontot](media/data-factory-quickstart-prerequisites/search-storage-account.png)
3. Filtrera på ditt lagringskonto (om det behövs) i listan med lagringskonton och välj sedan **ditt lagringskonto**. 
4. Gå till sidan **Lagringskonto** väljer du **Åtkomstnycklar** i menyn.

    ![Hämta lagringskontots namn och nyckel](media/data-factory-quickstart-prerequisites/storage-account-name-key.png)
5. Kopiera värdena från fälten med **lagringskontots namn** och **nyckel 1** till Urklipp. Klistra in dem i Anteckningar eller något annat redigeringsprogram och spara. Du använder det senare i den här snabbstarten.   

#### <a name="create-input-folder-and-files"></a>Skapa indatamapp och filer
Det här avsnittet förutsätter att du har en blobbehållare med namnet **adftutorial** i Azure Blob Storage. Skapa en mapp med namnet **input** i behållaren och ladda upp en exempelfil i indatamappen. 

1. På sidan **Lagringskonto** växlar du till **Översikt** och klickar på **Blobbar**. 

    ![Alternativet Välj blobar](media/data-factory-quickstart-prerequisites/select-blobs.png)
2. På sidan **Blob Service** klickar du på **+ Behållare** i verktygsfältet. 

    ![Lägga till behållarknapp](media/data-factory-quickstart-prerequisites/add-container-button.png)    
3. I dialogrutan **Ny behållare** anger du **adftutorial** som namn och klickar på **OK**. 

    ![Ange namn på behållare](media/data-factory-quickstart-prerequisites/new-container-dialog.png)
4. Klicka på **adftutorial** i listan över behållare. 

    ![Välja behållaren](media/data-factory-quickstart-prerequisites/seelct-adftutorial-container.png)
1. På sidan **Behållare** klickar du på **Ladda upp** i verktygsfältet.  

    ![Knappen för överföring](media/data-factory-quickstart-prerequisites/upload-toolbar-button.png)
6. På sidan **Ladda upp blob** klickar du på **Avancerat**.

    ![Klicka på länken Avancerat](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
7. Öppna **Anteckningar** och skapa en fil med namnet **emp.txt** med följande innehåll: Spara den i mappen **c:\ADFv2QuickStartPSH**: Skapa mappen **ADFv2QuickStartPSH** om den inte redan finns.
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. På sidan **Ladda upp blob** i Azure-portalen bläddrar du och väljer filen **emp.txt** för fältet **Filer**. 
9. Ange **input** som värdet som **Ladda upp till mapp** arkiverade. 

    ![Ladda upp blobinställningar](media/data-factory-quickstart-prerequisites/upload-blob-settings.png)    
10. Kontrollera att mappen är **input** och att filen är **emp.txt** och klicka på **Ladda upp**.
11. Du bör se filen **emp.txt** och uppladdningens status i listan. 
12. Stäng sidan **Ladda upp blob** genom att klicka på **X** i hörnet. 

    ![Stänga sidan Ladda upp blob](media/data-factory-quickstart-prerequisites/close-upload-blob.png)
1. Låt **behållarsidan** vara öppen. Du kommer att använda den för att bekräfta utdata i slutet av snabbstarten.