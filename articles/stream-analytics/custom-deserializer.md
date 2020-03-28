---
title: Självstudiekurs - Anpassade .NET-deserializers för Azure Stream Analytics-molnjobb
description: Den här självstudien visar hur du skapar en anpassad .NET-deserializer för ett Azure Stream Analytics-molnjobb med Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: 1fffeec1434cb066487bf383589554edec2e6a86
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75443699"
---
# <a name="tutorial-custom-net-deserializers-for-azure-stream-analytics"></a>Självstudiekurs: Anpassade .NET-deserializers för Azure Stream Analytics

Azure Stream Analytics har [inbyggt stöd för tre dataformat:](stream-analytics-parsing-json.md)JSON, CSV och Avro. Med anpassade .NET-deserializers kan du läsa data från andra format som [protokollbuffert,](https://developers.google.com/protocol-buffers/) [Bond](https://github.com/Microsoft/bond) och andra användardefinierade format för både moln- och kantjobb.

Den här självstudien visar hur du skapar en anpassad .NET-deserializer för ett Azure Stream Analytics-molnjobb med Visual Studio. 

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en anpassad deserializer för protokollbuffert.
> * Skapa ett Azure Stream Analytics-jobb i Visual Studio.
> * Konfigurera ditt Stream Analytics-jobb så att det använder den anpassade deserializern.
> * Kör ditt Stream Analytics-jobb lokalt för att testa den anpassade deserializern.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Installera [Visual Studio 2017](https://www.visualstudio.com/downloads/) eller [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/). Versionerna Enterprise (Ultimate/Premium), Professional och Community stöds. Express-utgåvan stöds inte.

* [Installera Stream Analytics-verktygen för Visual Studio](stream-analytics-tools-for-visual-studio-install.md) eller uppdatera till den senaste versionen. Följande versioner av Visual Studio stöds:
   * Visual Studio 2015
   * Visual Studio 2017

* Öppna **Cloud Explorer** i Visual Studio och logga in på din Azure-prenumeration.

* Skapa en behållare i ditt Azure Storage-konto.
Behållaren som du skapar används för att lagra tillgångar som är relaterade till ditt Stream Analytics-jobb. Om du redan har ett lagringskonto med befintliga containrar kan du använda dem. Annars måste du [skapa en ny container](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="create-a-custom-deserializer"></a>Skapa en anpassad deserializer

1. Öppna Visual Studio och välj **Arkiv > Nytt > Project**. Sök efter **Stream Analytics** och välj Azure Stream Analytics **Custom Deserializer Project (.NET)**. Ge projektet ett namn, till exempel **Protobuf Deserializer**.

   ![Skapa standardklassbiblioteksprojekt för Visual Studio dotnet](./media/custom-deserializer/create-dotnet-library-project.png)

2. Högerklicka på **protobuf Deserializer-projektet** i Solution Explorer och välj **Hantera NuGet-paket** på menyn. Installera sedan **paketen Microsoft.Azure.StreamAnalytics** och **Google.Protobuf** NuGet.

3. Lägg till [klassen MessageBodyProto](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) och [klassen MessageBodyDeserializer](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) i projektet.

4. Bygg **Protobuf Deserializer-projektet.**

## <a name="add-an-azure-stream-analytics-project"></a>Lägga till ett Azure Stream Analytics-projekt

1. Högerklicka på **Protobuf Deserializer-lösningen** i Solution Explorer och välj **Lägg till > Nytt projekt**. Under **Azure Stream Analytics > Stream Analytics**väljer du Azure Stream Analytics **Application**. Namnge den **ProtobufCloudDeserializer** och välj **OK**. 

2. Högerklicka på **Referenser** under **Projektet ProtobufCloudDeserializer** Azure Stream Analytics. Lägg till **Protobuf Deserializer under** **Projekt**. Det bör fyllas i automatiskt för dig.

## <a name="configure-a-stream-analytics-job"></a>Konfigurera ett Stream Analytics-jobb

1. Dubbelklicka på **JobConfig.json**. Använd standardkonfigurationerna, förutom följande inställningar:

   |Inställning|Föreslaget värde|
   |-------|---------------|
   |Resurs för globala lagringsinställningar|Välj datakälla från det aktuella kontot|
   |Prenumeration på globala lagringsinställningar| < din prenumeration >|
   |Lagringskonto för globala lagringsinställningar| < ditt lagringskonto >|
   |Resurs för anpassade inställningar för kodlagring|Välj datakälla från det aktuella kontot|
   |Lagringskonto för anpassade lagringsinställningar för kod|< ditt lagringskonto >|
   |Behållare för anpassade inställningar för kodlagring|< din förvaringsbehållare >|

2. Dubbelklicka på **Input.json**under **Ingångar.** Använd standardkonfigurationerna, förutom följande inställningar:

   |Inställning|Föreslaget värde|
   |-------|---------------|
   |Källa|Blob Storage|
   |Resurs|Välj datakälla från det aktuella kontot|
   |Prenumeration|< din prenumeration >|
   |Lagringskonto|< ditt lagringskonto >|
   |Container|< din förvaringsbehållare >|
   |Format för seriell händelse|Övrigt (Protobuf, XML, proprietär...)|
   |Resurs|Läsa in från ASA-projektreferens eller kodbländ|
   |CSharp-sammansättningsnamn|ProtobufDeserializer.dll|
   |Klassnamn|MessageBodyProto.MessageBodyDeserializer|
   |Typ av händelsekomprimering|Inget|

3. Lägg till följande fråga i filen **Script.asaql.**

   ```sql
   SELECT * FROM Input
   ```

4. Ladda ner [exempelprotobuf-inmatningsfilen](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). Högerklicka på **Input.json** i mappen **Ingångar** och välj **Lägg till lokal inmatning**. Dubbelklicka sedan **local_Input.json** och konfigurera följande inställningar:

   |Inställning|Föreslaget värde|
   |-------|---------------|
   |Indataalias|Indata|
   |Källtyp|Dataström|
   |Format för seriell händelse|Övrigt (Protobuf, XML, proprietär...)|
   |CSharp-sammansättningsnamn|ProtobufDeserializer.dll|
   |Klassnamn|MessageBodyProto.MessageBodyDeserializer|
   |Sökväg till lokal indatafil|< filsökvägen för den hämtade exempeldatafilen>|

## <a name="execute-the-stream-analytics-job"></a>Kör stream analytics-jobbet

1. Öppna **Script.asaql** och välj **Kör lokalt**.

2. Observera resultaten i **Strömma Analytics lokala körresultat**.

Du har implementerat en anpassad deserializer för ditt Stream Analytics-jobb! I den här självstudien testade du den anpassade deserializer lokalt. För faktiska data konfigurerar du indata och utdata korrekt. Skicka sedan jobbet till Azure från Visual Studio för att köra jobbet i molnet med hjälp av den anpassade deserializer som du just implementerade.

## <a name="debug-your-deserializer"></a>Felsöka din deserializer

Du kan felsöka .NET deserializer lokalt på samma sätt som du felsöker standard .NET-kod. 

1. Lägg till brytpunkter i din funktion.

2. Starta felsökningen genom att trycka på **F5**. Programmet kommer att avbrytas vid dina brytpunkter som förväntat.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, strömningsjobbet och alla relaterade resurser när de inte längre behövs. Om du tar bort jobbet undviker du att bli fakturerad för de strömmande enheter som används av jobbet. Om du planerar att använda jobbet i framtiden kan du stoppa det och sedan starta det igen när du behöver det. Om du inte tänker fortsätta använda det här jobbet tar du bort alla resurser som skapades i självstudien med följande steg:

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure-portalen och välj sedan namnet på den resurs du skapade.  

2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen som ska tas bort i textrutan och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att implementera en anpassad .NET-deserializer för protokollbuffertens serialisering. Om du vill veta mer om hur du skapar anpassade deserializers fortsätter du till följande artikel:

> [!div class="nextstepaction"]
> [Skapa olika .NET-deserializers för Azure Stream Analytics-jobb](custom-deserializer-examples.md)
