---
title: Utveckla .NET standard-funktioner för Azure Stream Analytics jobb (förhands granskning)
description: Lär dig hur du skriver användardefinierade c#-funktioner för Stream Analytics jobb.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2019
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: fff1a228e32f115c498678a654f6c8f028772161
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "89015687"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>Utveckla .NET standard-användardefinierade funktioner för Azure Stream Analytics jobb (för hands version)

Azure Stream Analytics erbjuder ett SQL-liknande frågespråk för att utföra omvandlingar och beräkningar över data strömmar av händelse data. Det finns många inbyggda funktioner, men vissa komplexa scenarier kräver ytterligare flexibilitet. Med .NET standard User Defined functions (UDF) kan du anropa egna funktioner som skrivits på ett .NET standard språk (C#, F # osv.) för att utöka Stream Analytics frågespråk. Med UDF: er kan du utföra komplexa matematiska beräkningar, importera anpassade ML-modeller med ML.NET och använda anpassad Imputation-logik för data som saknas. UDF-funktionen för Stream Analytics jobb är för närvarande en för hands version och bör inte användas i produktions arbets belastningar.

.NET-användardefinierad – funktion för moln jobb finns i:
* USA, västra centrala
* Norra Europa
* East US
* USA, västra
* USA, östra 2
* Europa, västra

Om du är intresse rad av att använda den här funktionen i en annan region kan du [begära åtkomst](https://aka.ms/ccodereqregion).

## <a name="overview"></a>Översikt
Visual Studio-verktyg för Azure Stream Analytics gör det enkelt för dig att skriva UDF: er, testa dina jobb lokalt (även offline) och publicera ditt Stream Analytics-jobb till Azure. När du har publicerat till Azure kan du distribuera jobbet till IoT-enheter med hjälp av IoT Hub.

Det finns tre sätt att implementera UDF:er:

* CodeBehind-filer i ett ASA-projekt
* UDF från ett lokalt projekt
* Ett befintligt paket från ett Azure Storage-konto

## <a name="package-path"></a>Paket Sök väg

Formatet för ett UDF-paket har sökvägen `/UserCustomCode/CLR/*` . DLL-filer och resurser kopieras under `/UserCustomCode/CLR/*` mappen, som hjälper dig att isolera användar-dll: er från system-och Azure Stream Analytics-dll: er. Den här paket Sök vägen används för alla funktioner oavsett vilken metod som används för att använda dem.

## <a name="supported-types-and-mapping"></a>Typer och mappning som stöds
För Azure Stream Analytics värden som ska användas i C# måste de konverteras från en miljö till en annan. Konvertering sker för alla indataparametrar i en UDF. Varje Azure Stream Analytics typ har en motsvarande typ i C# som visas i tabellen nedan:

|**Azure Stream Analytics typ** |**C#-typ** |
|---------|---------|
|bigint | long |
|flyt | double |
|nvarchar(max) | sträng |
|datetime | DateTime |
|Post | Ordlista\<string, object> |
|Matris | Objekt [] |

Detsamma gäller om data måste konverteras från C# till Azure Stream Analytics, vilket inträffar i utmatning svärdet för en UDF. Tabellen nedan visar vilka typer som stöds:

|**C#-typ**  |**Azure Stream Analytics typ**  |
|---------|---------|
|long  |  bigint   |
|double  |  flyt   |
|sträng  |  nvarchar(max)   |
|DateTime  |  dateTime   |
|struct  |  Post   |
|objekt  |  Post   |
|Objekt []  |  Matris   |
|Ordlista\<string, object>  |  Post   |

## <a name="codebehind"></a>CodeBehind
Du kan skriva användardefinierade funktioner i **skriptet. asql** CodeBehind. Visual Studio-verktyg kommer automatiskt att kompilera CodeBehind-filen till en sammansättnings fil. Sammansättningarna paketeras som en zip-fil och överförs till ditt lagrings konto när du skickar jobbet till Azure. Du kan lära dig hur du skriver en C#-UDF med hjälp av CodeBehind genom att följa själv studie kursen [om C# UDF för Stream Analytics Edge-jobb](stream-analytics-edge-csharp-udf.md) . 

## <a name="local-project"></a>Lokalt projekt
Användardefinierade funktioner kan skrivas i en sammansättning som senare refereras till i en Azure Stream Analytics fråga. Detta är det rekommenderade alternativet för avancerade funktioner som kräver full kraft av ett .NET-standard språk utanför dess uttrycks språk, till exempel procedur logik eller rekursion. UDF: er från ett lokalt projekt kan också användas när du behöver dela funktions logiken över flera Azure Stream Analytics-frågor. Genom att lägga till UDF: er i ditt lokala projekt kan du felsöka och testa dina funktioner lokalt från Visual Studio.

Referera till ett lokalt projekt:

1. Skapa ett nytt klass bibliotek i din lösning.
2. Skriv koden i din klass. Kom ihåg att klasserna måste definieras som *offentliga* och objekt måste definieras som *statisk offentlig*. 
3. Skapa ditt projekt. Verktygen kommer att paketera alla artefakter i bin-mappen till en zip-fil och ladda upp zip-filen till lagrings kontot. För externa referenser använder du sammansättnings referens i stället för NuGet-paketet.
4. Referera till den nya klassen i Azure Stream Analytics-projektet.
5. Lägg till en ny funktion i Azure Stream Analytics-projektet.
6. Konfigurera sammansättnings Sök vägen i jobb konfigurations filen `JobConfig.json` . Ange sammansättnings Sök vägen till den **lokala projekt referensen eller CodeBehind**.
7. Återskapa både Function Project och Azure Stream Analytics-projektet.  

### <a name="example"></a>Exempel

I det här exemplet är **UDFTest** ett C#-klass biblioteks projekt och **ASAUDFDemo** är Azure Stream Analytics projektet, som refererar till **UDFTest**.

![Azure Stream Analytics IoT Edge projekt i Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Skapa ett C#-projekt så att du kan lägga till en referens till din C# UDF från Azure Stream Analyticss frågan.
    
   ![Bygga ett Azure Stream Analytics IoT Edge projekt i Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Lägg till referensen i C#-projektet i ASA-projektet. Högerklicka på noden referenser och välj Lägg till referens.

   ![Lägga till en referens till ett C#-projekt i Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Välj C#-projekt namnet i listan. 
    
   ![Välj ditt C#-projekt namn från listan referens](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. Du bör se **UDFTest** som visas under **referenser** i **Solution Explorer**.

   ![Visa den användardefinierade funktions referensen i Solution Explorer](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Högerklicka på mappen **funktioner** och välj **nytt objekt**.

   ![Lägg till nytt objekt till funktioner i Azure Stream Analytics Edge-lösning](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Lägg till en C#-funktion **SquareFunction.jspå** Azure Stream Analytics projektet.

   ![Välj funktionen CSharp från Stream Analytics Edge-objekt i Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Dubbelklicka på funktionen i **Solution Explorer** för att öppna dialog rutan konfiguration.

   ![Funktion i C Sharps funktions konfiguration i Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. I C#-funktions konfigurationen väljer du **läsa in från ASA projekt referens** och relaterade sammansättnings-, klass-och metod namn i list rutan. För att referera till metoder, typer och funktioner i Stream Analyticss frågan, måste klasserna definieras som *offentliga* och objekten måste definieras som *statisk offentlig*.

   ![Stream Analytics C Sharps funktions konfiguration](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Befintliga paket

Du kan skapa .NET standard-UDF: er i valfri IDE som du väljer och anropa dem från din Azure Stream Analytics fråga. Kompilera först koden och paketera alla DLL-filer. Paketets format har sökvägen `/UserCustomCode/CLR/*` . Ladda sedan upp `UserCustomCode.zip` till roten för behållaren i ditt Azure Storage-konto.

När paketets zip-paket har laddats upp till ditt Azure Storage-konto kan du använda funktionerna i Azure Stream Analytics frågor. Allt du behöver göra är att inkludera lagrings informationen i Stream Analytics jobb konfigurationen. Du kan inte testa funktionen lokalt med det här alternativet eftersom det inte går att hämta ditt paket med Visual Studio Tools. Paket Sök vägen parsas direkt till tjänsten. 

För att konfigurera sammansättnings Sök vägen i jobb konfigurations filen `JobConfig.json` :

Expandera avsnittet **användardefinierade kod Configuration** avsnittet och fyll i konfigurationen med följande föreslagna värden:

   |**Inställning**|**Föreslaget värde**|
   |-------|---------------|
   |Resurs för globala lagrings inställningar|Välj datakälla från det aktuella kontot|
   |Prenumeration på globala lagrings inställningar| < prenumerationen >|
   |Lagrings konto för globala lagrings inställningar| < ditt lagrings konto >|
   |Resurs för anpassade kod lagrings inställningar|Välj datakälla från det aktuella kontot|
   |Lagrings konto för anpassade kod lagrings inställningar|< ditt lagrings konto >|
   |Behållare för lagrings inställningar för anpassade koder|< lagrings container >|
   |Sammansättnings källa för anpassad kod|Befintliga paket paket från molnet|
   |Sammansättnings källa för anpassad kod|UserCustomCode.zip|

## <a name="user-logging"></a>Användar loggning
Med loggnings metoden kan du samla in anpassad information medan ett jobb körs. Du kan använda loggdata för att felsöka eller utvärdera rätthet för den anpassade koden i real tid.

`StreamingContext`Med klassen kan du publicera diagnostikinformation med hjälp av `StreamingDiagnostics.WriteError` funktionen. I koden nedan visas gränssnittet som exponeras av Azure Stream Analytics.

```csharp
public abstract class StreamingContext
{
    public abstract StreamingDiagnostics Diagnostics { get; }
}

public abstract class StreamingDiagnostics
{
    public abstract void WriteError(string briefMessage, string detailedMessage);
}
```

`StreamingContext` skickas som en indataparameter till UDF-metoden och kan användas i UDF för att publicera anpassad logg information. I exemplet nedan `MyUdfMethod` definierar **data** inmatning, som tillhandahålls av frågan och en **Sammanhangs** inmatning som `StreamingContext` , som tillhandahålls av körnings motorn. 

```csharp
public static long MyUdfMethod(long data, StreamingContext context)
{
    // write log
    context.Diagnostics.WriteError("User Log", "This is a log message");
    
    return data;
}
```

`StreamingContext`Värdet behöver inte skickas av SQL-frågan. Azure Stream Analytics tillhandahåller ett kontext objekt automatiskt om en indataparameter finns. Användningen av `MyUdfMethod` ändras inte, som visas i följande fråga:

```sql
SELECT udf.MyUdfMethod(input.value) as udfValue FROM input
```

Du kan komma åt logg meddelanden via [diagnostikloggar](data-errors.md).

## <a name="limitations"></a>Begränsningar
UDF-förhands granskningen har för närvarande följande begränsningar:

* .NET standard-UDF: er kan bara redige ras i Visual Studio och publiceras till Azure. Skrivskyddade versioner av .NET standard UDF: er kan visas under **funktioner** i Azure Portal. Det finns inte stöd för redigering av .NET-standard funktioner i Azure Portal.

* Azure Portal Frågeredigeraren visar ett fel när du använder .NET standard UDF i portalen. 

* Eftersom den anpassade koden delar kontexten med Azure Stream Analytics motor kan den anpassade koden inte referera till något som har ett namn område/dll_name som står i konflikt med Azure Stream Analytics kod. Du kan till exempel inte referera till *Newtonsoft JSON*.

* Stödda filer som ingår i projektet kopieras till den användarens anpassade kod zip-fil som används när du publicerar jobbet i molnet. Alla filer i undermappar kopieras direkt till roten i mappen för den anpassade användar koden i molnet när den är zippad. Zip-filen är "förenklad" när den expanderas.

* Anpassad kod för användare stöder inte tomma mappar. Lägg inte till tomma mappar i de stödfiler som stöds i projektet.

## <a name="next-steps"></a>Nästa steg

* [Självstudie: skriva en C#-användardefinierad funktion för ett Azure Stream Analytics jobb (förhands granskning)](stream-analytics-edge-csharp-udf.md)
* [Självstudier: Användardefinierade funktioner i Azure Stream Analytics JavaScript](stream-analytics-javascript-user-defined-functions.md)
* [Använda Visual Studio för att visa Azure Stream Analytics-jobb](stream-analytics-vs-tools.md)
