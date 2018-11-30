---
title: Kom igång med Storage Explorer | Microsoft Docs
description: Hantera Azure storage-resurser med Storage Explorer
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2017
ms.author: cawa
ms.openlocfilehash: a540e6c585210f7b1dc36a89eaae7a4b4bf8fc70
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422736"
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

* Ubuntu 16.04 x64 (rekommenderas)
* Ubuntu 17.10 x64
* Ubuntu 14.04 x64

Azure Storage Explorer fungerar på andra distributioner, men endast som visas ovan stöds officiellt.

Du måste också ha följande beroenden/bibliotek installeras för körning av Azure Storage Explorer på Linux:

* [.NET core 2.x](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* libsecret (Obs: libsecret 1.so.0 måste vara tillgängliga på din dator. Om du har en annan version av libsecret installerad kan du länka mjuk dess so-fil till libsecret 1.so.0)
* libgconf-2-4
* Uppdaterade GCC

Azure Storage Explorer [viktig](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) innehålla specifika åtgärder för vissa distributioner.

[Hämta och installera Storage Explorer](https://www.storageexplorer.com)

---

## <a name="connect-to-a-storage-account-or-service"></a>Ansluta till ett lagringskonto eller en tjänst

I Storage Explorer finns flera olika sätt att ansluta till lagringskonton. Du kan till exempel:

* Ansluta till lagringskonton som är associerade med dina Azure-prenumerationer.
* Ansluta till lagringskonton och tjänster som delas från andra Azure-prenumerationer.
* Ansluta till och hantera lokal lagring med hjälp av Azure Storage-emulatorn.

Dessutom kan du arbeta med lagringskonton i globala och nationella Azure:

* [Ansluta till en Azure-prenumeration](#connect-to-an-azure-subscription): hantera lagringsresurser som hör till din Azure-prenumeration.
* [Arbeta med lokal utvecklingslagring](#work-with-local-development-storage): hantera lokal lagring med hjälp av Azure Storage-emulatorn.
* [Ansluta till extern lagring](#attach-or-detach-an-external-storage-account): hantera lagringsresurser som hör till en annan Azure-prenumeration eller nationella Azure-moln med hjälp av lagringskontots namn, nyckel och slutpunkter.
* [Ansluta ett lagringskonto med hjälp av en SAS](#attach-storage-account-using-sas): hantera lagringsresurser som tillhör en annan Azure-prenumeration med hjälp av en signatur för delad åtkomst (SAS).
* [Ansluta en tjänst med hjälp av SAS](#attach-service-using-sas): hantera en specifik lagringstjänst (blobcontainer, kö eller tabell) som hör till en annan Azure-prenumeration med hjälp av en SAS.
* [Ansluta till ett Azure Cosmos DB-konto med hjälp av en anslutningssträng](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): hantera Cosmos DB-konto med hjälp av en anslutningssträng.

## <a name="connect-to-an-azure-subscription"></a>Ansluta till en Azure-prenumeration

> [!NOTE]
> Om du inte redan har ett Azure-konto kan du [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
>
>

1. I Storage Explorer väljer **hantera konton** att gå till den **konto Management panelen**.

    ![Hantera konton][1]

2. Den vänstra rutan visar nu alla Azure-konton som du har loggat in till. Om du vill ansluta till ett annat konto, Välj **Lägg till ett konto**

3. Om du vill logga in på ett nationella moln eller en Azure Stack, klickar du på den **Azure-miljön** listrutan att välja vilket Azure moln som du vill använda. När du har valt din miljö, klickar du på den **inloggning...**  knappen. Om du loggar in på Azure Stack, se [ansluta Storage Explorer till en Azure stackprenumeration](azure-stack/user/azure-stack-storage-connect-se.md) för mer information.

    ![Logga In alternativet][2]

4. När du har loggat in med ett Azure-konto, läggs kontot och de Azure-prenumerationer kopplade till kontot till den vänstra rutan. Välj de Azure-prenumerationer som du vill arbeta med och välj sedan **tillämpa** (väljer **alla prenumerationer:** växlar du mellan att välja alla eller inga av de angivna Azure-prenumerationerna).

    ![Välja Azure-prenumerationer][3]

    I den vänstra rutan visas lagringskontona som är kopplade till de valda Azure-prenumerationerna.

    ![Valda Azure-prenumerationer][4]

## <a name="work-with-local-development-storage"></a>Arbeta med lokal utvecklingslagring

Med Lagringsutforskaren kan arbeta du med lokal lagring med hjälp av en emulator. Den här metoden kan du simulera arbeta med Azure Storage utan att nödvändigtvis ha distribuerat på Azure ett lagringskonto.

Från och med version 1.1.0, stöds lokala lagringsemulatorn på alla plattformar. Lagringsutforskaren kan ansluta till alla emulerade tjänster lyssna på dess standardslutpunkterna för lokal lagring.

> [!NOTE]
> Stöd för storage-tjänster och funktioner kan variera mycket beroende på ditt val av emulatorn. Kontrollera att din emulatorn stöder de tjänster och funktioner som du vill arbeta med.

1. Konfigurera tjänster i din emulatorn att lyssna på en ledig port.

   Emulerade Service | Standardslutpunkten
   -----------------|-------------------------
   Blobar            | `http://127.0.0.1:10000`
   Köer           | `http://127.0.0.1:10001`
   Tabeller           | `http://127.0.0.1:10002`

2. Starta emulatorn.
   > [!IMPORTANT]
   > Lagringsutforskaren startar inte automatiskt din emulatorn. Du måste starta det själv.

3. I Storage Explorer klickar du på den **Lägg till konto** knappen. Välj **koppla till en lokal emulator** och klicka på **nästa**.

4. Ange portnumren för tjänsterna som du konfigurerade ovan (lämna tomt om du inte planerar att använda tjänsten). Klicka på **nästa** sedan **Connect** att skapa anslutningen.

5. Expandera den **lokal och ansluten** > **Lagringskonton** > noder, sedan expanderar du tjänsten under den noden som motsvarar anslutningens emulatorn.

   Du kan använda den här noden för att skapa och arbeta med lokala blobbar, köer och tabeller. Om du vill veta hur du arbetar med varje typ av lagringskonto finns i följande handböcker:

   * [Hantera Azure Blob storage-resurser](vs-azure-tools-storage-explorer-blobs.md)
   * [Hantera Azure File storage-resurser](vs-azure-tools-storage-explorer-files.md)

## <a name="attach-or-detach-an-external-storage-account"></a>Ansluta eller koppla från ett externt lagringskonto

Med Lagringsutforskaren kan ansluta du till externa lagringskonton så att storage-konton kan delas. Det här avsnittet beskriver hur du ansluter till (och kopplar från) externa lagringskonton.

### <a name="get-the-storage-account-credentials"></a>Hämta autentiseringsuppgifterna för lagringskontot

Om du vill dela ett externt lagringskonto ägare för att först hämta autentiseringsuppgifterna (kontonamnet och nyckeln) för kontot och sedan dela att information med den person som ska ansluta till SA konto. Du kan hämta autentiseringsuppgifterna för ett lagringskonto via Azure portal genom att göra följande:

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Välj **Bläddra**.

3. Välj **Lagringskonton**.

4. I listan över **Lagringskonton**, Välj önskat lagringskonto.

5. Under **Inställningar** klickar du på **Åtkomstnycklar**.

    ![Alternativ för åtkomstnycklar][7]

6. Kopiera den **lagringskontonamn** och **key1**.

    ![Åtkomstnycklar][8]

### <a name="attach-to-an-external-storage-account"></a>Ansluta till ett externt lagringskonto

Om du vill ansluta till ett externt lagringskonto behöver du ha tillgång till kontots namn och nyckel. I avsnittet Hämta autentiseringsuppgifter för lagringskonto beskrivs hur du hämtar dessa värden från Azure Portal. Men i portalen kallas kontonyckeln **nyckel1**. Så när Lagringsutforskaren begär en kontonyckel, anger du den **key1** värde.

1. I Storage Explorer, öppna den **ansluta dialogrutan**.

    ![Alternativet Anslut till Azure Storage][9]

2. I den **ansluta dialogrutan**, Välj **använder ett lagringskontonamn och nyckel**

    ![Lägg till med namn och nyckel alternativet][10]

3. Klistra in namnet på ditt konto i den **kontonamn** text rutan och klistra in din kontonyckel (den **key1** värdet från Azure portal) till den **kontonyckel** textrutan och välj sedan **Nästa**.

    ![Namn och nyckel][11]

    > [!NOTE]
    > Om du vill använda ett namn och en nyckel från ett nationella moln, använder den **domän för lagringsslutpunkter:** listrutan och välj domänen som slutpunkter:
    >
    >

4. Kontrollera uppgifterna i dialogrutan **Anslutningssammanfattning**. Om du vill ändra något väljer du **Tillbaka** och anger sedan önskade inställningar på nytt.

5. Välj **Anslut**.

6. När lagringskontot har har kopplats, storage-konto visas med **(extern)** slutet av namnet.

    ![Resultatet av att ansluta till ett externt lagringskonto][12]

### <a name="detach-from-an-external-storage-account"></a>Ansluta från ett externt lagringskonto

1. Högerklicka på det externa lagringskonto som du vill koppla från och välj sedan **Koppla från**.

    ![Koppla bort lagringsalternativ][13]

2. I bekräftelsemeddelandet väljer du **Ja** för att bekräfta att du vill koppla bort det externa lagringskontot.

## <a name="attach-a-storage-account-by-using-a-shared-access-signature-sas"></a>Ansluta ett lagringskonto med hjälp av en signatur för delad åtkomst (SAS)

En signatur för delad åtkomst eller [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md), kan administratören för en Azure-prenumeration bevilja tillfällig åtkomst till ett lagringskonto utan att behöva ange autentiseringsuppgifter för Azure-prenumeration.

För att illustrera detta antar vi att Användare A är administratör för en Azure-prenumeration och vill ge Användare B åtkomst till ett lagringskonto under en begränsad tid med särskilda behörigheter:

1. Användare a genererar en SAS-anslutningssträng för en viss tidsperiod och med önskade behörigheter.

2. Användare a delar SAS med den person (användare b i det här exemplet) som vill ha åtkomst till lagringskontot.

3. B använder Lagringsutforskaren för att ansluta till det konto som hör till användare a med hjälp av SAS.

### <a name="generate-a-sas-connection-string-for-the-account-you-want-to-share"></a>Generera en SAS-anslutningssträng för det konto som du vill dela

1. Högerklicka på det lagringskonto som du vill dela och välj sedan i Storage Explorer **hämta signatur för delad åtkomst...** .

    ![Snabbmenyalternativet Hämta SAS][14]

2. I den **generera signatur för delad åtkomst** dialogrutan anger du det tidsintervall och behörigheter som du vill använda för kontot och klicka sedan på den **skapa** knappen.

    ![Hämta SAS-dialogrutan][15]

3. Bredvid den **Connection String** textrutan **kopia** kopiera till Urklipp och klicka sedan på **Stäng**.

### <a name="attach-to-a-storage-account-by-using-a-sas-connection-string"></a>Ansluta till ett lagringskonto med hjälp av en SAS-anslutningssträng

1. I Storage Explorer, öppna den **ansluta dialogrutan**.

    ![Alternativet Anslut till Azure Storage][9]

2. I den **ansluta dialogrutan** dialogrutan Välj **använder en anslutningssträng eller en signatur för delad åtkomst URI** och klicka sedan på **nästa**.

    ![Dialogrutan Anslut till Azure Storage][16]

3. Välj **använda en anslutningssträng** och klistra in anslutningssträngen i den **anslutningssträng:** fält. Klicka på den **nästa** knappen.

    ![Dialogrutan Anslut till Azure Storage][17]

4. Kontrollera uppgifterna i dialogrutan **Anslutningssammanfattning**. Om du vill göra ändringar väljer du **Bakåt** och anger sedan önskade inställningar.

5. Välj **Anslut**.

6. När lagringskontot har har kopplats, storage-konto visas med **(SAS)** slutet av namnet.

    ![Resultatet av anslutning till ett konto med SAS][18]

## <a name="attach-a-service-by-using-a-shared-access-signature-sas"></a>Ansluta en tjänst med hjälp av en signatur för delad åtkomst (SAS)

Avsnittet ”ansluta ett lagringskonto med hjälp av en SAS” förklarar hur administratören av en Azure-prenumeration kan bevilja tillfällig åtkomst till ett lagringskonto genom att generera och dela en SAS för lagringskontot. På samma sätt kan genereras en SAS för en specifik tjänst (blobbehållare, kö, tabell eller filresurs) i ett lagringskonto.

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>Generera en SAS för den tjänst som du vill dela

I det här sammanhanget, kan en tjänst vara en blobbehållare, kö, tabell eller filresurs. Om du vill generera SAS för en tjänst kan du läsa:

* [Hämta SAS för en blobcontainer](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

### <a name="attach-to-the-shared-account-service-by-using-a-sas-uri"></a>Ansluta till tjänsten för delade kontot med hjälp av en SAS-URI

1. I Storage Explorer, öppna den **ansluta dialogrutan**.

    ![Alternativet Anslut till Azure Storage][9]

2. I den **ansluta dialogrutan** dialogrutan väljer du **använder en anslutningssträng eller en signatur för delad åtkomst URI** och klicka sedan på **nästa**.

    ![Dialogrutan Anslut till Azure Storage][16]

3. Välj **använder en SAS-URI** och klistra in din URI till den **URI:** fält. Klicka på den **nästa** knappen.

    ![Dialogrutan Anslut till Azure Storage][19]

4. Kontrollera uppgifterna i dialogrutan **Anslutningssammanfattning**. Om du vill göra ändringar väljer du **Bakåt** och anger sedan önskade inställningar.

5. Välj **Anslut**.

6. När tjänsten har anslutits visas tjänsten den **(SAS-Attached tjänster)** noden.

    ![Resultatet av att ansluta till en delad tjänst med en SAS][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Ansluta till ett Azure Cosmos DB-konto med hjälp av en anslutningssträng

Förutom hantera Azure Cosmos DB-konton via Azure-prenumeration, ett alternativt sätt att ansluta till ett Azure Cosmos DB är att använda en anslutningssträng. Följ stegen nedan för att ansluta med en anslutningssträng.

1. Hitta **Lokala och anslutna** i trädet till vänster, högerklicka på **Azure Cosmos DB-konton** och välj **Anslut till Azure Cosmos DB...**

    ![ansluta till Azure Cosmos DB med anslutningssträngen][21]

2. Välj Azure Cosmos DB API, klistra in din **Connection String**, och klicka sedan på **OK** att ansluta Azure Cosmos DB-konto. Information om hur du hämtar anslutningssträngen finns i [Hämta anslutningssträngen](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![connection-string][22]

## <a name="connect-to-azure-data-lake-store-by-uri"></a>Ansluta till Azure Data Lake Store av URI: N

Om du vill komma åt resurser som inte finns i din prenumeration. Men andra ger dig behörighet att komma åt resursernas URI. I det här fallet kan du ansluta till Data Lake Store med denna URI när du loggat in. Se följande steg.

1. Öppna Storage Explorer.
2. I det vänstra fönstret expanderar du **Lokala och ansluten**.
3. Högerklicka på **Data Lake Store** och från snabbmenyn väljer du **Anslut till Data Lake Store...**.

    ![snabbmenyn anslut till Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Ange den URI du fått, verktyget går till den URL du angett.

    ![dialogen anslut till Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![resultat för ansluta till Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)

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
