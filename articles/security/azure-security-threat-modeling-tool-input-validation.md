---
title: Ange verifiering – Microsoft Hotmodelleringsverktyg – Azure | Microsoft Docs
description: åtgärder för hot som exponeras i Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: da2e742f0dde0cb4b98bfb107d18eca779d10021
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234603"
---
# <a name="security-frame-input-validation--mitigations"></a>Security ram: Indatavalidering | Åtgärder 
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Webbprogram** | <ul><li>[Inaktivera XSLT-skript för alla transformeringar med obetrodda formatmallar](#disable-xslt)</li><li>[Se till att alla sidor som kan innehålla användare kontrolleras innehåll innebär att inga automatiska MIME-kontroll](#out-sniffing)</li><li>[Förstärka eller inaktivera XML-entitet upplösning](#xml-resolution)</li><li>[Program som använder http.sys utföra verifiering av URL-auktorisering](#app-verification)</li><li>[Kontrollera att lämpliga kontroller är uppfyllda när du tar emot filer från användare](#controls-users)</li><li>[Se till att typen safe parametrar används i webbprogram för dataåtkomst](#typesafe)</li><li>[Använda separata modellklasser för bindning eller bindningsfilter listor för att förhindra MVC drivrutiner för masslagring tilldelning säkerhetsproblem](#binding-mvc)</li><li>[Koda obetrodda webbutdata innan rendering](#rendering)</li><li>[Utföra verifiering av indata och filtrering på alla strängtyp modellegenskaper](#typemodel)</li><li>[Gemensamt ska tillämpas på formulärfält som godkänner alla tecken, t.ex., textredigeraren](#richtext)</li><li>[Tilldela inte DOM-element till mottagare som inte har inbyggd kodning](#inbuilt-encode)</li><li>[Kontrollera att alla omdirigeringar i programmet är stängd eller för att göra på ett säkert sätt](#redirect-safe)</li><li>[Implementera verifiering av indata på alla typ strängparametrar accepteras av kontrollantmetoder](#string-method)</li><li>[Ange tidsgränsen för övre gräns för reguljärt uttryck som används för att förhindra DoS på grund av felaktiga reguljära uttryck](#dos-expression)</li><li>[Undvik att använda Html.Raw i Razor-vyer](#html-razor)</li></ul> | 
| **Databas** | <ul><li>[Använd inte dynamiska frågor i lagrade procedurer](#stored-proc)</li></ul> |
| **Webb-API** | <ul><li>[Se till att modellen verifieras webb-API-metoder](#validation-api)</li><li>[Implementera verifiering av indata på alla typ strängparametrar accepteras av webb-API-metoder](#string-api)</li><li>[Se till att typen safe parametrar används i webb-API för dataåtkomst](#typesafe-api)</li></ul> | 
| **Azure Document DB** | <ul><li>[Använda SQL-frågor för innehåller parametrar för Azure Cosmos DB](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[WCF-indata verifiering via schemabindning](#schema-binding)</li><li>[Indata WCF - verifiering via parametern Inspectors](#parameters)</li></ul> |

## <a id="disable-xslt"></a>Inaktivera XSLT-skript för alla transformeringar med obetrodda formatmallar

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [XSLT-Security](https://msdn.microsoft.com/library/ms763800(v=vs.85).aspx), [XsltSettings.EnableScript egenskap](https://msdn.microsoft.com/library/system.xml.xsl.xsltsettings.enablescript.aspx) |
| **Steg** | XSLT har stöd för skript i formatmallar med hjälp av den `<msxml:script>` element. På så sätt kan anpassade funktioner som ska användas i en XSLT-transformation. Skriptet har körts under kontexten för hur du utför transformering. XSLT-skriptet måste inaktiveras när du är i en miljö som inte är betrodd för att förhindra att ej betrodd kod körs. *Om du använder .NET:* XSLT-skript är inaktiverade som standard, men du måste se till att det inte uttryckligen aktiveras via den `XsltSettings.EnableScript` egenskapen.|

### <a name="example"></a>Exempel 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Exempel
Om du använder MSXML 6.0 är XSLT-skript inaktiverat som standard. dock måste du kontrollera att det inte uttryckligen aktiveras via XML DOM-objektegenskapen AllowXsltScript. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Exempel
Om du använder MSXML 5 eller nedan, XSLT-skript är aktiverat som standard och du måste uttryckligen inaktivera den. Egenskapen XML DOM-objekt AllowXsltScript till false. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a id="out-sniffing"></a>Se till att alla sidor som kan innehålla användare kontrolleras innehåll innebär att inga automatiska MIME-kontroll

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [IE8 Security del V - heltäckande skydd](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx)  |
| **Steg** | <p>För varje sida som kan innehålla användare kontrolleras innehåll, måste du använda HTTP-huvudet `X-Content-Type-Options:nosniff`. Du kan skapa det begärda sidhuvudet sida för sida för bara de sidor som kan innehålla användare kontrolleras innehåll för att uppfylla detta krav, eller du kan ange den globalt för alla sidor i programmet.</p><p>Varje typ av filen som levereras från en webbserver med en associerad [MIME-typ](http://en.wikipedia.org/wiki/Mime_type) (kallas även en *innehållstyp*) som beskriver vilken typ av innehåll (det vill säga bild, text, program osv.)</p><p>Rubriken X-innehåll-typ-Options är ett HTTP-huvud som gör att utvecklare kan ange att innehållet inte ska vara MIME-någon lyssnar. Den här rubriken är utformad för att minska MIME-kontroll attacker. Stöd för den här rubriken har lagts till i Internet Explorer 8 (IE8)</p><p>Enbart användare av Internet Explorer 8 (IE8) kan dra nytta X-innehåll-typ-Options. Tidigare versioner av Internet Explorer FELNIVÅ för närvarande rubriken X-innehåll-typ-Options</p><p>Internet Explorer 8 (och senare) är de enda större webbläsarna att implementera en MIME-kontroll avstår funktion. Om andra större webbläsare (Firefox, Safari, Chrome) implementerar liknande funktioner, och när den här rekommendationen kommer att uppdateras för att inkludera syntaxen för de här webbläsarna samt</p>|

### <a name="example"></a>Exempel
Om du vill aktivera nödvändiga huvudet globalt för alla sidor i programmet, kan du göra något av följande: 

* Lägg till rubriken i web.config-filen om programmet körs genom att Internet Information Services (IIS) 7 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* Lägg till sidhuvud via globala programmet\_BeginRequest 

``` 
void Application_BeginRequest(object sender, EventArgs e)
{
  this.Response.Headers[""X-Content-Type-Options""] = ""nosniff"";
} 
```

* Implementera anpassade HTTP-modul 

``` 
public class XContentTypeOptionsModule : IHttpModule 
  {
    #region IHttpModule Members 
    public void Dispose() 
    { 

    } 
    public void Init(HttpApplication context)
    { 
      context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders); 
    } 
    #endregion 
    void context_PreSendRequestHeaders(object sender, EventArgs e) 
      { 
        HttpApplication application = sender as HttpApplication; 
        if (application == null) 
          return; 
        if (application.Response.Headers[""X-Content-Type-Options ""] != null) 
          return; 
        application.Response.Headers.Add(""X-Content-Type-Options "", ""nosniff""); 
      } 
  } 

``` 

* Du kan aktivera rubriken krävs endast för specifika sidor genom att lägga till enskilda svar: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a id="xml-resolution"></a>Förstärka eller inaktivera XML-entitet upplösning

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [XML-entitet Expansion](http://capec.mitre.org/data/definitions/197.html), [XML Denial of Service-attacker och försvar](https://msdn.microsoft.com/magazine/ee335713.aspx), [MSXML Säkerhetsöversikt](https://msdn.microsoft.com/library/ms754611(v=VS.85).aspx), [bästa praxis för att skydda MSXML kod](https://msdn.microsoft.com/library/ms759188(VS.85).aspx), [ Protokollreferens för NSXMLParserDelegate](http://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html), [lösa externa referenser](https://msdn.microsoft.com/library/5fcwybb2.aspx) |
| **Steg**| <p>Även om den inte används ofta, är det en funktion i XML som gör att XML-parsern för att expandera makrot entiteter med värden som definieras i själva dokumentet eller från externa källor. Till exempel dokumentet kan definiera en entitet ”företagsnamn” med värdet ”Microsoft”, så som varje gång texten ”&companyname;” visas i dokumentet ersätts automatiskt med texten Microsoft. Eller dokumentet kan definiera en entitet ”MSFTStock” som refererar till en extern webbtjänst för att hämta det aktuella värdet för Microsoft-lager.</p><p>Sedan när som helst ”&MSFTStock;” visas i dokumentet ersätts automatiskt med den aktuella aktiekursen. Den här funktionen kan dock missbrukas för att skapa denial of service (DoS) villkor. En angripare kan kapsla flera entiteter för att skapa en exponentiell expansion XML bomb som förbrukar allt tillgängligt minne i systemet. </p><p>Du kan också han kan skapa en extern referens som strömmas tillbaka obegränsade data eller som bara låser sig tråden. Alla team måste därför inaktivera interna eller externa XML-entitet lösning helt och hållet om sitt program inte använda den eller manuellt begränsar hur mycket minne och tid som programmet kan använda för matchning av entiteten om den här funktionen är är absolut nödvändigt. Om entitet lösning inte krävs av ditt program, inaktiverar du den. </p>|

### <a name="example"></a>Exempel
Du kan använda följande metoder för .NET Framework-kod:

```csharp
XmlTextReader reader = new XmlTextReader(stream);
reader.ProhibitDtd = true;

XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);

// for .NET 4
XmlReaderSettings settings = new XmlReaderSettings();
settings.DtdProcessing = DtdProcessing.Prohibit;
XmlReader reader = XmlReader.Create(stream, settings);
```
Observera att värdet som standard `ProhibitDtd` i `XmlReaderSettings` är true, men med `XmlTextReader` det är FALSKT. Om du använder XmlReaderSettings, du behöver inte ange ProhibitDtd till true uttryckligen, men det rekommenderas för säkerhet tänker några viktiga att du gör. Observera också att klassen XmlDocument gör entitet upplösning som standard. 

### <a name="example"></a>Exempel
Om du vill inaktivera entiteten lösning för XmlDocuments, använda den `XmlDocument.Load(XmlReader)` överbelasta metodens belastning och ange lämpliga egenskaper i argumentet XmlReader att inaktivera lösning, vilket visas i följande kod: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Exempel
Om det inte går att inaktivera entiteten lösning för ditt program, kan du ange egenskapen XmlReaderSettings.MaxCharactersFromEntities till ett rimligt värde efter programmets behov. Detta begränsar effekten av potentiella exponentiell expansion DoS-attacker. Följande kod innehåller ett exempel på den här metoden: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Exempel
Om du vill lösa infogade entiteter men gör inte behöver för att lösa externa entiteter, ange egenskapen XmlReaderSettings.XmlResolver till null. Exempel: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Observera att i MSXML6, ProhibitDTD har angetts till true (inaktivera DTD bearbetning) som standard. Det finns två XML-Parser som du kan använda för Apple OSX/iOS kod: NSXMLParser och libXML2. 

## <a id="app-verification"></a>Program som använder http.sys utföra verifiering av URL-auktorisering

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Alla program som använder http.sys bör följa dessa riktlinjer:</p><ul><li>Begränsa URL-längd till mer än 16 384 tecken (ASCII eller Unicode). Det här är den absolut högsta URL-längd baserat på standardinställningen för Internet Information Services (IIS) 6. Webbplatser bör sträva efter en kortare än detta om det är möjligt</li><li>Använda .NET Framework-fil i/o standardklasser (till exempel FileStream) som de drar nytta av regler för auktorisering i .NET-FX</li><li>Uttryckligen skapa en Tillåt-lista över kända filnamn</li><li>Avvisa uttryckligen kända filtyper som du inte kommer att fungera UrlScan avvisar: exe, bat cmd, com, htw, ida, idq, htr, idc, shtm-[l], stm, skrivare, ini, pol, dat filer</li><li>Fånga upp följande undantag:<ul><li>System.ArgumentException (för enhetsnamn)</li><li>System.NotSupportedException (för dataströmmar)</li><li>System.IO.FileNotFoundException (för ogiltig ESC filnamn)</li><li>System.IO.DirectoryNotFoundException (för ogiltig ESC kataloger)</li></ul></li><li>*Inte* anropa till Win32-fil I/O APIs. På en ogiltig URL utan problem returneras ett 400-fel till användaren och logga riktigt fel.</li></ul>|

## <a id="controls-users"></a>Kontrollera att lämpliga kontroller är uppfyllda när du tar emot filer från användare

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Obegränsad Filuppladdning](https://www.owasp.org/index.php/Unrestricted_File_Upload), [filen signatur-tabell](http://www.garykessler.net/library/file_sigs.html) |
| **Steg** | <p>Överförda filer representerar en betydande risk för program.</p><p>Det första steget i många attacker är att få lite kod i systemet att utsättas för angrepp. Angreppet måste sedan endast att hitta ett sätt att få den kod som körs. Med hjälp av en uppladdning kan angriparen åstadkomma det första steget. Konsekvenserna av obegränsad filöverföringen kan variera, inklusive ta över hela systemet, en överbelastade filsystem eller databasen, vidarebefordran attacker till backend-system och enkel defacement.</p><p>Det beror på hur programmet fungerar med den överförda filen och särskilt var den lagras. Server validering av filöverföringar saknas. Följande säkerhetsåtgärder bör implementeras för filöverföring funktioner:</p><ul><li>Tillägget filkontroll (endast en giltig uppsättning tillåtna filtyp ska accepteras)</li><li>Gränsen för maximal filstorlek</li><li>Bör inte att överföra filen till webroot; platsen måste vara en katalog på icke-systemenhet</li><li>Namngivningskonvention ska följas, så att namnet på överförda filen har vissa slumpmässighet för att förhindra filen skrivs över</li><li>Filer bör genomsökas efter ett virusskyddsprogram innan du skriver till disken</li><li>Se till att filnamnet och andra metadata (t.ex. filsökväg) verifieras för skadliga tecken</li><li>Formatet filsignatur ska kontrolleras för att förhindra användare från att ladda upp en masqueraded fil (t.ex. ladda upp en exe-fil genom att ändra tillägget till txt)</li></ul>| 

### <a name="example"></a>Exempel
Den sista punkten om signaturverifiering finns klassen nedan för mer information: 

```csharp
        private static Dictionary<string, List<byte[]>> fileSignature = new Dictionary<string, List<byte[]>>
                    {
                    { ".DOC", new List<byte[]> { new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 } } },
                    { ".DOCX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".PDF", new List<byte[]> { new byte[] { 0x25, 0x50, 0x44, 0x46 } } },
                    { ".ZIP", new List<byte[]> 
                                            {
                                              new byte[] { 0x50, 0x4B, 0x03, 0x04 },
                                              new byte[] { 0x50, 0x4B, 0x4C, 0x49, 0x54, 0x55 },
                                              new byte[] { 0x50, 0x4B, 0x53, 0x70, 0x58 },
                                              new byte[] { 0x50, 0x4B, 0x05, 0x06 },
                                              new byte[] { 0x50, 0x4B, 0x07, 0x08 },
                                              new byte[] { 0x57, 0x69, 0x6E, 0x5A, 0x69, 0x70 }
                                                }
                                            },
                    { ".PNG", new List<byte[]> { new byte[] { 0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A } } },
                    { ".JPG", new List<byte[]>
                                    {
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE1 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE8 }
                                    }
                                    },
                    { ".JPEG", new List<byte[]>
                                        { 
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 }
                                        }
                                        },
                    { ".XLS", new List<byte[]>
                                            {
                                              new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 },
                                              new byte[] { 0x09, 0x08, 0x10, 0x00, 0x00, 0x06, 0x05, 0x00 },
                                              new byte[] { 0xFD, 0xFF, 0xFF, 0xFF }
                                            }
                                            },
                    { ".XLSX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".GIF", new List<byte[]> { new byte[] { 0x47, 0x49, 0x46, 0x38 } } }
                };

        public static bool IsValidFileExtension(string fileName, byte[] fileData, byte[] allowedChars)
        {
            if (string.IsNullOrEmpty(fileName) || fileData == null || fileData.Length == 0)
            {
                return false;
            }

            bool flag = false;
            string ext = Path.GetExtension(fileName);
            if (string.IsNullOrEmpty(ext))
            {
                return false;
            }

            ext = ext.ToUpperInvariant();

            if (ext.Equals(".TXT") || ext.Equals(".CSV") || ext.Equals(".PRN"))
            {
                foreach (byte b in fileData)
                {
                    if (b > 0x7F)
                    {
                        if (allowedChars != null)
                        {
                            if (!allowedChars.Contains(b))
                            {
                                return false;
                            }
                        }
                        else
                        {
                            return false;
                        }
                    }
                }

                return true;
            }

            if (!fileSignature.ContainsKey(ext))
            {
                return true;
            }

            List<byte[]> sig = fileSignature[ext];
            foreach (byte[] b in sig)
            {
                var curFileSig = new byte[b.Length];
                Array.Copy(fileData, curFileSig, b.Length);
                if (curFileSig.SequenceEqual(b))
                {
                    flag = true;
                    break;
                }
            }

            return flag;
        }
```

## <a id="typesafe"></a>Se till att typen safe parametrar används i webbprogram för dataåtkomst

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Om du använder parametrar insamling, SQL behandlar är indata som ett literalvärde stället som körbar kod. Parametersamlingen kan användas för att genomdriva typ och längd begränsningar i indata. Värden som ligger utanför intervallet utlöser ett undantag. Om typ safe SQL-parametrar inte används, kan angripare att kunna köra inmatningsattacker som är inbäddade i ofiltrerade indata.</p><p>Använd säkra typparametrar när SQL-frågor för att undvika möjlig SQL-inmatningsattacker som kan uppstå med ofiltrerade indata. Du kan använda säkra typparametrar med lagrade procedurer och dynamiska SQL-uttryck. Parametrar behandlas som litterala värden av databasen och inte som körbar kod. Parametrar kontrolleras även om typ och längd.</p>|

### <a name="example"></a>Exempel 
Följande kod visar hur du använder säker typparametrar med SqlParameterCollection när du anropar en lagrad procedur. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
I ovanstående kodexempel får inte indatavärdet vara längre än 11 tecken. Om data inte stämmer överens med typen eller längd som anges av parametern, utlöser SqlParameter klassen ett undantag. 

## <a id="binding-mvc"></a>Använda separata modellklasser för bindning eller bindningsfilter listor för att förhindra MVC drivrutiner för masslagring tilldelning säkerhetsproblem

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | MVC5 MVC6 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Metadata-attribut](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.metadatatypeattribute), [offentliga nyckel Security säkerhetsproblem och minskning](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation), [fullständig handbok för lagringsenheter tilldelning i ASP.NET MVC](http://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [komma igång med EF med hjälp av MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Steg** | <ul><li>**När ska jag för att leta efter över bokföring sårbarheter? -** Över bokföring säkerhetsproblem kan uppstå helst du binda modellklasser från användarindata. Ramverk som MVC kan representera användardata i anpassad .NET-klasser, inklusive vanlig gamla CLR-objekt (POCOs). MVC fyller automatiskt dessa modellklasser med data från begäran, vilket ger en lämplig representation för att hantera indata från användaren. När dessa klasser innehålla egenskaper som inte ska ställas in av användaren, kan programmet vara utsatt för overpostingattacker, som tillåter användarkontroll av data som programmet aldrig avsett. Som bindningen för MVC-modellen, databasen åtkomst till tekniker, till exempel Objektrelationer/Mappningskomponenter som Entity Framework ofta även support via POCO-objekt som representerar databasdata. Dessa data modellklasser ger samma bekvämlighet i hanterar databasdata precis MVC med indata från användaren. Eftersom både MVC och databasen har stöd för liknande modeller som POCO objekt verkar det enkelt att återanvända samma klasser för båda. Den här metoden misslyckas att bevara problemseparering och det är ett vanligt område där oönskade egenskaper exponeras för modell-bindningen, aktiverar över bokföring attacker.</li><li>**Varför bör inte jag använda min ofiltrerade databasen modellklasser som parametrar till min MVC-åtgärder? -** Eftersom MVC-modellen bindningen ska bindas till något som. Även om data inte visas i vyn, en obehörig användare kan skicka en HTTP-begäran med dessa data som ingår och MVC Binder gladeligen det eftersom åtgärden säger att databasklass är formen på data som den ska acceptera för indata från användaren.</li><li>**Varför bör jag bryr dig om den form som används för modellen bindning? -** Med hjälp av ASP.NET MVC-modellen bindning med breda modeller exponerar ett program till overpostingattacker. Överdrivet bokföring kan göra att angripare ändra programdata utöver vilka utvecklaren avsedd, till exempel åsidosätta priset för ett objekt eller behörighet för ett konto. Program ska använda åtgärden-specifika bindning modeller (eller specifika tillåtna filter egenskapslistor) Ange en explicit kontrakt för vilka obetrodda indata för att tillåta via modellen bindning.</li><li>**Är att ha en separat bindning modeller bara duplicera koden? -** Nej, det är bara några inkapsling av problem. Om du återanvänder databasmodeller i åtgärdsmetoder, innebär det att en egenskap (eller underordnade egenskapen) i den klass som kan ställas in av användaren i en HTTP-begäran. Om så är du inte vill MVC att göra, behöver du en filterlista eller formen separat klass för att visa MVC vilka data som kan komma från användarindata i stället.</li><li>**Om jag har en separat bindning modeller för indata från användaren, måste jag Duplicera alla mina anteckning dataattribut? -** Inte nödvändigtvis. Du kan använda MetadataTypeAttribute på modellklass för databasen för att länka till metadata i en modellklass för bindningen. Observera bara att den typ som refererar till MetadataTypeAttribute måste vara en delmängd av typen refererande (det kan ha färre egenskaper, men inte mer).</li><li>**Flytta data fram och tillbaka mellan användarens indata modeller och databasen är omständigt. Kan jag bara kopiera över alla egenskaper använda reflektion? -** Ja. De enda egenskaper som visas i bindningen modeller är de som du har fastställt att vara säker för indata från användaren. Det finns ingen säkerhet anledning som förhindrar att använda reflektion för att kopiera över alla egenskaper som finns i vanliga mellan dessa två modeller.</li><li>**Vad händer om [binda (exkludera = ”â€¦”)]. Kan jag använda som i stället för att separat bindning modeller? -** Den här metoden rekommenderas inte. Med hjälp av [binda (exkludera = ”â€¦”)] innebär att alla nya egenskapen kan bindas till som standard. När en ny egenskap läggs till, det är ett extra steg för att komma ihåg att skydda saker istället för att ha design vara säkert som standard. Beroende på utvecklaren är kontrollerar den här listan varje gång en egenskap läggs riskfyllda.</li><li>**Är [binda (inkludera = ”â€¦”)] användbara för att redigera åtgärder? -** Nej. [Bindning (inkludera = ”â€¦”)] är endast tillgänglig för INSERT-format (lägga till nya data). Använd en annan metod, som att ha en separat bindning modeller eller skicka en lista med tillåtna egenskaper till UpdateModel eller TryUpdateModel för UPDATE-format (ändra befintliga data). Lägga till en [binda (inkludera = ”â€¦”)]-attributet på en redigeringsåtgärd innebär att MVC skapar en objektinstans och ange bara de listade egenskaperna, lämnar du alla andra standardinställningarna. När data bevaras ersätter den helt befintliga entiteten, när du återställer värdena för alla utelämnade egenskaper till sina standardinställningar. Om exempelvis IsAdmin utelämnades från en [binda (inkludera = ”â€¦”)]-attributet på en Redigera åtgärd, alla användare vars namn har redigerats via den här åtgärden skulle återställas till IsAdmin = false (alla redigerade användare förlorar Administratörsstatus). Om du vill förhindra att uppdateringar till vissa egenskaper, använda en av de andra metoderna ovan. Observera att vissa versioner av MVC-tooling generera controller klasser med [binda (inkludera = ”â€¦”)] redigera åtgärder och innebär att ta bort en egenskap från listan för att överdrivet bokföring attacker. Dock enligt beskrivningen ovan, denna metod fungerar inte som avsett och i stället återställs alla data i utelämnade egenskaper till sina standardvärden.</li><li>**För att skapa åtgärder, finns det några varningar med [binda (inkludera = ”â€¦”)] i stället för en separat bindning modeller? -** Ja. Den här metoden fungerar först inte redigera scenarier som kräver underhåll av två olika metoder för hur du minimerar alla över bokföring sårbarheter. Andra, separat bindning modeller tillse åtskillnad mellan för frågor mellan den form som används för indata från användare och den form som används för persistence, något [binda (inkludera = ”â€¦”)] påverkas inte. Observera att det tredje [binda (inkludera = ”â€¦”)] kan endast hantera översta egenskaper. Du kan inte tillåta endast delar av underordnade egenskaper (till exempel ”Details.Name”) i attributet. Slutligen och kanske använder viktigast av allt [binda (inkludera = ”â€¦”)] lägger till ytterligare ett steg som måste registreras när klassen används för modellbindning. Om en ny åtgärdsmetod Binder till dataklassen direkt och glömmer att inkludera en [binda (inkludera = ”â€¦”)] attribut, kan det bli sårbart för overpostingattacker, så den [binda (inkludera = ”â€¦”)] metod är mindre säker som standard. Om du använder [binda (inkludera = ”â€¦”)], var alltid noga för att komma ihåg att ange det varje gång din dataklasser visas som åtgärden metodparametrar.</li><li>**För att skapa åtgärder, vad händer om att den [binda (inkludera = ”â€¦”)]-attributet på klassen modellen? Inte den här metoden att undvika att behöva komma ihåg placera attributet på varje åtgärdsmetod? -** Den här metoden fungerar i vissa fall. Med hjälp av [binda (inkludera = ”â€¦”)] på modelltypen sig själv (inte på åtgärdsparametrar med den här klassen) undvika att behöva komma ihåg att ta den [binda (inkludera = ”â€¦”)]-attributet på varje åtgärdsmetod. Använda attribut direkt på klassen effektivt skapar en separat ytan på den här klassen för modellen bindning. Den här metoden kan dock endast för en modell bindning form per modellklass. Om en åtgärdsmetod måste tillåta modellen bindningen för ett fält (till exempel en administrativ åtgärd som uppdaterar användarroller) och andra åtgärder behöver förhindra att modellen bindning av det här fältet, fungerar inte den här metoden. Varje klass kan bara ha en modell bindning form. Om olika åtgärder behöver annan modell bindning former, de behöver för att representera formerna separat med hjälp av antingen separata modellen bindning klasser eller olika [binda (inkludera = ”â€¦”)] attribut på åtgärdsmetoder.</li><li>**Vad bindning modeller? Är de samma sak som visa modeller? -** Här är två relaterade begrepp. Termen bindning modellen refererar till en modellklass som används i parameterlistan för en åtgärd (den form som skickas från MVC-modellen bindningen till metoden åtgärd). Termen vymodell refererar till en modellklass som skickas från en åtgärdsmetod en vy. Med hjälp av en vy-specifika modellen är en vanlig metod för att skicka data från en åtgärdsmetod till en vy. Ofta formen passar också för modellen bindningen och vymodell termen kan användas för att referera samma modell på båda platser. För att vara exakt, kommunicerar med den här proceduren specifikt information om hur du binder modeller, fokusera på formen som skickas till åtgärden, vilket är det som är viktigt för tilldelning av drivrutiner för masslagring.</li></ul>| 

## <a id="rendering"></a>Koda obetrodda webbutdata innan rendering

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk, Web Forms, MVC5, MVC6 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Hur du förhindrar Cross site scripting i ASP.NET](https://msdn.microsoft.com/library/ms998274.aspx), [skriptattacker](http://cwe.mitre.org/data/definitions/79.html), [XSS (Cross Site Scripting) förebyggande facit blad](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet) |
| **Steg** | Cross site scripting (ofta förkortat som XSS) är en angreppsvinkel för online-tjänster eller program/komponenter som förbrukar indata från webben. XSS-problem kan en angripare att köra skript på en annan användares dator via ett sårbara webbprogram. Skadliga skript kan användas för att stjäla cookies och manipulera annars en offrets dator via JavaScript. XSS förhindras genom att verifiera indata från användaren, att se till att den är felfritt och kodning innan den återges på en webbsida. Verifiering av indata och utdatakodning kan göras med hjälp av bibliotek för skydd av webbprogram. För förvaltad kod (C\#, VB.net osv), använda en eller flera lämpliga kodning metoder från biblioteket Web Protection (skydd mot XSS), beroende på kontext där indata från användaren hämtar begäranden som visas:| 

### <a name="example"></a>Exempel

```csharp
* Encoder.HtmlEncode 
* Encoder.HtmlAttributeEncode 
* Encoder.JavaScriptEncode 
* Encoder.UrlEncode
* Encoder.VisualBasicScriptEncode 
* Encoder.XmlEncode 
* Encoder.XmlAttributeEncode 
* Encoder.CssEncode 
* Encoder.LdapEncode 
```

## <a id="typemodel"></a>Utföra verifiering av indata och filtrering på alla strängtyp modellegenskaper

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Allmän och MVC5, MVC6 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Att lägga till verifiering](http://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), [verifierar modelldata i ett MVC-program](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [riktlinjer för dina ASP.NET MVC-program](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Steg** | <p>Alla indataparametrarna som måste verifieras innan de används i programmet så att programmet skyddas mot skadliga användarindata. Verifiera de indatavärden som med reguljära uttryck verifieringar på serversidan med en strategi för verifiering av listan över godkända. Unsanitized användarindata / parametrarna som skickades till metoder som kan orsaka kod inmatning.</p><p>För webbprogram innehålla startpunkter även formulärfält, frågesträngar, cookies, HTTP-huvuden och webbtjänstparametrar.</p><p>Följande kontroller för verifiering av indata måste utföras när modellen bindning:</p><ul><li>Modellegenskaper ska förses med reguljärt uttryck anteckning för att acceptera tillåtna tecken och tillåtna</li><li>Kontrollantmetoder bör utföra ModelState giltighet</li></ul>|

## <a id="richtext"></a>Gemensamt ska tillämpas på formulärfält som godkänner alla tecken, t.ex., textredigeraren

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Koda osäkra indata](https://msdn.microsoft.com/library/ff647397.aspx#paght000003_step3), [HTML rensare](https://github.com/mganss/HtmlSanitizer) |
| **Steg** | <p>Identifiera alla statiska taggar som du vill använda. En vanlig metod är att begränsa formateringen på säker HTML-element, till exempel `<b>` (fetstil) och `<i>` (kursiv).</p><p>Innan du skriver data, HTML-koda den. Detta gör att alla skadliga skript säker genom vilket gör att den hanteras som text, inte som körbar kod.</p><ol><li>Inaktivera validering av ASP.NET begäran genom att lägga till ValidateRequest = ”false” attribut för att den \@ Page-direktivet</li><li>Koda strängindata med metoden HtmlEncode</li><li>Använd en StringBuilder och anropa dess Ersätt-metoden för att selektivt ta bort kodning för HTML-element som du vill tillåta</li></ol><p>Sidan-modulen begärandevalideringen referenser inaktiverar ASP.NET genom att ange `ValidateRequest="false"`. Den HTML-kodar indata och selektivt tillåter den `<b>` och `<i>` också en .NET-bibliotek för HTML gemensamt kan också använda.</p><p>HtmlSanitizer är en .NET-bibliotek för att rensa HTML-kod och dokument från konstruktioner som kan leda till XSS-attacker. AngleSharp används för att parsa, manipulera och återge HTML och CSS. HtmlSanitizer kan installeras som ett NuGet-paket och indata från användaren kan skickas via relevanta HTML- eller CSS gemensamt metoder, som är tillämpligt, på serversidan. Observera som gemensamt som en säkerhetskontroll bör övervägas endast som en sista alternativet.</p><p>Verifiering av indata och utdatakodning anses bättre säkerhetskontroller.</p> |

## <a id="inbuilt-encode"></a>Tilldela inte DOM-element till mottagare som inte har inbyggd kodning

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | Många javascript-funktioner gör inte kodning som standard. När du tilldelar inte är betrodd indata till DOM-element via sådana funktioner kan resultera i mellan plats-skriptkörningar (XSS).| 

### <a name="example"></a>Exempel
Följande är osäkert exempel: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Använd inte `innerHtml`; i stället använda `innerText`. På samma sätt, i stället för `$("#elm").html()`, använda `$("#elm").text()` 

## <a id="redirect-safe"></a>Kontrollera att alla omdirigeringar i programmet är stängd eller för att göra på ett säkert sätt

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [OAuth 2.0 auktorisering Framework - öppna omdirigerare](http://tools.ietf.org/html/rfc6749#section-10.15) |
| **Steg** | <p>Programmets design kräver omdirigering till en plats som anges av användaren måste begränsa de möjliga omdirigering av mål till en fördefinierad ”säkra” lista över platser eller domäner. Alla omdirigeringar i programmet måste vara stängd/safe.</p><p>Gör så här:</p><ul><li>Identifiera alla omdirigeringar</li><li>Implementera en lämplig lösning för varje omdirigeras. Lämpliga åtgärder omfattar omdirigering lista över tillåtna eller användare bekräftelse. Om en webbplats eller tjänst med säkerhetsrisken öppen omdirigering använder Facebook/OAuth/OpenID identitetsleverantörer, kan en angripare stjäl en användares inloggningstoken och vara den användaren. Det här är en inneboende risker när du använder OAuth, som beskrivs i RFC 6749 ”The OAuth 2.0 auktorisering Framework”, avsnittet 10.15 ”öppna omdirigerar” på samma sätt, användarnas autentiseringsuppgifter kan komprometteras genom spearphishingmail nätfiskeattacker med öppna omdirigeringar</li></ul>|

## <a id="string-method"></a>Implementera verifiering av indata på alla typ strängparametrar accepteras av kontrollantmetoder

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Allmän och MVC5, MVC6 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Verifiera modelldata i ett MVC-program](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [riktlinjer för dina ASP.NET MVC-program](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Steg** | Verifiering av indata med reguljära uttryck ska göras för metoder som bara godkänner primitiv typ och inte modeller som argument. Här bör Regex.IsMatch användas tillsammans med ett giltigt regex-mönster. Om indata inte matchar det angivna reguljära uttrycket, kontroll ska inte gå vidare och en förvarning om verifieringsfel ska visas.| 

## <a id="dos-expression"></a>Ange tidsgränsen för övre gräns för reguljärt uttryck som används för att förhindra DoS på grund av felaktiga reguljära uttryck

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk, Web Forms, MVC5, MVC6  |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [DefaultRegexMatchTimeout egenskap ](https://msdn.microsoft.com/library/system.web.configuration.httpruntimesection.defaultregexmatchtimeout.aspx) |
| **Steg** | För att säkerställa DOS-attacker mot felaktigt ange skapade reguljära uttryck som orsakar långa slipper tidsgränsen för global standard. Om tid det tar längre tid än den angivna övre gränsen, returnerade den ett undantagsfel om Timeout. Om ingenting har konfigurerats, är tidsgränsen oändliga.| 

### <a name="example"></a>Exempel
Till exempel genererar följande konfiguration en RegexMatchTimeoutException om bearbetningen tar mer än 5 sekunder: 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a id="html-razor"></a>Undvik att använda Html.Raw i Razor-vyer

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | MVC5 MVC6 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| Steg | ASP.Net-webbsidor (Razor) utföra automatisk HTML-kodningen. Alla strängar som skrivits ut av inbäddad kod nuggets (@ block) är automatiskt HTML-kodad. Men när `HtmlHelper.Raw` -metoden har anropats, returnerar den kod som inte är HTML-kodat. Om `Html.Raw()` helper-metod som används, den kringgår det automatiska skyddet för kodning som Razor tillhandahåller.|

### <a name="example"></a>Exempel
Följande är ett osäkert exempel: 

```csharp
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
Använd inte `Html.Raw()` såvida du inte behöver visa markup. Den här metoden utför inte utdata kodning implicit. Använda andra ASP.NET-reläagenter t.ex. `@Html.DisplayFor()` 

## <a id="stored-proc"></a>Använd inte dynamiska frågor i lagrade procedurer

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Ett SQL-angrepp utnyttjar sårbarheter i indataverifieringen att köra godtycklig kommandon i databasen. Det kan inträffa när indata används i ditt program för att konstruera dynamiska SQL-uttryck för att få åtkomst till databasen. Det kan också inträffa om din kod använder lagrade procedurer som har skickats strängar som innehåller raw användarindata. Med hjälp av SQL-angrepp, kan angriparen köra godtycklig kommandon i databasen. Alla SQL-instruktioner (inklusive SQL-instruktioner i lagrade procedurer) måste parametriseras. SQL-instruktioner accepterar tecken som är särskilt utformade för SQL (till exempel enkelt citattecken) utan problem Eftersom de är starkt typbestämd. |

### <a name="example"></a>Exempel
Nedan följer ett exempel på osäkert dynamisk lagringsprocedur: 

```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteria]
(
  @productName nvarchar(200) = NULL,
  @startPrice float = NULL,
  @endPrice float = NULL
)
AS
 BEGIN
  DECLARE @sql nvarchar(max)
  SELECT @sql = ' SELECT ProductID, ProductName, Description, UnitPrice, ImagePath' +
       ' FROM dbo.Products WHERE 1 = 1 '
       PRINT @sql
  IF @productName IS NOT NULL
     SELECT @sql = @sql + ' AND ProductName LIKE ''%' + @productName + '%'''
  IF @startPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice > ''' + CONVERT(VARCHAR(10),@startPrice) + ''''
  IF @endPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice < ''' + CONVERT(VARCHAR(10),@endPrice) + ''''

  PRINT @sql
  EXEC(@sql)
 END
```

### <a name="example"></a>Exempel
Nedan följer samma lagrade procedur som implementeras på ett säkert sätt: 
```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteriaSecure]
(
             @productName nvarchar(200) = NULL,
             @startPrice float = NULL,
             @endPrice float = NULL
)
AS
       BEGIN
             SELECT ProductID, ProductName, Description, UnitPrice, ImagePath
             FROM dbo.Products where
             (@productName IS NULL or ProductName like '%'+ @productName +'%')
             AND
             (@startPrice IS NULL or UnitPrice > @startPrice)
             AND
             (@endPrice IS NULL or UnitPrice < @endPrice)         
       END
```

## <a id="validation-api"></a>Se till att modellen verifieras webb-API-metoder

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | MVC5 MVC6 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Modellvalidering i ASP.NET webb-API ](http://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Steg** | När en klient skickar data till ett webb-API, är det obligatoriskt att validera data innan du gör någon bearbetning. Använd data anteckningar för ASP.NET Web API: er som acceptera modeller som indata, på modeller för att ange valideringsregler för egenskaperna för modellen.|

### <a name="example"></a>Exempel
Följande kod visar samma: 

```csharp
using System.ComponentModel.DataAnnotations;

namespace MyApi.Models
{
    public class Product
    {
        public int Id { get; set; }
        [Required]
        [RegularExpression(@"^[a-zA-Z0-9]*$", ErrorMessage="Only alphanumeric characters are allowed.")]
        public string Name { get; set; }
        public decimal Price { get; set; }
        [Range(0, 999)]
        public double Weight { get; set; }
    }
}
```

### <a name="example"></a>Exempel
I metoden åtgärd i API-domänkontrollanter har giltigheten för modellen kontrolleras uttryckligen enligt nedan: 

```csharp
namespace MyApi.Controllers
{
    public class ProductsController : ApiController
    {
        public HttpResponseMessage Post(Product product)
        {
            if (ModelState.IsValid)
            {
                // Do something with the product (not shown).

                return new HttpResponseMessage(HttpStatusCode.OK);
            }
            else
            {
                return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
            }
        }
    }
}
```

## <a id="string-api"></a>Implementera verifiering av indata på alla typ strängparametrar accepteras av webb-API-metoder

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Allmän och MVC 5, MVC 6 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Verifiera modelldata i ett MVC-program](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [riktlinjer för dina ASP.NET MVC-program](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Steg** | Verifiering av indata med reguljära uttryck ska göras för metoder som bara godkänner primitiv typ och inte modeller som argument. Här bör Regex.IsMatch användas tillsammans med ett giltigt regex-mönster. Om indata inte matchar det angivna reguljära uttrycket, kontroll ska inte gå vidare och en förvarning om verifieringsfel ska visas.|

## <a id="typesafe-api"></a>Se till att typen safe parametrar används i webb-API för dataåtkomst

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | Gäller inte  |
| **Steg** | <p>Om du använder parametrar insamling, SQL behandlar är indata som ett literalvärde stället som körbar kod. Parametersamlingen kan användas för att genomdriva typ och längd begränsningar i indata. Värden som ligger utanför intervallet utlöser ett undantag. Om typ safe SQL-parametrar inte används, kan angripare att kunna köra inmatningsattacker som är inbäddade i ofiltrerade indata.</p><p>Använd säkra typparametrar när SQL-frågor för att undvika möjlig SQL-inmatningsattacker som kan uppstå med ofiltrerade indata. Du kan använda säkra typparametrar med lagrade procedurer och dynamiska SQL-uttryck. Parametrar behandlas som litterala värden av databasen och inte som körbar kod. Parametrar kontrolleras även om typ och längd.</p>|

### <a name="example"></a>Exempel
Följande kod visar hur du använder säker typparametrar med SqlParameterCollection när du anropar en lagrad procedur. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
I ovanstående kodexempel får inte indatavärdet vara längre än 11 tecken. Om data inte stämmer överens med typen eller längd som anges av parametern, utlöser SqlParameter klassen ett undantag. 

## <a id="sql-docdb"></a>Använda SQL-frågor för innehåller parametrar för Cosmos DB

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure Document DB | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [Vi presenterar SQL Parameterisering i Azure Cosmos DB](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Steg** | Även om Azure Cosmos DB stöder bara skrivskyddade frågor, är SQL-inmatning fortfarande möjligt om frågor skapas genom att sammanfoga med användarinformation. Det kan vara möjligt för en användare att få åtkomst till data som de inte får att ha åtkomst till i samma samling genom att utforma skadlig SQL-frågor. Använda parametriserade SQL-frågor om frågor för baserat på indata från användaren. |

## <a id="schema-binding"></a>WCF-indata verifiering via schemabindning

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk NET Framework 3 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff647820.aspx) |
| **Steg** | <p>Brist på verifiering leder till annan typ inmatningsangrepp.</p><p>Verifiering av meddelandet representerar en försvarslinje i skydd av WCF-program. Med den här metoden kan validera du meddelanden med scheman för att skydda WCF-tjänståtgärder mot angrepp genom att en falsk klient. Validera alla meddelanden som tas emot av klienten att skydda klienten mot angrepp av skadlig-tjänsten. Meddelandet verifiering gör det möjligt att verifiera meddelanden när åtgärder som förbrukar meddelandet kontrakt eller data avtal, som inte kan utföras med hjälp av parametern-verifiering. Meddelande-verifiering kan du skapa validering av logik i scheman, vilket ger mer flexibilitet och minska utvecklingstiden. Scheman kan återanvändas i olika program i organisationen, skapa standarder för datarepresentation. Dessutom kan meddelandet verifiering du skydda åtgärder när de använder mer komplexa datatyper som involverar kontrakt som representerar affärslogik.</p><p>Om du vill utföra verifiering av meddelandet skapa du först ett schema som representerar driften av din tjänst och de datatyper som används av dessa åtgärder. Du kan sedan skapa en .NET-klass som implementerar en anpassad klient meddelande inspector och anpassade dispatcher meddelande inspector för att verifiera de meddelanden som skickats/tagits emot till och från tjänsten. Nu ska implementera du en anpassad slutpunkt för att aktivera meddelandet autentiserades på både klienten och tjänsten. Slutligen kan du implementera en anpassad konfigurationselementet om klassen som gör det möjligt att exponera det utökade anpassa slutpunktsfunktionaliteter i konfigurationsfilen för tjänsten eller klienten ”</p>|

## <a id="parameters"></a>Indata WCF - verifiering via parametern Inspectors

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk NET Framework 3 |
| **Attribut**              | Gäller inte  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff647875.aspx) |
| **Steg** | <p>Verifiering av indata och data representerar en viktig försvarslinje i skydd av WCF-program. Du bör verifiera alla parametrar som visas i WCF-tjänståtgärder tjänsten skyddas från angrepp av en falsk klient. Däremot bör du också kontrollera alla returvärden som tagits emot av klienten för att skydda den mot angrepp av skadlig-tjänsten</p><p>WCF innehåller olika punkter som gör det möjligt att anpassa beteendet för WCF-runtime genom att skapa anpassade tillägg. Meddelande-kontroller och parametern Inspectors är två utökningsbarhet mekanismer som används för att få bättre kontroll över de data som skickas mellan en klient och en tjänst. Du bör använda parametern-kontroller för verifiering av indata och använder meddelande inspectors endast när du behöver granska hela meddelandet flödar in och ut ur en tjänst.</p><p>Om du vill utföra verifiering av indata måste du skapa en .NET-klass och implementera en anpassad parameter inspector för att kunna verifiera parametrar på åtgärder i din tjänst. Du kommer sedan att implementera en anpassad slutpunkt beteende om du vill aktivera validering av både klienten och tjänsten. Slutligen implementerar du en anpassad konfigurationselement på den klass som gör att du kan exponera det utökade anpassa slutpunktsfunktionaliteter i konfigurationsfilen för tjänsten eller klienten</p>|
