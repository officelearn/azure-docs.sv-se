---
title: Kom igång med Storage Explorer | Microsoft Docs
description: Hantera Azure Storage-resurser med Storage Explorer
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 04/22/2019
ms.author: cawa
ms.openlocfilehash: 43e76b9331eb8dbe95265810b9191a10d4caee08
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71272030"
---
# <a name="get-started-with-storage-explorer"></a>Kom igång med Storage Explorer

## <a name="overview"></a>Översikt

Microsoft Azure Storage Explorer är en fristående app som gör det enkelt att arbeta med Azure Storage data på Windows, macOS och Linux. I den här artikeln lär du dig flera olika sätt att ansluta till och hantera dina Azure Storage-konton.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>Förutsättningar

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Storage Explorer stöds i följande versioner av Windows:

* Windows 10 (rekommenderas)
* Windows 8
* Windows 7

För alla versioner av Windows krävs .NET Framework 4.6.2 eller senare.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Storage Explorer stöds i följande versioner av macOS:

* macOS 10,12 "Sierra" och senare versioner

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Storage Explorer finns i [Snap Store](https://snapcraft.io/storage-explorer) för de vanligaste distributionerna av Linux och är den rekommenderade installations metoden. Med Storage Explorer Snap installeras alla dess beroenden och uppdateringar automatiskt när nya versioner publiceras i Snap-arkivet.

En lista över distributioner som stöds finns på sidan för den [fästa installationen](https://snapcraft.io/docs/installing-snapd).

Storage Explorer kräver att du använder en lösen ords hanterare, som du kan behöva ansluta manuellt innan Storage Explorer fungerar som den ska. Du kan ansluta Storage Explorer till systemets lösen ords hanterare genom att köra följande kommando:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage Explorer är också tillgängligt som en. tar. gz-hämtning, men du måste installera beroenden manuellt. Installationen av. tar. gz stöds i följande distributioner av Linux:

* Ubuntu 18,04 x64
* Ubuntu 16,04 x64
* Ubuntu 14,04 x64

Installationen av. tar. gz kan fungera på andra distributioner, men endast dessa listade stöds officiellt.

Mer hjälp om hur du installerar Storage Explorer på Linux finns i [fel söknings guiden](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies).

---

## <a name="download-and-install"></a>Hämta och installera

[Hämta och installera Storage Explorer](https://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Ansluta till ett lagringskonto eller en tjänst

I Storage Explorer finns flera olika sätt att ansluta till lagringskonton. I allmänhet kan du antingen:

* [Logga in på Azure för att få åtkomst till dina prenumerationer och deras resurser](#sign-in-to-azure)
* [Anslut en speciell lagrings-eller CosmosDB-resurs](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Logga in på Azure

> [!NOTE]
> För att få fullständig åtkomst till resurser när du har loggat in, kräver Storage Explorer både hantering (Azure Resource Manager) och data lager behörigheter. Det innebär att du behöver Azure Active Directory (Azure AD)-behörigheter, vilket ger dig åtkomst till ditt lagrings konto, behållarna i kontot och data i behållarna. Om du har behörighet för data lagret kan du överväga [att lägga till en resurs via Azure AD](#add-a-resource-via-azure-ad). Mer information om de speciella behörigheter som Storage Explorer kräver finns i [fel söknings guiden](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#rbac-permissions-issues).

1. I Storage Explorer väljer du **Hantera konton** för att gå till panelen konto hantering.

    ![Hantera konton][1]

2. I den vänstra rutan visas nu alla Azure-konton som du har loggat in på. Om du vill ansluta till ett annat konto väljer du **Lägg till ett konto**.

3. Om du vill logga in på ett nationellt moln eller en Azure Stack väljer du List rutan **Azure-miljö** och väljer det Azure-moln som du vill använda. När du har valt din miljö väljer du **inloggnings** knappen. Mer information finns i [ansluta Storage Explorer till en Azure Stack-prenumeration](/azure-stack/user/azure-stack-storage-connect-se).

    ![Inloggnings alternativ][2]

4. När du har loggat in med ett Azure-konto läggs kontot och de Azure-prenumerationer som är kopplade till kontot till i den vänstra rutan. Välj de Azure-prenumerationer som du vill arbeta med och välj sedan **Använd**. (Om du väljer **alla prenumerationer** växlar du mellan att välja mellan alla eller inga av de listade Azure-prenumerationerna.)

    ![Välja Azure-prenumerationer][3]

    I den vänstra rutan visas lagringskontona som är kopplade till de valda Azure-prenumerationerna.

    ![Valda Azure-prenumerationer][4]

### <a name="attach-a-specific-resource"></a>Anslut en speciell resurs

Det finns flera sätt att ansluta till en resurs i Storage Explorer:

* [Lägg till en resurs via Azure AD](#add-a-resource-via-azure-ad). Om du har behörighet för data lagret kan du använda det här alternativet för att lägga till en BLOB-behållare eller en Azure Data Lake Storage Gen2 Blob Storage-behållare.
* [Använd en anslutnings sträng](#use-a-connection-string). Använd det här alternativet om du har en anslutnings sträng till ett lagrings konto. Storage Explorer stöder nyckel-och [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) -anslutningssträngar.
* [Använd en SAS-URI](#use-a-sas-uri). Om du har en [SAS-URI](storage/common/storage-dotnet-shared-access-signature-part-1.md) till en BLOB-behållare, fil resurs, kö eller tabell, använder du den för att ansluta till resursen. Om du vill hämta en SAS-URI kan du antingen använda [Storage Explorer](#generate-a-sas-in-storage-explorer) eller [Azure Portal](https://portal.azure.com).
* [Använd ett namn och en nyckel](#use-a-name-and-key). Om du känner till någon av konto nycklarna till ditt lagrings konto kan du använda det här alternativet för att snabbt ansluta. Nycklarna för ditt lagrings konto finns på panelen **åtkomst nycklar** för lagrings kontot i [Azure Portal](https://portal.azure.com).
* [Anslut till en lokal emulator](#attach-to-a-local-emulator). Om du använder någon av de tillgängliga Azure Storage-emulatorerna kan du använda det här alternativet för att enkelt ansluta till emulatorn.
* [Anslut till ett Azure Cosmos DB-konto med hjälp av en anslutnings sträng](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string). Använd det här alternativet om du har en anslutnings sträng till en CosmosDB-instans.
* [Anslut till Azure Data Lake Store med URI](#connect-to-azure-data-lake-store-by-uri). Använd det här alternativet om du har en URI för att Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Lägg till en resurs via Azure AD

1. Öppna dialog rutan **Anslut** genom att välja knappen **Anslut** i det lodräta verktygsfältet till vänster:

    ![Alternativet Anslut till Azure Storage][9]

2. Om du inte redan har gjort det använder du alternativet **Lägg till ett Azure-konto** för att logga in på det Azure-konto som har åtkomst till resursen. När du har loggat in går du tillbaka till dialog rutan **Anslut** .

3. Välj **Lägg till en resurs via Azure Active Directory (Azure AD)** och välj sedan **Nästa**.

4. Välj det Azure-konto och klient organisation som har åtkomst till den lagrings resurs som du vill ansluta till. Välj **Nästa**.

5. Välj den resurs typ som du vill bifoga och ange sedan den information som krävs för att ansluta. Den information som du anger på den här sidan beror på vilken typ av resurs du lägger till. Se till att välja rätt typ av resurs. När du har angett den information som krävs väljer du **Nästa**.

6. Granska anslutnings sammanfattningen och se till att all information är korrekt. Om det är det väljer du **Anslut**. Annars kan du använda **tillbaka** -knappen för att återgå till föregående sidor för att åtgärda eventuell felaktig information.

När anslutningen har lagts till skickas resurs trädet automatiskt till den nod som representerar anslutningen. Om den inte går till noden tittar du under **lokala & anslutna** > **lagrings konton** >  **(bifogade behållare)**  > **BLOB-behållare**. Om Storage Explorer inte kan lägga till anslutningen, eller om du inte kan komma åt dina data när du har lagt till anslutningen, kan du läsa [fel söknings guiden](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-connection-string"></a>Använd en anslutningssträng

1. Öppna dialog rutan **Anslut** genom att välja knappen **Anslut** i det lodräta verktygsfältet till vänster:

    ![Alternativet Anslut till Azure Storage][9]

2. Välj **Använd en anslutnings sträng**och välj sedan **Nästa**.

3. Välj ett visnings namn för anslutningen och ange din anslutnings sträng. Välj sedan **Nästa**.

4. Granska anslutnings sammanfattningen och se till att all information är korrekt. Om det är det väljer du **Anslut**. Annars kan du använda **tillbaka** -knappen för att återgå till föregående sidor för att åtgärda eventuell felaktig information.

När anslutningen har lagts till skickas resurs trädet automatiskt till den nod som representerar anslutningen. Om den inte går till noden, tittar du under **lokala & anslutna** > **lagrings konton**. Om Storage Explorer inte kan lägga till anslutningen, eller om du inte kan komma åt dina data när du har lagt till anslutningen, kan du läsa [fel söknings guiden](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-sas-uri"></a>Använd en SAS-URI

1. Öppna dialog rutan **Anslut** genom att välja knappen **Anslut** i det lodräta verktygsfältet till vänster:

    ![Alternativet Anslut till Azure Storage][9]

2. Välj **Använd en signatur för delad åtkomst-URI (SAS)** och välj sedan **Nästa**.

3. Välj ett visnings namn för anslutningen och ange din SAS-URI. Tjänst slut punkten för den typ av resurs som du lägger till ska Autofyll. Om du använder en anpassad slut punkt är det möjligt att den inte är det. Välj **Nästa**.

4. Granska anslutnings sammanfattningen och se till att all information är korrekt. Om det är det väljer du **Anslut**. Annars kan du använda **tillbaka** -knappen för att återgå till föregående sidor för att åtgärda eventuell felaktig information.

När anslutningen har lagts till skickas resurs trädet automatiskt till den nod som representerar anslutningen. Om den inte går till noden, tittar du under **lokala & anslutna** > **lagrings konton** >  **(anslutna behållare)**  > *noden tjänst för den typ av behållare som du har bifogat*. Om Storage Explorer inte kan lägga till anslutningen, eller om du inte kan komma åt dina data när du har lagt till anslutningen, kan du läsa [fel söknings guiden](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-name-and-key"></a>Använd ett namn och en nyckel

1. Öppna dialog rutan **Anslut** genom att välja knappen **Anslut** i det lodräta verktygsfältet till vänster:

    ![Alternativet Anslut till Azure Storage][9]

2. Välj **Använd ett lagrings konto namn och nyckel**och välj sedan **Nästa**.

3. Välj ett visnings namn för anslutningen.

4. Ange ditt lagrings konto namn och någon av dess åtkomst nycklar.

5. Välj den **lagrings domän** som du vill använda och välj sedan **Nästa**.

6. Granska anslutnings sammanfattningen och se till att all information är korrekt. Om det är det väljer du **Anslut**. Annars kan du använda **tillbaka** -knappen för att återgå till föregående sidor för att åtgärda eventuell felaktig information.

När anslutningen har lagts till skickas resurs trädet automatiskt till den nod som representerar anslutningen. Om den inte går till noden, tittar du under **lokala & anslutna** > **lagrings konton**. Om Storage Explorer inte kan lägga till anslutningen, eller om du inte kan komma åt dina data när du har lagt till anslutningen, kan du läsa [fel söknings guiden](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="attach-to-a-local-emulator"></a>Anslut till en lokal emulator

Storage Explorer stöder för närvarande två officiella lagrings emulatorer:
* [Azure Storage emulator](storage/common/storage-use-emulator.md) (Endast Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS eller Linux)

Om emulatorn lyssnar på standard portarna kan du använda noden **emulator-standard portar** (finns under **lokala & anslutna** > **lagrings konton**) för att snabbt komma åt din emulator.

Följ dessa steg om du vill använda ett annat namn för anslutningen, eller om emulatorn inte körs på standard portarna:

1. Starta din emulator. När du gör det ska du anteckna vilka portar som emulatorn lyssnar på för varje tjänst typ.

   > [!IMPORTANT]
   > Storage Explorer startar inte emulatorn automatiskt. Du måste starta den manuellt.

2. Öppna dialog rutan **Anslut** genom att välja knappen **Anslut** i det lodräta verktygsfältet till vänster:

    ![Alternativet Anslut till Azure Storage][9]

3. Välj **Anslut till en lokal emulator**och välj sedan **Nästa**.

4. Välj ett visnings namn för anslutningen och ange de portar som din emulator lyssnar på för varje tjänst typ. Text rutorna börjar med standard port värden för de flesta emulatorer. Rutan **fil port** lämnas tom, eftersom ingen av de officiella emulatorerna för närvarande stöder fil tjänsten. Om emulatorn du använder stöder filer kan du ange den port som används. Välj sedan **Nästa**.

5. Granska anslutnings sammanfattningen och se till att all information är korrekt. Om det är det väljer du **Anslut**. Annars kan du använda **tillbaka** -knappen för att återgå till föregående sidor för att åtgärda eventuell felaktig information.

När anslutningen har lagts till skickas resurs trädet automatiskt till den nod som representerar anslutningen. Om den inte går till noden, tittar du under **lokala & anslutna** > **lagrings konton**. Om Storage Explorer inte kan lägga till anslutningen, eller om du inte kan komma åt dina data när du har lagt till anslutningen, kan du läsa [fel söknings guiden](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Ansluta till ett Azure Cosmos DB-konto med hjälp av en anslutnings sträng

Som ett alternativ till att hantera Azure Cosmos DB-konton via en Azure-prenumeration kan du också ansluta till Azure Cosmos DB med hjälp av en anslutnings sträng. Det gör du genom att följa dessa steg:

1. På vänster sida av resurs trädet expanderar du **lokala och anslutna**, högerklickar på **Azure Cosmos DB konton**och väljer **Anslut till Azure Cosmos DB**.

    ![Ansluta till Azure Cosmos DB med hjälp av en anslutningssträng][21]

2. Välj Azure Cosmos DB API, ange dina **anslutnings sträng** data och välj sedan **OK** för att ansluta Azure Cosmos DB-kontot. Information om hur du hämtar anslutnings strängen finns i [Hämta anslutnings strängen](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![Anslutningssträng][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Anslut till Azure Data Lake Store per URI

Om du vill komma åt en resurs som inte finns i din prenumeration behöver du någon som har åtkomst till resursen för att ge resurs-URI: n. När du har loggat in kan du ansluta till Data Lake Store med hjälp av URI: n. Det gör du genom att följa dessa steg:

1. Öppna Storage Explorer.
2. I det vänstra fönstret expanderar du **Lokala och ansluten**.
3. Högerklicka på **data Lake Store**. I snabb menyn väljer **du Anslut till data Lake Store**.

    ![ansluta till Data Lake Store snabb meny](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Ange URI:n. Verktyget går till platsen för den URL som du nyss angav.

    ![Dialog rutan Anslut till Data Lake Store-kontext](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![Anslut till Data Lake Store resultat](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>Skapa en SAS i Storage Explorer

### <a name="account-level-sas"></a>SAS på konto nivå

1. Högerklicka på det lagrings konto som du vill dela och välj sedan **Hämta signatur för delad åtkomst**.

    ![Snabbmenyalternativet Hämta SAS][14]

2. I dialog rutan **Skapa signatur för delad åtkomst** anger du den tidsram och de behörigheter som du vill använda för kontot och väljer sedan **skapa**.

    ![Dialog rutan Hämta SAS][15]

3. Nu kan du kopiera antingen **anslutnings strängen** eller rå **frågesträngen** till Urklipp.

### <a name="service-level-sas"></a>SAS på service nivå

[Så här hämtar du en SAS för en BLOB-behållare i Storage Explorer](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>Söka efter lagringskonton

Om du behöver hitta en lagrings resurs och inte vet var den finns kan du söka efter resursen med hjälp av sökrutan längst upp i det vänstra fönstret.

När du skriver i sökrutan visar det vänstra fönstret alla resurser som matchar Sök värdet som du har angett till den punkten. Till exempel visas en sökning efter **slut punkter** i följande skärm bild:

![Lagringskontosökning][23]

> [!NOTE]
> Du kan påskynda sökningen genom att använda panelen konto hantering för att avmarkera alla prenumerationer som inte innehåller det objekt som du söker efter. Du kan också högerklicka på en nod och välja **Sök härifrån** för att börja söka från en speciell nod.
>
>

## <a name="next-steps"></a>Nästa steg

* [Hantera Azure Blob Storage-resurser med Storage Explorer](vs-azure-tools-storage-explorer-blobs.md)
* [Hantera Azure Cosmos DB i Storage Explorer (förhands granskning)](./cosmos-db/storage-explorer.md)
* [Hantera Azure Data Lake Store resurser med Storage Explorer](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/GetSharedAccessSignature.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SharedAccessSignatureDialog.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Search.png
