---
title: Komma igång med Storage Explorer | Microsoft-dokument
description: Hantera Azure-lagringsresurser med Storage Explorer
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 7886d5a1ad0745550767b7d6f19592ca3c84b00a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279798"
---
# <a name="get-started-with-storage-explorer"></a>Kom igång med Storage Explorer

## <a name="overview"></a>Översikt

Microsoft Azure Storage Explorer är en fristående app som gör det enkelt att arbeta med Azure Storage-data på Windows, macOS och Linux. I den här artikeln får du lära dig flera sätt att ansluta till och hantera dina Azure-lagringskonton.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>Krav

# <a name="windows"></a>[Windows](#tab/windows)

Följande versioner av Windows stöder Storage Explorer:

* Windows 10 (rekommenderas)
* Windows 8
* Windows 7

För alla versioner av Windows kräver Storage Explorer .NET Framework 4.6.2 eller senare.

# <a name="macos"></a>[Macos](#tab/macos)

Följande versioner av macOS stöder Storage Explorer:

* macOS 10.12 Sierra och senare versioner

# <a name="linux"></a>[Linux](#tab/linux)

Storage Explorer är tillgängligt i [Snap Store](https://snapcraft.io/storage-explorer) för de vanligaste distributionerna av Linux. Vi rekommenderar Snap Store för den här installationen. Snap-snapen Storage Explorer installerar alla dess beroenden och uppdateringar när nya versioner publiceras i Snap Store.

För distributioner som stöds finns på sidan [för fäst installation](https://snapcraft.io/docs/installing-snapd).

Storage Explorer kräver användning av en lösenordshanterare. Du kanske måste ansluta till en lösenordshanterare manuellt. Du kan ansluta Storage Explorer till systemets lösenordshanterare genom att köra följande kommando:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Lagring Explorer finns också som en *.tar.gz* nedladdning. Du måste installera beroenden manuellt. Följande distributioner av Linux stöder *.tar.gz-installation:*

* Ubuntu 18,04 x64
* Ubuntu 16,04 x64
* Ubuntu 14,04 x64

*.tar.gz-installationen* kan fungera på andra distributioner, men endast dessa listade stöds officiellt.

Mer hjälp med att installera Storage Explorer på Linux finns i [Linux-beroenden](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies) i felsökningsguiden för Azure Storage Explorer.

---

## <a name="download-and-install"></a>Hämta och installera

Information om hur du hämtar och installerar Storage Explorer finns i [Azure Storage Explorer](https://www.storageexplorer.com).

## <a name="connect-to-a-storage-account-or-service"></a>Ansluta till ett lagringskonto eller en tjänst

I Storage Explorer finns flera olika sätt att ansluta till lagringskonton. I allmänhet kan du antingen:

* [Logga in på Azure för att komma åt dina prenumerationer och deras resurser](#sign-in-to-azure)
* [Koppla en specifik Storage- eller CosmosDB-resurs](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Logga in på Azure

> [!NOTE]
> För att få fullständig åtkomst till resurser när du har loggat in kräver Storage Explorer både hantering (Azure Resource Manager) och datalagerbehörigheter. Det innebär att du behöver Azure Active Directory (Azure AD) behörigheter, som ger dig åtkomst till ditt lagringskonto, behållarna i kontot och data i behållarna. Om du bara har behörighet i datalagret bör du [överväga att lägga till en resurs via Azure AD](#add-a-resource-via-azure-ad). Mer information om de specifika behörigheter som Storage Explorer kräver finns i [felsökningsguiden för Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#rbac-permissions-issues).

1. I Storage Explorer väljer du **Visa** > **kontohantering** eller väljer knappen **Hantera konton.**

    ![Hantera konton][1]

1. **KONTOHANTERING** visar nu alla Azure-konton som du har loggat in på. Om du vill ansluta till ett annat konto väljer du **Lägg till ett konto**.

1. I **Anslut till Azure Storage**väljer du ett Azure-moln från **Azure-miljö** för att logga in på ett nationellt moln eller en Azure Stack. När du har valt din miljö väljer du **Nästa**.

    ![Möjlighet att logga in][2]

    Storage Explorer öppnar en sida där du kan logga in. Mer information finns i [Ansluta lagringsutforskaren till en Azure Stack-prenumeration eller ett lagringskonto](/azure-stack/user/azure-stack-storage-connect-se).

1. När du har loggat in med ett Azure-konto visas kontot och Azure-prenumerationerna som är associerade med kontot under **ACCOUNT MANAGEMENT**. Välj **Alla prenumerationer** för att växla ditt val mellan alla eller inga av de listade Azure-prenumerationerna. Välj de Azure-prenumerationer som du vill arbeta med och välj sedan **Använd**.

    ![Välja Azure-prenumerationer][3]

    **EXPLORER** visar de lagringskonton som är associerade med de valda Azure-prenumerationerna.

    ![Valda Azure-prenumerationer][4]

### <a name="attach-a-specific-resource"></a>Koppla en viss resurs

Det finns flera sätt att koppla till en resurs i Storage Explorer:

* [Lägg till en resurs via Azure AD](#add-a-resource-via-azure-ad). Om du bara har behörighet i datalagret använder du det här alternativet för att lägga till en blob-behållare eller en Azure Data Lake Storage Gen2 Blob-lagringsbehållare.
* [Använd en anslutningssträng](#use-a-connection-string). Använd det här alternativet om du har en anslutningssträng till ett lagringskonto. Storage Explorer stöder anslutningssträngar för både nyckel och delad åtkomstsignatur. [shared access signature](storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Använd en signatur för delad åtkomst URI](#use-a-shared-access-signature-uri). Om du har en [uri för delad åtkomstsignatur](storage/common/storage-dotnet-shared-access-signature-part-1.md) till en blob-behållare, filresurs, kö eller tabell använder du den för att koppla till resursen. Om du vill få en signatur-URI för delad åtkomst kan du antingen använda [Storage Explorer](#generate-a-sas-in-storage-explorer) eller [Azure-portalen](https://portal.azure.com).
* [Använd ett namn och en nyckel](#use-a-name-and-key). Om du känner till någon av kontonycklarna till ditt lagringskonto kan du använda det här alternativet för att snabbt ansluta. Hitta dina nycklar på sidan för lagringskonto genom att välja **Inställningar** > **Access-nycklar** i [Azure-portalen](https://portal.azure.com).
* [Anslut till en lokal emulator](#attach-to-a-local-emulator). Om du använder en av de tillgängliga Azure Storage-emulatorer, använd det här alternativet för att enkelt ansluta till din emulator.
* [Anslut till ett Azure Cosmos DB-konto med hjälp av en anslutningssträng](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string). Använd det här alternativet om du har en anslutningssträng till en CosmosDB-förekomst.
* [Anslut till Azure Data Lake Store med URI](#connect-to-azure-data-lake-store-by-uri). Använd det här alternativet om du har en URI till Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Lägga till en resurs via Azure AD

1. Välj knappen **Anslut** för att öppna **Anslut till Azure Storage**.

    ![Alternativet Anslut till Azure Storage][9]

1. Om du inte redan har gjort det använder du alternativet **Lägg till ett Azure-konto** för att logga in på Azure-kontot som har åtkomst till resursen. När du har loggat in går du tillbaka till **Anslut till Azure Storage**.

1. Välj **Lägg till en resurs via Azure Active Directory (Azure AD)** och välj sedan **Nästa**.

1. Välj ett Azure-konto och en klient. Dessa värden måste ha åtkomst till den lagringsresurs som du vill koppla till. Välj **Nästa**.

1. Välj den resurstyp som du vill koppla. Ange den information som behövs för att ansluta. 

   Vilken information du anger på den här sidan beror på vilken typ av resurs du lägger till. Se till att välja rätt typ av resurs. När du har angett den information som krävs väljer du **Nästa**.

1. Granska **anslutningssammanfattningen** för att kontrollera att all information är korrekt. Om så ärdsar du **på Anslut**. Annars väljer du **Tillbaka** för att gå tillbaka till föregående sidor för att åtgärda felaktig information.

När anslutningen har lagts till går resursträdet till noden som representerar anslutningen. Resursen visas under **Lokala & bifogade** > **lagringskonton** > **(bifogade behållare)** > **Blob Containers**. Om Storage Explorer inte kunde lägga till anslutningen, eller om du inte kan komma åt dina data efter att anslutningen har lagts till, läser [du felsökningsguiden för Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-connection-string"></a>Använda en anslutningssträng

1. Välj knappen **Anslut** för att öppna **Anslut till Azure Storage**.

    ![Alternativet Anslut till Azure Storage][9]

1. Välj **Använd en anslutningssträng**och välj sedan **Nästa**.

1. Välj ett visningsnamn för anslutningen och ange anslutningssträngen. Välj sedan **Nästa**.

1. Granska **anslutningssammanfattningen** för att kontrollera att all information är korrekt. Om så ärdsar du **på Anslut**. Annars väljer du **Tillbaka** för att gå tillbaka till föregående sidor för att åtgärda felaktig information.

När anslutningen har lagts till går resursträdet till noden som representerar anslutningen. Resursen visas under **Lokala & bifogade** > **lagringskonton**. Om Storage Explorer inte kunde lägga till anslutningen, eller om du inte kan komma åt dina data efter att anslutningen har lagts till, läser [du felsökningsguiden för Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-shared-access-signature-uri"></a>Använda en URI för delad åtkomst

1. Välj knappen **Anslut** för att öppna **Anslut till Azure Storage**.

    ![Alternativet Anslut till Azure Storage][9]

1. Välj **Använd en SAS-uri (Shared Access Signature)** och välj sedan **Nästa**.

1. Välj ett visningsnamn för anslutningen och ange signaturen URI för delad åtkomst. Tjänstslutpunkten för den typ av resurs som du kopplar ska fyllas i automatiskt. Om du använder en anpassad slutpunkt är det möjligt att det inte gör det. Välj **Nästa**.

1. Granska **anslutningssammanfattningen** för att kontrollera att all information är korrekt. Om så ärdsar du **på Anslut**. Annars väljer du **Tillbaka** för att gå tillbaka till föregående sidor för att åtgärda felaktig information.

När anslutningen har lagts till går resursträdet till noden som representerar anslutningen. Resursen visas under **Lokala & Bifogade** > **lagringskonton** > **(Bifogade behållare)** > *tjänstnoden för den typ av behållare som du bifogade*. Om Storage Explorer inte kunde lägga till anslutningen läser du [felsökningsguiden för Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting). Se felsökningsguiden om du inte kan komma åt dina data när du har lagt till anslutningen.

#### <a name="use-a-name-and-key"></a>Använda ett namn och en nyckel

1. Välj knappen **Anslut** för att öppna **Anslut till Azure Storage**.

    ![Alternativet Anslut till Azure Storage][9]

1. Välj **Använd ett namn och en lagringskontonyckel**och välj sedan **Nästa**.

1. Välj ett visningsnamn för anslutningen.

1. Ange ditt lagringskontonamn och någon av dess åtkomstnycklar.

1. Välj den **lagringsdomän** som ska användas och välj sedan **Nästa**.

1. Granska **anslutningssammanfattningen** för att kontrollera att all information är korrekt. Om så ärdsar du **på Anslut**. Annars väljer du **Tillbaka** för att gå tillbaka till föregående sidor för att åtgärda felaktig information.

När anslutningen har lagts till går resursträdet till noden som representerar anslutningen. Resursen visas under **Lokala & bifogade** > **lagringskonton**. Om Storage Explorer inte kunde lägga till anslutningen, eller om du inte kan komma åt dina data efter att anslutningen har lagts till, läser [du felsökningsguiden för Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="attach-to-a-local-emulator"></a>Anslut till en lokal emulator

Storage Explorer stöder för närvarande två officiella lagringsemulatorer:

* [Azure Storage-emulatorn](storage/common/storage-use-emulator.md) (endast Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS eller Linux)

Om emulatorn lyssnar på standardportarna kan du använda noden **Emulator - Standardportar** för att komma åt emulatorn. Leta efter **Emulator - Standardportar** under **Lokala & bifogade** > **lagringskonton**.

Om du vill använda ett annat namn för anslutningen, eller om emulatorn inte körs på standardportarna, gör du så här:

1. Starta din emulator. Ange kommandot `AzureStorageEmulator.exe status` för att visa portarna för varje tjänsttyp.

   > [!IMPORTANT]
   > Storage Explorer startar inte emulatorn automatiskt. Du måste starta den manuellt.

1. Välj knappen **Anslut** för att öppna **Anslut till Azure Storage**.

    ![Alternativet Anslut till Azure Storage][9]

1. Välj **Koppla till en lokal emulator**och välj sedan **Nästa**.

1. Välj ett visningsnamn för din anslutning och ange de portar som emulatorn lyssnar på för varje tjänsttyp. **Bifoga till en lokal emulator** föreslår standardportvärden för de flesta emulatorer. **Filporten** är tom, eftersom ingen av de officiella emulatorer för närvarande stöder filtjänsten. Om emulatorn du använder stöder filer kan du ange porten som ska användas. Välj sedan **Nästa**.

1. Granska **anslutningssammanfattningen** och kontrollera att all information är korrekt. Om så ärdsar du **på Anslut**. Annars väljer du **Tillbaka** för att gå tillbaka till föregående sidor för att åtgärda felaktig information.

När anslutningen har lagts till går resursträdet till noden som representerar anslutningen. Noden ska visas under **Lokala & bifogade** > **lagringskonton**. Om Storage Explorer inte kunde lägga till anslutningen, eller om du inte kan komma åt dina data efter att anslutningen har lagts till, läser [du felsökningsguiden för Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Ansluta till ett Azure Cosmos DB-konto med hjälp av en anslutningssträng

I stället för att hantera Azure Cosmos DB-konton via en Azure-prenumeration kan du ansluta till Azure Cosmos DB med hjälp av en anslutningssträng. Gör så här för att ansluta:

1. Expandera Lokala **& Bifogade under** **EXPLORER**, högerklicka på **Cosmos DB-konton**och välj **Anslut till Azure Cosmos DB**.

    ![Ansluta till Azure Cosmos DB med hjälp av en anslutningssträng][21]

1. Välj Azure Cosmos DB API, ange dina **anslutningssträngdata** och välj sedan **OK** för att ansluta Azure Cosmos DB-kontot. Information om hur du hämtar anslutningssträngen finns i [Hantera ett Azure Cosmos-konto](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![Anslutningssträng][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Ansluta till Azure Data Lake Store med URI

Du kan komma åt en resurs som inte finns i din prenumeration. Du behöver någon som har åtkomst till den resursen för att ge dig resursen URI. När du har loggat in ansluter du till DataSjöbutik med hjälp av URI:n. Gör så här för att ansluta:

1. Expandera Lokala **& bifogade under** **EXPLORER**.

1. Högerklicka på **Datasjölagring gen1**och välj **Anslut till datasjölagringgengen1**.

    ![Snabbmenyn Ansluta till DataSjö store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Ange URI och välj sedan **OK**. Datasjöarkivet visas under **Lagring av Datasjö.**

    ![Anslut till datasjölagringsresultat](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

I det här exemplet används Gen1 för lagring av datasjö. Azure Data Lake Storage Gen2 är nu tillgänglig. Mer information finns i [Vad är Azure Data Lake Storage Gen1](./data-lake-store/data-lake-store-overview.md).

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Generera en signatur för delad åtkomst i Storage Explorer<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Signatur för delad åtkomst på kontonivå

1. Högerklicka på det lagringskonto som du vill ha dela och välj sedan **Hämta signatur för delad åtkomst**.

    ![Snabbmenyalternativ för signatur för delad åtkomst][14]

1. I **Signature för delad åtkomst**anger du den tidsram och de behörigheter du vill använda för kontot och väljer sedan **Skapa**.

    ![Skaffa en signatur för delad åtkomst][15]

1. Kopiera antingen **anslutningssträngen** eller den råa **frågesträngen** till Urklipp.

### <a name="service-level-shared-access-signature"></a>Signatur för delad åtkomst på tjänstnivå

Du kan få en signatur för delad åtkomst på tjänstnivå. Mer information finns [i Hämta SAS för en blob-behållare](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

## <a name="search-for-storage-accounts"></a>Söka efter lagringskonton

Om du vill hitta en lagringsresurs kan du söka i **EXPLORER-fönstret.**

När du skriver text i sökrutan visar Storage Explorer alla resurser som matchar sökvärdet som du har angett fram till den punkten. I det här exemplet visas en sökning efter **slutpunkter:**

![Lagringskontosökning][23]

> [!NOTE]
> Om du vill snabba upp sökningen använder du **Kontohantering** för att avmarkera prenumerationer som inte innehåller objektet du söker efter. Du kan också högerklicka på en nod och välja **Sök härifrån** för att börja söka från en viss nod.
>
>

## <a name="next-steps"></a>Nästa steg

* [Hantera Azure Blob-lagringsresurser med Storage Explorer](vs-azure-tools-storage-explorer-blobs.md)
* [Arbeta med data med hjälp av Azure Storage Explorer](./cosmos-db/storage-explorer.md)
* [Hantera Azure Data Lake Store-resurser med Storage Explorer](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-azure-environment.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/account-panel-subscriptions-apply.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
