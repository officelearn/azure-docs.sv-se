---
title: Utveckla .NET standard-funktioner för Azure Stream Analytics jobb (förhands granskning)
description: Lär dig hur du skriver användardefinierade c#-funktioner för Stream Analytics jobb.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2019
ms.custom: seodec18
ms.openlocfilehash: f07c02df1b8e0032c9e1b4ef9a24c345fee20a40
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426318"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>Utveckla .NET standard-användardefinierade funktioner för Azure Stream Analytics jobb (för hands version)

Azure Stream Analytics har ett SQL-liknande frågespråk för att utföra omvandlingar och beräkningar över strömmar av händelsedata. Det finns många inbyggda funktioner, men vissa avancerade scenarier kräver extra flexibilitet. Med .NET Standard användardefinierade funktioner (UDF), kan du anropa dina egna funktioner som är skrivna på valfritt standard .NET-språk (C#, F#och så vidare) att utöka Stream Analytics-frågespråket. Användardefinierade funktioner kan du utföra komplexa matematiska beräkningar, importera anpassade ML-modeller med hjälp av ML.NET och använda anpassade uppräkning logik för data som saknas. UDF-funktionen för Stream Analytics jobb är för närvarande en för hands version och bör inte användas i produktions arbets belastningar.

.NET-användardefinierad – funktion för moln jobb finns i:
* USA, västra centrala
* Europa, norra
* USA, östra
* USA, västra
* USA, östra 2
* Europa, västra

Om du är intresse rad av att använda den här funktionen i en annan region kan du [begära åtkomst](https://aka.ms/ccodereqregion).

## <a name="overview"></a>Översikt
Visual Studio-verktyg för Azure Stream Analytics gör det enkelt för dig att skriva UDF: er, testa dina jobb lokalt (även offline), och publicera ditt Stream Analytics-jobb i Azure. När publicerats till Azure kan distribuera du ditt jobb till IoT-enheter med hjälp av IoT Hub.

Det finns tre sätt att implementera UDF:er:

* Bakomliggande koden filer i ett ASA-projekt
* UDF från ett lokalt projekt
* Ett befintligt paket från ett Azure storage-konto

## <a name="package-path"></a>Paketsökväg för

Formatet för alla UDF-paket har sökvägen `/UserCustomCode/CLR/*`. Dynamiska länkbibliotek (dll) och resurser kopieras den `/UserCustomCode/CLR/*` mapp, som hjälper dig att isolera användare DLL-filer från systemet och Azure Stream Analytics-DLL: er. Det här paketet används för alla funktioner oavsett vilken metod som används för att använda dem.

## <a name="supported-types-and-mapping"></a>Typer som stöds och mappning

|**UDF-typ (C#)**  |**Azure Stream Analytics skriver**  |
|---------|---------|
|long  |  bigint   |
|double  |  double   |
|sträng  |  nvarchar(max)   |
|Datum/tid  |  Datum/tid   |
|Struct  |  IRecord   |
|objekt  |  IRecord   |
|Matris\<objekt >  |  IArray   |
|Dictionary < string, object >  |  IRecord   |

## <a name="codebehind"></a>Bakomliggande koden
Du kan skriva användardefinierade funktioner i den **Script.asql** bakomliggande koden. Visual Studio-verktyg kommer automatiskt att kompilera den bakomliggande koden-filen till en sammansättningsfil. Sammansättningarna paketerad som en zip-fil och överförs till ditt storage-konto när du skickar jobbet till Azure. Du kan lära dig hur du skriver ett C#-UDF med bakomliggande koden genom att följa den [UDF C# för Stream Analytics Edge-jobb](stream-analytics-edge-csharp-udf.md) självstudien. 

## <a name="local-project"></a>Lokalt projekt
Användardefinierade funktioner kan skrivas i en sammansättning som anges senare i en Azure Stream Analytics-fråga. Det här är det rekommenderade alternativet för avancerade funktioner som kräver full nytta av ett .NET Standard språk utöver dess Uttrycksspråk, till exempel procedurmässig logik eller rekursion. UDF: er från ett lokalt projekt kan också användas när du vill dela logiken som funktionen över flera Azure Stream Analytics-frågor. Att lägga till UDF: er i din lokala projektet ger dig möjlighet att felsöka och testa dina funktioner lokalt från Visual Studio.

Att referera till ett lokalt projekt:

1. Skapa en ny klassbiblioteket i din lösning.
2. Skriva koden i klassen. Kom ihåg att klasser måste definieras som *offentliga* och objekt måste definieras som *statiska offentliga*. 
3. Skapa ditt projekt. Verktygen kommer paketera alla artefakter i bin-mappen till en zip-fil och ladda upp zip-filen till lagringskontot. Använda sammansättningsreferensen i stället för NuGet-paketet för externa referenser.
4. Referera till den nya klassen i projektet Azure Stream Analytics.
5. Lägg till en ny funktion i Azure Stream Analytics-projektet.
6. Konfigurera sökväg för sammansättning i konfigurationsfilen jobbet `JobConfig.json`. Ange sökväg för sammansättning **lokalt projektreferens eller bakomliggande koden**.
7. Återskapa både function-projekt och Azure Stream Analytics-projektet.  

### <a name="example"></a>Exempel

I det här exemplet är **UDFTest** ett C# klass biblioteks projekt och **ASAUDFDemo** är Azure Stream Analytics projektet, som refererar till **UDFTest**.

![Azure Stream Analytics IoT Edge-projekt i Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Skapa C#-projektet, vilket gör att du kan lägga till en referens till C#-UDF från Azure Stream Analytics-fråga.
    
   ![Skapa ett Azure Stream Analytics IoT Edge-projekt i Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Lägg till referensen till C# projektet i ASA-projektet. Högerklicka på noden referenser och välj Lägg till referens.

   ![Lägg till en referens till ett C#-projekt i Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Välj projektnamn C# i listan. 
    
   ![Välj ditt C#-projektnamn i referenslistan](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. Du bör se den **UDFTest** visas under **referenser** i **Solution Explorer**.

   ![Visa användaren definierade funktionsreferens i solution explorer](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Högerklicka på den **Functions** mapp och välj **nytt objekt**.

   ![Lägg till nytt objekt till funktioner i Azure Stream Analytics Edge-lösning](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Lägga till en C#-funktion **SquareFunction.json** i projektet Azure Stream Analytics.

   ![Välj c#-funktion från Stream Analytics Edge-objekt i Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Dubbelklicka på funktionen i **Solution Explorer** att öppna konfigurationsdialogrutan.

   ![Konfiguration av C sharp-funktion i Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. I C#-funktion-konfiguration, väljer **belastningen från ASA projektreferens** och relaterade sammansättning, klassen och metoden namn i listrutan. För att referera till metoder, typer och funktioner i Stream Analyticss frågan, måste klasserna definieras som *offentliga* och objekten måste definieras som *statisk offentlig*.

   ![Stream Analytics C sharp funktionen konfiguration](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Befintliga paket

Du kan skapa .NET Standard UDF: er i någon av valfri IDE och anropa dem från Azure Stream Analytics-fråga. Först kompilera din kod och paketet alla DLL: er. Formatet för paketet har sökvägen `/UserCustomCode/CLR/*`. Ladda sedan upp `UserCustomCode.zip` till roten på behållaren i Azure storage-kontot.

När sammansättningen zip-paket har överförts till ditt Azure storage-konto, kan du använda funktionerna i Azure Stream Analytics-frågor. Allt du behöver göra är att inkludera lagrings informationen i Stream Analytics jobb konfigurationen. Du kan testa funktionen lokalt med det här alternativet eftersom Visual Studio-verktyg inte kommer att ladda ned paketet. Paketsökvägen tolkas direkt till tjänsten. 

Konfigurera sökväg för sammansättning i konfigurationsfilen jobbet `JobConfig.json`:

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
   |Sammansättnings källa för anpassad kod|UserCustomCode. zip|

## <a name="limitations"></a>Begränsningar
UDF-förhandsgranskning har för närvarande följande begränsningar:

* .NET standard UDF: er kan endast redigerats i Visual Studio och publicerats till Azure. Skrivskyddad versioner av .NET Standard UDF: er kan visas under **Functions** i Azure-portalen. Redigering av .NET Standard funktioner stöds inte i Azure-portalen.

* Azure portal frågeredigeraren visar ett fel när du använder .NET Standard UDF i portalen. 

* Eftersom den anpassade koden delar kontext med Azure Stream Analytics-motorn, kan inte anpassad kod referera allt som har en motstridig namnområdet/dll med Azure Stream Analytics-kod-namn. Exempel: du kan inte referera till *Newtonsoft Json*.

## <a name="next-steps"></a>Nästa steg

* [Självstudie: skriva en C# användardefinierad funktion för ett Azure Stream Analytics jobb (förhands granskning)](stream-analytics-edge-csharp-udf.md)
* [Självstudier: Azure Stream Analytics JavaScript-användardefinierade funktioner](stream-analytics-javascript-user-defined-functions.md)
* [Använda Visual Studio för att visa Azure Stream Analytics-jobb](stream-analytics-vs-tools.md)
