---
title: "Guide för att skapa en datatjänst för Marketplace | Microsoft Docs"
description: "Detaljerade anvisningar för hur du skapar, certifiera och distribuera en Data-tjänst för att köpa på Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 107baab2-5828-4158-abdf-59a580c80d37
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: 8ff76ea21ba684ae2a2afcb74d66b4912d7be053
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="understanding-the-nodes-schema-for-mapping-an-existing-web-service-to-odata-through-csdl"></a>Förstå noder schemat för matchning av en befintlig webbtjänst till OData via CSDL
> [!IMPORTANT]
> **Just nu vi inte längre onboarding några nya Data Service-utgivare. Nya dataservices kommer inte godkännas för lista.** Om du har ett SaaS-affärsprogram som du vill publicera på AppSource hittar du mer information [här](https://appsource.microsoft.com/partners). Om du har en IaaS-program eller developer-tjänsten som du vill publicera på Azure Marketplace du hittar mer information [här](https://azure.microsoft.com/marketplace/programs/certified/).
>
>

Det här dokumentet hjälper dig att klargöra nod strukturen för matchning av en OData-protokollet till CSDL. Det är viktigt att Observera att noden struktur är korrekt formaterad XML. Så gäller rot, överordnade och underordnade schema när du utformar din OData-mappning.

## <a name="ignored-elements"></a>Ignoreras element
Följande är de hög nivå CSDL-element (XML-noder) som inte kommer att användas av Azure Marketplace-serverdelen vid import av webbtjänsten metadata. De kan vara närvarande men kommer att ignoreras.

| Element | Omfång |
| --- | --- |
| Med Element |Noden, sub noder och alla attribut |
| Documentation-elementet |Noden, sub noder och alla attribut |
| ComplexType |Noden, sub noder och alla attribut |
| Kopplingen Element |Noden, sub noder och alla attribut |
| En Extended-egenskap |Noden, sub noder och alla attribut |
| EntityContainer |Följande attribut ignoreras: *utökar* och *AssociationSet* |
| Schemat |Följande attribut ignoreras: *Namespace* |
| FunctionImport |Följande attribut ignoreras: *läge* (standardvärdet för ln förutsätts) |
| EntityType |Endast följande sub-noder ignoreras: *nyckeln* och *PropertyRef* |

Nedan beskrivs ändringar (lägga till och ignoreras element) till olika CSDL XML-noder i detalj.

## <a name="functionimport-node"></a>FunctionImport nod
En FunctionImport-nod representerar en URL (startpunkt) som Exponerar en tjänst till slutanvändaren. Noden tillåter som beskriver hur URL: en är adresserad, vilka parametrar som är tillgängliga för slutanvändaren och hur dessa parametrar har angetts.

Information om den här noden finns på [här][MSDNFunctionImportLink](https://msdn.microsoft.com/library/cc716710.aspx)

Följande är ytterligare attribut (eller tillägg till attribut) som visas av noden FunctionImport:

**d:BaseUri** -URI: N mall för REST-resurs som exponeras för Marketplace. Marketplace använder mallen för att skapa frågor mot REST-webbtjänst. URI-mall innehåller platshållare för parametrarna i form av {parameterName}, där parameterName är parameterns namn. T.ex. apiVersion = {apiVersion}.
Parametrar får förekomma som URI-parametrar eller som en del av URI-sökväg. Förekommer i sökvägen är de alltid obligatoriska (kan inte markeras som kan ha värdet null). *Exempel:*`d:BaseUri="http://api.MyWeb.com/Site/{url}/v1/visits?start={start}&amp;end={end}&amp;ApiKey=3fadcaa&amp;Format=XML"`

**Namnet** -namnet på den importerade funktionen.  Får inte vara samma som andra definierade namn i CSDL.  T.ex. Name = ”GetModelUsageFile”

**EntitySet** *(valfritt)* – om den här funktionen returnerar en mängd entitetstyper, värdet för den **EntitySet** måste entiteten anges som samlingen tillhör. I annat fall den **EntitySet** attributet får inte användas. *Exempel:*`EntitySet="GetUsageStatisticsEntitySet"`

**ReturnType** *(valfritt)* -anger vilken typ av element som returneras av URI: N.  Använd inte det här attributet om funktionen inte returnerar ett värde. Följande är typerna som stöds:

* **Samling (<Entity type name>)**: Anger en uppsättning definierade entitetstyper. Namnet finns namnattributet för noden EntityType. Ett exempel är en samling (WXC. HourlyResult).
* **Rådata (<mime type>)**: Anger en rå dokumentet/blob som returneras till användaren. Ett exempel är Raw(image/jpeg) andra exempel:

  * ReturnType="Raw(text/plain)”
  * ReturnType = ”samling (sage. DeleteAllUsageFilesEntity) ”*

**d:paging** – anger hur sidindelning ska hanteras av REST-resurs. Parametervärdena som används inom typografiska braches, t.ex. sidan = {$page} & itemsperpage = {$size} tillgängliga alternativ är:

* **Ingen:** inga växling är tillgänglig
* **Hoppa över:** sidindelning uttrycks via en logisk ”hoppa över” och ”ta” (överst). Hoppa över hwila M element och vidta Returnerar nästa N-element. Värdet för parametern: $skip
* **Vidta:** vidta Returnerar nästa N-element. Värdet för parametern: $take
* **PageSize:** sidindelning anges med en logisk sida och storlek (objekt per sida). Sidan representerar den aktuella sidan som returneras. Värdet för parametern: $page
* **Storlek:** storlek representerar antalet objekt som returneras för varje sida. Värdet för parametern: $size

**d:AllowedHttpMethods** *(valfritt)* – anger vilka verb hanteras av REST-resurs. Dessutom begränsar godkända verb till det angivna värdet.  Standard = POST.  *Exempel:* `d:AllowedHttpMethods="GET"` tillgängliga alternativ är:

* **GET:** används vanligtvis för att returnera data
* **POST:** används vanligtvis för att infoga ny data
* **PLACERA:** används vanligtvis för att uppdatera data
* **Ta bort:** används för att ta bort data

Ytterligare underordnade noder (som inte omfattas av CSDL-dokumentationen) under noden FunctionImport är:

**d:RequestBody** *(valfritt)* -begärandetexten används för att indikera att begäran förväntar sig en text som ska skickas. Parametrar kan anges i begärandetexten. De uttrycks inom klammerparenteser, t.ex. {parameterName}. Dessa parametrar mappas från användarindata i brödtexten som överförs till den innehållsleverantören-tjänsten. RequestBody-element har ett attribut med namnet httpMethod. Attributet tillåter två värden:

* **POST:** används om begäran är en HTTP POST
* **GET:** används om begäran är en HTTP GET

    Exempel:

        `<d:RequestBody d:httpMethod="POST">
        <![CDATA[
        <req1:Request xmlns:r1="http://schemas.mysite.com//generic/requests/1" Version="1.0">
        <req1:Query>{Query}</req1:Query><req1:AppId>D453474</req1:AppId>
        <req:DestinationSchemas><req1:Schema>Generic.RequestResponse[1.0]</req1:Schema>
        </req1:DestinationSchemas>
        </req1: Request>
        ]]>
        </d:RequestBody>`

**d:Namespaces** och **d:Namespace** -noden beskriver de namnområden som definierats i XML-filen som returneras av funktionsimporten (URI slutpunkten). XML som returneras av serverdelstjänsten kan innehålla valfritt antal namnområden att skilja mellan det innehåll som returneras. **Alla dessa namnområden om används i d:Map eller d:Match XPath-frågor måste anges.** Noden d:Namespaces innehåller en uppsättning/lista över d:Namespace noder. Var och en av dem innehåller en namnområdet som används i svaret på backend-tjänsten. Följande är attributet för noden d:Namespace:

* **d:prefix:** prefix för namnområdet, som visas i XML-resultaten som returnerades av tjänsten, t.ex. f:FirstName, f:LastName, där f är prefixet.
* **d:URI:** fullständig URI för namnområdet som används i resultat-dokument. Den representerar värdet som prefixet som matchas vid körning.

**d:ErrorHandling** *(valfritt)* -den här noden innehåller villkor för felhantering. Varje villkor verifieras mot det resultat som returneras av den innehållsleverantören-tjänsten. Om ett villkor matchar den föreslagna HTTP-felkoden returneras ett felmeddelande till slutanvändaren.

**d:ErrorHandling** *(valfritt)* och **d:Condition** *(valfritt)* -en condition-nod innehåller ett villkor som är markerad i resultatet som returneras av den innehållsleverantören service. Följande är de **krävs** attribut:

* **d:match:** ett XPath-uttryck som kontrollerar om det finns en viss nod/värde i innehållsleverantören utdata från XML. XPath körs mot utdata och ska returnera true om villkoret är en match eller false annars.
* **d:HttpStatusCode:** HTTP-statuskod som ska returneras av Marketplace om villkoret matchar. Marketplace signalizes felen till användare via HTTP-statuskoder. En lista över HTTP-statuskoder finns på http://en.wikipedia.org/wiki/HTTP_status_code
* **d:errorMessage:** felmeddelandet som returneras – med HTTP-statuskod – till slutanvändaren. Detta bör vara ett eget felmeddelande som inte innehåller några hemligheter.

**d:Title** *(valfritt)* -tillåter som beskriver titeln på funktionen. Värdet för rubriken som kommer från

* Valfria kartan attributet (en xpath) som anger var man hittar rubriken i svaret som returnerades från tjänstbegäran.
* - Eller - rubrik som angetts som värde för noden.

**d:Rights** *(valfritt)* -rättigheter (t.ex. copyright) som är associerade med funktionen. Värdet för rättigheterna som kommer från:

* Valfria kartan attributet (en xpath) som anger var du hittar rättigheterna i svaret som returnerades från tjänstbegäran.
* - Eller - rättigheter som anges som värde för noden.

**d:Description** *(valfritt)* – en kort beskrivning av funktionen. Värdet för beskrivningen kommer från

* Valfria kartan attributet (en xpath) som anger var man hittar beskrivningen i svaret som returnerades från tjänstbegäran.
* - Eller – beskrivning som angetts som värde för noden.

**d:EmitSelfLink** - *finns ovanför exempel ”FunctionImport för sidindelning om du via returnerade data”*

**d:EncodeParameterValue** -valfritt tillägg för OData

**d:QueryResourceCost** -valfritt tillägg för OData

**d:Map** -valfritt tillägg för OData

**d:headers** -valfritt tillägg för OData

**d:headers** -valfritt tillägg för OData

**d:value** -valfritt tillägg för OData

**d:HttpStatusCode** -valfritt tillägg för OData

**d:errorMessage** -valfritt tillägg för OData

## <a name="parameter-node"></a>Parametern nod
Den här noden representerar en parameter som visas som en del av URI-mall / begärandetext som har angetts i noden FunctionImport.

En användbar information dokumentsidan om noden ”parameterelement” påträffades vid [här](http://msdn.microsoft.com/library/ee473431.aspx) (Använd den **andra Version** listrutan och välj en annan version om du vill visa dokumentationen). *Exempel:*`<Parameter Name="Query" Nullable="false" Mode="In" Type="String" d:Description="Query" d:SampleValues="Rudy Duck" d:EncodeParameterValue="true" MaxLength="255" FixedLength="false" Unicode="false" annotation:StoreGeneratedPattern="Identity"/>`

| Parameterattributet | Krävs | Värde |
| --- | --- | --- |
| Namn |Ja |Parameterns namn. Skiftlägeskänsliga!  BaseUri stämmer. **Exempel:**`<Property Name="IsDormant" Type="Byte" />` |
| Typ |Ja |Parametertypen. Värdet måste vara ett **EDMSimpleType** eller en komplex typ som är inom omfånget för modellen. Mer information finns i ”6 stöds Parameter/egenskap types”.  (Skiftlägeskänslig! Första tecken är versaler, resten är gemen.)  Också se [konceptuella modellen typer (CSDL)][MSDNParameterLink](http://msdn.microsoft.com/library/bb399548.aspx). **Exempel:**`<Property Name="LimitedPartnershipID " Type="Int32" />` |
| Läge |Nej |**I**, Out eller InOut beroende på om parametern är ett indata eller utdata i/o-parametern. (Endast ”i” är tillgängliga i Azure Marketplace.) **Exempel:**`<Parameter Name="StudentID" Mode="In" Type="Int32" />` |
| maxLength |Nej |Största tillåtna längden på parametern. **Exempel:**`<Property Name="URI" Type="String" MaxLength="100" FixedLength="false" Unicode="false" />` |
| Precision |Nej |Precisionen för parametern. **Exempel:**`<Property Name="PreviousDate" Type="DateTime" Precision="0" />` |
| Skala |Nej |Skalan för parametern. **Exempel:**`<Property Name="SICCode" Type="Decimal" Precision="10" Scale="0" />` |

Följande är de attribut som har lagts till i CSDL-specifikationen:

| Parameterattributet | Beskrivning |
| --- | --- |
| **d:Regex** *(valfritt)* |En regex-uttrycket som används för att verifiera indatavärde för parametern. Om indatavärdet inte matchar instruktionen avvisas värdet. Detta gör att du även ange en uppsättning värden, t.ex. ^ [0-9] +? $ att endast tillåta siffror. **Exempel:** ' < parameternamn = ”name” läge = ”i” Type = ”sträng” d: kan ha värdet null = ”false” d:Regex = ”^ [a-öA-Ö] * $” d:Description = ”ett namn som inte får innehålla blanksteg eller icke-engelska tecken icke-alfanumeriska” d:SampleValues = ”George |
| **d:enum** *(valfritt)* |En pipe avgränsade lista med värden som är giltigt för parametern. Typ av värden måste matcha den definierade typen för parametern. Exempel: ' engelska |
| **d: kan ha värdet null** *(valfritt)* |Kan du definiera om en parameter kan vara null. Standardvärdet är: SANT. Dock får inte parametrar som visas som en del av sökvägen i URI-mall vara null. När attributet är inställt på false för dessa parametrar – åsidosätts indata från användaren. **Exempel:**`<Parameter Name="BikeType" Type="String" Mode="In" Nullable="false"/>` |
| **d:SampleValue** *(valfritt)* |Ett exempelvärde ska visas som en kommentar till klienten i Användargränssnittet.  Det är möjligt att lägga till flera värden med hjälp av en pipe avgränsade lista, d.v.s. ' en |

## <a name="entitytype-node"></a>EntityType-nod
Den här noden representerar en av de typer som returneras från Marketplace för slutanvändaren. Den innehåller också mappningen från utdata som returneras av den innehållsleverantören service till de värden som returneras till slutanvändaren.

Information om den här noden finns på [här](http://msdn.microsoft.com/library/bb399206.aspx) (Använd den **andra Version** listrutan och välj en annan version om du vill visa i dokumentationen.)

| Attributets namn | Krävs | Värde |
| --- | --- | --- |
| Namn |Ja |Namnet på entitetstypen. **Exempel:**`<EntityType Name="ListOfAllEntities" d:Map="//EntityModel">` |
| BaseType |Nej |Namnet på en annan enhetstyp som är bastypen för entitetstypen som definieras. **Exempel:**`<EntityType Name="PhoneRecord" BaseType="dqs:RequestRecord">` |

Följande är de attribut som har lagts till i CSDL-specifikationen:

**d:Map** -ett XPath-uttryck som körs mot service-utdata. Här antas att tjänsten utdata innehåller en uppsättning element som upprepas, som en ATOM-feed där det finns en uppsättning post noder som upprepas. Var och en av dessa upprepande noder innehåller en post. XPath anges sedan så att den pekar på den enskilda upprepade noden i leverantörens service resultat som innehåller värdena för en enskild post. Exempel utdata från tjänsten:

        `<foo>
          <bar> … content … </bar>
          <bar> … content … </bar>
          <bar> … content … </bar>
        </foo>`

XPath-uttrycket skulle vara /foo/stapel eftersom varje stapelns noden är den upprepande noden i utdata och innehåller det faktiska innehållet som returneras till slutanvändaren.

**Nyckeln** – det här attributet ignoreras av Marketplace. REST-baserat webbtjänsterna, exponera i allmänhet inte en primärnyckel.

## <a name="property-node"></a>Egenskapsnod
Den här noden innehåller en egenskap i posten.

Information om den här noden finns på [http://msdn.microsoft.com/library/bb399546.aspx](http://msdn.microsoft.com/library/bb399546.aspx) (Använd den **andra Version** listrutan och välj en annan version om du vill visa i dokumentationen.) *Exempel:*`<EntityType Name="MetaDataEntityType" d:Map="/MyXMLPath">
        <Property Name="Name"     Type="String" Nullable="true" d:Map="./Service/Name" d:IsPrimaryKey="true" DefaultValue=”Joe Doh” MaxLength="25" FixedLength="true" />
        ...
        </EntityType>`

| AttributeName | Krävs | Värde |
| --- | --- | --- |
| Namn |Ja |Namnet på egenskapen. |
| Typ |Ja |Typ av egenskapens värde. Egenskapsvärdetypen måste vara en **EDMSimpleType** eller en komplex typ (identifieras med ett fullständigt kvalificerat namn) som ligger inom omfånget för modellen. Mer information finns i konceptuella modellen typer (CSDL). |
| Kan ha värdet null |Nej |**SANT** (standardvärdet) eller **FALSKT** beroende på om egenskapen kan ha värdet null. Obs: I versionen av CSDL som anges av den [http://schemas.microsoft.com/ado/2006/04/edm](http://schemas.microsoft.com/ado/2006/04/edm) namnområde, en komplex typ-egenskap måste ha Nullable = ”False”. |
| Standardvärde |Nej |Standardvärdet för egenskapen. |
| maxLength |Nej |Maximal längd på egenskapens värde. |
| Multisträng med fast |Nej |**SANT** eller **FALSKT** beroende på om egenskapsvärdet lagras som en sträng med längden fiexed. |
| Precision |Nej |Refererar till det maximala antalet siffror som ska kvarhållas i det numeriska värdet. |
| Skala |Nej |Maximalt antal decimaler som ska kvarhållas i det numeriska värdet. |
| Unicode |Nej |**SANT** eller **FALSKT** beroende på om egenskapens värde vara lagras som en Unicode-sträng. |
| Sortering |Nej |En sträng som anger sorteringsordningen som ska användas i datakällan. |
| ConcurrencyMode |Nej |**Ingen** (standardvärdet) eller **fast**. Om värdet anges till **fast**, egenskapens värde som ska användas i Optimistisk samtidighet kontroller. |

Följande är ytterligare attribut som har lagts till i CSDL-specifikationen:

**d:Map** -XPath-uttryck som körs mot tjänsten utdata och extraherar en egenskap för utdata. XPath som angetts är i förhållande till den upprepade noden som har valts i noden EntityType XPath. Det är också möjligt att ange en absolut XPath för att tillåta inklusive en statisk resurs i varje nod utdata, som till exempel en upphovsrätt som endast finns en gång i den ursprungliga tjänsten utdata, men ska finnas i var och en av raderna i OData-utdata. Exempel från tjänsten:

        `<foo>
          <bar>
           <baz0>… value …</baz0>
           <baz1>… value …</baz1>
           <baz2>… value …</baz2>
          </bar>
        </foo>`

Det här XPath-uttrycket är ./bar/baz0 att hämta noden baz0 från leverantörens service.

**d:CharMaxLength** -för strängtyp, kan du ange den maximala längden. Se DataService CSDL-exempel

**d:IsPrimaryKey** -anger om kolumnen är den primära nyckeln i tabellen/vyn. Se DataService CSDL exempel.

**d:isExposed** -anger om tabellschemat exponeras (vanligtvis true). Se DataService CSDL-exempel

**d:IsView** *(valfritt)* – SANT om detta baseras på en vy i stället för en tabell.  Se DataService CSDL-exempel

**d:Tableschema** -Se DataService CSDL-exempel

**d:ColumnName** -är namnet på kolumnen i tabellen/vyn.  Se DataService CSDL-exempel

**d:IsReturned** -är den booleskt värde som anger om tjänsten visar det här värdet till klienten.  Se DataService CSDL-exempel

**d:IsQueryable** -är den booleskt värde som anger om kolumnen kan användas i en databasfråga.   Se DataService CSDL-exempel

**d:OrdinalPosition** -är kolumnens numeriska position utseende, x, i tabellen eller vyn, där x är från 1 till antalet kolumner i tabellen.  Se DataService CSDL-exempel

**d:DatabaseDataType** -är datatypen för kolumnen i databasen, d.v.s. SQL-datatypen. Se DataService CSDL-exempel

## <a name="supported-parametersproperty-types"></a>Typer stöds parametrar-egenskap
Följande är typerna som stöds för parametrar och egenskaper. (Skiftlägeskänslig)

| Primitiva typer | Beskrivning |
| --- | --- |
| Null |Representerar frånvaron av ett värde |
| Booleskt värde |Representerar matematiska begreppet binary-valued logik |
| Mottagna byte |Värdet 8-bitars heltal utan tecken |
| Datum och tid |Representerar datum och tid med värden mellan 12:00:00 midnatt 1 januari, 1753 e. kr. via 11:59:59 P.M, December 9999 e. kr. |
| Decimal |Representerar numeriska värden med fast precision och skala. Den här typen kan beskriva ett numeriskt värde mellan negativa 10 ^ 255 + 1-positivt 10 ^ 255 -1 |
| dubbla |Representerar ett tal med 15 decimaler precision som kan representera värden med ungefärliga intervall på + 2, 23E-308 via + 1, 79E flyttalsvärden +308. **Använd Decimal på grund av Exel export problem** |
| Enskild |Representerar en flytande nummer med 7 siffror precision som kan representera värden med ungefärliga intervall på + 1, 18E-38 via + 3, 40E + 38 |
| GUID |Representerar ett värde för unik identifierare för 16 byte (128-bitars) |
| Int16 |Representerar ett signerat 16-bitars heltal |
| Int32 |Representerar ett signerat 32-bitars heltal |
| Int64 |Representerar ett signerat 64-bitars heltal |
| Sträng |Representerar fixed - eller variabel längd teckendata |

## <a name="see-also"></a>Se även
* Om du vill förstå den övergripande processen för OData-mappning och syfte, den här artikeln [mappning för OData-tjänsten](marketplace-publishing-data-service-creation-odata-mapping.md) att granska definitioner, strukturer och instruktioner.
* Om du vill granska exempel den här artikeln [mappa Data Service OData-exempel](marketplace-publishing-data-service-creation-odata-mapping-examples.md) att exempelkoden förstå och kodsyntax och på snabbmenyn.
* Om du vill återgå till den föreskrivna sökvägen för att publicera en datatjänst på Azure Marketplace, den här artikeln [Data Service publiceringsguide](marketplace-publishing-data-service-creation.md).
