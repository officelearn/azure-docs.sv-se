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
ms.openlocfilehash: c4aad2f2f5bca25ead03518b2de9ac9315172052
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934749"
---
# <a name="get-started-with-storage-explorer"></a>Kom igång med Storage Explorer

## <a name="overview"></a>Översikt

Microsoft Azure Storage Explorer är en fristående app som gör det enkelt att arbeta med Azure Storage data på Windows, macOS och Linux. I den här artikeln får du lära dig flera olika sätt att ansluta till och hantera dina Azure Storage-konton.

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

Storage Explorer finns i [snapin Store](https://snapcraft.io/storage-explorer) för de vanligaste distributionerna av Linux och är den rekommenderade installations metoden. Med Storage Explorer Snap installeras alla dess beroenden och uppdateringar automatiskt när nya versioner publiceras i snapin-arkivet.

En lista över distributioner som stöds finns på [sidan för Snap-installation](https://snapcraft.io/docs/installing-snapd).

Storage Explorer kräver att du använder en lösen ords hanterare, som kan behöva anslutas manuellt innan Storage Explorer fungerar som den ska. Du kan ansluta Storage Explorer till systemets lösen ords hanterare med följande kommando:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage Explorer är också tillgängligt som en. tar. gz-hämtning, men du måste installera beroenden manuellt. Installationen av. tar. gz stöds i följande distributioner av Linux:

* Ubuntu 18,04 x64
* Ubuntu 16,04 x64
* Ubuntu 14,04 x64

Installationen av. tar. gz kan fungera på andra distributioner, men endast de som anges ovan stöds officiellt.

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
> För att få fullständig åtkomst till resurser efter inloggningen kräver Storage Explorer både hantering (ARM) och data lager behörigheter. Det innebär att du behöver Azure AD-behörigheter som ger dig åtkomst till ditt lagrings konto, behållarna i kontot och data i behållarna. Om du bara har behörighet för data lagret bör du överväga att använda [bifoga med Azure AD](#add-a-resource-via-azure-ad). Mer information om de exakta behörigheter som Storage Explorer kräver finns i [fel söknings guiden](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#role-based-access-control-permission-issues).

1. I Storage Explorer väljer du **Hantera konton** för att gå till **panelen konto hantering**.

    ![Hantera konton][1]

2. I den vänstra rutan visas nu alla Azure-konton som du har loggat in på. Om du vill ansluta till ett annat konto väljer du **Lägg till ett konto**

3. Om du vill logga in på ett nationellt moln eller en Azure Stack klickar du på list rutan i **Azure-miljön** och väljer vilket Azure-moln som du vill använda. När du har valt din miljö klickar du på knappen **Logga in...** . Mer information finns i [ansluta Storage Explorer till en Azure Stack-prenumeration](/azure-stack/user/azure-stack-storage-connect-se).

    ![Inloggnings alternativ][2]

4. När du har loggat in med ett Azure-konto läggs kontot och de Azure-prenumerationer som är kopplade till kontot till i den vänstra rutan. Välj de Azure-prenumerationer som du vill arbeta med och välj sedan **Använd** (välja **alla prenumerationer:** växlar Markera alla eller inga av de listade Azure-prenumerationerna).

    ![Välja Azure-prenumerationer][3]

    I den vänstra rutan visas lagringskontona som är kopplade till de valda Azure-prenumerationerna.

    ![Valda Azure-prenumerationer][4]

### <a name="attach-a-specific-resource"></a>Anslut en speciell resurs

Du kan koppla till en resurs i Storage Explorer att använda olika alternativ:

* [Lägg till en resurs via Azure AD](#add-a-resource-via-azure-ad): Om du bara har behörighet på data lagret kan du använda det här alternativet för att lägga till en BLOB-behållare eller en ADLS Gen2 BLOB-behållare.
* [Använd en anslutnings sträng](#use-a-connection-string): Om du har en anslutnings sträng till ett lagrings konto. Storage Explorer stöder nyckel-och [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) -anslutningssträngar.
* [Använd en SAS-URI](#use-a-sas-uri): Om du har en [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) -URI till en BLOB-behållare, fil resurs, kö eller tabell. Om du vill hämta en SAS-URI kan du antingen använda [Storage Explorer](#generate-a-sas-in-storage-explorer) eller [Azure Portal](https://portal.azure.com).
* [Använd ett namn och en nyckel](#use-a-name-and-key): Om du känner till någon av konto nycklarna till ditt lagrings konto kan du använda det här alternativet för att snabbt ansluta. Nycklarna för ditt lagrings konto finns på panelen **åtkomst nycklar** för lagrings kontot på [Azure Portal](https://portal.azure.com).
* [Anslut till en lokal emulator](#attach-to-a-local-emulator): Om du använder någon av de tillgängliga Azure Storage-emulatorerna kan du använda det här alternativet för att enkelt ansluta till emulatorn.
* [Ansluta till ett Azure Cosmos DB-konto med hjälp av en anslutnings sträng](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): Om du har en anslutnings sträng till en CosmosDB-instans.
* [Anslut till Azure Data Lake Store per URI](#connect-to-azure-data-lake-store-by-uri): Om du har en URI till en Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Lägg till en resurs via Azure AD

1. Öppna **dialog rutan Anslut** genom att klicka på **knappen Anslut** till vänster, lodrätt verktygsfält.

    ![Alternativet Anslut till Azure Storage][9]

2. Om du inte redan har gjort det använder du alternativet **Lägg till ett Azure-konto** för att logga in på det Azure-konto som har åtkomst till resursen. När du har loggat in går du tillbaka till **dialog rutan Anslut**.

3. Välj alternativet **Lägg till en resurs via Azure Active Directory (Azure AD)** och klicka på **Nästa**.

4. Välj det Azure-konto och klient organisation som har åtkomst till den lagrings resurs som du vill ansluta till. Klicka på **Nästa**.

5. Välj den resurs typ som du vill bifoga och ange sedan den information som krävs för att ansluta. Indata på den här sidan kommer att ändras beroende på vilken typ av resurs som du lägger till. Se till att välja rätt typ av resurs. När du har fyllt i den information som krävs klickar du på **Nästa**.

6. Granska anslutnings sammanfattningen och se till att all information är korrekt. Om all information ser korrekt ut klickar du på **Anslut**. Annars kan du gå tillbaka till föregående sidor med **tillbaka** -knappen för att korrigera all felaktig information.

När anslutningen har lagts till kommer resurs trädet automatiskt att navigera till den nod som representerar anslutningen. Du kan också titta under **lokala & anslutna** → **lagrings konton** → **(anslutna behållare)** → **BLOB-behållare** om det är av någon anledning. Om Storage Explorer inte kunde lägga till anslutningen, eller om du inte kan komma åt dina data när du har lagt till anslutningen, kan du läsa [fel söknings guiden](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) för hjälp.

#### <a name="use-a-connection-string"></a>Använd en anslutningssträng

1. Öppna **dialog rutan Anslut** genom att klicka på **knappen Anslut** till vänster, lodrätt verktygsfält.

    ![Alternativet Anslut till Azure Storage][9]

2. Välj alternativet **Använd en anslutnings sträng** och klicka på **Nästa**.

3. Välj ett visnings namn för anslutningen och ange i anslutnings strängen. Klicka sedan på **Nästa**.

4. Granska anslutnings sammanfattningen och se till att all information är korrekt. Om all information ser korrekt ut klickar du på **Anslut**, annars återgår till föregående sidor med **tillbaka** -knappen för att korrigera all felaktig information.

När anslutningen har lagts till kommer resurs trädet automatiskt att navigera till den nod som representerar anslutningen. Du kan också titta under **lokala & anslutna** → **lagrings konton** om det av någon anledning inte är det. Om Storage Explorer inte kunde lägga till anslutningen, eller om du inte kan komma åt dina data när du har lagt till anslutningen, kan du läsa [fel söknings guiden](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) för hjälp.

#### <a name="use-a-sas-uri"></a>Använd en SAS-URI

1. Öppna **dialog rutan Anslut** genom att klicka på **knappen Anslut** till vänster, lodrätt verktygsfält.

    ![Alternativet Anslut till Azure Storage][9]

2. Välj **URI-alternativet Använd en signatur för delad åtkomst (SAS)** och klicka på **Nästa**.

3. Välj ett visnings namn för anslutningen och ange i SAS-URI: n. Tjänst slut punkten för den typ av resurs som du lägger till ska Autofyll. Om du använder en anpassad slut punkt är det möjligt att den inte är det. Klicka på **Nästa**.

4. Granska anslutnings sammanfattningen och se till att all information är korrekt. Om all information ser korrekt ut klickar du på **Anslut**, annars återgår till föregående sidor med **tillbaka** -knappen för att korrigera all felaktig information.

När anslutningen har lagts till kommer resurs trädet automatiskt att navigera till den nod som representerar anslutningen. Du kan också titta under **lokala & anslutna** → **lagrings konton** → **(anslutna behållare)** → **noden tjänst för den typ av behållare som du har bifogat** av någon anledning. Om Storage Explorer inte kunde lägga till anslutningen, eller om du inte kan komma åt dina data när du har lagt till anslutningen, kan du läsa [fel söknings guiden](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) för hjälp.

#### <a name="use-a-name-and-key"></a>Använd ett namn och en nyckel

1. Öppna **dialog rutan Anslut** genom att klicka på **knappen Anslut** till vänster, lodrätt verktygsfält.

    ![Alternativet Anslut till Azure Storage][9]

2. Välj alternativet **Använd ett lagrings konto namn och nyckel** och klicka på **Nästa**.

3. Välj ett visnings namn för anslutningen.

4. Ange i ditt lagrings konto namn och någon av dess åtkomst nycklar.

5. Välj den **lagrings domän** som du vill använda och klicka sedan på **Nästa**.

4. Granska anslutnings sammanfattningen och se till att all information är korrekt. Om all information ser korrekt ut klickar du på **Anslut**, annars återgår till föregående sidor med **tillbaka** -knappen för att korrigera all felaktig information.

När anslutningen har lagts till kommer resurs trädet automatiskt att navigera till den nod som representerar anslutningen. Du kan också titta under **lokala & anslutna** → **lagrings konton** om det av någon anledning inte är det. Om Storage Explorer inte kunde lägga till anslutningen, eller om du inte kan komma åt dina data när du har lagt till anslutningen, kan du läsa [fel söknings guiden](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) för hjälp.

#### <a name="attach-to-a-local-emulator"></a>Anslut till en lokal emulator

Storage Explorer stöder för närvarande två officiella lagrings emulatorer:
* [Azure Storage-emulator](storage/common/storage-use-emulator.md) (Endast Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS eller Linux)

Om emulatorn lyssnar på standard portarna kan du använda noden **emulator-standard portar** (finns under **lokala & anslutna** → **lagrings konton**) för att snabbt komma åt din emulator.

Om du vill använda ett annat namn för anslutningen, eller om emulatorn inte körs på standard portarna:

1. Starta din emulator. När du gör det ska du anteckna vilka portar som emulatorn lyssnar på för varje tjänst typ.

   > [!IMPORTANT]
   > Storage Explorer startar inte emulatorn automatiskt. Du måste starta den själv.

2. Öppna **dialog rutan Anslut** genom att klicka på **knappen Anslut** till vänster, lodrätt verktygsfält.

    ![Alternativet Anslut till Azure Storage][9]

3. Välj alternativet **Anslut till en lokal emulator** och klicka på **Nästa**.

4. Välj ett visnings namn för anslutningen och ange i de portar som din emulator lyssnar på för varje tjänst typ. Text rutorna börjar med standard port värden för de flesta emulatorer. **Filporten** lämnas tom, eftersom ingen av de officiella emulatorerna för närvarande stöder fil tjänsten. Om emulatorn du använder stöder filer kan du ange den port som används. Klicka på **Nästa**.

5. Granska anslutnings sammanfattningen och se till att all information är korrekt. Om all information ser korrekt ut klickar du på **Anslut**, annars återgår till föregående sidor med **tillbaka** -knappen för att korrigera all felaktig information.

När anslutningen har lagts till kommer resurs trädet automatiskt att navigera till den nod som representerar anslutningen. Du kan också titta under **lokala & anslutna** → **lagrings konton** om det av någon anledning inte är det. Om Storage Explorer inte kunde lägga till anslutningen, eller om du inte kan komma åt dina data när du har lagt till anslutningen, kan du läsa [fel söknings guiden](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) för hjälp.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Ansluta till ett Azure Cosmos DB-konto med hjälp av en anslutnings sträng

Förutom att hantera Azure Cosmos DB konton via Azure-prenumerationen, är ett alternativt sätt att ansluta till en Azure Cosmos DB att använda en anslutnings sträng. Följ stegen nedan för att ansluta med en anslutningssträng.

1. Hitta **Lokala och anslutna** i trädet till vänster, högerklicka på **Azure Cosmos DB-konton** och välj **Anslut till Azure Cosmos DB...**

    ![Anslut till Azure Cosmos DB efter anslutnings sträng][21]

2. Välj Azure Cosmos DB API, klistra in **anslutnings strängen**och klicka sedan på **OK** för att ansluta Azure Cosmos DB-kontot. Information om hur du hämtar anslutningssträngen finns i [Hämta anslutningssträngen](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![connection-string][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Anslut till Azure Data Lake Store per URI

Om du behöver åtkomst till en resurs som inte finns i din prenumeration behöver du någon med åtkomst för att ge resurs-URI: n. När du har loggat in kan du ansluta till Data Lake Store med hjälp av URI: n genom att följa dessa steg:

1. Öppna Storage Explorer.
2. I det vänstra fönstret expanderar du **Lokala och ansluten**.
3. Högerklicka på **Data Lake Store** och från snabbmenyn väljer du **Anslut till Data Lake Store...** .

    ![snabbmenyn anslut till Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Ange den URI du fått, verktyget går till den URL du angett.

    ![dialogen anslut till Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![resultat för ansluta till Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>Skapa en SAS i Storage Explorer

### <a name="account-level-sas"></a>SAS på konto nivå

1. Högerklicka på det lagrings konto som du vill dela och välj sedan **Hämta signatur för delad åtkomst...** .

    ![Snabbmenyalternativet Hämta SAS][14]

2. I dialog rutan **Skapa signatur för delad åtkomst** anger du den tidsram och de behörigheter som du vill använda för kontot. Klicka på **Skapa**.

    ![Dialog rutan Hämta SAS][15]

3. Nu kan du antingen kopiera **anslutnings strängen** eller den råa **frågesträngen** till Urklipp.

### <a name="service-level-sas"></a>SAS på service nivå

[Så här hämtar du en SAS för en BLOB-behållare i Storage Explorer](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>Söka efter lagringskonton

Om du behöver hitta en lagrings resurs och inte vet var den finns kan du söka efter resursen med hjälp av sökrutan längst upp i det vänstra fönstret.

När du skriver i sökrutan visar det vänstra fönstret alla resurser som matchar Sök värdet som du har angett till den punkten. Till exempel visas en sökning efter **slut punkter** i följande skärm bild:

![Lagringskontosökning][23]

> [!NOTE]
> Använd **panelen konto hantering** för att avmarkera eventuella prenumerationer som inte innehåller det objekt som du söker efter för att förbättra körnings tiden för din sökning. Du kan också högerklicka på en nod och välja **Sök härifrån** för att börja söka från en speciell nod.
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
