---
title: "Kom igång med Lagringsutforskaren (förhandsversion) | Microsoft Docs"
description: "Hantera Azure-lagringsresurser med Lagringsutforskaren (förhandsversion)"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2017
ms.author: cawa
ms.openlocfilehash: 58ab8a9c5864ce0cb505b78fd087df2973a7e0d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-storage-explorer-preview"></a>Kom igång med Lagringsutforskaren (förhandsversion)
## <a name="overview"></a>Översikt
Azure Lagringsutforskaren (förhandsversion) är en fristående app som gör det enkelt att arbeta med Azure Storage-data i Windows, Mac OS och Linux. I den här artikeln lär du dig hur du kan ansluta till och hantera dina Azure Storage-konton på olika sätt.

![Microsoft Azure Lagringsutforskaren (förhandsversion)][15]

## <a name="prerequisites"></a>Krav
* [Hämta och installera Lagringsutforskaren (förhandsversion)](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Ansluta till ett lagringskonto eller en tjänst
Med Lagringsutforskaren (förhandsversion) kan du ansluta till lagringskonton på flera olika sätt. Du kan till exempel:
* Ansluta till lagringskonton som är associerade med dina Azure-prenumerationer.
* Ansluta till lagringskonton och tjänster som delas från andra Azure-prenumerationer.
* Ansluta till och hantera lokal lagring med hjälp av Azure Storage-emulatorn. 

Dessutom kan du arbeta med lagringskonton i globala och nationella Azure:

* [Ansluta till en Azure-prenumeration](#connect-to-an-azure-subscription): hantera lagringsresurser som hör till din Azure-prenumeration.
* [Arbeta med lokal utvecklingslagring](#work-with-local-development-storage): hantera lokal lagring med hjälp av Azure Storage-emulatorn.
* [Ansluta till extern lagring](#attach-or-detach-an-external-storage-account): hantera lagringsresurser som hör till en annan Azure-prenumeration eller nationella Azure-moln med hjälp av lagringskontots namn, nyckel och slutpunkter.
* [Ansluta ett lagringskonto med hjälp av en SAS](#attach-storage-account-using-sas): hantera lagringsresurser som tillhör en annan Azure-prenumeration med hjälp av en signatur för delad åtkomst (SAS).
* [Ansluta en tjänst med hjälp av SAS](#attach-service-using-sas): hantera en specifik lagringstjänst (blobbehållare, kö eller tabell) som hör till en annan Azure-prenumeration med hjälp av en SAS.
* [Ansluta till ett Azure DB som Cosmos-konto med hjälp av en anslutningssträng](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): hantera Cosmos-DB-konto med hjälp av en anslutningssträng.

## <a name="connect-to-an-azure-subscription"></a>Ansluta till en Azure-prenumeration
> [!NOTE]
> Om du inte redan har ett Azure-konto kan du [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
>
>

1. I Lagringsutforskaren (förhandsversion) väljer du **Inställningar för Azure-konto**.

    ![Inställningar för Azure-konto][0]

2. Den vänstra rutan visar alla Microsoft-konton som du har loggat in på. Om du vill ansluta till ett annat konto väljer du **Lägg till ett konto** och följer instruktionerna för att logga in med ett Microsoft-konto som är kopplat till minst en aktiv Azure-prenumeration.

    >[!NOTE]
    >För närvarande går det inte att ansluta till nationella Azure (t.ex. Azure Germany, Azure Government och Azure China) via inloggning. Mer information om hur du ansluter till nationella Azure Storage-konton finns i avsnittet Ansluta eller koppla från ett externt lagringskonto.

3. När du har loggat in med ett Microsoft-konto fylls den vänstra rutan i med de Azure-prenumerationer som är kopplade till kontot. Välj de Azure-prenumerationer som du vill arbeta med och välj sedan **Använd**. (Om du klickar på **Alla prenumerationer** växlar du mellan att välja alla eller inga av de Azure-prenumerationer som visas.)

    ![Välja Azure-prenumerationer][3]  
    I den vänstra rutan visas lagringskontona som är kopplade till de valda Azure-prenumerationerna.

    ![Valda Azure-prenumerationer][4]

## <a name="connect-to-an-azure-stack-subscription"></a>Ansluta till en Azure Stack-prenumeration

Information om hur du ansluter till en Azure Stackprenumeration finns i [Ansluta Storage Explorer till en prenumeration på Azure Stack](azure-stack/user/azure-stack-storage-connect-se.md).

## <a name="work-with-local-development-storage"></a>Arbeta med lokal utvecklingslagring
Med Lagringsutforskaren (förhandsversion) kan du arbeta mot lokal lagring med hjälp av Azure Storage-emulatorn. På så sätt kan du skriva kod mot och testa lagring utan att nödvändigtvis ha distribuerat ett lagringskonto i Azure (eftersom lagringskontot emuleras av Azure Storage-emulatorn).

> [!NOTE]
> Azure Storage-emulatorn stöds för närvarande endast för Windows.
>
>

1. I den vänstra rutan i Lagringsutforskaren (förhandsversion) utökar du noden **(Lokala och anslutna)** > **Lagringskonton** > **(Utveckling)**.

    ![Noden Lokal utveckling][21]

2. Om du inte har installerat Azure Storage-emulatorn än uppmanas du att göra det via ett informationsfält. Om informationsfältet visas väljer du **Hämta den senaste versionen** och installerar sedan emulatorn.

    ![Fråga om att hämta Azure Storage-emulatorn][22]

3. När emulatorn har installerats kan du skapa och arbeta med lokala blobbar, köer och tabeller. Mer information om hur du arbetar med varje typ av lagringskonto finns i följande avsnitt:

    * [Hantera Azure-bloblagringsresurser](vs-azure-tools-storage-explorer-blobs.md)
    * Hantera Azure-filresurslagringsresurser: *kommer snart*
    * Hantera Azure-kölagringsresurser: *kommer snart*
    * Hantera Azure-tabellagringsresurser: *kommer snart*

## <a name="attach-or-detach-an-external-storage-account"></a>Ansluta eller koppla från ett externt lagringskonto
Med Lagringsutforskaren (förhandsversion) kan du ansluta till externa lagringskonton så att du enkelt kan dela lagringskonton. Det här avsnittet beskriver hur du ansluter till (och kopplar från) externa lagringskonton.

### <a name="get-the-storage-account-credentials"></a>Hämta autentiseringsuppgifterna för lagringskontot
För att kunna dela ett externt lagringskonto måste kontoägaren först hämta autentiseringsuppgifterna (kontonamnet och nyckeln) för kontot och sedan dela den informationen med den person som ska ansluta till det (externa) kontot. Så här hämtar du autentiseringsuppgifterna för ett lagringskonto via Azure Portal:

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Välj **Bläddra**.

3. Välj **Lagringskonton**.

4. Välj önskat lagringskonto på bladet **Lagringskonton**.

5. Välj **Åtkomstnycklar** på bladet **Inställningar** för det valda lagringskontot.

    ![Alternativ för åtkomstnycklar][5]

6. På bladet **Åtkomstnycklar** kopierar du värdena för **lagringskontonnamn** och **nyckel1** som används för att ansluta till lagringskontot.

    ![Åtkomstnycklar][6]

### <a name="attach-to-an-external-storage-account"></a>Ansluta till ett externt lagringskonto
Om du vill ansluta till ett externt lagringskonto behöver du ha tillgång till kontots namn och nyckel. I avsnittet Hämta autentiseringsuppgifter för lagringskonto beskrivs hur du hämtar dessa värden från Azure Portal. Men i portalen kallas kontonyckeln **nyckel1**. Om Lagringsutforskaren (förhandsgranskning) begär en kontonyckel anger du alltså **nyckel1**-värdet.

1. I Lagringsutforskaren (förhandsversion) väljer du **Anslut till Azure Storage**.

    ![Alternativet Anslut till Azure Storage][23]

2. I dialogrutan **Anslut till Azure Storage** anger du kontonyckeln (**nyckel 1**-värdet från Azure Portal) och väljer sedan **Nästa**.

    > [!NOTE]
    > Du kan ange en anslutningssträng för lagring från ett lagringskonto på nationella Azure. Om du till exempel vill ansluta till Azure Germany-lagringskonton anger du anslutningssträngar som ser ut ungefär så här: 
    >
    >* DefaultEndpointsProtocol=https
    >* AccountName=cawatest03
    >* AccountKey=<storage_account_key>
    >* EndpointSuffix=core.cloudapi.de
    
    >Du kan hämta anslutningssträngen på Azure Portal på samma sätt som beskrivs i avsnittet Hämta autentiseringsuppgifterna för lagringskontot.

    ![Dialogrutan Anslut till Azure Storage][24]

3. I dialogrutan **Anslut extern lagring** anger du lagringskontots namn i rutan **Kontonamn**, anger eventuella andra önskade inställningar och väljer sedan **Nästa**.

    ![Dialogrutan Anslut extern lagring][8]

4. Kontrollera uppgifterna i dialogrutan **Anslutningssammanfattning**. Om du vill ändra något väljer du **Tillbaka** och anger sedan önskade inställningar på nytt. 

5. Välj **Anslut**.

6. När det externa lagringskontot har anslutits visas det med texten **(Externt)** sist i namnet.

    ![Resultatet av att ansluta till ett externt lagringskonto][9]

### <a name="detach-from-an-external-storage-account"></a>Ansluta från ett externt lagringskonto
1. Högerklicka på det externa lagringskonto som du vill koppla från och välj sedan **Koppla från**.

    ![Koppla bort lagringsalternativ][10]

2. I bekräftelsemeddelandet väljer du **Ja** för att bekräfta att du vill koppla bort det externa lagringskontot.

## <a name="attach-a-storage-account-by-using-an-sas"></a>Ansluta ett lagringskonto med hjälp av en SAS
Med en [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) kan administratören för en Azure-prenumeration bevilja tillfällig åtkomst till ett lagringskonto utan att uppge autentiseringsuppgifter för Azure-prenumerationen.

För att illustrera detta antar vi att Användare A är administratör för en Azure-prenumeration och vill ge Användare B åtkomst till ett lagringskonto under en begränsad tid med särskilda behörigheter:

1. Användare A genererar en SAS (som består av anslutningssträngen för lagringskontot) för en viss tidsperiod och med önskade behörigheter.

2. Användare A delar SAS med den person (Användare B i vårt exempel) som vill ha åtkomst till lagringskontot.  

3. Användare B använder Lagringsutforskaren (förhandsversion) för att ansluta till kontot som hör till Användare A med hjälp av den SAS som han eller hon fått.

### <a name="get-an-sas-for-the-account-you-want-to-share"></a>Hämta en SAS för det konto som du vill dela
1. I Lagringshanteraren (förhandsversion) högerklickar du på det lagringskonto som du vill dela och väljer sedan **Hämta signatur för delad åtkomst**.

    ![Snabbmenyalternativet Hämta SAS][13]

2. I dialogrutan **Signatur för delad åtkomst** anger du det tidsintervall och de behörigheter som du vill använda för kontot och väljer sedan **Skapa**.

    Dialogrutan ![Hämta SAS][14]  
    SAS visas i dialogrutan **Signatur för delad åtkomst**.

3. Välj **Kopiera** bredvid **anslutningssträngen** för att kopiera den till Urklipp och välj sedan **Stäng**.

### <a name="attach-to-the-shared-account-by-using-the-sas"></a>Ansluta till det delade kontot med hjälp av SAS
1. I Lagringsutforskaren (förhandsversion) väljer du **Anslut till Azure Storage**.

    ![Alternativet Anslut till Azure Storage][23]

2. I dialogrutan **Anslut till Azure Storage** anger du anslutningssträngen och väljer sedan **Nästa**.

    ![Dialogrutan Anslut till Azure Storage][24]

3. Kontrollera uppgifterna i dialogrutan **Anslutningssammanfattning**. Om du vill göra ändringar väljer du **Bakåt** och anger sedan önskade inställningar. 

4. Välj **Anslut**.

5. När lagringskontot har anslutits visas det med texten **(SAS)** sist i kontonamnet som du angav.

    ![Resultatet av anslutning till ett konto med SAS][17]

## <a name="attach-a-service-by-using-an-sas"></a>Ansluta en tjänst med en SAS
I avsnittet Ansluta ett lagringskonto med hjälp av SAS beskrivs hur en administratör för en Azure-prenumeration kan bevilja tillfällig åtkomst till ett lagringskonto genom att generera och dela en SAS för lagringskontot. På samma sätt kan en SAS genereras för en specifik tjänst (blobbehållare, kö eller tabell) i ett lagringskonto.  

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>Generera en SAS för den tjänst som du vill dela
I det här scenariot kan en tjänst vara en blobbehållare, en kö eller en tabell. Om du vill generera SAS för en tjänst kan du läsa:

* [Hämta SAS för en blobbehållare](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* Hämta SAS för en filresurs: *kommer snart*
* Hämta SAS för en kö: *kommer snart*
* Hämta SAS för en tabell: *kommer snart*

### <a name="attach-to-the-shared-account-service-by-using-the-sas"></a>Ansluta till tjänsten för det delade kontot med hjälp av SAS
1. I Lagringsutforskaren (förhandsversion) väljer du **Anslut till Azure Storage**.

    ![Alternativet Anslut till Azure Storage][23]

2. I dialogrutan **Anslut till Azure Storage** anger du SAS-URI och väljer sedan **Nästa**.

    ![Dialogrutan Anslut till Azure Storage][24]

3. Kontrollera uppgifterna i dialogrutan **Anslutningssammanfattning**. Om du vill göra ändringar väljer du **Bakåt** och anger sedan önskade inställningar. 

4. Välj **Anslut**.

5. När tjänsten har anslutits visas den under noden **(SAS för tjänst)**.

    ![Resultatet av att ansluta till en delad tjänst med en SAS][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Ansluta till ett Azure DB som Cosmos-konto med hjälp av en anslutningssträng
Förutom hantera Azure Cosmos DB konton via Azure-prenumeration, ett alternativt sätt att ansluta till en Azure-Cosmos-databas är att använda en anslutningssträng. Använd följande steg för att ansluta med en anslutningssträng.

1. Hitta **lokala och bifogad** i vänster träd högerklickar du på **Azure Cosmos DB konton**, Välj **Anslut till Azure Cosmos DB...**

    ![ansluta till Azure Cosmos-databas med anslutningssträngen][33]

2. Välj Azure Cosmos DB-API, klistra in din **anslutningssträngen**, och klicka sedan på **OK** att ansluta till Azure DB som Cosmos-konto. Information om hur du hämtar anslutningssträngen finns [hämta anslutningssträngen](https://docs.microsoft.com/en-us/azure/cosmos-db/manage-account#get-the--connection-string).

    ![connection-string][32]

## <a name="search-for-storage-accounts"></a>Söka efter lagringskonton
Om du har en lång lista med lagringskonton kan du snabbt hitta ett visst lagringskonto genom att använda sökrutan överst i den vänstra rutan.

När du skriver i sökrutan visas de lagringskonton som matchar sökvärdet som du har skrivit hittills i den vänstra rutan. På följande skärmbild visas ett exempel på en sökning efter alla lagringskonton vars namn innehåller **tarcher**:

![Lagringskontosökning][11]

## <a name="next-steps"></a>Nästa steg
* [Hantera Azure Blob Storage-resurser med Lagringsutforskaren (förhandsversion)](vs-azure-tools-storage-explorer-blobs.md)
* [Hantera Azure Cosmos DB i Azure Lagringsutforskaren (förhandsversion)](./cosmos-db/tutorial-documentdb-and-mongodb-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-icon.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-account-link.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/subscriptions-list.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-accounts-list.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/mase.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-icon.png
[24]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-next.png
[25]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-certificate-azure-stack.png
[26]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/export-root-cert-azure-stack.png
[27]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/import-azure-stack-cert-storage-explorer.png
[28]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-target-azure-stack.png
[29]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-azure-stack-account.png
[30]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-accounts-azure-stack.png
[31]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/azure-stack-storage-account-list.png
[32]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.PNG
[33]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.PNG
