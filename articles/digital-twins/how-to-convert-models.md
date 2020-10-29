---
title: Omvandla bransch standard modeller
titleSuffix: Azure Digital Twins
description: Förstå mönstret för att konvertera bransch standard modeller (RDF/OWL) till DTDL
author: baanders
ms.author: baanders
ms.date: 10/28/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b9e298e55f06501bbbb4271f0643012c3cb6c5fe
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92915443"
---
# <a name="convert-industry-standard-models-to-dtdl-for-azure-digital-twins"></a>Omvandla bransch standard modeller till DTDL för Azure Digitals, dubbla

Modeller i digitala Azure-grupper representeras i JSON-LD-baserade [**digitala dubbla definitions språk (DTDL)**](concepts-models.md). Om du har befintliga modeller utanför Azures digitala dubbla, som baseras på en bransch standard, till exempel RDF eller OWL, måste du **konvertera dem till DTDL** för att kunna använda dem med Azure Digitals dubbla. DTDL-versionen kommer sedan att bli källan till sanningen för modellen i Azure Digitals flätar.

I den här artikeln beskrivs ett mönster för att konvertera RDF-baserade branscher eller anpassade modeller till DTDL. Den innehåller:
* **Vägledning på strategi nivå** som kan tillämpas på olika standarder och modell typer
* [**Exempel kod** för en RDF konverterare](#sample-converter-application)

## <a name="industry-models"></a>Bransch modeller  

Genom att använda bransch modeller får du en rik start punkt när du utformar din Azure Digitals dubbla modell modell. Genom att använda bransch modeller kan du också hjälpa till med standardisering och informations delning. 

Några vanliga bransch modeller är:  

| Bransch vertikal | Modell |
| --- | --- | 
| Utveckling/hantering av anläggningar | [RealEstateCore](https://www.realestatecore.io/)<br>[BRICK schema](https://brickschema.org/ontology/1.1/)<br>[Skapa topologi Ontology (BOT)](https://w3c-lbd-cg.github.io/bot/)<br>[Semantisk sensor nätverk](https://www.w3.org/TR/vocab-ssn/)<br>[buildingSmart Industry Foundation-klasser (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/) |
| Smarta städer | [ETSI NGSI – LD](https://www.etsi.org/deliver/etsi_gr/CIM/001_099/008/01.01.01_60/gr_CIM008v010101p.pdf)<br>[Smart Applications-referens (SAREF)](https://saref.etsi.org/)<br>[Fiware](https://www.fiware.org/)<br>[Öppna & Agile Smart städer (OASC)](https://oascities.org/) |
| Energi rutnät | [CIM](https://cimug.ucaiug.org/) / [IEC 61968](https://en.wikipedia.org/wiki/IEC_61968)<br>[ADRM-modeller](http://www.adrm.com/) för energi-och råvaru handel |
| Fordon | [Signal specifikation för fordon](https://github.com/GENIVI/vehicle_signal_specification/tree/master/spec) |

Beroende på dina behov kan du också använda DTDL för att anpassa eller utöka bransch modeller eller utveckla en egen anpassad modell från grunden. 

## <a name="create-and-edit-models"></a>Skapa och redigera modeller

Det första steget i modelleringen skapar dina modeller. Du kan skapa och slutföra redigering av bransch standard modeller innan du konverterar dem till DTDL, eller så kan du konvertera dem till DTDL tidigt och fortsätta redigera dem som DTDL-dokument.

### <a name="with-industry-standards"></a>Med bransch standarder

De flesta bransch modeller (kallas även **Ontologies** ) baseras på semantiska webb standarder som [Owl](https://www.w3.org/OWL/[), [RDF](https://www.w3.org/2001/sw/wiki/RDF)och [RDFS](https://www.w3.org/2001/sw/wiki/RDFS). 

I vissa fall kanske du vill skapa eller redigera en modell med hjälp av OWL-baserade modell verktyg. Du kan använda valfritt antal modell redigerings verktyg för att utforma en OWL-baserad modell, inklusive följande.
* [WebProtégé](https://protege.stanford.edu/products.php#web-protege) och [protégé Desktop](https://protege.stanford.edu/products.php#desktop-protege) är populära exempel. Du kan importera bransch modeller i flera format, redigera eller utöka en modell och exportera modellen. 
* [WebVOWL](http://www.visualdataweb.de/webvowl/) är ett annat populärt verktyg för visualisering av modeller. 

Om du skapar en modell som använder en viss bransch standard som inte är DTDL, måste du konvertera den till DTDL och ladda upp den till Azures digitala dubbla. 

#### <a name="common-model-file-formats"></a>Vanliga modell fil format 

RDF, OWL och RDFS är de grundläggande Bygg stenarna för den semantiska webben. 

**RDF** tillhandahåller en konceptuell struktur för att beskriva saker i form av **tredubbla** . Tredubbla består av tre delar: **subject** , **predikat** och **Object** . Objekt kan skapas för URI: er. 

Här följer några exempel på RDF tredubbla:

```
<LogicalDevice> <hasCapabiity> <Temperature>
<Chiller> <locatedIn> <Level1>
<Asset> <isPartOf> <Asset>  
```

De här exemplen är alla giltiga RDF, men den sista instruktionen är semantiskt ogiltig. Den här situationen är den plats där OWL-specifikationen anger bilden. **Owl** definierar vad du kan skriva med RDF för att kunna ha giltiga Ontology.

Här är ett exempel på hur du använder OWL: 

```
<Asset> <isPartOf> <Building>
<TemperatureSensor> <isType> <Sensor>
```

**RDFS** tillhandahåller ytterligare semantik för vokabulär som hjälper dig att definiera och beskriva klasser. En sådan klass är till exempel `rdfs:subClassOf` :

```
<Equipment> <subClassOf> <Asset>
```

Modeller kan sparas, importeras och exporteras i många olika fil format, inklusive:  
* RDF/XML 
* Sköld padd (TTL) 
* OWL/XML 

### <a name="with-dtdl"></a>Med DTDL

Azure Digitals dubbla, använder JSON-LD-baserade **digitala DTDL (Digital Definition Language)** för modellering. Alla modeller som ska användas med digitala Azure-dubbla måste antingen skrivas in eller konverteras till DTDL.

När du arbetar med modeller i DTDL kan du använda [**DTDL-tillägget**](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)   som är tillgängligt för  [Visual Studio Code](https://code.visualstudio.com/), som innehåller syntax för autentisering och andra funktioner som gör det lättare att skriva DTDL-modeller.

Du kan läsa mer om Azure Digitals dubbla modeller och deras komponenter i [*begrepp: anpassade modeller*](concepts-models.md) och [*anvisningar: Hantera anpassade modeller*](how-to-manage-model.md).

## <a name="convert-models-to-dtdl"></a>Konvertera modeller till DTDL

Om du vill använda en modell med digitala Azure-dubbla, måste den vara i DTDL-format. Det här avsnittet beskriver hur du konverterar RDF-baserade modeller till DTDL så att de kan användas med digitala Azure-dubbla.

Det finns flera bibliotek från tredje part som kan användas när du konverterar RDF-baserade modeller till DTDL. Med vissa av dessa bibliotek kan du läsa in modell filen i ett diagram. Du kan gå igenom grafen som söker efter vissa RDFS-och OWL-konstruktioner och konvertera dessa till DTDL.   

Följande tabell är ett exempel på hur RDFS-och OWL-konstruktioner kan mappas till DTDL. 

| RDFS/OWL-koncept | RDFS/OWL-konstruktion | DTDL-koncept | DTDL-konstruktion |
| --- | --- | --- | --- |
| Klasser | `owl:Class`<br>IRI-suffix<br>``rdfs:label``<br>``rdfs:comment`` | Gränssnitt | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Underklasser | `owl:Class`<br>IRI-suffix<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Gränssnitt | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Egenskaper för datatyp | `owl:DatatypeProperty`<br>`rdfs:label` eller `INode`<br>`rdfs:label`<br>`rdfs:range` | Gränssnitts egenskaper | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Objekt egenskaper | `owl:ObjectProperty`<br>`rdfs:label` eller `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relation | `type:Relationship`<br>`name`<br>`target` (eller utelämnat om Nej `rdfs:range` )<br>`comment`<br>`displayName`<br>

Följande C#-kodfragment visar hur en RDF modell fil läses in i ett diagram och konverteras till DTDL med hjälp av [**dotNetRDF**](https://www.dotnetrdf.org/) -biblioteket. 

```csharp
using VDS.RDF.Ontology; 
using VDS.RDF.Parsing; 
using Microsoft.Azure.DigitalTwins.Parser; 

//...

Console.WriteLine("Reading file..."); 

FileLoader.Load(_ontologyGraph, rdfFile.FullName); 

// Start looping through for each owl:Class 
foreach (OntologyClass owlClass in _ontologyGraph.OwlClasses) 
{ 

    // Generate a DTMI for the owl:Class 
    string Id = GenerateDTMI(owlClass); 

    if (!String.IsNullOrEmpty(Id)) 
    { 

        Console.WriteLine($"{owlClass.ToString()} -> {Id}"); 

        // Create Interface
        DtdlInterface dtdlInterface = new DtdlInterface 
        { 
            Id = Id, 
            Type = "Interface", 
            DisplayName = GetInterfaceDisplayName(owlClass), 
            Comment = GetInterfaceComment(owlClass), 
            Contents = new List<DtdlContents>() 
        }; 

        // Use DTDL 'extends' for super classes 
        IEnumerable<OntologyClass> foundSuperClasses = owlClass.DirectSuperClasses; 

        //... 
     }

     // Add interface to the list of interfaces 
     _interfaceList.Add(dtdlInterface); 
} 

// Serialize to JSON 
var json = JsonConvert.SerializeObject(_interfaceList); 

//...
``` 

## <a name="validate-and-upload-dtdl-models"></a>Verifiera och ladda upp DTDL-modeller

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

När en modell har konverterats och verifierats kan du **Ladda upp den till din Azure Digital-instansen** . Mer information om den här processen finns i avsnittet om att [*Ladda upp modeller*](how-to-manage-model.md#upload-models) i *anvisningar: Hantera anpassade modeller* .

## <a name="sample-converter-application"></a>Exempel program för konverterare 

Det finns ett tillgängligt exempel program som konverterar en RDF-baserad modell fil till [DTDL (version 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) för användning av tjänsten Azure Digitals dubbla. Exemplet är ett .NET Core-kommando rads program som heter **RdfToDtdlConverter** .

Du kan hämta exemplet här: [**RdfToDtdlConverter**](https://docs.microsoft.com/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Du kan ladda ned koden till datorn genom att trycka på *Hämta zip* -knappen under rubriken på exempel landnings sidan. En *zip* -fil kommer att hämtas under namnet *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip* , som du sedan kan packa upp och utforska.

Du kan använda det här exemplet för att se konverterings mönstren i sammanhanget och ha som bygg block för dina egna program som utför modell konverteringar enligt dina egna specifika behov.

## <a name="next-steps"></a>Nästa steg 

Läs mer om att utforma och hantera digitala dubbla modeller:
 
* [*Begrepp: anpassade modeller*](concepts-models.md)
* [*Anvisningar: Hantera anpassade modeller*](how-to-manage-model.md)
* [*Anvisningar: parsa och validera modeller*](how-to-parse-models.md)