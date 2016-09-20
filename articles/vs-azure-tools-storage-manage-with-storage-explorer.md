<properties
    pageTitle="Komma igång med Lagringsutforskaren (förhandsversion) | Microsoft Azure"
    description="Hantera Azure-lagringsresurser med Lagringsutforskaren (förhandsversion)"
    services="storage"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor="" />

 <tags
    ms.service="storage"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/17/2016"
    ms.author="tarcher" />

# Komma igång med Lagringsutforskaren (förhandsversion)

## Översikt 

Microsoft Azure Lagringsutforskaren (förhandsversion) är en fristående app som gör det enkelt att arbeta med Azure Storage-data på Windows, OS X och Linux. I den här artikeln lär du dig hur du kan ansluta till och hantera dina Azure Storage-konton på olika sätt.

![Microsoft Azure Lagringsutforskaren (förhandsversion)][15]

## Krav

- [Hämta och installera Lagringsutforskaren (förhandsversion)](http://www.storageexplorer.com)

## Ansluta till ett lagringskonto eller en tjänst

Med Lagringsutforskaren (förhandsversion) kan du ansluta till lagringskonton på många olika sätt. Du kan till exempel ansluta till lagringskonton som är kopplade till dina Azure-prenumerationer, ansluta till lagringskonton och tjänster som delas från andra Azure-prenumerationer och även ansluta till och hantera lokal lagring med hjälp av Azure Storage-emulatorn:

- [Ansluta till en Azure-prenumeration](#connect-to-an-azure-subscription) – Hantera lagringsresurser som hör till din Azure-prenumeration.
- [Arbeta med lokal utvecklingslagring](#work-with-local-development-storage) – Hantera lokal lagring med hjälp av Azure Storage-emulatorn. 
- [Ansluta till extern lagring](#attach-or-detach-an-external-storage-account) – Hantera lagringsresurser som hör till en annan Azure-prenumeration med hjälp av lagringskontots kontonamn och nyckel.
- [Ansluta ett lagringskonto med hjälp av SAS](#attach-storage-account-using-sas) – Hantera lagringsresurser som hör till en annan Azure-prenumeration med hjälp av en SAS.
- [Ansluta en tjänst med hjälp av SAS](#attach-service-using-sas) – Hantera en specifik lagringstjänst (blobbehållare, kö eller tabell) som hör till en annan Azure-prenumeration med hjälp av en SAS.

## Ansluta till en Azure-prenumeration

> [AZURE.NOTE] Om du inte redan har ett Azure-konto kan du [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

1. I Lagringsutforskaren (förhandsversion) väljer du **Inställningar för Azure-konto**. 

    ![Inställningar för Azure-konto][0]

1. Den vänstra rutan visar nu alla Microsoft-konton som du har loggat in på. Om du vill ansluta till ett annat konto väljer du **Lägg till ett konto** och följer dialogrutorna för att logga in med ett Microsoft-konto som är kopplat till minst en aktiv Azure-prenumeration.

    ![Lägga till ett konto][1]

1. När du har loggat in med ett Microsoft-konto fylls den vänstra rutan i med de Azure-prenumerationer som är kopplade till det kontot. Välj de Azure-prenumerationer som du vill arbeta med och välj sedan **Använd**. (Om du klickar på **Alla prenumerationer** växlar du mellan att välja alla eller inga av de Azure-prenumerationer som visas.)

    ![Välja Azure-prenumerationer][3]

1. I den vänstra rutan visas nu de lagringskonton som är kopplade till de valda Azure-prenumerationerna.

    ![Valda Azure-prenumerationer][4]

## Arbeta med lokal utvecklingslagring

Med Lagringsutforskaren (förhandsversion) kan du arbeta mot lokal lagring med hjälp av Azure Storage-emulatorn. På så sätt kan du skriva kod mot och testa lagring utan att nödvändigtvis ha distribuerat ett lagringskonto i Azure (eftersom lagringskontot emuleras av Azure Storage-emulatorn).

>[AZURE.NOTE] Azure Storage-emulatorn stöds för närvarande endast för Windows. 

1. I den vänstra rutan i Lagringsutforskaren (förhandsversion) utökar du noden **(Lokala och anslutna)** > **Lagringskonton** > **(Utveckling)**.

    ![Noden Lokal utveckling][21]

1. Om du inte har installerat Azure Storage-emulatorn än uppmanas du att göra det via ett informationsfält. Om informationsfältet visas väljer du **Hämta den senaste versionen** och installerar emulatorn. 

    ![Fråga om att hämta Azure Storage-emulatorn][22]

1. När emulatorn har installerats kan du välja att skapa och arbeta med lokala blobbar, köer och tabeller. Om du vill veta hur du arbetar med respektive lagringskontotyp väljer du lämplig länk nedan:

    - [Hantera Azure-blobblagringsresurser](./vs-azure-tools-storage-explorer-blobs.md)
    - Hantera Azure-filresurslagringsresurser – *kommer snart*
    - Hantera Azure-kölagringsresurser – *kommer snart*
    - Hantera Azure-tabellagringsresurser – *kommer snart*

## Ansluta eller koppla från ett externt lagringskonto

Lagringsutforskaren (förhandsversion) ger dig möjlighet att ansluta till externa lagringskonton så att du enkelt kan dela lagringskonton. Det här avsnittet beskriver hur du ansluter till (och kopplar från) externa lagringskonton.

### Hämta autentiseringsuppgifterna för lagringskontot

För att kunna dela ett externt lagringskonto måste kontoägaren först hämta autentiseringsuppgifterna – kontonamnet och nyckeln – för kontot och sedan dela den informationen med den person som ska ansluta till det (externa) kontot. Du kan hämta autentiseringsuppgifterna för ett lagringskonto via Azure Portal genom att följa dessa steg: 

1.  Logga in på [Azure Portal](https://portal.azure.com).
1.  Välj **Bläddra**.
1.  Välj **Lagringskonton**.
1.  Välj önskat lagringskonto i bladet **Lagringskonton**.
1.  Välj **Åtkomstnycklar** i bladet **Inställningar** för det valda lagringskontot.

    ![Alternativ för åtkomstnycklar][5]
    
1.  I bladet **Åtkomstnycklar** kopierar du värdena för **LAGRINGSKONTONAMN** och **NYCKEL 1** som används för att ansluta till lagringskontot. 

    ![Åtkomstnycklar][6]

### Ansluta till ett externt lagringskonto
Om du vill ansluta till ett externt lagringskonto behöver du ha tillgång till kontots namn och nyckel. Avsnittet *Hämta autentiseringsuppgifter för lagringskonto* förklarar hur du hämtar dessa värden från Azure Portal. Observera dock att på portalen kallas kontonyckeln för "nyckel 1", så när Lagringsutforskaren (förhandsversion) begär en kontonyckel ska du ange (eller klistra in) värdet "nyckel 1". 
 
1.  I Lagringsutforskaren (förhandsversion) väljer du **Anslut till Azure Storage**.

    ![Alternativet Anslut till Azure Storage][23]

1.  I dialogrutan **Anslut till Azure Storage** anger du kontonyckeln ("nyckel 1"-värdet från Azure Portal) och väljer sedan **Nästa**.

    ![Dialogrutan Anslut till Azure Storage][24] 

1.  I dialogrutan **Anslut extern lagring** anger du lagringskontots namn i rutan **Kontonamn**, anger eventuella andra önskade inställningar och väljer sedan **Nästa** när du är klar. 

    ![Dialogrutan Anslut extern lagring][8]

1.  Kontrollera uppgifterna i dialogrutan **Anslutningssammanfattning**. Om du vill ändra något väljer du **Tillbaka** och anger sedan önskade inställningar på nytt. När du är klar väljer du **Anslut**.

1.  När det externa lagringskontot har anslutits visas det med texten **(Externt)** sist i namnet. 

    ![Resultatet av att ansluta till ett externt lagringskonto][9]

### Ansluta från ett externt lagringskonto

1.  Högerklicka på det externa lagringskonto som du vill koppla från och välj **Koppla från** på snabbmenyn.

    ![Koppla bort lagringsalternativ][10]

1.  När rutan med bekräftelsemeddelandet visas väljer du **Ja** för att bekräfta att du vill koppla bort det externa lagringskontot.

## Ansluta ett lagringskonto med hjälp av SAS

En [SAS (signatur för delad åtkomst)](storage/storage-dotnet-shared-access-signature-part-1.md) gör det möjligt för administratören av en Azure-prenumeration att bevilja tillfällig åtkomst till ett lagringskonto utan att behöva uppge sina autentiseringsuppgifter för Azure-prenumerationen. 

För att illustrera detta antar vi att Användare A som är administratör för en Azure-prenumeration vill ge Användare B åtkomst till ett lagringskonto under en begränsad tid med särskilda behörigheter:

1. Användare A genererar en SAS (som består av anslutningssträngen för lagringskontot) för en viss tidsperiod och med önskade behörigheter.
1. Användare A delar SAS med den person som vill ha åtkomst till lagringskontot, Användare B i vårt exempel.  
1. Användare B använder Lagringsutforskaren (förhandsversion) för att ansluta till kontot som hör till Användare A med hjälp av den SAS som han eller hon fått. 

### Hämta en SAS för det konto som du vill dela

1.  I Lagringshanteraren (förhandsversion) högerklickar du på det lagringskonto som du vill dela och väljer **Hämta signatur för delad åtkomst** i snabbmenyn.

    ![Snabbmenyalternativet Hämta SAS][13]

1. I dialogrutan **Signatur för delad åtkomst** anger du det tidsintervall och de behörigheter som du vill använda för kontot och väljer sedan **Skapa**.

    ![Dialogrutan Hämta SAS][14]
 
1. En andra dialogruta av typen **Signatur för delad åtkomst** öppnas och visar signaturen för delad åtkomst. Välj **Kopiera** bredvid **anslutningssträngen** för att kopiera den till Urklipp. Stäng dialogrutan genom att välja **Stäng**.

### Ansluta till det delade kontot med hjälp av SAS

1.  I Lagringsutforskaren (förhandsversion) väljer du **Anslut till Azure Storage**.

    ![Alternativet Anslut till Azure Storage][23]

1.  I dialogrutan **Anslut till Azure Storage** anger du anslutningssträngen och väljer sedan **Nästa**.

    ![Dialogrutan Anslut till Azure Storage][24] 

1.  Kontrollera uppgifterna i dialogrutan **Anslutningssammanfattning**. Om du vill ändra något väljer du **Tillbaka** och anger sedan önskade inställningar på nytt. När du är klar väljer du **Anslut**.

1.  När lagringskontot har anslutits visas det med texten (SAS) sist i kontonamnet som du angav.

    ![Resultatet av anslutning till ett konto med SAS][17]

## Ansluta en tjänst med hjälp av SAS

Avsnittet [Ansluta ett lagringskonto med hjälp av SAS](#attach-storage-account-using-sas) illustrerar hur administratören av en Azure-prenumeration kan bevilja tillfällig åtkomst till ett lagringskonto genom att generera (och dela) en SAS för lagringskontot. På samma sätt kan en SAS genereras för en specifik tjänst (blobbehållare, kö eller tabell) i ett lagringskonto.  

### Generera en SAS för den tjänst som du vill dela

I det här scenariot kan en tjänst vara en blobbehållare, en kö eller en tabell. Följande avsnitt beskriver hur du skapar en SAS för den angivna tjänsten:

- [Hämta SAS för en blobbbehållare](./vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
- Hämta SAS för en filresurs – *kommer snart*
- Hämta SAS för en kö – *kommer snart*
- Hämta SAS för en tabell – *kommer snart*

### Ansluta till tjänsten för det delade kontot med hjälp av SAS

1.  I Lagringsutforskaren (förhandsversion) väljer du **Anslut till Azure Storage**.

    ![Alternativet Anslut till Azure Storage][23]

1.  I dialogrutan **Anslut till Azure Storage** anger du SAS-URI och väljer sedan **Nästa**.

    ![Dialogrutan Anslut till Azure Storage][24] 

1.  Kontrollera uppgifterna i dialogrutan **Anslutningssammanfattning**. Om du vill ändra något väljer du **Tillbaka** och anger sedan önskade inställningar på nytt. När du är klar väljer du **Anslut**.

1.  När tjänsten har anslutits visas den under noden **(SAS för tjänst)**.

    ![Resultatet av att ansluta till en delad tjänst med hjälp av SAS][20]

## Söka efter lagringskonton

Om du har en lång lista med lagringskonton kan du snabbt hitta ett visst lagringskonto genom att använda sökrutan överst i den vänstra rutan. 

När du skriver i sökrutan visas endast de lagringskonton som matchar sökvärdet som du har skrivit hittills i den vänstra rutan. Följande skärmbild visas ett exempel där jag har sökt efter alla lagringskonton där lagringskontonamnet innehåller texten ”tarcher”.

![Lagringskontosökning][11]
    
Om du vill rensa sökningen väljer du knappen **x** i sökrutan.

## Nästa steg
- [Hantera Azure-blobblagringsresurser med Lagringsutforskaren (förhandsversion)](./vs-azure-tools-storage-explorer-blobs.md)

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



<!--HONumber=sep16_HO1-->


