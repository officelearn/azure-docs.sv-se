---
title: "Komma igång med Lagringsutforskaren (förhandsversion) | Microsoft Docs"
description: "Hantera Azure-lagringsresurser med Lagringsutforskaren (förhandsversion)"
services: storage
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 07b62cd6f6deb0cf3ff1c806204ebc26c773a164
ms.lasthandoff: 04/13/2017


---
# <a name="getting-started-with-storage-explorer-preview"></a>Komma igång med Lagringsutforskaren (förhandsversion)
## <a name="overview"></a>Översikt
Microsoft Azure Storage Explorer (förhandsversion) är en fristående app som gör det enkelt att arbeta med Azure Storage-data i Windows, Mac OS och Linux. I den här artikeln lär du dig hur du kan ansluta till och hantera dina Azure Storage-konton på olika sätt.

![Microsoft Azure Lagringsutforskaren (förhandsversion)][15]

## <a name="prerequisites"></a>Krav
* [Hämta och installera Lagringsutforskaren (förhandsversion)](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Ansluta till ett lagringskonto eller en tjänst
Med Storage Explorer (förhandsversion) kan du ansluta till lagringskonton på flera olika sätt. Du kan t.ex. ansluta till lagringskonton som är kopplade till dina Azure-prenumerationer, ansluta till lagringskonton och tjänster som delas från andra Azure-prenumerationer och även ansluta till och hantera lokal lagring med hjälp av Azure Storage-emulatorn. Dessutom kan du arbeta med lagringskonton i globala och nationella Azure:

* [Ansluta till en Azure-prenumeration](#connect-to-an-azure-subscription) – Hantera lagringsresurser som hör till din Azure-prenumeration.
* [Arbeta med lokal utvecklingslagring](#work-with-local-development-storage) – Hantera lokal lagring med hjälp av Azure Storage-emulatorn.
* [Ansluta till extern lagring](#attach-or-detach-an-external-storage-account) – Hantera lagringsresurser som hör till en annan Azure-prenumeration eller nationella Azure-moln med hjälp av lagringskontots namn, nyckel och slutpunkter.
* [Ansluta ett lagringskonto med hjälp av SAS](#attach-storage-account-using-sas) – Hantera lagringsresurser som hör till en annan Azure-prenumeration med hjälp av en SAS.
* [Ansluta en tjänst med hjälp av SAS](#attach-service-using-sas) – Hantera en specifik lagringstjänst (blobbehållare, kö eller tabell) som hör till en annan Azure-prenumeration med hjälp av en SAS.

## <a name="connect-to-an-azure-subscription"></a>Ansluta till en Azure-prenumeration
> [!NOTE]
> Om du inte redan har ett Azure-konto kan du [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
>
>

1. I Lagringsutforskaren (förhandsversion) väljer du **Inställningar för Azure-konto**.

    ![Inställningar för Azure-konto][0]
2. Den vänstra rutan visar alla Microsoft-konton som du har loggat in på. Om du vill ansluta till ett annat konto väljer du **Lägg till ett konto** och följer dialogrutorna för att logga in med ett Microsoft-konto som är kopplat till minst en aktiv Azure-prenumeration.
> [!NOTE]
>För närvarande går det inte att ansluta till nationella Azure, t.ex. Black Forest Azure, Fairfax Azure eller Mooncake Azure, via inloggning. Mer information om hur du ansluter till nationella Azure Storage-konton finns i avsnittet **Koppla eller koppla från ett externt lagringskonto**.

3. När du har loggat in med ett Microsoft-konto fylls den vänstra rutan i med de Azure-prenumerationer som är kopplade till kontot. Välj de Azure-prenumerationer som du vill arbeta med och välj sedan **Använd**. (Om du klickar på **Alla prenumerationer** växlar du mellan att välja alla eller inga av de Azure-prenumerationer som visas.)

    ![Välja Azure-prenumerationer][3]
4. I den vänstra rutan visas lagringskontona som är kopplade till de valda Azure-prenumerationerna.

    ![Valda Azure-prenumerationer][4]

## <a name="connect-to-an-azure-stack-subscription"></a>Ansluta till en Azure Stack-prenumeration

1. En VPN-anslutning krävs så att Lagringsutforskaren kan komma åt Azure Stack-prenumerationen via fjärranslutning. Mer information om hur du konfigurerar en VPN-anslutning till Azure Stack finns i [Connect to Azure Stack with VPN](azure-stack/azure-stack-connect-azure-stack.md#connect-with-vpn) (Anslut till Azure Stack via VPN).

2. För Azure Stack POC måste du exportera rotcertifikatet för Azure Stack-behörighet. Öppna `mmc.exe` på MAS-CON01, värddatorn för Azure Stack eller en lokal dator med VPN-anslutning till Azure Stack. Under **Arkiv** väljer du **Lägg till/ta bort snapin-modul**. Lägg till **Certifikat** för hantering av **datorkontot** **Lokal dator**.

   ![Läsa in rotcertifikatet för Azure Stack via mmc.exe][25]   

   Leta rätt på **AzureStackCertificationAuthority** under **[konsolrot]\Certificated (Local Computer)\Trusted Root Certification Authorities\Certificates**. Högerklicka på objektet och välj **Alla uppgifter -> Exportera**. Följ sedan dialogrutorna och exportera certifikatet med **Base 64-kodad X.509 (.CER)**. Det exporterade certifikatet ska användas i nästa steg.   

   ![Exportera rotcertifikatet för Azure Stack-behörighet][26]   

3. I Lagringsutforskaren (förhandsversion) väljer du menyn **Redigera**. Välj sedan **SSL-certifikat** och **Importera certifikat**. Använd filväljaren till att leta rätt på och öppna certifikatet du exporterade i föregående steg. Efter importen uppmanas du att starta om Lagringsutforskaren.

   ![Importera certifikatet till Lagringsutforskaren (förhandsversion)][27]

4. När Lagringsutforskaren (förhandsversion) startats om väljer du menyn **Redigera** och ser till att **Target Azure Stack** (Använd Azure Stack som mål) är markerat. Om inte så markerar du alternativet och startar om Lagringsutforskaren så att ändringen börjar gälla. Denna konfiguration krävs för kompatibilitet med Azure Stack-miljön.

   ![Se till att Target Azure Stack (Använd Azure Stack som mål) är markerat][28]

5. Välj **Hantera konton** i den vänstra listen. Du ser alla Microsoft-konton som du har loggat in på i den vänstra rutan. Om du vill ansluta till Azure Stack-kontot väljer du **Lägg till ett konto**.

   ![Lägga till ett Azure Stack-konto][29]

6. Välj **Create Custom Environment** (Skapa anpassad miljö) under **Azure-miljö** i dialogrutan **Lägg till nytt konto** och klicka på **Nästa**.

7. Ange all nödvändig information om den anpassade Azure Stack-miljön och klicka på **Logga in**.  Fyll i dialogrutan **Sign in to a Custom Cloud environment** (Logga in i en anpassad molnmiljö) och logga in med ett Azure Stack-konto som är associerat med minst en aktiv Stack Azure-prenumeration. Här är information om de olika fälten i dialogrutan:

    * **Miljönamn** – fältet kan anpassas av användaren.
    * **Myndighet** – värdet ska vara https://login.windows.net. Använd https://login.chinacloudapi.cn för Azure Kina (Mooncake).
    * **Sign in resource id** (Id för inloggningsresurs) – hämta värdet genom att köra följande PowerShell:

    Om du är administratör för molnet:

    ```powershell
    PowerShell (Invoke-RestMethod -Uri https://adminmanagement.local.azurestack.external/metadata/endpoints?api-version=1.0 -Method Get).authentication.audiences[0]
    ```

    Om du är en klient:

    ```powershell
    PowerShell (Invoke-RestMethod -Uri https://management.local.azurestack.external/metadata/endpoints?api-version=1.0 -Method Get).authentication.audiences[0]
    ```

    * **Graph endpoint** (Graph-slutpunkt) – värdet ska vara https://graph.windows.net. Använd https://graph.chinacloudapi.cn för Azure Kina (Mooncake).
    * **ARM resource id** (Id för ARM-resurs) – använd samma värde som id:t för inloggningsresursen.
    * **ARM resurs endpoint** (Slutpunkt för ARM-resurs) – exempel på slutpunkter för ARM-resursen:

    För molnadministratörer: https://adminmanagement.local.azurestack.external   
    For klienter: https://management.local.azurestack.external
 
    * **Tenant Ids** (Klient-id:n) – valfritt. Värdet anges bara när du måste ange katalogen.

8. När du har loggat in med ett Azure Stack-konto fylls den vänstra rutan i med de Azure Stack-prenumerationer som är kopplade till kontot. Välj de Azure Stack-prenumerationer du vill arbeta med och välj sedan **Använd**. (Om du klickar på **Alla prenumerationer** växlar du mellan att välja alla eller inga av de Azure Stack-prenumerationer som visas.)

   ![Välj Azure Stack-prenumerationer när du har fyllt i dialogrutan Custom Cloud Environment (Anpassad molnmiljö)][30]

9. I den vänstra rutan visas de lagringskonton som är kopplade till de valda Azure Stack-prenumerationerna.

   ![Lista med lagringskonton inklusive konton för Azure Stack-prenumerationer][31]

## <a name="work-with-local-development-storage"></a>Arbeta med lokal utvecklingslagring
Med Lagringsutforskaren (förhandsversion) kan du arbeta mot lokal lagring med hjälp av Azure Storage-emulatorn. På så sätt kan du skriva kod mot och testa lagring utan att nödvändigtvis ha distribuerat ett lagringskonto i Azure (eftersom lagringskontot emuleras av Azure Storage-emulatorn).

> [!NOTE]
> Azure Storage-emulatorn stöds för närvarande endast för Windows.
>
>

1. I den vänstra rutan i Lagringsutforskaren (förhandsversion) utökar du noden **(Lokala och anslutna)** > **Lagringskonton** > **(Utveckling)**.

    ![Noden Lokal utveckling][21]
2. Om du inte har installerat Azure Storage-emulatorn än uppmanas du att göra det via ett informationsfält. Om informationsfältet visas väljer du **Hämta den senaste versionen** och installerar emulatorn.

    ![Fråga om att hämta Azure Storage-emulatorn][22]
3. När emulatorn har installerats kan du välja att skapa och arbeta med lokala blobbar, köer och tabeller. Välj någon av följande länkar om du vill lära dig mer om hur du arbetar med varje typ av lagringskonto:

   * [Hantera Azure-bloblagringsresurser](vs-azure-tools-storage-explorer-blobs.md)
   * Hantera Azure-filresurslagringsresurser – *kommer snart*
   * Hantera Azure-kölagringsresurser – *kommer snart*
   * Hantera Azure-tabellagringsresurser – *kommer snart*

## <a name="attach-or-detach-an-external-storage-account"></a>Ansluta eller koppla från ett externt lagringskonto
Lagringsutforskaren (förhandsversion) ger dig möjlighet att ansluta till externa lagringskonton så att du enkelt kan dela lagringskonton. Det här avsnittet beskriver hur du ansluter till (och kopplar från) externa lagringskonton.

### <a name="get-the-storage-account-credentials"></a>Hämta autentiseringsuppgifterna för lagringskontot
För att kunna dela ett externt lagringskonto måste kontoägaren först hämta autentiseringsuppgifterna – kontonamnet och nyckeln – för kontot och sedan dela den informationen med den person som ska ansluta till det (externa) kontot. Du kan hämta autentiseringsuppgifterna för ett lagringskonto via Azure Portal genom att följa dessa steg:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Bläddra**.
3. Välj **Lagringskonton**.
4. Välj önskat lagringskonto i bladet **Lagringskonton**.
5. Välj **Åtkomstnycklar** i bladet **Inställningar** för det valda lagringskontot.

   ![Alternativ för åtkomstnycklar][5]
6. I bladet **Åtkomstnycklar** kopierar du värdena för **LAGRINGSKONTONAMN** och **NYCKEL 1** som används för att ansluta till lagringskontot.

   ![Åtkomstnycklar][6]

### <a name="attach-to-an-external-storage-account"></a>Ansluta till ett externt lagringskonto
Om du vill ansluta till ett externt lagringskonto behöver du ha tillgång till kontots namn och nyckel. Avsnittet *Hämta autentiseringsuppgifter för lagringskonto* förklarar hur du hämtar dessa värden från Azure Portal. Observera dock att på portalen kallas kontonyckeln för "nyckel 1", så när Lagringsutforskaren (förhandsversion) begär en kontonyckel ska du ange (eller klistra in) värdet "nyckel 1".

1. I Lagringsutforskaren (förhandsversion) väljer du **Anslut till Azure Storage**.

   ![Alternativet Anslut till Azure Storage][23]
2. I dialogrutan **Anslut till Azure Storage** anger du kontonyckeln ("nyckel 1"-värdet från Azure Portal) och väljer sedan **Nästa**.
> [!NOTE]
> Du kan ange en anslutningssträng för lagring från ett lagringskonto på nationella Azure. Du kan t.ex. ange anslutningssträngar som liknande de som här följer om du vill ansluta till Azure Black Forest-lagringskonton: DefaultEndpointsProtocol = https; AccountName = cawatest03; AccountKey =<storage_account_key>; EndpointSuffix=core.cloudapi.de; Du kan hämta anslutningssträngen från Azure Portal på det sätt som beskrivs i avsnittet **Hämta autentiseringsuppgifter för lagringskonto**

   ![Dialogrutan Anslut till Azure Storage][24]

3. I dialogrutan **Anslut extern lagring** anger du lagringskontots namn i rutan **Kontonamn**, anger eventuella andra önskade inställningar och väljer sedan **Nästa** när du är klar.

   ![Dialogrutan Anslut extern lagring][8]
4. Kontrollera uppgifterna i dialogrutan **Anslutningssammanfattning**. Om du vill ändra något väljer du **Tillbaka** och anger sedan önskade inställningar på nytt. När du är klar väljer du **Anslut**.
5. När det externa lagringskontot har anslutits visas det med texten **(Externt)** sist i namnet.

   ![Resultatet av att ansluta till ett externt lagringskonto][9]

### <a name="detach-from-an-external-storage-account"></a>Ansluta från ett externt lagringskonto
1. Högerklicka på det externa lagringskonto som du vill koppla från och välj **Koppla från** på snabbmenyn.

   ![Koppla bort lagringsalternativ][10]
2. När rutan med bekräftelsemeddelandet visas väljer du **Ja** för att bekräfta att du vill koppla bort det externa lagringskontot.

## <a name="attach-storage-account-using-sas"></a>Ansluta ett lagringskonto med hjälp av SAS
En [SAS (signatur för delad åtkomst)](storage/storage-dotnet-shared-access-signature-part-1.md) gör det möjligt för administratören av en Azure-prenumeration att bevilja tillfällig åtkomst till ett lagringskonto utan att behöva uppge sina autentiseringsuppgifter för Azure-prenumerationen.

För att illustrera detta antar vi att Användare A som är administratör för en Azure-prenumeration vill ge Användare B åtkomst till ett lagringskonto under en begränsad tid med särskilda behörigheter:

1. Användare A genererar en SAS (som består av anslutningssträngen för lagringskontot) för en viss tidsperiod och med önskade behörigheter.
2. Användare A delar SAS med den person som vill ha åtkomst till lagringskontot, Användare B i vårt exempel.  
3. Användare B använder Lagringsutforskaren (förhandsversion) för att ansluta till kontot som hör till Användare A med hjälp av den SAS som han eller hon fått.

### <a name="get-a-sas-for-the-account-you-want-to-share"></a>Hämta en SAS för det konto som du vill dela
1. I Lagringshanteraren (förhandsversion) högerklickar du på det lagringskonto som du vill dela och väljer **Hämta signatur för delad åtkomst** i snabbmenyn.

   ![Snabbmenyalternativet Hämta SAS][13]
2. I dialogrutan **Signatur för delad åtkomst** anger du det tidsintervall och de behörigheter som du vill använda för kontot och väljer sedan **Skapa**.

    ![Dialogrutan Hämta SAS][14]
3. SAS-signaturen visas i en andra dialogruta av typen **Signatur för delad åtkomst**. Välj **Kopiera** bredvid **anslutningssträngen** för att kopiera den till Urklipp. Stäng dialogrutan genom att välja **Stäng**.

### <a name="attach-to-the-shared-account-using-the-sas"></a>Ansluta till det delade kontot med hjälp av SAS
1. I Lagringsutforskaren (förhandsversion) väljer du **Anslut till Azure Storage**.

   ![Alternativet Anslut till Azure Storage][23]
2. I dialogrutan **Anslut till Azure Storage** anger du anslutningssträngen och väljer sedan **Nästa**.

   ![Dialogrutan Anslut till Azure Storage][24]
3. Kontrollera uppgifterna i dialogrutan **Anslutningssammanfattning**. Om du vill ändra något väljer du **Tillbaka** och anger sedan önskade inställningar på nytt. När du är klar väljer du **Anslut**.
4. När lagringskontot har anslutits visas det med texten (SAS) sist i kontonamnet som du angav.

   ![Resultatet av anslutning till ett konto med SAS][17]

## <a name="attach-service-using-sas"></a>Ansluta en tjänst med hjälp av SAS
Avsnittet [Ansluta ett lagringskonto med hjälp av SAS](#attach-storage-account-using-sas) illustrerar hur administratören av en Azure-prenumeration kan bevilja tillfällig åtkomst till ett lagringskonto genom att generera (och dela) en SAS för lagringskontot. På samma sätt kan en SAS genereras för en specifik tjänst (blobbehållare, kö eller tabell) i ett lagringskonto.  

### <a name="generate-a-sas-for-the-service-you-want-to-share"></a>Generera en SAS för den tjänst som du vill dela
I det här scenariot kan en tjänst vara en blobbehållare, en kö eller en tabell. Följande avsnitt beskriver hur du skapar en SAS för den angivna tjänsten:

* [Hämta SAS för en blobbehållare](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* Hämta SAS för en filresurs – *kommer snart*
* Hämta SAS för en kö – *kommer snart*
* Hämta SAS för en tabell – *kommer snart*

### <a name="attach-to-the-shared-account-service-using-the-sas"></a>Ansluta till tjänsten för det delade kontot med hjälp av SAS
1. I Lagringsutforskaren (förhandsversion) väljer du **Anslut till Azure Storage**.

   ![Alternativet Anslut till Azure Storage][23]
2. I dialogrutan **Anslut till Azure Storage** anger du SAS-URI och väljer sedan **Nästa**.

   ![Dialogrutan Anslut till Azure Storage][24]
3. Kontrollera uppgifterna i dialogrutan **Anslutningssammanfattning**. Om du vill ändra något väljer du **Tillbaka** och anger sedan önskade inställningar på nytt. När du är klar väljer du **Anslut**.
4. När den har kopplats visas den nyligen kopplade tjänsten under noden **(Service SAS)**.

   ![Resultatet av att ansluta till en delad tjänst med hjälp av SAS][20]

## <a name="search-for-storage-accounts"></a>Söka efter lagringskonton
Om du har en lång lista med lagringskonton kan du snabbt hitta ett visst lagringskonto genom att använda sökrutan överst i den vänstra rutan.

När du skriver i sökrutan visas endast de lagringskonton som matchar sökvärdet som du har skrivit hittills i den vänstra rutan. Följande skärmbild visas ett exempel där jag har sökt efter alla lagringskonton där lagringskontonamnet innehåller texten ”tarcher”.

![Lagringskontosökning][11]

Om du vill rensa sökningen väljer du knappen **x** i sökrutan.

## <a name="next-steps"></a>Nästa steg
* [Hantera Azure-blobblagringsresurser med Lagringsutforskaren (förhandsversion)](vs-azure-tools-storage-explorer-blobs.md)

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

