<properties
    pageTitle="Komma igång med Lagringsutforskaren (förhandsversion) | Microsoft Azure"
    description="Hantera Azure-lagringsresurser med Lagringsutforskaren (förhandsversion)"
    services="visual-studio-online"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor="" />

 <tags
    ms.service="visual-studio-online"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="06/05/2016"
    ms.author="tarcher" />

# Komma igång med Lagringsutforskaren (förhandsversion)

## Översikt 

Microsoft Azure Lagringsutforskaren (förhandsversion) är en fristående app som gör det enkelt att arbeta med Azure Storage-data i Windows, OSX och Linux. I den här artikeln lär du dig hur du kan ansluta till och hantera dina Azure Storage-konton på olika sätt.

## Krav

- [Hämta och installera Lagringsutforskaren (förhandsversion)](http://go.microsoft.com/fwlink/?LinkId=708343)

## Ansluta till ett lagringskonto eller en tjänst

Med Lagringsutforskaren (förhandsversion) kan du ansluta till lagringskonton på många olika sätt. Du kan till exempel ansluta till lagringskonton som är kopplade till dina Azure-prenumerationer, ansluta till lagringskonton och tjänster som delas från andra Azure-prenumerationer och även ansluta till och hantera lokal lagring med hjälp av Azure Storage-emulatorn:

- [Ansluta till en Azure-prenumeration](#connect-to-an-azure-subscription) – Hantera lagringsresurser som hör till din Azure-prenumeration.
- [Ansluta till lokal lagring](#connect-to-local-storage) – Hantera lokal lagring med hjälp av Azure Storage-emulatorn. 
- [Ansluta till extern lagring](#attach-or-detach-an-external-storage-account) – Hantera lagringsresurser som hör till en annan Azure-prenumeration med hjälp av lagringskontots kontonamn och nyckel.
- [Ansluta ett konto med hjälp av SAS](#attach-account-using-sas) – Hantera lagringsresurser som hör till en annan Azure-prenumeration med hjälp av en SAS.
- [Ansluta en tjänst med hjälp av SAS](#attach-service-using-sas) – Hantera en specifik lagringstjänst (blobbehållare, kö eller tabell) som hör till en annan Azure-prenumeration med hjälp av en SAS.

## Ansluta till en Azure-prenumeration

> [AZURE.NOTE] Om du inte redan har ett Azure-konto kan du [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

1. Starta Lagringsutforskaren (förhandsversion). 

1. Om du kör Lagringsutforskaren (förhandsversion) för första gången eller om du har kört Lagringsutforskaren (förhandsversion) tidigare men inte har anslutit till ett Azure-konto visas ett informationsfält som ger dig möjlighet att ansluta till ett Azure-konto.

    ![][0]
    
1. Välj **Anslut till Microsoft Azure** och följ dialogrutorna för att logga in med ett Microsoft-konto som är kopplat till minst en aktiv Azure-prenumeration.

När du loggar in med ett Microsoft-konto fylls den vänstra rutan i Lagringsutforskaren (förhandsversion) med alla lagringskonton som är kopplade till alla Azure-prenumerationer som är associerade med Microsoft-kontot.

### Filtrera Azure-prenumerationerna

I Lagringsutforskaren (förhandsversion) kan du filtrera fram de Azure-prenumerationer som är kopplade till dina inloggade Microsoft-konton vars lagringskonton ska visas i den vänstra rutan.

1. Välj ikonen för **Inställningar** (kugghjulet).

    ![][1]   

1.  Längst upp i det vänstra fönstret visas en listruta som innehåller alla Microsoft-konton som du är inloggad på. 

    ![][3]
     
1.  Välj nedpilen bredvid listrutan så visas alla Microsoft-konton som du är inloggad på samt en länk för att lägga till (logga in på) ytterligare Microsoft-konton.

    ![][4]

1.  Välj önskat Microsoft-konto i listrutan.

1.  I den vänstra rutan visas alla Azure-prenumerationer som är kopplade till det valda Microsoft-kontot.
Du kan använda kryssrutan till vänster om varje Azure-prenumeration för att ange om du vill att Lagringsutforskaren (förhandsversion) ska visa alla lagringskonton som är kopplade till respektive Azure-prenumeration eller inte. Du kan växla mellan att välja alla eller inga av Azure-prenumerationerna som visas genom att markera eller avmarkera **Alla prenumerationer**.

    ![][2]  

1.  När du har valt de Azure-prenumerationer som du vill hantera väljer du **Tillämpa**. Det vänstra fönstret uppdateras och visar en lista med alla lagringskonton för varje vald Azure-prenumeration för det aktuella Microsoft-kontot. 

### Lägga till ytterligare Microsoft-konton

Följande steg beskriver hur du ansluter till ytterligare Microsoft-konton för att visa respektive kontos Azure-prenumeration(er) och lagringskonton.

1.  Välj ikonen för **Inställningar** (kugghjulet).

    ![][1]   

1.  Längst upp i det vänstra fönstret ser du en listruta som innehåller alla anslutna Microsoft-konton.

    ![][3]
     
1.  Välj nedpilen bredvid listrutan så visas alla Microsoft-konton som du är inloggad på samt en länk för att lägga till (logga in på) ytterligare Microsoft-konton.

    ![][4]

1.  Välj **Lägg till ett konto** och följ dialogrutorna för att logga in på ett konto som associeras med minst en aktiv Azure-prenumeration.

1.  Markera kryssrutorna för de Azure-prenumerationer som du vill bläddra igenom. 

    ![][2]  

1.  Välj **Använd**.

### Växla mellan Microsoft-konton

Du kan ansluta till flera Microsoft-konton, men i det vänstra fönstret visas endast de lagringskonton som är associerade med prenumerationerna för ett enskilt (det aktuella) Microsoft-konto. Om du ansluter till flera Microsoft-konton kan du växla mellan kontona genom att utföra följande steg:

1.  Välj ikonen för **Inställningar** (kugghjulet).

    ![][1]   

1.  Längst upp i det vänstra fönstret ser du en listruta som innehåller alla anslutna Microsoft-konton.

    ![][3]
     
1.  Välj nedpilen bredvid listrutan så visas alla Microsoft-konton som du är inloggad på samt en länk för att lägga till (logga in på) ytterligare Microsoft-konton.

    ![][4]

1.  Välj önskat Microsoft-konto.

1.  Markera kryssrutorna för de Azure-prenumerationer som du vill bläddra igenom. 

    ![][2]  

1.  Välj **Använd**.
  
## Ansluta till lokal lagring

Med Lagringsutforskaren (förhandsversion) kan du arbeta mot lokal lagring med hjälp av Azure Storage-emulatorn. På så sätt kan du skriva kod mot och testa lagring utan att nödvändigtvis ha distribuerat ett lagringskonto i Azure (eftersom lagringskontot emuleras av Azure Storage-emulatorn).

>[AZURE.NOTE] Azure Storage-emulatorn stöds för närvarande endast för Windows. 

1. Starta Lagringsutforskaren (förhandsversion). 

1. Expandera noden **(Utveckling)** i den vänstra rutan.

    ![][21]

1. Om du inte har installerat Azure Storage-emulatorn än uppmanas du att göra det via ett informationsfält. Om informationsfältet visas väljer du **Hämta den senaste versionen** och installerar emulatorn. 

    ![][22]

1. När emulatorn har installerats kan du välja att skapa och arbeta med lokala blobbar, köer och tabeller. Om du vill veta hur du arbetar med respektive lagringskontotyp väljer du lämplig länk nedan:

    - [Hantera Azure-blobblagringsresurser](./vs-azure-tools-storage-explorer-blobs.md)
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

    ![][5]
    
1.  I bladet **Åtkomstnycklar** kopierar du värdena för **LAGRINGSKONTONAMN** och **NYCKEL 1** som används för att ansluta till lagringskontot. 

    ![][6]

### Ansluta till ett externt lagringskonto

1.  I Lagringsutforskaren (förhandsversion) högerklickar du på **Lagringskonton** och väljer **Anslut extern lagring** på snabbmenyn.

    ![][7]
    
1.  Avsnittet *Hämta autentiseringsuppgifter för lagringskonto* förklarar hur du hämtar värdena för lagringskontots namn och nyckel 1. Dessa värden används i det här steget. I dialogrutan **Anslut extern lagring** anger du lagringskontots namn i rutan **Kontonamn** och värdet för Nyckel 1 i rutan **Kontonyckel**. Välj **OK** när du är klar. 

    ![][8]

    När det externa lagringskontot har anslutits visas det med texten **(Externt)** sist i namnet. 

    ![][9]

### Ansluta från ett externt lagringskonto

1.  Högerklicka på det externa lagringskonto som du vill koppla från och välj **Koppla från** på snabbmenyn.

    ![][10]

1.  När rutan med bekräftelsemeddelandet visas väljer du **Ja** för att bekräfta att du vill koppla bort det externa lagringskontot.

    ![][12]

## Ansluta ett konto med hjälp av SAS

En SAS (signatur för delad åtkomst) gör det möjligt för administratören av en Azure-prenumeration att bevilja tillfällig åtkomst till ett lagringskonto utan att behöva uppge sina autentiseringsuppgifter för Azure-prenumerationen. 

För att illustrera detta antar vi att Användare A som är administratör för en Azure-prenumeration vill ge Användare B åtkomst till ett lagringskonto under en begränsad tid med särskilda behörigheter:

1. Användare A genererar en SAS (som består av anslutningssträngen för lagringskontot) för en viss tidsperiod och med önskade behörigheter.
1. Användare A delar SAS med den person som vill ha åtkomst till lagringskontot, Användare B i vårt exempel.  
1. Användare B använder Lagringsutforskaren (förhandsversion) för att ansluta till kontot som hör till Användare A med hjälp av den SAS som han eller hon fått. 

### Hämta en SAS för det konto som du vill dela

1.  Öppna Lagringsutforskaren (förhandsversion).
1.  I det vänstra fönstret högerklickar du på det lagringskontot som du vill dela och väljer **Hämta signatur för delad åtkomst** på snabbmenyn.

    ![][13]

1. I dialogrutan **Signatur för delad åtkomst** anger du det tidsintervall och de behörigheter som du vill använda för kontot och väljer sedan **Skapa**.

    ![][14]
 
1. En andra dialogruta av typen **Signatur för delad åtkomst** öppnas och visar signaturen för delad åtkomst. Välj **Kopiera** bredvid **anslutningssträngen** för att kopiera den till Urklipp. Stäng dialogrutan genom att välja **Stäng**.

### Ansluta till det delade kontot med hjälp av SAS

1.  Öppna Lagringsutforskaren (förhandsversion).
1.  I den vänstra rutan högerklickar du på **Lagringskonton** och väljer sedan **Anslut konto med hjälp av SAS** på snabbmenyn.
    ![][15]

1. I dialogrutan **Anslut konto med hjälp av SAS**:

    - **Kontonamn** – Ange det namn som du vill associera med det här kontot. **Obs!** Kontonamnet måste inte matcha det ursprungliga lagringskontonamnet som SAS genererades för. 
    - **Anslutningssträng** – Klistra in anslutningssträngen som du kopierade tidigare.
    - Välj **OK** när du är klar.
    
    ![][16]

När lagringskontot har anslutits visas det med texten (SAS) sist i kontonamnet som du angav.

![][17]

## Ansluta en tjänst med hjälp av SAS

Avsnittet [Ansluta konto med hjälp av SAS](#attach-account-using-sas) illustrerar hur administratören av en Azure-prenumeration kan bevilja tillfällig åtkomst till ett lagringskonto genom att generera (och dela) en SAS för lagringskontot. På samma sätt kan en SAS genereras för en specifik tjänst (blobbehållare, kö eller tabell) i ett lagringskonto.  

### Generera en SAS för den tjänst som du vill dela

I det här scenariot kan en tjänst vara en blobbehållare, en kö eller en tabell. Följande avsnitt beskriver hur du skapar en SAS för den angivna tjänsten:

- [Hämta SAS för en blobbbehållare](./vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
- Hämta SAS för en kö – *kommer snart*
- Hämta SAS för en tabell – *kommer snart*

### Ansluta till tjänsten för det delade kontot med hjälp av SAS

1.  Öppna Lagringsutforskaren (förhandsversion).
1.  I den vänstra rutan högerklickar du på **Lagringskonton** och väljer sedan **Anslut tjänst med hjälp av SAS** på snabbmenyn.
    ![][18]

1. I dialogrutan **Anslut konto med hjälp av SAS** klistrar du in SAS-URI:n som du kopierade tidigare och väljer **OK**.

    ![][19]

När tjänsten har anslutits visas den under noden **(SAS för tjänst)**. 

![][20]

## Söka efter lagringskonton

Om du har en lång lista med lagringskonton kan du snabbt hitta ett visst lagringskonto genom att använda sökrutan överst i den vänstra rutan. 

När du skriver i sökrutan visas endast de lagringskonton som matchar sökvärdet som du har skrivit hittills i den vänstra rutan. Följande skärmbild visas ett exempel där jag har sökt efter alla lagringskonton där lagringskontonamnet innehåller texten ”tarcher”.

![][11]
    
Om du vill rensa sökningen väljer du knappen **x** i sökrutan.

## Nästa steg
- [Hantera Azure-blobblagringsresurser med Lagringsutforskaren (förhandsversion)](./vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-gear.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/filter-subscriptions.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/filter-accounts.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/accounts-drop-down.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-context-menu.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-dlg.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-context-menu.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-dlg.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png



<!--HONumber=Jun16_HO2-->


