---
title: Utveckla .NET Standard-funktioner för Azure Stream Analytics-jobb (förhandsversion)
description: Läs om hur du skriver c# användardefinierade funktioner för Stream Analytics-jobb.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2019
ms.custom: seodec18
ms.openlocfilehash: f07c02df1b8e0032c9e1b4ef9a24c345fee20a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426318"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>Utveckla .NET Standard användardefinierade funktioner för Azure Stream Analytics-jobb (förhandsversion)

Azure Stream Analytics erbjuder ett SQL-liknande frågespråk för att utföra omvandlingar och beräkningar över strömmar av händelsedata. Det finns många inbyggda funktioner, men vissa komplexa scenarier kräver ytterligare flexibilitet. Med .NET Standard användardefinierade funktioner (UDF) kan du anropa dina egna funktioner skrivna på valfritt .NET-standardspråk (C#, F#, etc.) för att utöka Frågespråket Stream Analytics. Med UDF:er kan du utföra komplexa matematiska beräkningar, importera anpassade ML-modeller med ML.NET och använda anpassad imputeringslogik för saknade data. UDF-funktionen för Stream Analytics-jobb är för närvarande i förhandsversion och bör inte användas i produktionsarbetsbelastningar.

.NET-användardefinierad funktion för molnjobb är tillgänglig i:
* USA, västra centrala
* Europa, norra
* USA, östra
* USA, västra
* USA, östra 2
* Europa, västra

Om du är intresserad av att använda den här funktionen i någon annan region kan du [begära åtkomst](https://aka.ms/ccodereqregion).

## <a name="overview"></a>Översikt
Visual Studio-verktyg för Azure Stream Analytics gör det enkelt för dig att skriva UDFs, testa dina jobb lokalt (även offline) och publicera ditt Stream Analytics-jobb på Azure. När du har publicerats i Azure kan du distribuera jobbet till IoT-enheter med IoT Hub.

Det finns tre sätt att implementera UDF:er:

* CodeBehind filer i ett ASA-projekt
* UDF från ett lokalt projekt
* Ett befintligt paket från ett Azure-lagringskonto

## <a name="package-path"></a>Sökväg till paket

Formatet på ett UDF-paket `/UserCustomCode/CLR/*`har sökvägen . Dll-filer (Dynamic Link Libraries) och `/UserCustomCode/CLR/*` resurser kopieras under mappen, vilket hjälper till att isolera användares DLL-filer från system- och Azure Stream Analytics-DLL:er. Den här paketsökvägen används för alla funktioner oavsett vilken metod som används för att använda dem.

## <a name="supported-types-and-mapping"></a>Typer och mappning som stöds

|**UDF-typ (C#)**  |**Typ av Azure Stream Analytics-typ**  |
|---------|---------|
|long  |  bigint   |
|double  |  double   |
|sträng  |  nvarchar(max)   |
|Datetime  |  Datetime   |
|Struct  |  IRecord (IRecord)   |
|objekt  |  IRecord (IRecord)   |
|Matrisobjekt\<>  |  Mer från IArray   |
|ordlista<sträng,>  |  IRecord (IRecord)   |

## <a name="codebehind"></a>Codebehind
Du kan skriva användardefinierade funktioner i **Script.asql-koden** Bakom. Visual Studio-verktygen kompilerar automatiskt CodeBehind-filen till en sammansättningsfil. Sammansättningarna paketeras som en zip-fil och överförs till ditt lagringskonto när du skickar jobbet till Azure. Du kan lära dig hur du skriver en C# UDF med CodeBehind genom att följa självstudien [C# UDF för Stream Analytics Edge-jobb.](stream-analytics-edge-csharp-udf.md) 

## <a name="local-project"></a>Lokalt projekt
Användardefinierade funktioner kan skrivas i en sammansättning som senare refereras i en Azure Stream Analytics-fråga. Detta är det rekommenderade alternativet för komplexa funktioner som kräver full kraft för ett .NET Standard-språk utöver dess uttrycksspråk, till exempel procedurlogik eller rekursion. UDF:er från ett lokalt projekt kan också användas när du behöver dela funktionslogiken i flera Azure Stream Analytics-frågor. Genom att lägga till UDF:er i ditt lokala projekt kan du felsöka och testa dina funktioner lokalt från Visual Studio.

Så här refererar du till ett lokalt projekt:

1. Skapa ett nytt klassbibliotek i din lösning.
2. Skriv koden i din klass. Kom ihåg att klasserna måste definieras som *offentliga* och objekt måste definieras som *statisk offentlig*. 
3. Skapa ditt projekt. Verktygen paketerar alla artefakter i bin-mappen till en zip-fil och överför zip-filen till lagringskontot. För externa referenser använder du sammansättningsreferens i stället för NuGet-paketet.
4. Referera till den nya klassen i ditt Azure Stream Analytics-projekt.
5. Lägg till en ny funktion i ditt Azure Stream Analytics-projekt.
6. Konfigurera monteringssökvägen i jobbkonfigurationsfilen `JobConfig.json`. Ange monteringssökvägen till **lokal projektreferens eller kod Bakom**.
7. Återskapa både funktionsprojektet och Azure Stream Analytics-projektet.  

### <a name="example"></a>Exempel

I det här exemplet är **UDFTest** ett C#-klassbiblioteksprojekt och **ASAUDFDemo** är Azure Stream Analytics-projektet, som refererar till **UDFTest**.

![Azure Stream Analytics IoT Edge-projekt i Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Skapa ditt C#-projekt, vilket gör att du kan lägga till en referens till din C# UDF från Azure Stream Analytics-frågan.
    
   ![Skapa ett IoT Edge-projekt för Azure Stream Analytics i Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Lägg till referensen till C#-projektet i ASA-projektet. Högerklicka på noden Referenser och välj Lägg till referens.

   ![Lägga till en referens till ett C#-projekt i Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Välj C#-projektnamnet i listan. 
    
   ![Välj ditt C#-projektnamn i referenslistan](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. Du bör se **UDFTest** som anges under **Referenser** i **Solution Explorer**.

   ![Visa den användardefinierade funktionsreferensen i lösningsutforskaren](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Högerklicka på mappen **Funktioner** och välj **Nytt objekt**.

   ![Lägga till nytt objekt i Funktioner i Azure Stream Analytics Edge-lösning](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Lägg till en C#-funktion **SquareFunction.json** i ditt Azure Stream Analytics-projekt.

   ![Välj CSharp-funktion från Stream Analytics Edge-objekt i Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Dubbelklicka på funktionen i **Solution Explorer** för att öppna konfigurationsdialogrutan.

   ![C skarp funktionskonfiguration i Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. I funktionskonfigurationen C#väljer du **Läs in från ASA-projektreferens** och relaterade sammansättnings-, klass- och metodnamn i listrutan. Om du vill referera till metoder, typer och funktioner i Stream Analytics-frågan måste klasserna definieras som *offentliga* och objekten måste definieras som *statiska offentliga*.

   ![Sharp funktionskonfiguration för Stream Analytics C](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Befintliga paket

Du kan skapa .NET Standard UDFs i valfri IDE och anropa dem från din Azure Stream Analytics-fråga. Kompilera först koden och paketera alla DLL:er. Paketets format har sökvägen `/UserCustomCode/CLR/*`. Ladda sedan `UserCustomCode.zip` upp till roten av behållaren i ditt Azure-lagringskonto.

När zip-paket för montering har överförts till ditt Azure-lagringskonto kan du använda funktionerna i Azure Stream Analytics-frågor. Allt du behöver göra är att inkludera lagringsinformationen i jobbkonfigurationen för Stream Analytics. Du kan inte testa funktionen lokalt med det här alternativet eftersom Visual Studio-verktygen inte hämtar paketet. Paketsökvägen tolkas direkt till tjänsten. 

Så här konfigurerar du monteringssökvägen i `JobConfig.json`jobbkonfigurationsfilen:

Expandera avsnittet **användardefinierade kod Configuration** avsnittet och fyll i konfigurationen med följande föreslagna värden:

   |**Inställning**|**Föreslaget värde**|
   |-------|---------------|
   |Resurs för globala lagringsinställningar|Välj datakälla från det aktuella kontot|
   |Prenumeration på globala lagringsinställningar| < din prenumeration >|
   |Lagringskonto för globala lagringsinställningar| < ditt lagringskonto >|
   |Resurs för anpassade inställningar för kodlagring|Välj datakälla från det aktuella kontot|
   |Lagringskonto för anpassade lagringsinställningar för kod|< ditt lagringskonto >|
   |Behållare för anpassade inställningar för kodlagring|< din förvaringsbehållare >|
   |Källa för anpassad kodsammansättning|Befintliga monteringspaket från molnet|
   |Källa för anpassad kodsammansättning|AnvändareCustomCode.zip|

## <a name="limitations"></a>Begränsningar
UDF-förhandsversionen har för närvarande följande begränsningar:

* .NET Standard UDFs kan bara skapas i Visual Studio och publiceras på Azure. Skrivskyddade versioner av .NET Standard UDFs kan visas under **Funktioner** i Azure-portalen. Redigering av .NET Standard-funktioner stöds inte i Azure-portalen.

* Azure portal frågeredigeraren visar ett fel när du använder .NET Standard UDF i portalen. 

* Eftersom den anpassade koden delar kontext med Azure Stream Analytics-motorn kan anpassad kod inte referera till något som har ett namnområde i konflikt/dll_name med Azure Stream Analytics-kod. Du kan till exempel inte referera till *Newtonsoft Json*.

## <a name="next-steps"></a>Nästa steg

* [Självstudiekurs: Skriv en C# användardefinierad funktion för ett Azure Stream Analytics-jobb (förhandsversion)](stream-analytics-edge-csharp-udf.md)
* [Självstudier: Användardefinierade funktioner i Azure Stream Analytics JavaScript](stream-analytics-javascript-user-defined-functions.md)
* [Använda Visual Studio för att visa Azure Stream Analytics-jobb](stream-analytics-vs-tools.md)
