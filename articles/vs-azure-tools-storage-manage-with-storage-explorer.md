---
title: Kom igång med Storage Explorer | Microsoft Docs
description: Börja hantera Azure Storage-resurser med Storage Explorer. Hämta och installera Azure Storage Explorer, Anslut till ett lagrings konto eller en tjänst med mera.
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: be9b2d9a31d4affc9615f5d2f4b2585b7533a0f6
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545915"
---
# <a name="get-started-with-storage-explorer"></a>Kom igång med Storage Explorer

## <a name="overview"></a>Översikt

Microsoft Azure Storage Explorer är en fristående app som gör det enkelt att arbeta med Azure Storage-data i Windows, macOS och Linux. I den här artikeln lär du dig flera olika sätt att ansluta till och hantera dina Azure Storage-konton.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>Förutsättningar

# <a name="windows"></a>[Windows](#tab/windows)

Följande versioner av Windows stöder Storage Explorer:

* Windows 10 (rekommenderas)
* Windows 8
* Windows 7

För alla versioner av Windows kräver Storage Explorer .NET Framework 4.7.2.

# <a name="macos"></a>[macOS](#tab/macos)

Följande versioner av macOS stöder Storage Explorer:

* macOS 10,12 Sierra och senare versioner

# <a name="linux"></a>[Linux](#tab/linux)

Storage Explorer finns i [Snap Store](https://snapcraft.io/storage-explorer) för de vanligaste distributionerna av Linux. Vi rekommenderar Snap Store för den här installationen. Snapin-modulen Storage Explorer installerar alla dess beroenden och uppdateringar när nya versioner publiceras i Snap-arkivet.

Information om vilka distributioner som stöds finns på [sidan för Snap-installation](https://snapcraft.io/docs/installing-snapd).

Storage Explorer kräver att du använder en lösen ords hanterare. Du kan behöva ansluta till en lösen ords hanterare manuellt. Du kan ansluta Storage Explorer till systemets lösen ords hanterare genom att köra följande kommando:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage Explorer är också tillgängligt som en *. tar. gz* -hämtning. Du måste installera beroenden manuellt. Följande distributioner av Linux-support *. tar. gz* -installation:

* Ubuntu 20,04 x64
* Ubuntu 18,04 x64
* Ubuntu 16,04 x64

Installationen av *. tar. gz* kan fungera på andra distributioner, men endast dessa listade stöds officiellt.

Mer hjälp med att installera Storage Explorer på Linux finns i [Linux-beroenden](./storage/common/storage-explorer-troubleshooting.md#linux-dependencies) i Azure Storage Explorer fel söknings guide.

---

## <a name="download-and-install"></a>Hämta och installera

Information om hur du hämtar och installerar Storage Explorer finns i [Azure Storage Explorer](https://www.storageexplorer.com).

## <a name="connect-to-a-storage-account-or-service"></a>Ansluta till ett lagringskonto eller en tjänst

I Storage Explorer finns flera olika sätt att ansluta till lagringskonton. I allmänhet kan du antingen:

* [Logga in på Azure för att få åtkomst till dina prenumerationer och deras resurser](#sign-in-to-azure)
* [Anslut en speciell lagrings-eller CosmosDB-resurs](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Logga in på Azure

> [!NOTE]
> För att få fullständig åtkomst till resurser när du har loggat in, kräver Storage Explorer både hantering (Azure Resource Manager) och data lager behörigheter. Det innebär att du behöver Azure Active Directory (Azure AD)-behörigheter, vilket ger dig åtkomst till ditt lagrings konto, behållarna i kontot och data i behållarna. Om du har behörighet för data lagret kan du överväga [att lägga till en resurs via Azure AD](#add-a-resource-via-azure-ad). Mer information om de speciella behörigheter som Storage Explorer kräver finns i [fel söknings guiden för Azure Storage Explorer](./storage/common/storage-explorer-troubleshooting.md#azure-rbac-permissions-issues).

1. I Storage Explorer väljer du **Visa**  >  **konto hantering** eller väljer knappen **Hantera konton** .

    ![Hantera konton][1]

1. **Konto hantering** visar nu alla Azure-konton som du har loggat in på. Om du vill ansluta till ett annat konto väljer du **Lägg till ett konto**.

1. I **Anslut till Azure Storage** väljer du ett Azure-moln från **Azure-miljön** för att logga in på ett nationellt moln eller en Azure Stack. När du har valt din miljö väljer du **Nästa**.

    ![Alternativ för att logga in][2]

    Storage Explorer öppnar en sida där du kan logga in. Mer information finns i [ansluta lagrings Utforskaren till en Azure Stack-prenumeration eller ett lagrings konto](/azure-stack/user/azure-stack-storage-connect-se).

1. När du har loggat in med ett Azure-konto visas kontot och de Azure-prenumerationer som är kopplade till kontot under **konto hantering**. Välj **alla prenumerationer** om du vill växla mellan alla eller inga av de listade Azure-prenumerationerna. Välj de Azure-prenumerationer som du vill arbeta med och välj sedan **Använd**.

    ![Välja Azure-prenumerationer][3]

    **Explorer** visar de lagrings konton som är kopplade till de valda Azure-prenumerationerna.

    ![Valda Azure-prenumerationer][4]

### <a name="attach-a-specific-resource"></a>Anslut en speciell resurs

Det finns flera sätt att koppla till en resurs i Storage Explorer:

* [Lägg till en resurs via Azure AD](#add-a-resource-via-azure-ad). Om du har behörighet för data lagret kan du använda det här alternativet för att lägga till en BLOB-behållare eller en Azure Data Lake Storage Gen2 Blob Storage-behållare.
* [Använd en anslutnings sträng](#use-a-connection-string). Använd det här alternativet om du har en anslutnings sträng till ett lagrings konto. Storage Explorer stöder anslutnings strängar för signaturer för både nyckel och [delad åtkomst](./storage/common/storage-sas-overview.md) .
* [Använd en signatur-URI för delad åtkomst](#use-a-shared-access-signature-uri). Om du har en [signatur-URI för delad åtkomst](./storage/common/storage-sas-overview.md) till en BLOB-behållare, fil resurs, kö eller tabell, använder du den för att ansluta till resursen. Om du vill hämta en URI för signatur för delad åtkomst kan du antingen använda [Storage Explorer](#generate-a-sas-in-storage-explorer) eller [Azure Portal](https://portal.azure.com).
* [Använd ett namn och en nyckel](#use-a-name-and-key). Om du känner till någon av konto nycklarna till ditt lagrings konto kan du använda det här alternativet för att snabbt ansluta. Hitta dina nycklar på sidan lagrings konto genom att välja **Inställningar**  >  **åtkomst nycklar** i [Azure Portal](https://portal.azure.com).
* [Anslut till en lokal emulator](#attach-to-a-local-emulator). Om du använder någon av de tillgängliga Azure Storage-emulatorerna kan du använda det här alternativet för att enkelt ansluta till emulatorn.
* [Anslut till ett Azure Cosmos DB-konto med hjälp av en anslutnings sträng](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string). Använd det här alternativet om du har en anslutnings sträng till en CosmosDB-instans.
* [Anslut till Azure Data Lake Store med URI](#connect-to-azure-data-lake-store-by-uri). Använd det här alternativet om du har en URI för att Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Lägg till en resurs via Azure AD

1. Välj **kopplings** symbolen för att öppna **Anslut till Azure Storage**.

    ![Alternativet Anslut till Azure Storage][9]

1. Om du inte redan har gjort det använder du alternativet **Lägg till ett Azure-konto** för att logga in på det Azure-konto som har åtkomst till resursen. När du har loggat in går du tillbaka till **Anslut till Azure Storage**.

1. Välj **Lägg till en resurs via Azure Active Directory (Azure AD)** och välj sedan **Nästa**.

1. Välj ett Azure-konto och klient organisation. Dessa värden måste ha åtkomst till den lagrings resurs som du vill ansluta till. Välj **Nästa**.

1. Välj den resurs typ som du vill bifoga. Ange den information som krävs för att ansluta. 

   Den information som du anger på den här sidan beror på vilken typ av resurs du lägger till. Se till att välja rätt typ av resurs. När du har angett den information som krävs väljer du **Nästa**.

1. Granska **anslutnings sammanfattningen** för att se till att all information är korrekt. Om det är det väljer du **Anslut**. Annars väljer du **tillbaka** för att återgå till föregående sidor för att åtgärda felaktig information.

När anslutningen har lagts till går resurs trädet till den nod som representerar anslutningen. Resursen visas under **lokala & anslutna**  >  **lagrings konton**  >  **(bifogade behållare)**  >  **BLOB-behållare**. Om Storage Explorer inte kan lägga till anslutningen, eller om du inte kan komma åt dina data när du har lagt till anslutningen, kan du läsa mer i [fel söknings guiden för Azure Storage Explorer](./storage/common/storage-explorer-troubleshooting.md).

#### <a name="use-a-connection-string"></a>Använd en anslutningssträng

1. Välj **kopplings** symbolen för att öppna **Anslut till Azure Storage**.

    ![Alternativet Anslut till Azure Storage][9]

1. Välj **Använd en anslutnings sträng** och välj sedan **Nästa**.

1. Välj ett visnings namn för anslutningen och ange din anslutnings sträng. Välj **Nästa**.

1. Granska **anslutnings sammanfattningen** för att se till att all information är korrekt. Om det är det väljer du **Anslut**. Annars väljer du **tillbaka** för att återgå till föregående sidor för att åtgärda felaktig information.

När anslutningen har lagts till går resurs trädet till den nod som representerar anslutningen. Resursen visas under **lokala & anslutna**  >  **lagrings konton**. Om Storage Explorer inte kan lägga till anslutningen, eller om du inte kan komma åt dina data när du har lagt till anslutningen, kan du läsa mer i [fel söknings guiden för Azure Storage Explorer](./storage/common/storage-explorer-troubleshooting.md).

#### <a name="use-a-shared-access-signature-uri"></a>Använd en URI för delad åtkomstsignatur

1. Välj **kopplings** symbolen för att öppna **Anslut till Azure Storage**.

    ![Alternativet Anslut till Azure Storage][9]

1. Välj **Använd en signatur för delad åtkomst-URI (SAS)** och välj sedan **Nästa**.

1. Välj ett visnings namn för anslutningen och ange din signatur-URI för delad åtkomst. Tjänst slut punkten för den typ av resurs som du lägger till ska Autofyll. Om du använder en anpassad slut punkt är det möjligt att den inte är det. Välj **Nästa**.

1. Granska **anslutnings sammanfattningen** för att se till att all information är korrekt. Om det är det väljer du **Anslut**. Annars väljer du **tillbaka** för att återgå till föregående sidor för att åtgärda felaktig information.

När anslutningen har lagts till går resurs trädet till den nod som representerar anslutningen. Resursen visas under **lokala & anslutna**  >  **lagrings konton**  >  **(anslutna behållare)**  >  *noden tjänst för den typ av behållare som du har bifogat*. Om Storage Explorer inte kan lägga till din anslutning, se [fel söknings guiden för Azure Storage Explorer](./storage/common/storage-explorer-troubleshooting.md). Se fel söknings guiden om du inte kan komma åt dina data när du har lagt till anslutningen.

#### <a name="use-a-name-and-key"></a>Använd namn och nyckel

1. Välj **kopplings** symbolen för att öppna **Anslut till Azure Storage**.

    ![Alternativet Anslut till Azure Storage][9]

1. Välj **Använd ett lagrings konto namn och nyckel** och välj sedan **Nästa**.

1. Välj ett visnings namn för anslutningen.

1. Ange ditt lagrings konto namn och någon av dess åtkomst nycklar.

1. Välj den **lagrings domän** som du vill använda och välj sedan **Nästa**.

1. Granska **anslutnings sammanfattningen** för att se till att all information är korrekt. Om det är det väljer du **Anslut**. Annars väljer du **tillbaka** för att återgå till föregående sidor för att åtgärda felaktig information.

När anslutningen har lagts till går resurs trädet till den nod som representerar anslutningen. Resursen visas under **lokala & anslutna**  >  **lagrings konton**. Om Storage Explorer inte kan lägga till anslutningen, eller om du inte kan komma åt dina data när du har lagt till anslutningen, kan du läsa mer i [fel söknings guiden för Azure Storage Explorer](./storage/common/storage-explorer-troubleshooting.md).

#### <a name="attach-to-a-local-emulator"></a>Anslut till lokal emulator

Storage Explorer stöder för närvarande två officiella lagrings emulatorer:

* [Azure Storage emulator](storage/common/storage-use-emulator.md) (endast Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, MacOS eller Linux)

Om emulatorn lyssnar på standard portarna kan du använda noden **emulator-standard portar** för att få åtkomst till din emulator. Sök efter **emulator – standard portar** under **lokala & anslutna**  >  **lagrings konton**.

Följ dessa steg om du vill använda ett annat namn för anslutningen, eller om emulatorn inte körs på standard portarna:

1. Starta din emulator. Ange kommandot `AzureStorageEmulator.exe status` för att visa portarna för varje tjänst typ.

   > [!IMPORTANT]
   > Storage Explorer startar inte emulatorn automatiskt. Du måste starta den manuellt.

1. Välj **kopplings** symbolen för att öppna **Anslut till Azure Storage**.

    ![Alternativet Anslut till Azure Storage][9]

1. Välj **Anslut till en lokal emulator** och välj sedan **Nästa**.

1. Välj ett visnings namn för anslutningen och ange de portar som din emulator lyssnar på för varje tjänst typ. **När du ansluter till en lokal emulator** föreslås standardvärdena för de flesta emulatorer. **Filernas port** är tom, eftersom ingen av de officiella emulatorerna för närvarande stöder fil tjänsten. Om emulatorn du använder stöder filer kan du ange den port som ska användas. Välj **Nästa**.

1. Granska **anslutnings sammanfattningen** och se till att all information är korrekt. Om det är det väljer du **Anslut**. Annars väljer du **tillbaka** för att återgå till föregående sidor för att åtgärda felaktig information.

När anslutningen har lagts till går resurs trädet till den nod som representerar anslutningen. Noden bör visas under **lokala & anslutna**  >  **lagrings konton**. Om Storage Explorer inte kan lägga till anslutningen, eller om du inte kan komma åt dina data när du har lagt till anslutningen, kan du läsa mer i [fel söknings guiden för Azure Storage Explorer](./storage/common/storage-explorer-troubleshooting.md).

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Ansluta till ett Azure Cosmos DB-konto med hjälp av en anslutnings sträng

I stället för att hantera Azure Cosmos DB-konton via en Azure-prenumeration kan du ansluta till Azure Cosmos DB med hjälp av en anslutnings sträng. Gör så här för att ansluta:

1. Under **Utforskaren**, expandera **lokala & anslutna**, högerklicka på **Cosmos DB konton** och välj **Anslut till Azure Cosmos DB**.

    ![Ansluta till Azure Cosmos DB med hjälp av en anslutningssträng][21]

1. Välj Azure Cosmos DB API, ange dina **anslutnings sträng** data och välj sedan **OK** för att ansluta Azure Cosmos DB-kontot. Information om hur du hämtar anslutnings strängen finns i [hantera ett Azure Cosmos-konto](./cosmos-db/how-to-manage-database-account.md).

    ![Anslutningssträng][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Anslut till Azure Data Lake Store per URI

Du kan komma åt en resurs som inte finns i din prenumeration. Du behöver någon som har åtkomst till den resursen för att ge resurs-URI: n. När du har loggat in ansluter du till Data Lake Store med hjälp av URI: n. Gör så här för att ansluta:

1. Under **Utforskaren** expanderar du **lokala & anslutna**.

1. Högerklicka på **data Lake Storage gen1** och välj **Anslut till data Lake Storage gen1**.

    ![Ansluta till Data Lake Store snabb meny](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Ange URI och välj sedan **OK**. Data Lake Store visas under **data Lake Storage**.

    ![Anslut till Data Lake Store resultat](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

I det här exemplet används Data Lake Storage Gen1. Azure Data Lake Storage Gen2 är nu tillgängligt. Mer information finns i [Vad är Azure Data Lake Storage gen1](./data-lake-store/data-lake-store-overview.md).

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Generera en signatur för delad åtkomst i Storage Explorer<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Signatur för delad åtkomst på konto nivå

1. Högerklicka på det lagrings konto som du vill dela och välj sedan **Hämta signatur för delad åtkomst**.

    ![Snabb meny alternativet Hämta signatur för delad åtkomst][14]

1. I **signaturen för delad åtkomst** anger du den tidsram och de behörigheter som du vill använda för kontot och väljer sedan **skapa**.

    ![Hämta en signatur för delad åtkomst][15]

1. Kopiera antingen **anslutnings strängen** eller den råa **frågesträngen** till Urklipp.

### <a name="service-level-shared-access-signature"></a>Signatur för delad åtkomst på service nivå

Du kan få en signatur för delad åtkomst på tjänst nivå. Mer information finns i [Hämta SAS för en BLOB-behållare](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

## <a name="search-for-storage-accounts"></a>Söka efter lagringskonton

Om du vill hitta en lagrings resurs kan du söka i fönstret **Utforskaren** .

När du anger text i sökrutan visar Storage Explorer alla resurser som matchar Sök värdet som du har angett till den punkten. I det här exemplet visas en sökning efter **slut punkter**:

![Lagringskontosökning][23]

> [!NOTE]
> Om du vill påskynda din sökning använder du **konto hantering** för att avmarkera alla prenumerationer som inte innehåller det objekt som du söker efter. Du kan också högerklicka på en nod och välja **Sök härifrån** för att börja söka från en speciell nod.
>
>

## <a name="next-steps"></a>Nästa steg

* [Hantera Azure Blob Storage-resurser med Storage Explorer](vs-azure-tools-storage-explorer-blobs.md)
* [Arbeta med data med hjälp av Azure Storage Explorer](./cosmos-db/storage-explorer.md)
* [Hantera Azure Data Lake Store resurser med Storage Explorer](./data-lake-store/data-lake-store-in-storage-explorer.md)

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