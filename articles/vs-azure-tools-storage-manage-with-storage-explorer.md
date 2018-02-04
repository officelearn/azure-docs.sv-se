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
ms.openlocfilehash: d2b93eec9d3ac575e771bceb0ac45823254c142d
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="get-started-with-storage-explorer-preview"></a>Kom igång med Lagringsutforskaren (förhandsversion)
## <a name="overview"></a>Översikt
Azure Lagringsutforskaren (förhandsversion) är en fristående app som gör det enkelt att arbeta med Azure Storage-data i Windows, Mac OS och Linux. I den här artikeln får du lära dig flera sätt att ansluta till och hantera dina Azure storage-konton.

![Microsoft Azure Lagringsutforskaren (förhandsversion)][0]

## <a name="prerequisites"></a>Förutsättningar
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

1. I Lagringsutforskaren (förhandsversion), väljer **hantera konton** att gå till den **konto Management panelen**.

    ![Hantera konton][1]

2. Den vänstra rutan visas nu de Azure konton som du har loggat in till. För att ansluta till ett annat konto väljer **Lägg till ett konto**

3. Om du vill logga in på ett nationella moln eller en Azure-stacken, klickar du på den **Azure-miljön** listrutan för att välja vilka Azure moln som du vill använda. När du har valt att din miljö, klickar du på den **logga in...**  knappen. Om du loggar in på Azure-stacken, se [ansluta Lagringsutforskaren till en prenumeration på Azure-stacken](azure-stack/user/azure-stack-storage-connect-se.md) för mer information.

    ![Logga In alternativet][2]

3. När du loggar in med ett Azure-konto, läggs kontot och Azure-prenumerationer som är associerade med det kontot till den vänstra rutan. Välj de Azure-prenumerationer som du vill arbeta med och välj sedan **tillämpa** (Selecting **alla prenumerationer:** växlar mellan att välja alla eller inga av de listade Azure-prenumerationerna).

    ![Välja Azure-prenumerationer][3]

    I den vänstra rutan visas lagringskontona som är kopplade till de valda Azure-prenumerationerna.

    ![Valda Azure-prenumerationer][4]

## <a name="work-with-local-development-storage"></a>Arbeta med lokal utvecklingslagring
Med Lagringsutforskaren (förhandsversion) kan du arbeta mot lokal lagring med hjälp av Azure Storage-emulatorn. Den här metoden kan du simulera arbeta med Azure Storage utan att ha ett lagringskonto som har distribuerats på Azure, eftersom lagringskontot emuleras av Azure Storage-emulatorn.

> [!NOTE]
> Azure Storage-emulatorn stöds för närvarande endast för Windows.
>
>

> [!NOTE]
> Azure Storage-emulatorn har inte stöd för filresurser.
>
>

1. I den vänstra rutan i Lagringsutforskaren (förhandsversion), expandera den **(lokala och bifogad)** > **Lagringskonton** > **(utveckling)**  >  **Blobbbehållare** nod.

    ![Noden Lokal utveckling][5]

2. Om du ännu inte har installerat Azure Storage-emulatorn, uppmanas du att göra det via ett informationsfält. Om informationsfältet visas väljer du **Hämta den senaste versionen** och installerar sedan emulatorn.

    ![Fråga om att hämta Azure Storage-emulatorn][6]

3. När emulatorn har installerats kan du skapa och arbeta med lokala blobbar, köer och tabeller. Om du vill veta hur du arbetar med respektive lagringskontotyp finns i följande handböcker:

    * [Hantera Azure-bloblagringsresurser](vs-azure-tools-storage-explorer-blobs.md)
    * Hantera Azure-filresurslagringsresurser: *kommer snart*
    * Hantera Azure-kölagringsresurser: *kommer snart*
    * Hantera Azure-tabellagringsresurser: *kommer snart*

## <a name="attach-or-detach-an-external-storage-account"></a>Ansluta eller koppla från ett externt lagringskonto
Med Lagringsutforskaren (förhandsversion) kan du ansluta till externa lagringskonton så att du enkelt kan dela lagringskonton. Det här avsnittet beskriver hur du ansluter till (och kopplar från) externa lagringskonton.

### <a name="get-the-storage-account-credentials"></a>Hämta autentiseringsuppgifterna för lagringskontot
Om du vill dela ett externt lagringskonto ägaren av det kontot först hämta autentiseringsuppgifterna (kontonamnet och nyckeln) för kontot och sedan dela informationen med den person som vill ansluta till rapporterade att kontot. Du kan hämta autentiseringsuppgifter för lagringskonto via Azure portal genom att göra följande:

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Välj **Bläddra**.

3. Välj **Lagringskonton**.

4. I listan över **Lagringskonton**, Välj önskat lagringskonto.

5. Under **inställningar**väljer **åtkomstnycklar**.

    ![Alternativ för åtkomstnycklar][7]

6. Kopiera den **lagringskontonamnet** och **key1**.

    ![Åtkomstnycklar][8]

### <a name="attach-to-an-external-storage-account"></a>Ansluta till ett externt lagringskonto
Om du vill ansluta till ett externt lagringskonto behöver du ha tillgång till kontots namn och nyckel. I avsnittet Hämta autentiseringsuppgifter för lagringskonto beskrivs hur du hämtar dessa värden från Azure Portal. Men i portalen kallas kontonyckeln **nyckel1**. Så, när Lagringsutforskaren (förhandsversion) begär en kontonyckel, anger du den **key1** värde.

1. I Lagringsutforskaren (förhandsversion), öppnar den **ansluta dialogrutan**.

    ![Alternativet Anslut till Azure Storage][9]

2. I den **ansluta dialogrutan**, Välj **använder ett lagringskontonamn och nyckel**

    ![Lägg till med namn och alternativet][10]

3. Klistra in namnet på ditt konto i den **kontonamn** text rutan och klistra in din kontonyckel (den **key1** värdet från Azure portal) till den **kontonyckel** textruta och välj sedan **Nästa**.

    ![Namn och nyckel][11]

    > [!NOTE]
    > Använd ett namn och en nyckel från ett nationella moln i **lagring slutpunkter domän:** listrutan och välj domänen slutpunkter: 
    >
    >

4. Kontrollera uppgifterna i dialogrutan **Anslutningssammanfattning**. Om du vill ändra något väljer du **Tillbaka** och anger sedan önskade inställningar på nytt. 

5. Välj **Anslut**.

6. När lagringskontot har har kopplats, storage-konto visas med **(externt)** i slutet av namnet.

    ![Resultatet av att ansluta till ett externt lagringskonto][12]

### <a name="detach-from-an-external-storage-account"></a>Ansluta från ett externt lagringskonto
1. Högerklicka på det externa lagringskonto som du vill koppla från och välj sedan **Koppla från**.

    ![Koppla bort lagringsalternativ][13]

2. I bekräftelsemeddelandet väljer du **Ja** för att bekräfta att du vill koppla bort det externa lagringskontot.

## <a name="attach-a-storage-account-by-using-a-shared-access-signature-sas"></a>Koppla ett lagringskonto med hjälp av en delad signatur åtkomst (SAS)
En signatur för delad åtkomst eller [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md), kan administratören av en Azure-prenumeration bevilja tillfällig åtkomst till ett lagringskonto utan att behöva ange autentiseringsuppgifter för Azure-prenumeration.

För att illustrera detta antar vi att Användare A är administratör för en Azure-prenumeration och vill ge Användare B åtkomst till ett lagringskonto under en begränsad tid med särskilda behörigheter:

1. Användare a genererar en SAS-anslutningssträng för en viss tidsperiod och med önskade behörigheter.

2. Användare a delar SAS med den person (i det här exemplet b) som vill ha åtkomst till lagringskontot.  

3. Användare B använder Lagringsutforskaren (förhandsversion) för att ansluta till kontot som hör till Användare A med hjälp av den SAS som han eller hon fått.

### <a name="generate-a-sas-connection-string-for-the-account-you-want-to-share"></a>Generera en SAS-anslutningssträng för det konto som du vill dela
1. I Lagringsutforskaren (förhandsversion) högerklickar du på det lagringskontot som du vill dela och välj sedan **hämta signatur för delad åtkomst...** .

    ![Snabbmenyalternativet Hämta SAS][14]

2. I den **Generera en signatur för delad åtkomst** dialogrutan Ange tidsintervall och behörigheter som du vill använda för kontot och klicka sedan på den **skapa** knappen.

    ![Hämta SAS-dialogrutan][15]  

3. Bredvid den **anslutningssträngen** text markerar **kopiera** kopiera till Urklipp och klicka sedan på **Stäng**.

### <a name="attach-to-a-storage-account-by-using-a-sas-connection-string"></a>Anslut till ett lagringskonto med hjälp av en SAS-anslutningssträng
1. I Lagringsutforskaren (förhandsversion), öppnar den **ansluta dialogrutan**.

    ![Alternativet Anslut till Azure Storage][9]

2. I den **ansluta dialogrutan** dialogrutan Välj **använder en anslutningssträng eller en signatur för delad åtkomst URI** och klicka sedan på **nästa**.

    ![Dialogrutan Anslut till Azure Storage][16]

3. Välj **använder en anslutningssträng** och klistra in anslutningssträngen i den **anslutningssträng:** fält. Klicka på den **nästa** knappen.

    ![Dialogrutan Anslut till Azure Storage][17]

4. Kontrollera uppgifterna i dialogrutan **Anslutningssammanfattning**. Om du vill göra ändringar väljer du **Bakåt** och anger sedan önskade inställningar. 

5. Välj **Anslut**.

6. När lagringskontot har har kopplats, storage-konto visas med **(SAS)** i slutet av namnet.

    ![Resultatet av anslutning till ett konto med SAS][18]

## <a name="attach-a-service-by-using-a-shared-access-signature-sas"></a>Koppla en tjänst med hjälp av en delad signatur åtkomst (SAS)
Avsnittet ”Koppla ett lagringskonto med hjälp av en SAS” beskrivs hur en administratör för Azure-prenumeration kan bevilja tillfällig åtkomst till ett lagringskonto genom att generera och dela en SAS för lagringskontot. På samma sätt kan en SAS genereras för en specifik tjänst (blobbehållare, kön, tabell eller filresurs) i ett lagringskonto.  

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>Generera en SAS för den tjänst som du vill dela
I den här kontexten en tjänst vara en blobbehållare, kö, tabell eller filresurs. Om du vill generera SAS för en tjänst kan du läsa:

* [Hämta SAS för en blobbehållare](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* Hämta SAS för en filresurs: *kommer snart*
* Hämta SAS för en kö: *kommer snart*
* Hämta SAS för en tabell: *kommer snart*

### <a name="attach-to-the-shared-account-service-by-using-a-sas-uri"></a>Ansluta till tjänsten delade kontot med hjälp av SAS-URI
1. I Lagringsutforskaren (förhandsversion), öppnar den **ansluta dialogrutan**.

    ![Alternativet Anslut till Azure Storage][9]

2. I den **ansluta dialogrutan** dialogrutan Välj **använder en anslutningssträng eller en signatur för delad åtkomst URI** och klicka sedan på **nästa**.

    ![Dialogrutan Anslut till Azure Storage][16]

3. Välj **använder en SAS-URI** och klistra in din URI i den **URI:** fält. Klicka på den **nästa** knappen.

    ![Dialogrutan Anslut till Azure Storage][19]

3. Kontrollera uppgifterna i dialogrutan **Anslutningssammanfattning**. Om du vill göra ändringar väljer du **Bakåt** och anger sedan önskade inställningar. 

4. Välj **Anslut**.

5. När tjänsten har kopplats tjänsten visas under den **(SAS-Attached Services)** nod.

    ![Resultatet av att ansluta till en delad tjänst med en SAS][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Ansluta till ett Azure DB som Cosmos-konto med hjälp av en anslutningssträng
Förutom hantera Azure Cosmos DB konton via Azure-prenumeration, ett alternativt sätt att ansluta till en Azure-Cosmos-databas är att använda en anslutningssträng. Följ stegen nedan för att ansluta med en anslutningssträng.

1. Hitta **Lokala och anslutna** i trädet till vänster, högerklicka på **Azure Cosmos DB-konton** och välj **Anslut till Azure Cosmos DB...**

    ![ansluta till Azure Cosmos-databas med anslutningssträngen][21]

2. Välj Azure Cosmos DB-API, klistra in din **anslutningssträngen**, och klicka sedan på **OK** att ansluta till Azure DB som Cosmos-konto. Information om hur du hämtar anslutningssträngen finns i [Hämta anslutningssträngen](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![connection-string][22]

## <a name="search-for-storage-accounts"></a>Söka efter lagringskonton
Om du vill söka efter en lagringsresurs och inte vet om det är, kan du använda sökrutan överst i den vänstra rutan för att söka efter resursen.

När du skriver i sökrutan visar alla resurser som matchar sökvärdet som du har skrivit hittills i den vänstra rutan. Till exempel en sökning efter **slutpunkter** visas i följande skärmbild:

![Lagringskontosökning][23]

> [!NOTE]
> Använd den **konto Management panelen** att avmarkera alla prenumerationer som inte innehåller objekt som du söker efter för att förbättra körningstiden för sökningen. Du kan också högerklicka på en nod och välja **sökning från här** söka från en viss nod.
>
>

## <a name="next-steps"></a>Nästa steg
* [Hantera Azure Blob Storage-resurser med Lagringsutforskaren (förhandsversion)](vs-azure-tools-storage-explorer-blobs.md)
* [Hantera Azure Cosmos DB i Azure Lagringsutforskaren (förhandsversion)](./cosmos-db/storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/DevelopmentNode.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/EmulatorNotInstalled.png
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
