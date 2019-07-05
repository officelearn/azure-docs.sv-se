---
title: Kom igång med Storage Explorer | Microsoft Docs
description: Hantera Azure storage-resurser med Storage Explorer
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 04/22/2019
ms.author: cawa
ms.openlocfilehash: f7dd6d3d30f34ba2c69b40111bb28d484ce572e7
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508734"
---
# <a name="get-started-with-storage-explorer"></a>Kom igång med Storage Explorer

## <a name="overview"></a>Översikt

Azure Storage Explorer är en fristående app som gör det enkelt att arbeta med Azure Storage-data i Windows, macOS och Linux. I den här artikeln lär du dig flera sätt att ansluta till och hantera dina Azure storage-konton.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>Förutsättningar

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Azure Storage Explorer stöds på följande versioner av Windows:

* Windows 10 (rekommenderas)
* Windows 8
* Windows 7

För alla versioner av Windows .NET Framework 4.6.2 eller högre krävs.

[Hämta och installera Storage Explorer](https://www.storageexplorer.com)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Azure Storage Explorer stöds på följande versioner av macOS:

* macOS 10.12 ”Sierra” och senare versioner

[Hämta och installera Storage Explorer](https://www.storageexplorer.com)

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Azure Storage Explorer stöds på följande distributioner av Linux:

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

Azure Storage Explorer fungerar på andra distributioner, men endast som visas ovan stöds officiellt.

Mer hjälp med att installera Storage Explorer på Linux, finns det [felsökningsguiden för](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies).

Azure Storage Explorer [viktig](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) innehålla specifika åtgärder för vissa distributioner.

[Hämta och installera Storage Explorer](https://www.storageexplorer.com)

---

## <a name="connect-to-a-storage-account-or-service"></a>Ansluta till ett lagringskonto eller en tjänst

I Storage Explorer finns flera olika sätt att ansluta till lagringskonton. I allmänhet kan du antingen:

* [Logga in på Azure för att få åtkomst till dina prenumerationer och deras resurser](#sign-in-to-azure)
* [Koppla en viss resurs för lagring eller CosmosDB](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Logga in på Azure

> [!NOTE]
> För att få fullständig åtkomst till resurser efter inloggning, kräver både (ARM) och layer databehörigheter Storage Explorer. Det innebär att du behöver Azure AD-behörigheter som ger dig åtkomst till ditt lagringskonto, behållare i kontot och data i behållare. Om du endast har behörighet på data-nivå kan du använda [bifoga med Azure AD](#add-a-resource-via-azure-ad). Mer information om de exakta behörigheter Lagringsutforskaren kräver finns i den [felsökningsguide för](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#role-based-access-control-permission-issues).
>
>

1. I Storage Explorer väljer **hantera konton** att gå till den **konto Management panelen**.

    ![Hantera konton][1]

2. Den vänstra rutan visar nu alla Azure-konton som du har loggat in till. Om du vill ansluta till ett annat konto, Välj **Lägg till ett konto**

3. Om du vill logga in på ett nationella moln eller en Azure Stack, klickar du på den **Azure-miljön** listrutan att välja vilket Azure moln som du vill använda. När du har valt din miljö, klickar du på den **inloggning...**  knappen. Om du loggar in på Azure Stack, se [ansluta Storage Explorer till en Azure stackprenumeration](/azure-stack/user/azure-stack-storage-connect-se) för mer information.

    ![Logga In alternativet][2]

4. När du har loggat in med ett Azure-konto, läggs kontot och de Azure-prenumerationer kopplade till kontot till den vänstra rutan. Välj de Azure-prenumerationer som du vill arbeta med och välj sedan **tillämpa** (väljer **alla prenumerationer:** växlar du mellan att välja alla eller inga av de angivna Azure-prenumerationerna).

    ![Välja Azure-prenumerationer][3]

    I den vänstra rutan visas lagringskontona som är kopplade till de valda Azure-prenumerationerna.

    ![Valda Azure-prenumerationer][4]

### <a name="attach-a-specific-resource"></a>Koppla en viss resurs
    
Det finns en mängd olika alternativ för att bifoga en resurs i Storage Explorer. Du kan:

* [Lägg till en resurs via Azure AD](#add-a-resource-via-azure-ad): Om du endast har behörighet på data-nivå, kan du använda det här alternativet för att lägga till en Blob-behållare eller en ADLS Gen2 Blob-behållare.
* [Använd en anslutningssträng](#use-a-connection-string): Om du har en anslutningssträng för ett Lagringskonto. Storage Explorer stöd för både nyckel och [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) anslutningssträngar.
* [Använda en SAS-URI](#use-a-sas-uri): Om du har en [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) URI till en Blob-behållare, filresurs, kö eller tabell. Om du vill hämta en SAS-URI som du kan använda [Lagringsutforskaren](#generate-a-sas-in-storage-explorer) eller [Azure-portalen](https://portal.azure.com).
* [Använd ett namn och nyckel](#use-a-name-and-key): Om du känner någon av nycklar för kontot till ditt Lagringskonto, kan du använda det här alternativet om du enkelt kan ansluta. Nycklarna för ditt Lagringskonto finns på Lagringskontot **åtkomstnycklar** bladet på den [Azure-portalen](https://portal.azure.com).
* [Ansluta till en lokal emulator](#attach-to-a-local-emulator): Om du använder en av de tillgängliga Azure Storage-emulatorerna kan du använda det här alternativet för att enkelt ansluta till din emulatorn.
* [Ansluta till ett Azure Cosmos DB-konto med hjälp av en anslutningssträng](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): Om du har en anslutningssträng till en CosmosDB-instans.
* [Ansluta till Azure Data Lake Store av URI: N](#connect-to-azure-data-lake-store-by-uri): Om du har en URI till en Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Lägg till en resurs via Azure AD

1. Öppna den **ansluta dialogrutan** genom att klicka på den **knappen Anslut** på vänstra lodräta verktygsfältet.

    ![Alternativet Anslut till Azure Storage][9]

2. Om du inte redan har gjort det, Använd den **Lägg till ett Azure-konto** alternativet att logga in på Azure-konto som har åtkomst till resursen. När du har registrerat i utbyte till den **ansluta dialogrutan**.

3. Välj den **lägga till en resurs via Azure Active Directory (Azure AD)** och klickar på **nästa**.

4. Välj det Azure-konto som har åtkomst till lagringsresursen som du vill bifoga och den prenumeration som resursen tillhör och klicka sedan på **nästa**.

5. Välj den resurstyp som du vill bifoga och sedan ange den information som behövs för att ansluta. Indata på den här sidan kommer att ändras beroende på vilken typ av resurs som du lägger till. Se till att välja rätt typ av resurs. När du har fyllt i nödvändig information klickar du på **nästa**.

6. Granska anslutningssammanfattningen och kontrollera att all information är korrekt. Om all information som verkar vara korrekta klickar **Connect**, annars gå tillbaka till föregående sidor med den **tillbaka** knappen för att åtgärda eventuella fel information.

När anslutningen har lagts till genom resursträdet automatiskt att gå till noden som representerar anslutningen. Om du av någon anledning inte titta under **lokal och ansluten** → **Lagringskonton** → **(anslutna behållarna)** → **Blob-behållare** . Om Lagringsutforskaren kunde inte lägga till din anslutning, eller om du inte kan komma åt dina data när du har lagt till anslutningen, och sedan läser den [felsökningsguide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) om du behöver hjälp.

#### <a name="use-a-connection-string"></a>Använd en anslutningssträng

1. Öppna den **ansluta dialogrutan** genom att klicka på den **knappen Anslut** på vänstra lodräta verktygsfältet.

    ![Alternativet Anslut till Azure Storage][9]

2. Välj den **använda en anslutningssträng** och klickar på **nästa**.

3. Välj ett visningsnamn för anslutningen och ange i anslutningssträngen. Klicka sedan på **Nästa**.

4. Granska anslutningssammanfattningen och kontrollera att all information är korrekt. Om all information som verkar vara korrekta klickar **Connect**, annars gå tillbaka till föregående sidor med den **tillbaka** knappen för att åtgärda eventuella fel information.

När anslutningen har lagts till genom resursträdet automatiskt att gå till noden som representerar anslutningen. Om du av någon anledning inte titta under **lokal och ansluten** → **Lagringskonton**. Om Lagringsutforskaren kunde inte lägga till din anslutning, eller om du inte kan komma åt dina data när du har lagt till anslutningen, och sedan läser den [felsökningsguide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) om du behöver hjälp.

#### <a name="use-a-sas-uri"></a>Använda en SAS-URI

1. Öppna den **ansluta dialogrutan** genom att klicka på den **knappen Anslut** på vänstra lodräta verktygsfältet.

    ![Alternativet Anslut till Azure Storage][9]

2. Välj den **använda en signatur för delad åtkomst (SAS) URI** och klickar på **nästa**.

3. Välj ett visningsnamn för anslutningen och ange i din SAS-URI. Tjänstens slutpunkt för typ av resurs som du kopplar bör Autofyll. Om du använder en anpassad slutpunkt är det möjligt inte kan den. Klicka på **Nästa**.

4. Granska anslutningssammanfattningen och kontrollera att all information är korrekt. Om all information som verkar vara korrekta klickar **Connect**, annars gå tillbaka till föregående sidor med den **tillbaka** knappen för att åtgärda eventuella fel information.

När anslutningen har lagts till genom resursträdet automatiskt att gå till noden som representerar anslutningen. Om du av någon anledning inte titta under **lokal och ansluten** → **Lagringskonton** → **(anslutna behållarna)** → **service-noden för typ av behållare som du har kopplat**. Om Lagringsutforskaren kunde inte lägga till din anslutning, eller om du inte kan komma åt dina data när du har lagt till anslutningen, och sedan läser den [felsökningsguide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) om du behöver hjälp.

#### <a name="use-a-name-and-key"></a>Använd ett namn och nyckel

1. Öppna den **ansluta dialogrutan** genom att klicka på den **knappen Anslut** på vänstra lodräta verktygsfältet.

    ![Alternativet Anslut till Azure Storage][9]

2. Välj den **använder en lagringskontonamnet och nyckeln** och klickar på **nästa**.

3. Välj ett namn för anslutningen.

4. Ange namnet på ditt Lagringskonto och något av dess åtkomstnycklar.

5. Välj den **lagringsdomän** att använda och klicka sedan på **nästa**.

4. Granska anslutningssammanfattningen och kontrollera att all information är korrekt. Om all information som verkar vara korrekta klickar **Connect**, annars gå tillbaka till föregående sidor med den **tillbaka** knappen för att åtgärda eventuella fel information.

När anslutningen har lagts till genom resursträdet automatiskt att gå till noden som representerar anslutningen. Om du av någon anledning inte titta under **lokal och ansluten** → **Lagringskonton**. Om Lagringsutforskaren kunde inte lägga till din anslutning, eller om du inte kan komma åt dina data när du har lagt till anslutningen, och sedan läser den [felsökningsguide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) om du behöver hjälp.

#### <a name="attach-to-a-local-emulator"></a>Ansluta till en lokal emulator

Lagringsutforskaren stöder emulatorer på alla plattformar. Två finns för närvarande officiella tillgängliga emulatorer:
* [Azure storage-emulatorn](storage/common/storage-use-emulator.md) (endast Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS eller Linux)

Om din emulatorn körs på standardportarna kan du använda den **emulatorn - standardportar** nod som alltid finns under **lokal och ansluten** → **Lagringskonton** , att snabbt komma åt din emulatorn. Om du vill använda ett annat namn för anslutningen eller om din emulatorn inte körs på standardportar, följer du de stegen nedan.

1. Starta din emulatorn. När du gör genom att anteckna vilka portar lyssnar emulatorn på för varje typ av tjänst. Du behöver veta den här informationen senare.

   > [!IMPORTANT]
   > Lagringsutforskaren startar inte automatiskt din emulatorn. Du måste starta det själv.

2. Öppna den **ansluta dialogrutan** genom att klicka på den **knappen Anslut** på vänstra lodräta verktygsfältet.

    ![Alternativet Anslut till Azure Storage][9]

3. Välj den **koppla till en lokal emulator** och klickar på **nästa**.

4. Välj ett visningsnamn för anslutningen och ange i de portar som din emulatorn lyssnar på efter varje typ av tjänst. Som standard innehåller textrutorna portarnas standardvärden för de flesta emulatorer. Den **filer port** också lämnas tomt som standard eftersom ingen av de officiella emulatorerna har för närvarande stöd för Files-tjänsten. Om du använder emulatorn har stöd för det dock, kan du ange i den port som används. Klicka på **Nästa**.

5. Granska anslutningssammanfattningen och kontrollera att all information är korrekt. Om all information som verkar vara korrekta klickar **Connect**, annars gå tillbaka till föregående sidor med den **tillbaka** knappen för att åtgärda eventuella fel information.

När anslutningen har lagts till genom resursträdet automatiskt att gå till noden som representerar anslutningen. Om du av någon anledning inte titta under **lokal och ansluten** → **Lagringskonton**. Om Lagringsutforskaren kunde inte lägga till din anslutning, eller om du inte kan komma åt dina data när du har lagt till anslutningen, och sedan läser den [felsökningsguide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) om du behöver hjälp.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Ansluta till ett Azure Cosmos DB-konto med hjälp av en anslutningssträng

Förutom hantera Azure Cosmos DB-konton via Azure-prenumeration, ett alternativt sätt att ansluta till ett Azure Cosmos DB är att använda en anslutningssträng. Följ stegen nedan för att ansluta med en anslutningssträng.

1. Hitta **Lokala och anslutna** i trädet till vänster, högerklicka på **Azure Cosmos DB-konton** och välj **Anslut till Azure Cosmos DB...**

    ![ansluta till Azure Cosmos DB med anslutningssträngen][21]

2. Välj Azure Cosmos DB API, klistra in din **Connection String**, och klicka sedan på **OK** att ansluta Azure Cosmos DB-konto. Information om hur du hämtar anslutningssträngen finns i [Hämta anslutningssträngen](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![connection-string][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Ansluta till Azure Data Lake Store av URI: N

Om du vill komma åt resurser som inte finns i din prenumeration. Men andra ger dig behörighet att komma åt resursernas URI. I det här fallet kan du ansluta till Data Lake Store med denna URI när du loggat in. Se följande steg.

1. Öppna Storage Explorer.
2. I det vänstra fönstret expanderar du **Lokala och ansluten**.
3. Högerklicka på **Data Lake Store** och från snabbmenyn väljer du **Anslut till Data Lake Store...** .

    ![snabbmenyn anslut till Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Ange den URI du fått, verktyget går till den URL du angett.

    ![dialogen anslut till Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![resultat för ansluta till Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>Generera en SAS i Storage Explorer

### <a name="account-level-sas"></a>SAS på kontonivå

1. Högerklicka på det lagringskonto som du vill dela och välj sedan **hämta signatur för delad åtkomst...** .

    ![Snabbmenyalternativet Hämta SAS][14]

2. I den **generera signatur för delad åtkomst** dialogrutan anger du det tidsintervall och behörigheter som du vill använda för kontot och klicka sedan på den **skapa** knappen.

    ![Hämta SAS-dialogrutan][15]

3. Nu kan du antingen kopierar den **anslutningssträngen** eller den råa **frågesträng** till Urklipp.

### <a name="service-level-sas"></a>SAS på servicenivå

[Så här hämtar du en SAS för en blob-behållare i Storage Explorer](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>Söka efter lagringskonton

Om du vill hitta en resurs för lagring och inte vet var den finns, du kan använda sökrutan överst i det vänstra fönstret för att söka efter resursen.

När du skriver i sökrutan, visar alla resurser som matchar sökvärdet som du har skrivit hittills i den vänstra rutan. Till exempel en sökning efter **slutpunkter** visas i följande skärmbild:

![Lagringskontosökning][23]

> [!NOTE]
> Använd den **konto Management panelen** att avmarkera alla prenumerationer som inte innehåller det objekt som du söker efter för att förbättra körningstiden för sökningen. Du kan också högerklicka på en nod och välja **Search från här** söka från en viss nod.
>
>

## <a name="next-steps"></a>Nästa steg

* [Hantera Azure Blob Storage-resurser med Storage Explorer](vs-azure-tools-storage-explorer-blobs.md)
* [Hantera Azure Cosmos DB i Azure Storage Explorer (förhandsversion)](./cosmos-db/storage-explorer.md)
* [Hantera Azure Data Lake Store-resurser med Storage Explorer](./data-lake-store/data-lake-store-in-storage-explorer.md)

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
