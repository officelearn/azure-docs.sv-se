---
title: Ange validering - verktyget Microsoft Threat modellering - Azure | Microsoft Docs
description: "ändringar för hot som exponeras i verktyget Modeling hot"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: b7ce6f353cf8cf48d5fb038ee77b0d3fdae16fb7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="security-frame-input-validation--mitigations"></a>Säkerhet ram: Indata validering | Åtgärder 
| Produkter eller tjänster | Artikel |
| --------------- | ------- |
| **Webbprogram** | <ul><li>[Inaktivera XSLT-skript för alla transformeringar med obetrodda formatmallar](#disable-xslt)</li><li>[Se till att varje sida som kan innehålla användare kan kontrolleras innehåll bort automatisk MIME-kontroll](#out-sniffing)</li><li>[Skydda eller inaktivera XML-entitet upplösning](#xml-resolution)</li><li>[Program som använder http.sys utföra verifiering för URL-auktorisering](#app-verification)</li><li>[Se till att lämpliga kontroller som finns på plats när accepterar filer från användare](#controls-users)</li><li>[Se till att typen säkert parametrar används i webbprogram för dataåtkomst](#typesafe)</li><li>[Använda separat modell bindning klasser eller bindningsfilter listar för att förhindra MVC masslagring tilldelning säkerhetsproblem](#binding-mvc)</li><li>[Koda obetrodda webbutdata innan återgivning](#rendering)</li><li>[Utföra verifiering av indata- och filtrering på alla strängtyp modellegenskaper](#typemodel)</li><li>[Rensning ska tillämpas på formulärfält som accepterar tecken, t.ex., textredigeraren](#richtext)</li><li>[Inte tilldela DOM-element sänkor som inte har inbyggda kodning](#inbuilt-encode)</li><li>[Verifiera att alla omdirigeringar i programmet är stängda eller göra på ett säkert sätt](#redirect-safe)</li><li>[Implementera verifiering av indata på alla typen strängparametrar accepteras av kontrollantmetoder](#string-method)</li><li>[Ange övre gräns för tidsgränsen för reguljärt uttryck bearbetning för att förhindra DoS på grund av felaktiga reguljära uttryck](#dos-expression)</li><li>[Undvik att använda Html.Raw i Razor vyer](#html-razor)</li></ul> | 
| **Databas** | <ul><li>[Använd inte dynamisk frågor i lagrade procedurer](#stored-proc)</li></ul> |
| **Webb-API** | <ul><li>[Se till att modellen verifieringen är klar för Web API-metoder](#validation-api)</li><li>[Implementera verifiering av indata på alla typen strängparametrar accepteras av Web API-metoder](#string-api)</li><li>[Se till att typen säkert parametrar används i Web API för dataåtkomst](#typesafe-api)</li></ul> | 
| **Azure dokumentet DB** | <ul><li>[Använd innehåller parametrar SQL-frågor för DocumentDB](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[WCF-indata verifiering via schemabindning](#schema-binding)</li><li>[WCF - indata verifiering via parametern kontrollanter](#parameters)</li></ul> |

## <a id="disable-xslt"></a>Inaktivera XSLT-skript för alla transformeringar med obetrodda formatmallar

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | [XSLT-säkerhet](https://msdn.microsoft.com/library/ms763800(v=vs.85).aspx), [egenskapen XsltSettings.EnableScript](http://msdn.microsoft.com/library/system.xml.xsl.xsltsettings.enablescript.aspx) |
| **Steg** | XSLT har stöd för skript i formatmallar med hjälp av den `<msxml:script>` element. Detta gör att anpassade funktioner som ska användas i en XSLT-transformation. Skriptet körs i processen utför transformeringen kontext. XSLT-skriptet måste inaktiveras när i en ej betrodd miljö, så att körningen av en icke betrodd kod. *Om du använder .NET:* XSLT-skript är inaktiverat som standard, men du måste se till att den inte har uttryckligen aktiverats via den `XsltSettings.EnableScript` egenskapen.|

### <a name="example"></a>Exempel 

```C#
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Exempel
Om du använder med hjälp av MSXML 6.0, är XSLT-skript inaktiverat som standard. dock måste du kontrollera att den inte har uttryckligen aktiverats via egenskapen XML DOM-objektet AllowXsltScript. 

```C#
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Exempel
Om du använder MSXML 5 eller nedan, XSLT-skript är aktiverat som standard och du måste uttryckligen inaktivera den. Egenskapen XML DOM-objektet AllowXsltScript till false. 

```C#
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a id="out-sniffing"></a>Se till att varje sida som kan innehålla användare kan kontrolleras innehåll bort automatisk MIME-kontroll

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | [IE8 Säkerhet del V - heltäckande skydd](http://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx)  |
| **Steg** | <p>För varje sida som kan innehålla användare kan kontrolleras innehåll, måste du använda HTTP-huvudet `X-Content-Type-Options:nosniff`. Du kan antingen ange obligatorisk rubrik varje sida för de sidor som kan innehålla användare kan kontrolleras innehåll för att uppfylla detta krav, eller kan du ändra det globalt för alla sidor i programmet.</p><p>Varje typ av fil från en server som har en associerad [MIME-typ](http://en.wikipedia.org/wiki/Mime_type) (kallas även en *innehållstypen*) som beskriver typ av innehåll (det vill säga image, text, program osv.)</p><p>Huvudet X-innehåll-typ-alternativ är ett HTTP-huvud som gör att utvecklare kan ange att innehållet inte får någon MIME-lyssnar. Det här sidhuvudet är utformad för att minska MIME-kontroll attacker. Stöd för det här huvudet har lagts till i Internet Explorer 8 (IE8)</p><p>Endast användare av Internet Explorer 8 (IE8) drar nytta av X-innehåll-typ-alternativ. Tidigare versioner av Internet Explorer hänsyn inte för närvarande huvudet X-innehåll-typ-alternativ</p><p>Internet Explorer 8 (och senare) är de enda större webbläsarna att implementera en MIME-kontroll CEIP-funktion. Om andra större webbläsare (Firefox, Safari, Chrome) implementerar liknande funktioner, och när den här rekommendationen uppdateras för att inkludera syntaxen för de här webbläsarna samt</p>|

### <a name="example"></a>Exempel
Om du vill aktivera obligatorisk rubrik globalt för alla sidor i programmet kan du göra något av följande: 

* Lägga till rubriken i filen Web.config om programmet finns av Internet Information Services (IIS) 7 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* Lägg till sidhuvud via programmet globala\_BeginRequest 

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

* Du kan aktivera rubriken krävs endast för specifika sidor genom att lägga till den enskilda svar: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a id="xml-resolution"></a>Skydda eller inaktivera XML-entitet upplösning

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | [XML-entitet Expansion](http://capec.mitre.org/data/definitions/197.html), [XML Denial of Service-attacker och försvar](http://msdn.microsoft.com/magazine/ee335713.aspx), [MSXML Säkerhetsöversikt](http://msdn.microsoft.com/library/ms754611(v=VS.85).aspx), [bästa praxis för att skydda MSXML kod](http://msdn.microsoft.com/library/ms759188(VS.85).aspx), [ Protokollreferens för NSXMLParserDelegate](http://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html), [lösa externa referenser](https://msdn.microsoft.com/library/5fcwybb2.aspx) |
| **Steg**| <p>Även om den inte används ofta, finns det en funktion i XML som gör att XML-parsern för att expandera makrot entiteter med värden som definieras i själva dokumentet eller från externa källor. Till exempel dokumentet kan definiera en entitet ”företagsnamn” med värdet ”Microsoft”, så som varje gång texten ”&companyname;” visas i dokumentet, ersätts automatiskt med Microsoft. Eller dokumentet kan definiera en entitet ”MSFTStock” som refererar till en extern webbtjänst för att hämta det aktuella värdet för Microsoft-lager.</p><p>Sedan helst ”&MSFTStock;” visas i dokumentet, ersätts automatiskt med det aktuella lager priset. Den här funktionen kan dock missbrukas för att skapa denial of service (DoS) villkor. En angripare kan kapsla flera entiteter för att skapa en exponentiell expansion XML bomb som förbrukar allt tillgängligt minne i systemet. </p><p>Du kan också han kan skapa en extern referens som strömmas tillbaka en oändlig mängd data eller som bara låser sig tråden. Alla team måste därför inaktivera interna eller externa XML-entitet upplösning helt om sina program inte använda den, eller manuellt begränsa mängden minne och den tid som programmet kan använda för matchning av entiteten om den här funktionen är absolut nödvändigt. Om entiteten upplösning inte krävs av programmet, inaktiverar du den. </p>|

### <a name="example"></a>Exempel
Du kan använda följande metoder för .NET Framework-kod:

```C#
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
Observera att standardvärdet `ProhibitDtd` i `XmlReaderSettings` är true, men i `XmlTextReader` det är false. Om du använder XmlReaderSettings du behöver inte ange ProhibitDtd till true explicit, men det rekommenderas för saké säkerhet att göra. Observera också att klassen XmlDocument gör entitet upplösning som standard. 

### <a name="example"></a>Exempel
Inaktivera entiteten lösning för XmlDocuments med den `XmlDocument.Load(XmlReader)` överlagring för metoden Load och ange lämpliga egenskaper i argumentet XmlReader att inaktivera upplösning, enligt beskrivningen i följande kod: 

```C#
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Exempel
Om det inte går att inaktivera entiteten matchning för ditt program, kan du ange egenskapen XmlReaderSettings.MaxCharactersFromEntities till ett rimligt värde enligt dina programbehov. Detta begränsar effekten av potentiella exponentiell expansion DoS-attacker. Följande kod innehåller ett exempel på den här metoden: 

```C#
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Exempel
Om du behöver lösa infogade entiteter men har inte behöver för att lösa externa enheter XmlReaderSettings.XmlResolver egenskapen värdet null. Exempel: 

```C#
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Observera att i MSXML6, ProhibitDTD har angetts till true (inaktivera DTD-bearbetning) som standard. Det finns två XML-Parser kan du använda för Apple OSX/iOS kod: NSXMLParser och libXML2. 

## <a id="app-verification"></a>Program som använder http.sys utföra verifiering för URL-auktorisering

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | Saknas  |
| **Steg** | <p>Alla program som använder http.sys bör följa dessa riktlinjer:</p><ul><li>Begränsa URL: er till mer än 16 384 tecken (ASCII eller Unicode). Det här är en absolut URL maxlängden baserat på Internet Information Services (IIS) 6 standardinställningen. Webbplatser bör sträva efter en kortare än detta om möjligt</li><li>Använd standard .NET Framework-filen i/o-klasser (till exempel FileStream) som dessa drar nytta av regler för auktorisering i .NET-FX</li><li>Explicit Bygg en Tillåt-lista över kända filnamn</li><li>Explicit avvisa kända filtyper som du inte kommer att uppfylla UrlScan avvisar: exe, bat, cmd, com, htw, ida, idq, htr, idc, shtm-[l], stm, skrivare, ini, pol, dat filer</li><li>Fånga följande undantag:<ul><li>System.ArgumentException (för enhetsnamn)</li><li>System.NotSupportedException (för dataströmmar)</li><li>System.IO.FileNotFoundException (för ogiltig ESC filnamn)</li><li>System.IO.DirectoryNotFoundException (för ogiltig ESC kataloger)</li></ul></li><li>*Inte* anropa till Win32-fil I/O APIs. På en ogiltig URL att returnera ett 400-fel till användaren och logga verkliga fel.</li></ul>|

## <a id="controls-users"></a>Se till att lämpliga kontroller som finns på plats när accepterar filer från användare

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | [Obegränsad filöverföringen](https://www.owasp.org/index.php/Unrestricted_File_Upload), [filen signatur tabell](http://www.garykessler.net/library/file_sigs.html) |
| **Steg** | <p>Överförda filer utgör en betydande risk för program.</p><p>Det första steget i många attacker är att hämta kod till systemet att utsättas för angrepp. Sedan behöver angrepp bara söka efter ett sätt att få den kod som körs. När du använder en filöverföring kan angripare göra det första steget. Konsekvenserna av obegränsad filöverföringen kan variera, inklusive hela systemet övertag, överbelastad filsystem eller databasen, vidarebefordran attacker till backend-system och enkel defacement.</p><p>Det beror på hur programmet fungerar med den överförda filen och särskilt där den lagras. Servern validering av filöverföringar saknas. Följande kontroller för informationssäkerhet bör implementeras för filöverföring funktioner:</p><ul><li>Kontrollen av tillägget (endast en giltig uppsättning tillåtna filtyp ska accepteras)</li><li>Maxstorleken</li><li>Bör inte att överföra filen till webroot; platsen bör vara en katalog på icke-systemenhet</li><li>Namngivningskonvention ska följas, så att namnet på överförda filen har vissa slumpmässighet för att förhindra filen skrivs över</li><li>Filer bör genomsökas efter ett virusskyddsprogram innan skrivningen till disken</li><li>Kontrollera att filnamnet och andra metadata (t.ex. sökväg) verifieras för skadliga tecken</li><li>Filens format signatur ska kontrolleras att förhindra att användare överför en masqueraded fil (t.ex. ladda upp ett exe-filen genom att ändra filnamnstillägget till txt)</li></ul>| 

### <a name="example"></a>Exempel
Den sista punkten om filen format signaturverifiering finns klassen nedan för mer information: 

```C#
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

## <a id="typesafe"></a>Se till att typen säkert parametrar används i webbprogram för dataåtkomst

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | Saknas  |
| **Steg** | <p>Om du använder Parametersamlingen, SQL hanterar är indata som ett litteralvärde stället som körbar kod. Parametersamlingen kan användas för att framtvinga begränsningar för typ och längd på indata. Värden som ligger utanför intervallet utlöser ett undantag. Om typen säkert SQL-parametrar inte är används, kan angripare kunna köra injection attacker som är inbäddade i ofiltrerade indata.</p><p>Säker typparametrar vid SQL-frågor Undvik att använda SQL injection attacker som kan uppstå med ofiltrerade indata. Du kan använda säker typparametrar med lagrade procedurer och dynamiska SQL-uttryck. Parametrarna behandlas som litterala värden av databasen och inte som körbar kod. Parametrar kontrolleras även för typ och längd.</p>|

### <a name="example"></a>Exempel 
Följande kod visar hur du använder säker typparametrar med SqlParameterCollection när du anropar en lagrad procedur. 

```C#
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter(LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
I föregående kodexempel kan inte indatavärdet vara längre än 11 tecken. Om data inte stämmer överens med typen eller längd som anges av parametern utlöser klassen SqlParameter ett undantag. 

## <a id="binding-mvc"></a>Använda separat modell bindning klasser eller bindningsfilter listar för att förhindra MVC masslagring tilldelning säkerhetsproblem

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | MVC5 MVC6 |
| **Attribut**              | Saknas  |
| **Referenser**              | [Metadataattribut](http://msdn.microsoft.com/library/system.componentmodel.dataannotations.metadatatypeattribute), [offentliga nyckel säkerhet säkerhetsproblem och minskning](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation), [fullständig Guide till massa tilldelning i ASP.NET MVC](http://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [komma igång med EF med MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Steg** | <ul><li>**När ska leta för över bokföring säkerhetsproblem? -** Över bokföring säkerhetsproblem kan inträffa varje plats du binda modellen klasser från användaren. Ramverk som MVC kan representera användardata i anpassade .NET-klasser, inklusive vanlig gamla CLR-objekt (POCOs). MVC fyller automatiskt dessa klasser för modellen med data från begäran, vilket ger en smidig representation för att hantera indata från användaren. När dessa klasser innehåller egenskaper som inte ska ställas in av användaren, kan programmet vara utsatt för overpostingattacker, som gör att kontrollera data som programmet aldrig avsedd. Som MVC modellbindning databasen åtkomst till tekniker, till exempel Objektrelationer/mappers som Entity Framework ofta också stöd med POCO-objekt för att representera data från databasen. Dessa dataklasser modellen ger samma bekvämlighet i behandlar databasdata som MVC med indata från användaren. Eftersom både MVC och databasen stöder liknande modeller som POCO objekt verkar det enkelt att återanvända samma klasser för båda. Den här övningen misslyckas att bevara avgränsning av säkerhetsskäl och är det ett gemensamt område där oönskade egenskaper som exponeras av modellbindning aktiverar över bokföring attacker.</li><li>**Varför bör jag använda min ofiltrerade databasklasser modellen som parametrar till min MVC-åtgärder? -** Eftersom MVC modellbindning binda något i den här klassen. Även om data inte visas i vyn kan en angripare kan skicka en HTTP-begäran med dessa data ingår och MVC kommer gladeligen binda eftersom åtgärden säger att databasen klass har formen av data som den ska acceptera för indata från användaren.</li><li>**Varför bör hand om formen som används för modellbindning? -** Med ASP.NET MVC modellbindning med alltför många modeller exponerar ett program till overpostingattacker. Överdrivet bokföring möjliggör angripare att ändra programdata utöver vilka utvecklaren avsedda, till exempel åsidosätta priset för ett objekt eller säkerhetsbehörighet för ett konto. Program ska använda åtgärden-specifik bindning modeller (eller specifika tillåtna filter egenskapslistor) för att ange ett explicit kontraktet för vilka obetrodda indata att tillåta via modellbindning.</li><li>**Har separat bindning modeller bara kopiera koden? -** Nej, det är en fråga om avgränsning av säkerhetsskäl. Om du återanvänder databasmodeller i åtgärdsmetoder säger du alla egenskapen (eller underordnade) i den klass som kan anges av användaren i en HTTP-begäran. Om detta är att du inte vill MVC att göra, behöver du en filterlista eller formen separat klass för att visa MVC vilka data kan komma från användarindata i stället.</li><li>**Om jag har en separat bindning modeller för indata från användaren, måste jag Duplicera alla mina data anteckningens attribut? -** Inte nödvändigtvis. Du kan använda MetadataTypeAttribute på modellklass databasen för att länka till metadata i en modellklass för bindningen. Observera bara att typen som refereras av MetadataTypeAttribute måste vara en delmängd av typen refererande (det kan ha färre egenskaper, men inte mer).</li><li>**Det är tråkigt att flytta data fram och tillbaka mellan användaren inkommande modeller och databasen. Kan jag bara kopiera över alla egenskaper med hjälp av reflektion? -** Ja. Endast egenskaperna som visas i bindningen modeller är de som du har konstaterat är säker för indata från användaren. Det finns ingen säkerhet anledning som förhindrar med hjälp av reflektion ska skriva över alla egenskaper som finns i vanliga mellan dessa två modeller.</li><li>**Vad händer om [binda (exkludera = ”â€¦”)]. Kan jag använda som i stället för att separat bindning modeller? -** Rekommenderas inte den här metoden. Med hjälp av [binda (exkludera = ”â€¦”)] innebär att alla nya egenskapen kan bindas till som standard. När en ny egenskap läggs det finns ett extra steg för att komma ihåg att skydda saker i stället designen vara säker som standard. Beroende på utvecklaren är kontrollerar den här listan varje gång en egenskap läggs riskfyllda.</li><li>**Är [binda (inkludera = ”â€¦”)] användbara för att redigera åtgärder? -** Nej. [Bindning (inkludera = ”â€¦”)] är endast tillgänglig för INSERT-format (lägga till nya data). Använd en annan metod som har en separat bindning modeller eller skicka en lista med tillåtna egenskaper till UpdateModel eller TryUpdateModel för UPDATE-format (ändra befintliga data). Lägga till en [binda (inkludera = ”â€¦”)]-attributet på en redigeringsåtgärd innebär att MVC skapar en objektinstans och ange bara de listade egenskaperna, lämnar du alla andra standardinställningarna. Om data sparas ersätts helt befintliga entiteten, återställs värdena för eventuella utelämnat egenskaper till sina standardinställningar. Om exempelvis IsAdmin utelämnades från en [binda (inkludera = ”â€¦”)]-attributet på en Redigera åtgärd, alla användare vars namn har redigerats via den här åtgärden skulle återställas till IsAdmin = false (alla redigerade användare förlorar Administratörsstatus). Om du vill förhindra uppdateringar till vissa egenskaper med någon av de andra metoderna ovan. Observera att vissa versioner av MVC-tooling generera controller klasser med [binda (inkludera = ”â€¦”)] redigera åtgärder och innebär att ta bort en egenskap från listan för att överdrivet bokföring attacker. Som beskrivs ovan, kan den metoden fungerar inte som avsett och i stället återställs alla data i utelämnade egenskaper till sina standardvärden</li><li>**För att skapa åtgärder, finns det några varningar med [binda (inkludera = ”â€¦”)] i stället för en separat bindning modeller? -** Ja. Den här metoden fungerar inte redigera scenarier som kräver underhåll av två separata tillvägagångssätt för att minimera alla över bokföring sårbarheter först. Andra, separat bindning modeller tillse åtskillnad mellan för frågor mellan den form som används för indata från användare och den form som används för persistence, något [binda (inkludera = ”â€¦”)] påverkas inte. Observera att det tredje [binda (inkludera = ”â€¦”)] kan endast hantera översta egenskaper. Du kan inte tillåta endast delar av underordnade egenskaper (till exempel ”Details.Name”) i attributet. Slutligen och kanske använder viktigast av allt [binda (inkludera = ”â€¦”)] lägger till ytterligare ett steg som måste registreras när klassen används för modellbindning. Om en ny åtgärdsmetod Binder till dataklassen direkt och glömmer att inkludera en [binda (inkludera = ”â€¦”)] attribut, kan det bli sårbart för overpostingattacker, så den [binda (inkludera = ”â€¦”)] metod är mindre säker som standard. Om du använder [binda (inkludera = ”â€¦”)], var alltid noga för att komma ihåg att ange det varje gång din dataklasser visas som åtgärden metodparametrar.</li><li>**För att skapa åtgärder, vad händer om att den [binda (inkludera = ”â€¦”)]-attributet på klassen modellen? Inte den här metoden undvika att behöva komma ihåg att publicera attributet på varje åtgärdsmetod? -** Den här metoden fungerar i vissa fall. Med hjälp av [binda (inkludera = ”â€¦”)] på modelltypen sig själv (inte på åtgärdsparametrar med den här klassen) undvika att behöva komma ihåg att ta den [binda (inkludera = ”â€¦”)]-attributet på varje åtgärdsmetod. Använda attributet direkt i klassen effektivt skapar en separat ytan på den här klassen för modellen bindning. Den här metoden kan dock endast för en modell bindning form per modellklass. Den här metoden fungerar inte om en åtgärdsmetod måste tillåta modellbindning för ett fält (till exempel en administrativ åtgärd som uppdaterar användarroller) och andra åtgärder behöver förhindra att modellbindning i det här fältet. Varje klass kan bara ha en modell bindning form. Om olika åtgärder behöver annan modell bindning former, de behöver för att representera formerna separat med hjälp av antingen separata modellen bindning klasser eller olika [binda (inkludera = ”â€¦”)] attribut på åtgärdsmetoder.</li><li>**Vad bindning modeller? De är detsamma som att visa modeller? -** Dessa är två begreppen. Bindningen modellen refererar till en modell termen klass som används i en åtgärd är parameterlista (den form som överförts från MVC modellbindning till metoden åtgärden). Termen vymodellen refererar till en modellklass som skickas från en åtgärdsmetod till en vy. Med hjälp av en vy-specifika modellen är en vanlig metod för att skicka data från en åtgärdsmetod till en vy. Ofta formen passar också för modellbindning och vymodellen termen kan användas för att referera samma modell på båda platser. För att vara exakt den här proceduren nämns specifikt bindning modeller, fokuserar på formen skickas till åtgärden, vilket är det som är viktigt för masslagring tilldelning.</li></ul>| 

## <a id="rendering"></a>Koda obetrodda webbutdata innan återgivning

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk Web Forms, MVC5, MVC6 |
| **Attribut**              | Saknas  |
| **Referenser**              | [Förhindra globala webbplatsskript i ASP.NET](http://msdn.microsoft.com/library/ms998274.aspx), [webbplatser skript](http://cwe.mitre.org/data/definitions/79.html), [XSS (skriptkörning över flera) förebyggande Cheat blad](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet) |
| **Steg** | Globala webbplatsskript (förkortas ofta XSS) är en angreppsvinkel för alla/programkomponent som förbrukar indata från webben eller online services. XSS-problem kan en angripare att köra skript på en annan användares dator via ett sårbara webbprogram. Skadliga skript kan användas för att stjäla cookies och annars manipulera en drabbade datorn via JavaScript. XSS förhindras genom att verifiera indata från användaren, se till att den är felfritt och kodning innan den återges på en webbsida. Verifiering av indata och utdatakodning kan göras med hjälp av bibliotek för skydd av webbprogram. För förvaltad kod (C\#, VB.net osv), använda en eller flera lämpliga kodning metoder från biblioteket Web Protection (skydd mot XSS), beroende på kontext där användarens indata hämtar manifesterad:| 

### <a name="example"></a>Exempel

```C#
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

## <a id="typemodel"></a>Utföra verifiering av indata- och filtrering på alla strängtyp modellegenskaper

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Allmän och MVC5, MVC6 |
| **Attribut**              | Saknas  |
| **Referenser**              | [Lägga till verifiering](http://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), [verifierar modelldata i ett MVC-program](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [riktlinjer för ASP.NET MVC-program](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Steg** | <p>Alla indataparametrar måste verifieras innan de kan användas i programmet för att säkerställa att programmet skyddas mot skadliga användarindata. Validera indatavärdena med reguljära uttryck verifieringar på serversidan med en strategi för verifiering av listan över godkända. Unsanitized användarindata / parametrar till metoder som kan orsaka kod injektion säkerhetsproblem.</p><p>För webbprogram, kan även startpunkter inkludera formulärfält, QueryStrings, cookies, HTTP-huvuden och webbtjänstparametrar.</p><p>Följande kontroller för verifiering av indata måste utföras när modellbindning:</p><ul><li>Egenskaper för modell ska förses med reguljärt uttryck anteckningen, för att acceptera tillåtna tecken och högsta tillåtna längden</li><li>Metoderna som domänkontrollant utför ModelState giltighetsperiod</li></ul>|

## <a id="richtext"></a>Rensning ska tillämpas på formulärfält som accepterar tecken, t.ex., textredigeraren

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | [Koda osäkra indata](https://msdn.microsoft.com/library/ff647397.aspx#paght000003_step3), [HTML rensare](https://github.com/mganss/HtmlSanitizer) |
| **Steg** | <p>Identifiera alla statiska koder som du vill använda. En vanlig metod är att begränsa formatering för säker HTML-element, som `<b>` (fetstil) och `<i>` (kursiv).</p><p>Innan skrivningen av data som HTML-koda den. Detta gör att alla skadliga skript säker genom att göra det som ska hanteras som text, inte som körbar kod.</p><ol><li>Inaktivera validering av ASP.NET-begäran genom att lägga till ValidateRequest = ”false” @ Page-direktivet-attribut</li><li>Koda indata sträng med metoden HtmlEncode</li><li>Använd en StringBuilder och anropar dess Ersätt metod för att selektivt ta bort kodning för HTML-element som du vill tillåta</li></ol><p>Sidan in valideringen referenser inaktiverar ASP.NET genom att ange `ValidateRequest="false"`. Den HTML-kodas indata och selektivt tillåter den `<b>` och `<i>` också en .NET-bibliotek för HTML-rensning också kan användas.</p><p>HtmlSanitizer är en .NET-bibliotek för rensning av HTML-kod och dokument från konstruktioner som kan leda till XSS attacker. AngleSharp används för att analysera, hantera och återge HTML- och CSS. HtmlSanitizer kan installeras som en NuGet-paketet och indata från användaren kan skickas via relevanta HTML- eller CSS rensning metoder som tillämpas på serversidan. Observera som rensning som en säkerhetskontroll ska betraktas som ett sista alternativ.</p><p>Verifiering av indata och utdatakodning anses bättre säkerhetsåtgärder.</p> |

## <a id="inbuilt-encode"></a>Inte tilldela DOM-element sänkor som inte har inbyggda kodning

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | Saknas  |
| **Steg** | Många javascript-funktioner göra inte kodning som standard. När du tilldelar obetrodda indata till DOM-element via sådana funktioner kan orsaka mellan platsen skript (XSS) körningar.| 

### <a name="example"></a>Exempel
Följande är osäker exempel: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Använd inte `innerHtml`; i stället använda `innerText`. På samma sätt i stället för `$("#elm").html()`, använda`$("#elm").text()` 

## <a id="redirect-safe"></a>Verifiera att alla omdirigeringar i programmet är stängda eller göra på ett säkert sätt

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | [Auktorisering av OAuth 2.0 - Framework öppna omdirigerare](http://tools.ietf.org/html/rfc6749#section-10.15) |
| **Steg** | <p>Programdesign som kräver omdirigering till en plats som anges av användaren måste begränsa mål för omdirigering av möjligt att en fördefinierad ”säker” lista över platser eller domäner. Alla omdirigeringar i programmet måste vara stängd/säkert.</p><p>Gör så här:</p><ul><li>Identifiera alla omdirigeringar</li><li>Implementera en lämplig lösning för varje omdirigeras. Lämpliga åtgärder omfattar omdirigering godkända eller användare bekräftelse. Om en webbplats eller tjänst med en öppen omdirigering säkerhetsproblem använder OpenID-Facebook/OAuth identitetsleverantörer, kan en angripare stjäl en användares inloggningstoken och personifiera användaren. Detta är en inbyggd risk när du använder OAuth som beskrivs i RFC 6749 ”The OAuth 2.0 auktorisering Framework”, avsnittet 10.15 ”öppna omdirigerar” på samma sätt, användarnas autentiseringsuppgifter kan äventyras av spear nätfiskeattacker med öppna omdirigeringar</li></ul>|

## <a id="string-method"></a>Implementera verifiering av indata på alla typen strängparametrar accepteras av kontrollantmetoder

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Allmän och MVC5, MVC6 |
| **Attribut**              | Saknas  |
| **Referenser**              | [Validera modelldata i ett MVC-program](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [riktlinjer för ASP.NET MVC-program](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Steg** | Verifiering av indata med reguljära uttryck ska göras för metoder som bara godkänner primitiv datatyp och inte modeller som argument. Här ska Regex.IsMatch användas med ett giltigt regex-mönster. Om indata inte matchar det angivna reguljära uttrycket, kontrollen inte ska kunna fortsätta och en förvarning om valideringsfelet ska visas.| 

## <a id="dos-expression"></a>Ange övre gräns för tidsgränsen för reguljärt uttryck bearbetning för att förhindra DoS på grund av felaktiga reguljära uttryck

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk Web Forms, MVC5, MVC6  |
| **Attribut**              | Saknas  |
| **Referenser**              | [Egenskapen DefaultRegexMatchTimeout](https://msdn.microsoft.com/library/system.web.configuration.httpruntimesection.defaultregexmatchtimeout.aspx) |
| **Steg** | För att säkerställa DOS-attacker mot felaktigt ange skapade reguljära uttryck som orsaka mycket slipper tidsgränsen för global standard. Om tid det tar längre tid än den angivna övre gränsen, skulle den utlösa ett undantag. Om inget konfigureras skulle tidsgränsen vara oändlig.| 

### <a name="example"></a>Exempel
Till exempel genereras följande konfiguration en RegexMatchTimeoutException om bearbetningen tar mer än 5 sekunder: 

```C#
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a id="html-razor"></a>Undvik att använda Html.Raw i Razor vyer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | MVC5 MVC6 |
| **Attribut**              | Saknas  |
| **Referenser**              | Saknas  |
| Steg | ASP.Net-webbsidor (Razor) utför automatisk HTML-kodningen. Alla strängar som skrivits ut av inbäddad kod nuggets (@ block) är automatiskt HTML-kodas. Men när `HtmlHelper.Raw` -metoden har anropats, returneras markup som inte är HTML-kodat. Om `Html.Raw()` hjälpmetod används kan det kringgår det automatiska kodning skydd som Razor.|

### <a name="example"></a>Exempel
Följande är en osäker exempel: 

```C#
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
Använd inte `Html.Raw()` om du inte behöver visa markering. Den här metoden utför inte utdata kodning implicit. Använda andra ASP.NET hjälpprogram t.ex.`@Html.DisplayFor()` 

## <a id="stored-proc"></a>Använd inte dynamisk frågor i lagrade procedurer

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | Saknas  |
| **Steg** | <p>En attack med SQL injection utnyttjar sårbarheter i verifiering av indata att köra godtycklig kommandon i databasen. Det kan inträffa när ditt program använder indata för att konstruera dynamiska SQL-uttryck för att få åtkomst till databasen. Det kan också inträffa om din kod använder lagrade procedurer som skickas strängar som innehåller rådata användarindata. Använder SQL injection angrepp, kan angripare köra godtycklig kommandon i databasen. Alla SQL-instruktioner (inklusive SQL-instruktioner i lagrade procedurer) måste vara parametriserade. SQL-instruktioner accepterar tecken som är särskilt utformade för SQL (till exempel enkelt citattecken) utan problem Eftersom de har strikt typkontroll. |

### <a name="example"></a>Exempel
Följande är ett exempel på osäker dynamisk lagrade proceduren: 

```C#
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
Följande är den samma lagrade proceduren som implementeras på ett säkert sätt: 
```C#
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

## <a id="validation-api"></a>Se till att modellen verifieringen är klar för Web API-metoder

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | MVC5 MVC6 |
| **Attribut**              | Saknas  |
| **Referenser**              | [Modellverifiering i ASP.NET webb-API](http://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Steg** | När en klient skickar data till ett webb-API, är det obligatoriskt att verifiera data innan du gör eventuell bearbetning. Använd data anteckningar på modeller för att ange valideringsregler i egenskaperna för modellen för ASP.NET Web API-gränssnitt som acceptera modeller som indata.|

### <a name="example"></a>Exempel
Följande kod visar samma: 

```C#
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
I metoden åtgärd i API-domänkontrollanter har giltighet modellen kontrolleras uttryckligen enligt nedan: 

```C#
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

## <a id="string-api"></a>Implementera verifiering av indata på alla typen strängparametrar accepteras av Web API-metoder

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Allmän och MVC 5, MVC 6 |
| **Attribut**              | Saknas  |
| **Referenser**              | [Validera modelldata i ett MVC-program](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [riktlinjer för ASP.NET MVC-program](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Steg** | Verifiering av indata med reguljära uttryck ska göras för metoder som bara godkänner primitiv datatyp och inte modeller som argument. Här ska Regex.IsMatch användas med ett giltigt regex-mönster. Om indata inte matchar det angivna reguljära uttrycket, kontrollen inte ska kunna fortsätta och en förvarning om valideringsfelet ska visas.|

## <a id="typesafe-api"></a>Se till att typen säkert parametrar används i Web API för dataåtkomst

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | Saknas  |
| **Steg** | <p>Om du använder Parametersamlingen, SQL hanterar är indata som ett litteralvärde stället som körbar kod. Parametersamlingen kan användas för att framtvinga begränsningar för typ och längd på indata. Värden som ligger utanför intervallet utlöser ett undantag. Om typen säkert SQL-parametrar inte är används, kan angripare kunna köra injection attacker som är inbäddade i ofiltrerade indata.</p><p>Säker typparametrar vid SQL-frågor Undvik att använda SQL injection attacker som kan uppstå med ofiltrerade indata. Du kan använda säker typparametrar med lagrade procedurer och dynamiska SQL-uttryck. Parametrarna behandlas som litterala värden av databasen och inte som körbar kod. Parametrar kontrolleras även för typ och längd.</p>|

### <a name="example"></a>Exempel
Följande kod visar hur du använder säker typparametrar med SqlParameterCollection när du anropar en lagrad procedur. 

```C#
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter(LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
I föregående kodexempel kan inte indatavärdet vara längre än 11 tecken. Om data inte stämmer överens med typen eller längd som anges av parametern utlöser klassen SqlParameter ett undantag. 

## <a id="sql-docdb"></a>Använd innehåller parametrar SQL-frågor för Cosmos DB

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure dokumentet DB | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk |
| **Attribut**              | Saknas  |
| **Referenser**              | [Om SQL-Parameterisering i DocumentDB](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Steg** | DocumentDB stöder bara skrivskyddade frågor, är SQL injection fortfarande möjligt om frågor skapas genom att sammanbinda indata från användaren. Det är möjligt att en användare kan komma åt data som de inte får komma åt inom samma samling genom att utforma skadliga SQL-frågor. Använd parametriserade SQL-frågor om frågor baserat på indata från användaren. |

## <a id="schema-binding"></a>WCF-indata verifiering via schemabindning

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk NET Framework 3 |
| **Attribut**              | Saknas  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff647820.aspx) |
| **Steg** | <p>Brist på verifiering leder till annan typ inmatningsangrepp.</p><p>Meddelandevalidering representerar en försvarslinje av WCF-program. Med den här metoden kan du validera meddelanden med scheman för att skydda WCF-tjänståtgärder mot angrepp av en obehörig klient. Validera alla meddelanden som tagits emot av klienten skyddas klienten från angrepp av skadlig tjänsten. Meddelandevalidering gör det möjligt att verifiera meddelanden när aktiviteter kan förbruka meddelandet avtal eller datakontrakt som inte kan göras med Parametervalidering. Meddelandevalidering kan du skapa valideringslogik inuti scheman, vilket ger bättre flexibilitet och minska tid. Scheman kan återanvändas i olika program i organisationen, skapa normer för data representation. Dessutom kan meddelandevalidering du skydda åtgärder när de använder mer komplexa datatyper som involverar kontrakt som representerar affärslogik.</p><p>Om du vill utföra meddelandevalidering skapa du först ett schema som representerar din tjänst och datatyper som används av dessa åtgärder. Sedan kan du skapa en .NET-klass som implementerar en anpassad klient meddelandet inspector och anpassade dispatcher meddelandet inspector för att verifiera de meddelanden som skickas/tas emot till eller från tjänsten. Därefter kan du implementera en anpassad slutpunkt för att aktivera meddelandevalidering på både klienten och tjänsten. Slutligen kan du implementera en anpassad konfigurationselement på den klass som gör att du kan visa den utökade anpassade slutpunkt i konfigurationsfilen för tjänsten eller klienten ”</p>|

## <a id="parameters"></a>WCF - indata verifiering via parametern kontrollanter

| Rubrik                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL fas**               | Utveckla |  
| **Tillämpliga tekniker** | Generisk NET Framework 3 |
| **Attribut**              | Saknas  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff647875.aspx) |
| **Steg** | <p>Indata och dataverifiering representerar en viktig försvarslinje av WCF-program. Du bör verifiera alla parametrar som visas i WCF-tjänståtgärder tjänsten skyddas från angrepp av en obehörig klient. Däremot bör du också verifiera alla returvärden tas emot av klient skyddas klienten från angrepp av skadlig tjänsten</p><p>WCF erbjuder olika punkter så att du kan anpassa beteendet WCF runtime genom att skapa anpassade tillägg. Meddelande-kontrollanter och parametern-kontrollanter är två utökningsbarhet mekanismer som används för att få bättre kontroll över de data som skickas mellan en klient och en tjänst. Du bör använda parametern-kontrollanter för verifiering av indata och använder meddelandet kontrollanter endast när du behöver granska hela meddelandet flödar till och från en tjänst.</p><p>För att utföra verifiering av indata måste du skapa en .NET-klass och implementera en anpassad parameter inspector för att kunna verifiera parametrar på åtgärder i tjänsten. Du kommer sedan att implementera en anpassad slutpunkt för att aktivera verifiering på både klienten och tjänsten. Du kommer slutligen att implementera en anpassad konfigurationselement på den klass som gör att du kan visa den utökade anpassade slutpunkt i konfigurationsfilen för tjänsten eller klienten</p>|
