<properties
    pageTitle="Komma igång med Azure Storage på fem minuter | Microsoft Azure"
    description="Kom snabbt igång med blobbar, tabeller och köer i Microsoft Azure med hjälp av snabbstarterna för Azure Storage, Visual Studio och Azure Storage-emulatorn. Kör ditt första Azure Storage-program inom fem minuter."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="dineshm;tamram"/>


# Komma igång med Azure Storage på fem minuter

## Översikt

Det är enkelt att börja utveckla med Azure Storage. I den här självstudiekursen lär du dig hur du snabbt kommer igång och kör ett Azure Storage-program. Du ska använda snabbstartsmallarna som ingår i Azure SDK för .NET. Dessa snabbstarter innehåller färdig kod som demonstrerar några grundläggande programmeringsscenarier för Azure Storage.

Om du vill lära dig mer om Azure Storage innan du dyker in i koden går du till [Nästa steg](#next-steps).

## Krav

Du behöver följande innan du börjar:

1. För att kompilera och bygga programmet behöver du en version av [Visual Studio](https://www.visualstudio.com/) installerad på datorn.

2. Installera den senaste versionen av [Azure SDK för .NET](https://azure.microsoft.com/downloads/). SDK innehåller Azure QuickStart-exempelprojekt, Azure Storage-emulatorn och [Azure Storage-klientbiblioteket för .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

3. Kontrollera att du har [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) installerat på datorn eftersom det krävs för Azure-snabbstartsexempelprojekten som vi ska använda i den här självstudiekursen.

    Om du inte är säker på vilken version av .NET Framework som är installerad på datorn läser du [Så här gör du: Kontrollera vilka .NET Framework-versioner som är installerade](https://msdn.microsoft.com/vstudio/hh925568.aspx). Eller tryck på **Start**-knappen eller Windows-tangenten och skriv **Kontrollpanelen**. Klicka sedan på **Program** > **Program och funktioner** och kontrollera om .NET Framework 4.5 visas i listan med installerade program.

4. Du behöver en Azure-prenumeration och ett Azure Storage-konto.

    - Om du vill skaffa en Azure-prenumeration besöker du sidorna för [en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/), [köpalternativ](https://azure.microsoft.com/pricing/purchase-options/) och [medlemserbjudanden](https://azure.microsoft.com/pricing/member-offers/) (för medlemmar i MSDN, Microsoft Partner Network, BizSpark och andra Microsoft-program).
    - Om du vill skapa ett lagringskonto i Azure går du till [Skapa ett lagringskonto](storage-create-storage-account.md#create-a-storage-account).

## Köra ditt första Azure Storage-program mot Azure Storage i molnet

När du har ett konto kan du skapa ett enkelt Azure Storage-program med hjälp av något av Azure-snabbstartsexempelprojekten i Visual Studio. Den här självstudiekursen fokuserar på exempelprojekten för Azure Storage: **Azure Storage: Blobbar**, **Azure Storage: Filer**, **Azure Storage: Köer** och **Azure Storage: Tabeller**:

1. Starta Visual Studio.
2. Öppna **Arkiv**-menyn och klicka på **Nytt projekt**.
3. I dialogrutan **Nytt projekt** klickar du på **Installerade** > **Mallar** > **Visual C#** > **Moln** > **Snabbstart** > **Datatjänster**.
    a. Välj någon av följande mallar: **Azure Storage: Blobbar**, **Azure Storage: Filer**, **Azure Storage: Köer** eller **Azure Storage: Tabeller**.
    b. Kontrollera att **.NET Framework 4.5** är valt som målramverk.
    - 3.c. Ange ett namn för ditt projekt och skapa den nya Visual Studio-lösningen, som du ser här:

    ![Snabbstarter för Azure][Image1]

Du kanske vill granska källkoden innan du kör programmet. Om du vill granska koden väljer du **Solution Explorer** på **Visa**-menyn i Visual Studio. Dubbelklicka sedan på filen Program.cs.

Kör exempelprogrammet:

1.  I Visual Studio väljer du **Solution Explorer** på **Visa**-menyn. Öppna filen App.config och kommentera ut anslutningssträngen för Azure Storage-emulatorn:

    `<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.  Ta bort kommentarerna för anslutningssträngen för Azure Storage-tjänsten och ange lagringskontots namn och åtkomstnyckeln i filen App.config: `<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

    Om du vill hämta åtkomstnyckeln för lagringskontot läser du [Hantera dina åtkomstnycklar för lagring](storage-create-storage-account.md#manage-your-storage-access-keys).

3.  När du har angett lagringskontots namn och åtkomstnyckel i filen App.config öppnar du **Arkiv**-menyn och klickar på **Spara alla** för att spara alla projektfiler.
4.  Klicka på **Skapa lösning** på **Skapa**-menyn.
5.  Tryck på **F11** på **Felsök**-menyn om du vill köra lösningen steg för steg eller på **F5** om du vill köra lösningen från början till slut.


## Köra ditt första Azure Storage-program lokalt mot Azure Storage-emulatorn

[Azure Storage-emulatorn](storage-use-emulator.md) tillhandahåller en lokal miljö som emulerar Azures blobb-, kö- och tabelltjänster för utvecklingsändamål. Du kan använda lagringsemulatorn för att testa ditt lagringsprogram lokalt, utan att skapa en Azure-prenumeration eller ett lagringskonto och utan några kostnader.

Vill du prova? Då ska vi skapa ett enkelt Azure Storage-program med hjälp av ett av snabbstartsexempelprojekten för Azure i Visual Studio. I den här självstudiekursen fokuserar vi på exempelprojekten för **Azure Blob Storage**, **Azure Table Storage** och **Azure Queue Storage**:

1. Starta Visual Studio.
2. Öppna **Arkiv**-menyn och klicka på **Nytt projekt**.
3. I dialogrutan **Nytt projekt** klickar du på **Installerade** > **Mallar** > **Visual C#** > **Moln** > **Snabbstart** > **Datatjänster**.
    a. Välj någon av följande mallar: **Azure Storage: Blobbar**, **Azure Storage: Filer**, **Azure Storage: Köer** eller **Azure Storage: Tabeller**.
    b. Kontrollera att **.NET Framework 4.5** är valt som målramverk.
    c. Ange ett namn för ditt projekt och skapa den nya Visual Studio-lösningen, som du ser här:

    ![Snabbstarter för Azure][Image1]

4.  I Visual Studio väljer du **Solution Explorer** på **Visa**-menyn. Öppna filen App.config och kommentera ut anslutningssträngen för ditt Azure Storage-konto om du redan har lagt till ett. Ta sedan bort kommentarerna i anslutningssträngen för Azure Storage-emulator:

    `<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>`

Du kanske vill granska källkoden innan du kör programmet. Om du vill granska koden väljer du **Solution Explorer** på **Visa**-menyn i Visual Studio. Dubbelklicka sedan på filen Program.cs.

Kör exempelprogrammet i Azure Storage-emulatorn:

1.  Tryck på **Start**-knappen eller Windows-tangenten, sök efter *Microsoft Azure Storage-emulatorn* och starta programmet. När emulatorn startar visas en ikon och ett meddelande i aktivitetsvyn i Windows.
2.  I Visual Studio klickar du på **Skapa lösning** på **Skapa**-menyn.
3.  Tryck på **F11** på **Felsök**-menyn om du vill köra lösningen steg för steg eller på **F5** om du vill köra lösningen från början till slut.

## Nästa steg

Se dessa resurser om du vill lära dig mer om Azure Storage:

* [Introduktion till Microsoft Azure Storage](storage-introduction.md)
* [Komma igång med Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Komma igång med Azure Blob Storage med hjälp av .NET](storage-dotnet-how-to-use-blobs.md)
* [Komma igång med Azure Table Storage med hjälp av .NET](storage-dotnet-how-to-use-tables.md)
* [Komma igång med Azure Queue Storage med hjälp av .NET](storage-dotnet-how-to-use-queues.md)
* [Komma igång med Azure File Storage i Windows](storage-dotnet-how-to-use-files.md)
* [Överföra data med kommandoradsverktyget AzCopy](storage-use-azcopy.md)
* [Azure Storage-dokumentation](https://azure.microsoft.com/documentation/services/storage/)
* [Microsoft Azure Storage-klientbibliotek för .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [REST-API för Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png



<!--HONumber=Sep16_HO3-->


