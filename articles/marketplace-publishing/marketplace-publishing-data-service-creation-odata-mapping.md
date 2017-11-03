---
title: "Guide för att skapa en datatjänst för Marketplace | Microsoft Docs"
description: "Detaljerade anvisningar för hur du skapar, certifiera och distribuera en Data-tjänst för att köpa på Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 3a632825-db5b-49ec-98bd-887138798bc4
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: a853b4dbd1952ba4ea8ee68ea3ca98f588bb71a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="mapping-an-existing-web-service-to-odata-through-csdl"></a>Mappning av en befintlig webbtjänst till OData via CSDL
> [!IMPORTANT]
> **Just nu vi inte längre onboarding några nya Data Service-utgivare. Nya dataservices kommer inte godkännas för lista.** Om du har ett SaaS-affärsprogram som du vill publicera på AppSource hittar du mer information [här](https://appsource.microsoft.com/partners). Om du har en IaaS-program eller developer-tjänsten som du vill publicera på Azure Marketplace du hittar mer information [här](https://azure.microsoft.com/marketplace/programs/certified/).
> 
> 

Den här artikeln ger en översikt på hur du använder en CSDL för att mappa en befintlig tjänst till en kompatibel OData-tjänst. Den förklarar hur för att skapa mappning dokumentet (CSDL) som omvandlar en inkommande begäran från klienten via ett och utdata (data) tillbaka till klienten via en kompatibel OData-feed. Microsoft Azure Marketplace visar slutanvändarna-tjänster med hjälp av OData-protokollet. Tjänster som exponeras av innehållsleverantörer (dataägare) visas i flera olika former REST, SOAP, t.ex.

## <a name="what-is-a-csdl-and-its-structure"></a>Vad är en CSDL och dess struktur?
En CSDL (konceptuellt Schema Definition Language) är en specifikation som definierar hur att beskriva webbtjänst eller database-tjänsten i vanliga XML ordflöde på Azure Marketplace.

Enkel översikt över de **flödet för begäran:**

  `Client -> Azure Marketplace -> Content Provider’s Web Service (Get, Post, Delete, Put)`

Den **dataflöde** i motsatt riktning:

  `Client <- Azure Marketplace <- Content Provider’s WebService`

**Bild 1** diagram en klient skulle hur hämta data från en innehållsleverantör (din tjänst) genom att gå via Azure Marketplace.  CSDL används av komponenten mappning-transformering för att hantera begäran och data som skickas mellan den innehållsleverantören tjänster och den begärande klienten.

*Bild 1: Detaljerad flödar från begärande klienten till innehållsleverantören via Azure Marketplace*

  ![Rita](media/marketplace-publishing-data-service-creation-odata-mapping/figure-1.png)

För bakgrundsinformation om Atom Atom Pub och OData-protokollet som skapar Azure Marketplace-tillägg, hittar: [http://msdn.microsoft.com/library/ff478141.aspx](http://msdn.microsoft.com/library/ff478141.aspx)

Utdrag ovan länk: *”syftet med den Open Data protocol (kallas nedan OData) är att tillhandahålla ett REST-baserat protokoll för CRUD-format åtgärder (skapa, läsa, uppdatera och ta bort) mot resurser visas som data services. En ”datatjänst” är en slutpunkt har angett namn / värde-par där det finns data exponeras från en eller flera ”samlingar” varje med noll eller flera ”poster”, som består av. OData har publicerats av Microsoft under OASIS (organisation för framflyttning för strukturerade Information standarden) standarder så att vem som helst som vill skapa servrar, klienter eller verktyg utan royalty eller begränsningar ”.*

### <a name="three-critical-pieces-that-have-to-be-defined-by-the-csdl-are"></a>Tre viktiga uppgifter som ska definieras av CSDL är:
* Den **endpoint** för Service Provider i Web adress (URI) för tjänsten
* Den **dataparametrar** som skickas som indata till tjänstprovidern definitionerna av de parametrar som skickas till den innehållsleverantören service till datatypen.
* **Schemat** av data som returneras till tjänsten begär schemat för de data som skickas av den innehållsleverantören tjänsten, inklusive behållare, samlingar-tabeller, variabler eller kolumner och datatyper.

Följande diagram visar en översikt över flödet från där klienten anger OData-uttryck (anrop till innehåll provider-webbtjänsten) att få tillbaka Resultatdata.

  ![Rita](media/marketplace-publishing-data-service-creation-odata-mapping/figure-2.png)

### <a name="steps"></a>Så här:
1. Klienten skickar begäran via tjänsten anropet slutfördes med indataparametrar som definierats i XML-filen på Azure Marketplace
2. CSDL används för att verifiera anropet till tjänsten.
   * Tjänsten formaterad anropa sedan skickas till tjänsten innehåll Providers av Azure Marketplace
3. Webbtjänsten som körs och preforms för Http-verbet (d.v.s. får) data returneras till Azure Marketplace där den begärda informationen (eventuella) är visar i XML-Format till klienten med hjälp av mappningen som definierats i CSDL.
4. Klienten skickas data (om sådan finns) i XML- eller JSON-format

## <a name="definitions"></a>Definitioner
### <a name="odata-atom-pub"></a>OData-ATOM pub
Ange ett tillägg till den ATOM pub där varje post representerar en rad med ett resultat. Innehåll tillhör transaktionen är utökat för att innehålla värdena på raden – som nyckel/värde-par. Mer information finns här: [https://www.odata.org/documentation/odata-version-3-0/atom-format/](https://www.odata.org/documentation/odata-version-3-0/atom-format/)

### <a name="csdl---conceptual-schema-definition-language"></a>CSDL - Conceptual Schema Definition Language
Här kan du definiera funktioner (SPROCs) och de entiteter som är tillgängliga via en databas. Mer information finns här: [http://msdn.microsoft.com/library/bb399292.aspx](http://msdn.microsoft.com/library/bb399292.aspx)  

> [!TIP]
> Klicka på den **andra versioner** listrutan och väljer en version om du inte ser artikeln.
> 
> 

### <a name="edm---entry-data-model"></a>EDM - post-datamodell
* Översikt: [http://msdn.microsoft.com/library/vstudio/ee382825 (v=vs.100).aspx][OverviewLink]

[OverviewLink]:http://msdn.microsoft.com/library/vstudio/ee382825(v=vs.100).aspx
* Förhandsversion: [http://msdn.microsoft.com/library/aa697428 (v=vs.80).aspx][PreviewLink]

[PreviewLink]:http://msdn.microsoft.com/library/aa697428(v=vs.80).aspx
* Datatyper: [http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx][DataTypesLink]

[DataTypesLink]:http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx

I följande visas detaljerad vänster till höger som flödar från där klienten anger OData-uttryck (anrop till innehåll provider-webbtjänsten) att hämta Resultatdata tillbaka:

  ![Rita](media/marketplace-publishing-data-service-creation-odata-mapping/figure-3.png)

## <a name="csdl-basics"></a>CSDL-grunderna
En CSDL (konceptuellt Schema Definition Language) är en specifikation som definierar hur att beskriva webbtjänst eller database-tjänsten i vanliga XML ordflöde på Azure Marketplace. CSDL beskriver de viktiga delar som **möjliggör överföring av data från datakällan på Azure Marketplace.** Här beskrivs de viktigaste delarna:

* Gränssnittsinformation som beskriver alla offentligt tillgängliga funktioner (FunctionImport nod)
* Information om datatyp för alla requests(input) meddelandet och meddelandet responses(outputs) (EntityType-EntityContainer/EntitySet noder)
* Bindningsinformation om transportprotokollet ska användas (sidhuvud nod)
* Information om för att hitta den angivna tjänsten (BaseURI attribut)

I kort sagt kan representerar CSDL ett kontrakt och språk-plattformsoberoende mellan service begärande och tjänstleverantören. Med CSDL kan en klient Leta upp en webbtjänst för service-databasen och anropa en av dess offentligt tillgängliga funktioner.

### <a name="relating-a-csdl-to-a-database-or-a-collection"></a>Om en CSDL till en databas eller en samling
**Specifikationen CSDL**

CSDL är XML-grammatiken för att beskriva en webbtjänst. Specifikationen själva är uppdelad i 4 viktiga element: EntitySet, FunctionImport; Namnområdet och EntityType.

Att göra denna framställning lättare att förstå kan en CSDL avser en tabell.

Kom ihåg;

  CSDL representerar en plattform och språkoberoende avtal mellan den **service begärande** och **tjänstleverantör**. Med hjälp av CSDL, en **klienten** kan hitta en **webbtjänsten tjänstdatabasen/** och anropa en av dess offentligt tillgängliga **funktioner.**

För en datatjänst kan fyra delar av en CSDL betraktas som en databas, tabeller, kolumner och Store-procedur.

Om dessa på följande sätt för en datatjänst:

* EntityContainer ~ = databas
* EntitySet ~ = tabell
* EntityType ~ = kolumner
* FunctionImport ~ = lagrad procedur

**HTTP-verb tillåts**

* – Returnerar värden hämtas från db (returnerar en mängd)
* POST – används för att skicka data till och valfria returvärden från db (skapa en ny post i samlingen, return-id eller-URI)
* Ta bort – tar bort data från DB (tar bort en samling)
* PLACERA – uppdatera data i en DB (ersätta en samling eller skapa ett)

## <a name="metadatamapping-document"></a>Metadata-mappning dokumentet
Metadata-mappning dokumentet används för att mappa en innehållsleverantör befintliga webbtjänster så att den kan visas som en OData-webbtjänst i Azure Marketplace-systemet. Den är baserad på CSDL och implementerar några tillägg till CSDL till passar REST baserat webbtjänsterna som exponeras via Azure Marketplace. Tillägg finns i den [http://schemas.microsoft.com/dallas/2010/04](http://schemas.microsoft.com/dallas/2010/04) namnområde.

Ett exempel på CSDL följer: (kopiera och klistra in den nedan exempel CSDL till en XML-redigerare och ändrar så att den matchar din tjänst.  Klistra in i CSDL mappning DataService fliken när du skapar din tjänst i den [Azure Marketplace Publishing Portal](https://publish.windowsazure.com)).

**Villkor:** om CSDL villkor till den [Publiceringsportal](https://publish.windowsazure.com) Användargränssnittet (PPUI) villkor.

* Erbjuder ”Title” i PPUI relaterar till MyWebOffer
* Mitt företag i PPUI avser **Publisher visningsnamn** i den [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) UI
* Din API är kopplat till en webbplats eller en Data-tjänsten (en Plan i PPUI)

**Hierarki:** ett företag (innehållsleverantören) äger erbjudanden som har plan eller de planer, nämligen service(s) vilken rad upp med ett API.

### <a name="webservice-csdl-example"></a>Webbtjänsten CSDL-exempel
Ansluter till en tjänst som visar en web application slutpunkt (t.ex. en C#-program)

        <?xml version="1.0" encoding="utf-8"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyWebOffer" Alias="MyOffer" xmlns="http://schemas.microsoft.com/ado/2009/08/edm" xmlns:d="http://schemas.microsoft.com/dallas/2010/04" >
        <!-- EntityContainer groups all the web service calls together into a single offering. Every web service call has a FunctionImport definition. -->
          <EntityContainer Name="MyOffer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        @Name is used as reference by FunctionImport @EntitySet. And is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition near the bottom of this file. -->
            <EntitySet Name="MyEntities" EntityType="MyOffer.MyEntityType" />
        <!-- Add a FunctionImport for every service method. Multiple FunctionImports can share a single return type (EntityType). -->
        <!-- ReturnType is either Raw() for a stream or Collection() for an Atom feed. Ex. of Raw: ReturnType=”Raw(text/plain)” -->
        <!—EntitySet is the entityset defined above, and is needed if ReturnType is not Raw -->
        <!-- BaseURI attribute defines the service call, replace & with the encode value (&amp;).
        In the input name value pairs {param} represents passed in value.
        Or the value can be hard coded as with AccountKey. -->
        <!-- AllowedHttpMethods optional (default = “GET”), allows the CSDL to specifically specify the verb of the service, “Get”, “Post”, “Put”, or “Delete”. -->
        <!-- EncodeParameterValues, True encodes the parameter values, false does not. -->
        <!-- BaseURI is translated into an URITemplate which defines how the web service call is exposed to marketplace customers.
        Ex. https://api.datamarket.azure.com/mycompany/MyOfferPlan?name={name}
        BaseURI is XML encoded, the {...} point to the parameters defined below.
        Marketplace will read the parameters from this URITemplate and fill the values into the corresponding parameters of the BaseUri or RequestBody (below) during calls to your service.  
        It is okay for @d:BaseUri to include information only for Marketplace consumption, it will not be exposed to end users. i.e. the hardcoded AccountKey in the above BaseURI does not show up in the client facing URITemplate. -->
            <FunctionImport Name="MyWebServiceMethod"
                            EntitySet="MyEntities"
                            ReturnType="Collection(MyOffer.MyEntityType)"
        d:AllowedHttpMethods="GET"
        d:EncodeParameterValues="true"
        d:BaseUri="http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <!-- Definition of the RequestBody is only required for HTTP POST requests and is optional for HTTP GET requests. -->
        <d:RequestBody d:httpMethod="POST">
                <!-- Use {} for placeholders to insert parameters. -->
                <!-- This example uses SOAP formatting, but any POST body can be used. -->
            <!-- This example shows how to pass userid and password via the header -->
                <![CDATA[<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:MyOffer="http://services.organization.net/MyServicePath">
                  <soapenv:Header/>
                  <soapenv:Body>
                    <MyOffer:ws_MyWebServiceMethod>
                      <myWebServiceMethodRequest>
                        <!--This information is not exposed to end users. -->
                        <UserId>userid</UserId>
                        <Password>password</Password>
                        <!-- {name} is replaced with the value read from @d:UriTemplate above -->
                        <Name>{name}</Name>
                        <!-- Parameters can be used more than once and are not limited to appearing as the value of an element -->
                        <CustomField Name="{name}" />
                        <MyField>Static content</MyField>
                      </myWebServiceMethodRequest>
                    </MyOffer:ws_MyWebServiceMethod>
                  </soapenv:Body>
                </soapenv:Envelope>      
              ]]>
        </d:RequestBody>
        <!-- Title, Rights and Description are optional and used to specify values to insert into the ATOM feed returned to the end user.  You can specify the element to contain a fixed message by providing a value for the element (this is the default value).  @d:Map is an XPath expression that points into the response returned by your service and is optional.  -->
        <d:Title d:Map="/MyResponse/Title">Default title.</d:Title>
        <d:Rights>© My copyright. This is a fixed response. It is okay to also add a d:Map attribute to override this text.</d:Rights>
        <d:Description d:Map="/MyResponse/Description"></d:Description>
        <d:Namespaces>
        <d:Namespace d:Prefix="p"  d:Uri="http://schemas.organization.net/2010/04/myNamespace" />
        <d:Namespace d:Prefix="p2" d:Uri="http://schemas.organization.net/2010/04/MyNamespace2" />
        </d:Namespaces>
        <!-- Parameters of the web service call:
        @Name should match exactly (case sensitive) the {…} placeholders in the @d:BaseUri, @d:UriTemplate, and d:RequestBody, i.e. “name” parameter in above BaseURI.
        @Mode is always "In", compatibility with CSDL
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx
        @d:Nullable indicates whether the parameter is required.
        @d:Regex - optional, attribute to describe the string, limiting unwanted input at the entry of the system
        @d:Description - optional, is used by Service Explorer as help information
        @d:SampleValues - optional, is used by Service Explorer as help information. Multiple Sample values are separated by '|', e.g. "804735132|234534224|23409823234"
        @d:Enum - optional for string type. Contains an enumeration of possible values separated by a '|', e.g. d:enum="english|metric|raw". Will be converted in a dropdown list in the Service Explorer.
        -->
        <Parameter name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters"
        d:Enum="George|John|Thomas|James"
        d:SampleValues="George"/>
        <Parameter Name=" AccountKey" Mode="In" Type="String" d:Nullable="false" />

        <!-- d:ErrorHandling is an optional element. Use it define standardized errors by evaluating the service response. -->
        <d:ErrorHandling>
        <!-- Any number of d:Condition elements are allowed, they are evaluated in the order listed.
        @d:Match is an Xpath query on the service response, it should return true or false where true indicates an error.
        @d:httpStatusCode is the error code to return if an response matches the error.
        @d:errorMessage is the user friendly message to return when an error occurs.
        -->
        <d:Condition d:Match="/Result/ErrorMessage[text()='Invalid token']" d:HttpStatusCode="403" d:ErrorMessage="User cannot connect to the service." />
        </d:ErrorHandling>
           </FunctionImport>

            <!-- The EntityContainer defines the output data schema -->
        </EntityContainer>
        <!-- The EntityType @d:Map defines the repeating node (an XPath query) in the response (output data schema). -->
        <!-- If these nodes are outside a namespace, add the prefix in the xpath. -->
        <!--
        @Name - define your user readable name, will become an XML element in the ATOM feed, so comply with the XML element naming restrictions (no spaces or other illegal characters).
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx.
        @d:Map uses an Xpath query to point at the location to extract the content from your services response.
        The "." is relative to the repeating node in the EntityType @d:Map Xpath expression.
        -->
            <EntityType Name="MyEntityType" d:Map="/MyResponse/MyEntities">
        <Property Name="ID"    d:IsPrimaryKey="True" Type="Int32"    Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="Amount"    Type="Double"    Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="City"    Type="String"    Nullable="false" d:Map="./City"/>
        <Property Name="State"    Type="String"    Nullable="false" d:Map="./State"/>
        <Property Name="Zip"    Type="Int32"    Nullable="false" d:Map="./Zip"/>
        <Property Name="Updated"    Type="DateTime"    Nullable="false" d:Map="./Updated"/>
        <Property Name="AdditionalInfo" Type="String" Nullable="true"
        d:Map="./Info/More[1]"/>
            </EntityType>
        </Schema>

> [!TIP]
> Visa fler CSDL Web Service-exempel i artikeln [exempel för att matcha en befintlig webbtjänst för OData via CSDLs](marketplace-publishing-data-service-creation-odata-mapping-examples.md)
> 
> 

### <a name="dataservice-csdl-example"></a>DataService CSDL-exempel
Ansluter till en tjänst som Exponerar en databastabell eller vy som en slutpunkt som exemplet nedan visar två API: erna för grundläggande Data baserat API CSDL (kan använda vyer i stället för tabeller).

        <?xml version="1.0"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyDataOffer" Alias="MyOffer" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ado/2009/08/edm">
        <!-- EntityContainer groups all the data service calls together into a single offering. Every web service call has a FunctionImport definition. -->
        <EntityContainer Name="MyOfferContainer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
            Think of the EntitySet as a Service
        @Name is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition (returned set of table columns). -->
        <EntitySet Name="CompanyInfoEntitySet" EntityType="MyOffer.CompanyInfo" />
        <EntitySet Name="ProductInfoEntitySet" EntityType="MyOffer.ProductInfo" />
        </EntityContainer>
        <!-- EntityType defines result (output); the table (or view) and columns to be returned by the data service.)
            Map is the schema.tabel or schema.view
            dals.TableName is the table Name
            Name is the name identifier for the EntityType and the Name of the service exposed to the client via the UI.
            dals:IsExposed determines if the table schema is exposed (generally true).
            dals:IsView (optional) true if this is based on a view rather than a table
            dals:TableSchema is the schema name of the table/view
        -->
        <EntityType
        Map="[dbo].[CompanyInfo]"
        dals:TableName="CompanyInfo"
        Name="CompanyInfo"
        dals:IsExposed="true"
        dals:IsView="false"
        dals:TableSchema="dbo"
        xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <!-- Property defines the column properties and the output of the service.
            dals:ColumnName is the name of the column in the table /view.
            Type is the emd.SimpleType
            Nullable determines if NULL is a valid output value
            dals.CharMaxLenght is the maximum length of the output value
            Name is the name of the Property and is exposed to the client facing UI
            dals:IsReturned is the Boolean that determines if the Service exposes this value to the client.
            IsQueryable is the Boolean that determines if the column can be used in a database query
            (For data Services: To improve Performance make sure that columns marked ISQueryable=”true” are in an index.)
            dals:OrdinalPosition is the numerical position x in the table or the View, where x is from 1 to the number of columns in the table.
            dals:DatabaseDataType is the data type of the column in the database, i.e. SQL data type dals:IsPrimaryKey indicates if the column is the Primary key in the table/view.  (The columns marked ISPrimaryKey are used in the Order by clause when returning data.)
        -->
        <Property dals:ColumnName="data" Type="String" Nullable="true" dals:CharMaxLength="-1" Name="data" dals:IsReturned="true" dals:IsQueryable="false" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="ticker" Type="String" Nullable="true" dals:CharMaxLength="10" Name="ticker" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        <EntityType Map="[dbo].[ProductInfo]" dals:TableName="ProductInfo" Name="ProductInfo" dals:IsExposed="true" dals:IsView="false" dals:TableSchema="dbo" xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <Property dals:ColumnName="companyid" Type="Int32" Nullable="true" Name="companyid" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="product" Type="String" Nullable="true" dals:CharMaxLength="50" Name="product" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        </Schema>

## <a name="see-also"></a>Se även
* Om du är intresserad av att lära sig och förstå de specifika noderna och deras parametrar kan den här artikeln [mappa Data Service OData-noder](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) definitioner och förklaringar, exempel och Använd case kontext.
* Om du vill granska exempel den här artikeln [mappa Data Service OData-exempel](marketplace-publishing-data-service-creation-odata-mapping-examples.md) att exempelkoden förstå och kodsyntax och på snabbmenyn.
* Om du vill återgå till den föreskrivna sökvägen för att publicera en datatjänst på Azure Marketplace, den här artikeln [Data Service publiceringsguide](marketplace-publishing-data-service-creation.md).

