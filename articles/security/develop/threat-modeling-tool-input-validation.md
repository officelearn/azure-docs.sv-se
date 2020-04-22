---
title: Validering av indata – Microsofts verktyg för hotmodellering – Azure | Microsoft-dokument
description: mildrande åtgärder för hot som exponeras i hotmodelleringsverktyget
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 712a0707826f97f29b015a2c5892f8d20577e41b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687883"
---
# <a name="security-frame-input-validation--mitigations"></a>Säkerhetsram: Validering av indata | Mitigations 
| Produkt/tjänst | Artikel |
| --------------- | ------- |
| **Webbprogram** | <ul><li>[Inaktivera XSLT-skript för alla transformeringar med otrodda formatmallar](#disable-xslt)</li><li>[Se till att varje sida som kan innehålla användarkontrollbart innehåll väljer bort automatisk MIME-sniffning](#out-sniffing)</li><li>[Härda eller inaktivera upplösning av XML-entitet](#xml-resolution)</li><li>[Program som använder http.sys utföra URL kanoniskisering verifiering](#app-verification)</li><li>[Se till att lämpliga kontroller finns på plats när du accepterar filer från användare](#controls-users)</li><li>[Kontrollera att typsäkra parametrar används i webbprogrammet för dataåtkomst](#typesafe)</li><li>[Använd separata modellbindningsklasser eller bindningsfilterlistor för att förhindra mvc-massatilldelningssårbarhet](#binding-mvc)</li><li>[Koda ej betrodda webbutdata före rendering](#rendering)</li><li>[Utföra indataverifiering och filtrering på alla egenskaper för strängtyp Modell](#typemodel)</li><li>[Sanitisering bör tillämpas i formulärfält som accepterar alla tecken, t.ex.](#richtext)</li><li>[Tilldela inte DOM-element till sänkor som inte har inbyggd kodning](#inbuilt-encode)</li><li>[Validera alla omdirigeringar i programmet är stängda eller görs på ett säkert sätt](#redirect-safe)</li><li>[Implementera indataverifiering på alla strängtypsparametrar som accepteras av controller-metoder](#string-method)</li><li>[Ange övre tidsgräns för bearbetning av reguljära uttryck för att förhindra DoS på grund av felaktiga reguljära uttryck](#dos-expression)</li><li>[Undvik att använda Html.Raw i razor-vyer](#html-razor)</li></ul> | 
| **Databas** | <ul><li>[Använd inte dynamiska frågor i lagrade procedurer](#stored-proc)</li></ul> |
| **Webb-API** | <ul><li>[Kontrollera att modellvalidering görs på webb-API-metoder](#validation-api)</li><li>[Implementera indataverifiering på alla strängtypsparametrar som accepteras av webb-API-metoder](#string-api)</li><li>[Kontrollera att typsäkra parametrar används i webb-API för dataåtkomst](#typesafe-api)</li></ul> | 
| **Azure-dokument DB** | <ul><li>[Använda parameteriserade SQL-frågor för Azure Cosmos DB](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[WCF-indataverifiering via schemabindning](#schema-binding)</li><li>[WCF- Indatavalidering via parameterinspektörer](#parameters)</li></ul> |

## <a name="disable-xslt-scripting-for-all-transforms-using-untrusted-style-sheets"></a><a id="disable-xslt"></a>Inaktivera XSLT-skript för alla transformeringar med otrodda formatmallar

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [XSLT-säkerhet](https://msdn.microsoft.com/library/ms763800(v=vs.85).aspx), [XsltSettings.EnableScript-egenskap](https://msdn.microsoft.com/library/system.xml.xsl.xsltsettings.enablescript.aspx) |
| **Steg** | XSLT stöder skript inuti formatmallar med hjälp av elementet. `<msxml:script>` Detta gör att anpassade funktioner kan användas i en XSLT-omvandling. Skriptet körs under kontexten för processen som utför transformeringen. XSLT-skript måste inaktiveras i en ej betrodd miljö för att förhindra körning av ej betrodd kod. *Om du använder .NET:* XSLT-skript är inaktiverat som standard. Du måste dock se till att den inte `XsltSettings.EnableScript` uttryckligen har aktiverats via egenskapen.|

### <a name="example"></a>Exempel 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Exempel
Om du använder MSXML 6.0 inaktiveras XSLT-skript som standard. Du måste dock se till att den inte uttryckligen har aktiverats via XML DOM-objektegenskapen AllowXsltScript. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Exempel
Om du använder MSXML 5 eller lägre aktiveras XSLT-skript som standard och du måste uttryckligen inaktivera det. Ange egenskapen ALLOWXSltScript för XML DOM till false. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a name="ensure-that-each-page-that-could-contain-user-controllable-content-opts-out-of-automatic-mime-sniffing"></a><a id="out-sniffing"></a>Se till att varje sida som kan innehålla användarkontrollbart innehåll väljer bort automatisk MIME-sniffning

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [IE8 Säkerhet Del V - Omfattande skydd](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx)  |
| **Steg** | <p>För varje sida som kan innehålla användarkontrollbart innehåll `X-Content-Type-Options:nosniff`måste du använda HTTP-huvudet . För att uppfylla detta krav kan du antingen ange den obligatoriska rubriksidan för sida för endast de sidor som kan innehålla användarkontrollbart innehåll, eller så kan du ställa in det globalt för alla sidor i programmet.</p><p>Varje typ av fil som levereras från en webbserver har en associerad [MIME-typ](https://en.wikipedia.org/wiki/Mime_type) (kallas även *innehållstyp)* som beskriver innehållets natur (det vill ha, bild, text, program osv.)</p><p>X-Content-Type-Options-huvudet är ett HTTP-huvud som gör det möjligt för utvecklare att ange att deras innehåll inte ska MIME-sniffas. Det här huvudet är utformat för att minska MIME-Sniffing-attacker. Stöd för det här huvudet har lagts till i Internet Explorer 8 (IE8)</p><p>Endast användare av Internet Explorer 8 (IE8) kan dra nytta av X-Content-Type-Options. Tidigare versioner av Internet Explorer respekterar för närvarande inte x-content-type-options-huvudet</p><p>Internet Explorer 8 (och senare) är de enda större webbläsare som implementerar en MIME-sniffning opt-out-funktion. Om och när andra större webbläsare (Firefox, Safari, Chrome) implementerar liknande funktioner, kommer denna rekommendation att uppdateras för att inkludera syntax för dessa webbläsare samt</p>|

### <a name="example"></a>Exempel
Om du vill aktivera det rubriker som krävs globalt för alla sidor i programmet kan du göra något av följande: 

* Lägg till huvudet i filen web.config om programmet finns hos Internet Information Services (IIS) 7 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* Lägg till rubriken via\_det globala programmet BeginRequest 

``` 
void Application_BeginRequest(object sender, EventArgs e)
{
  this.Response.Headers[""X-Content-Type-Options""] = ""nosniff"";
} 
```

* Implementera anpassad HTTP-modul 

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

* Du kan aktivera det obligatoriska huvudet endast för specifika sidor genom att lägga till det i enskilda svar: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a name="harden-or-disable-xml-entity-resolution"></a><a id="xml-resolution"></a>Härda eller inaktivera upplösning av XML-entitet

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [XML-entitetsexpansion](https://capec.mitre.org/data/definitions/197.html), [XML Denial of Service-attacker och försvar](https://msdn.microsoft.com/magazine/ee335713.aspx), [MSXML-säkerhetsöversikt](https://msdn.microsoft.com/library/ms754611(v=VS.85).aspx), [Metodtips för att skydda MSXML-kod](https://msdn.microsoft.com/library/ms759188(VS.85).aspx), [NSXMLParserDelegate-protokollreferens](https://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html), [Lösa externa referenser](https://msdn.microsoft.com/library/5fcwybb2.aspx) |
| **Steg**| <p>Även om det inte används i stor utsträckning finns det en funktion i XML som gör att XML-tolken kan expandera makroentiteter med värden som definierats antingen i själva dokumentet eller från externa källor. Dokumentet kan till exempel definiera ett entitetsnamn med värdet "Microsoft",&companyname;så att texten " " visas i dokumentet ersätts automatiskt med texten Microsoft varje gång texten " " visas i dokumentet. Eller så kan dokumentet definiera en entitet "MSFTStock" som refererar till en extern webbtjänst för att hämta det aktuella värdet för Microsoft-lager.</p><p>När som&MSFTStock;helst " " visas det i dokumentet, ersätts det automatiskt med den aktuella aktiekursen. Den här funktionen kan dock missbrukas för att skapa DoS-villkor (Denial of Service). En angripare kan kapsla flera entiteter för att skapa en exponentiell expansion XML-bomb som förbrukar allt tillgängligt minne på systemet. </p><p>Alternativt kan han skapa en extern referens som strömmar tillbaka en oändlig mängd data eller som helt enkelt låser tråden. Därför måste alla team inaktivera intern och/eller extern XML-entitetsmatchning helt om deras program inte använder det, eller manuellt begränsa mängden minne och tid som programmet kan använda för entitetsmatchning om den här funktionen är absolut nödvändig. Om entitetsmatchning inte krävs av ditt program inaktiverar du den. </p>|

### <a name="example"></a>Exempel
För .NET Framework-kod kan du använda följande metoder:

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
Observera att standardvärdet `ProhibitDtd` `XmlReaderSettings` för in är `XmlTextReader` sant, men i det är falskt. Om du använder XmlReaderSettings behöver du inte ange ProhibitDtd till true explicit, men det rekommenderas av säkerhetsskäl som du gör. Observera också att klassen XmlDocument tillåter entitetsmatchning som standard. 

### <a name="example"></a>Exempel
Om du vill inaktivera entitetsupplösning för XmlDocuments använder du `XmlDocument.Load(XmlReader)` överbelastningen av inläsningsmetoden och anger lämpliga egenskaper i argumentet XmlReader för att inaktivera upplösning, vilket illustreras i följande kod: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Exempel
Om det inte är möjligt att inaktivera entitetsmatchning för ditt program anger du egenskapen XmlReaderSettings.MaxCharactersFromEntities till ett rimligt värde enligt programmets behov. Detta kommer att begränsa effekten av potentiella exponentiell expansion DoS-attacker. Följande kod är ett exempel på den här metoden: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Exempel
Om du behöver lösa infogade entiteter men inte behöver lösa externa entiteter anger du egenskapen XmlReaderSettings.XmlResolver till null. Ett exempel: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Observera att i MSXML6 är ProhibitDTD inställt på true (inaktivera DTD-bearbetning) som standard. För Apple OSX/iOS-kod finns det två XML-tolkar som du kan använda: NSXMLParser och libXML2. 

## <a name="applications-utilizing-httpsys-perform-url-canonicalization-verification"></a><a id="app-verification"></a>Program som använder http.sys utföra URL kanoniskisering verifiering

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Alla program som använder http.sys bör följa dessa riktlinjer:</p><ul><li>Begränsa URL-längden till högst 16 384 tecken (ASCII eller Unicode). Det här är den absoluta maximala URL-längden baserat på standardinställningen Internet Information Services (IIS) 6. Webbplatser bör sträva efter en kortare längd än så om möjligt</li><li>Använd standard-I/O-standardklasserna för .NET Framework (till exempel FileStream) eftersom dessa drar nytta av kanoniseringsreglerna i .NET FX</li><li>Skapa en tillåtlista över kända filnamn</li><li>Uttryckligen avvisa kända filtyper du inte kommer att tjäna UrlScan avvisar: exe, bat, cmd, com, htw, ida, idq, htr, idc, shtm[l], stm, skrivare, ini, pol, dat-filer</li><li>Fånga följande undantag:<ul><li>System.ArgumentException (för enhetsnamn)</li><li>System.NotSupportedException (för dataströmmar)</li><li>System.IO.FileNotFoundException (för ogiltiga förrymd filnamn)</li><li>System.IO.DirectoryNotFoundException (för ogiltiga förrymd dirs)</li></ul></li><li>*Kalla inte* ut till Win32-fil-I/O API:er. På en ogiltig URL returnera graciöst ett 400-fel till användaren och logga det verkliga felet.</li></ul>|

## <a name="ensure-appropriate-controls-are-in-place-when-accepting-files-from-users"></a><a id="controls-users"></a>Se till att lämpliga kontroller finns på plats när du accepterar filer från användare

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Obegränsad filöverföring](https://www.owasp.org/index.php/Unrestricted_File_Upload), [tabell för filsignatur](https://www.garykessler.net/library/file_sigs.html) |
| **Steg** | <p>Uppladdade filer utgör en betydande risk för program.</p><p>Det första steget i många attacker är att få lite kod till systemet som ska attackeras. Då attacken behöver bara hitta ett sätt att få koden utförd. Med hjälp av en filöverföring hjälper angriparen att utföra det första steget. Konsekvenserna av obegränsad filuppladdning kan variera, inklusive fullständigt systemövertagande, ett överbelastat filsystem eller databas, vidarebefordra attacker till backend-system och enkel defacement.</p><p>Det beror på vad programmet gör med den uppladdade filen och särskilt var den lagras. Validering på serversidan av filöverföringar saknas. Följande säkerhetskontroller bör implementeras för filuppladdningsfunktioner:</p><ul><li>Filändelsen kontrollera (endast en giltig uppsättning tillåtna filtyp bör accepteras)</li><li>Maximal filstorleksgräns</li><li>Filen bör inte laddas upp till webroot; platsen ska vara en katalog på icke-systemenhet</li><li>Namngivningskonventionen bör följas, så att det uppladdade filnamnet har viss slumpmässighet, för att förhindra att fil skrivs över</li><li>Filer bör genomsökas efter anti-virus innan du skriver till disken</li><li>Kontrollera att filnamnet och andra metadata (t.ex. filsökvägen) valideras för skadliga tecken</li><li>Filformat signatur bör kontrolleras, för att förhindra att en användare från att ladda upp en maskerad fil (t.ex. ladda upp en exe-fil genom att ändra förlängning till txt)</li></ul>| 

### <a name="example"></a>Exempel
Den sista punkten om validering av signatur för filformat finns i klassen nedan för mer information: 

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

## <a name="ensure-that-type-safe-parameters-are-used-in-web-application-for-data-access"></a><a id="typesafe"></a>Kontrollera att typsäkra parametrar används i webbprogrammet för dataåtkomst

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Om du använder samling parametrar behandlas indata som ett litteralt värde snarare än körbar kod. Samlingen Parametrar kan användas för att framtvinga typ- och längdbegränsningar för indata. Värden utanför intervallet utlöser ett undantag. Om typsäkra SQL-parametrar inte används kan angripare utföra injektionsattacker som är inbäddade i den ofiltrerade indata.</p><p>Använd typsäkra parametrar när du konstruerar SQL-frågor för att undvika eventuella SQL-injektionsattacker som kan uppstå med ofiltrerad indata. Du kan använda typsäkra parametrar med lagrade procedurer och med dynamiska SQL-uttryck. Parametrar behandlas som litterala värden av databasen och inte som körbar kod. Parametrar kontrolleras också för typ och längd.</p>|

### <a name="example"></a>Exempel 
Följande kod visar hur du använder typsäkra parametrar med SqlParameterCollection när du anropar en lagrad procedur. 

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
I exemplet med föregående kod får indatavärdet inte vara längre än 11 tecken. Om data inte överensstämmer med den typ eller längd som definieras av parametern, genererar klassen SqlParameter ett undantag. 

## <a name="use-separate-model-binding-classes-or-binding-filter-lists-to-prevent-mvc-mass-assignment-vulnerability"></a><a id="binding-mvc"></a>Använd separata modellbindningsklasser eller bindningsfilterlistor för att förhindra mvc-massatilldelningssårbarhet

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | MVC5, MVC6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Metadataattribut](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.metadatatypeattribute), [säkerhetsproblem och begränsning av säkerhetsproblem och begränsning av offentliga](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation)nyckelrum, [komplett guide till masstilldelning i ASP.NET MVC](https://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [Komma igång med EF med MVC](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Steg** | <ul><li>**När ska jag leta efter överpublicering sårbarheter? -** Överpublicering sårbarheter kan uppstå var som helst du binda modellklasser från användarindata. Ramverk som MVC kan representera användardata i anpassade .NET-klasser, inklusive Vanliga gamla CLR-objekt (POCOs). MVC fyller automatiskt dessa modellklasser med data från begäran, vilket ger en bekväm representation för att hantera användarindata. När dessa klasser innehåller egenskaper som inte ska anges av användaren kan programmet vara sårbart för överpubliceringsattacker, vilket gör att användaren kan kontrollera data som programmet aldrig avsett. Precis som MVC-modellbindning stöder databasåtkomsttekniker som objekt-/relationsmappningar som Entity Framework ofta också att använda POCO-objekt för att representera databasdata. Dessa datamodellklasser ger samma bekvämlighet när det gäller att hantera databasdata som MVC gör när det gäller att hantera indata från användaren. Eftersom både MVC och databasen stöder liknande modeller, som POCO-objekt, verkar det lätt att återanvända samma klasser för båda ändamålen. Denna praxis misslyckas med att bevara separation av problem, och det är ett gemensamt område där oavsiktliga egenskaper utsätts för modellbindning, vilket möjliggör överpubliceringsattacker.</li><li>**Varför ska jag inte använda mina ofiltrerade databasmodellklasser som parametrar för mina MVC-åtgärder?** Eftersom MVC modell bindning kommer att binda allt i den klassen. Även om data inte visas i din vy kan en obehörig användare skicka en HTTP-begäran med dessa data inkluderade, och MVC binder gärna den eftersom din åtgärd säger att databasklassen är formen på data som den bör acceptera för indata från användaren.</li><li>**Varför ska jag bry mig om formen som används för modellbindning?** Om du använder ASP.NET MVC-modellbindning med alltför breda modeller exponeras ett program för överbokade attacker. Överpublicering kan göra det möjligt för angripare att ändra programdata utöver vad utvecklaren avsett, till exempel att åsidosätta priset för ett objekt eller säkerhetsbehörigheterna för ett konto. Program bör använda åtgärdsspecifika bindningsmodeller (eller specifika tillåtna egenskapsfilterlistor) för att tillhandahålla ett uttryckligt kontrakt för vad som inte är betrodda indata för att tillåta via modellbindning.</li><li>**Är att ha separata bindande modeller bara duplicera kod? -** Nej, det handlar om att skilja oro. Om du återanvänder databasmodeller i åtgärdsmetoder säger du att alla egenskaper (eller underegenskaper) i den klassen kan ställas in av användaren i en HTTP-begäran. Om det inte är vad du vill att MVC ska göra behöver du en filterlista eller en separat klassform för att visa MVC vilka data som kan komma från indata från användaren i stället.</li><li>**Om jag har separata bindningsmodeller för användarindata, måste jag duplicera alla mina dataanteckningsattribut?** Inte nödvändigtvis. Du kan använda MetadataTypeAttribute i databasmodellklassen för att länka till metadata på en modellbindningsklass. Observera bara att den typ som refereras av MetadataTypeAttribute måste vara en delmängd av referenstypen (den kan ha färre egenskaper, men inte fler).</li><li>**Det är tråkigt att flytta data fram och tillbaka mellan användarindatamodeller och databasmodeller. Kan jag bara kopiera över alla fastigheter med hjälp av reflektion? -** Ja, det gör jag. De enda egenskaper som visas i bindningsmodellerna är de som du har bestämt dig för att vara säkra för indata från användaren. Det finns ingen säkerhetsorsak som förhindrar att du använder reflektion för att kopiera alla egenskaper som finns gemensamt mellan dessa två modeller.</li><li>**Vad sägs om [Bind(Uteslut ="â&euro;¦")]. Kan jag använda det istället för att ha separata bindningsmodeller? -** Detta tillvägagångssätt rekommenderas inte. Att använda [Bind(Exclude&euro;="â ¦")] innebär att alla nya egenskaper kan bindas som standard. När en ny egenskap läggs till finns det ett extra steg att komma ihåg för att skydda saker, i stället för att designen ska vara säker som standard. Beroende på utvecklaren kontrollera denna lista varje gång en fastighet läggs till är riskabelt.</li><li>**Är [Bind(Include ="â&euro;¦")] användbart för redigeringsåtgärder?** Nej. [Bind(Include ="â&euro;¦")] är endast lämplig för INSERT-liknande åtgärder (lägga till nya data). För UPDATE-liknande åtgärder (revidera befintliga data) använder du en annan metod, som att ha separata bindningsmodeller eller skicka en explicit lista över tillåtna egenskaper till UpdateModel eller TryUpdateModel. Om du lägger till ett&euro;[Bind(Include ="â ¦")] -attribut för en redigeringsåtgärd skapas en objektförekomst och endast de angivna egenskaperna anges, vilket gör att alla andra får standardvärdena. När data sparas ersätter den helt den befintliga entiteten och återställer värdena för utelämnade egenskaper till standardvärdena. Om IsAdmin till exempel utelämnades från ett attribut [Bind(Include ="â&euro;¦")] i en redigeringsåtgärd, återställs alla användare vars namn redigerades via den här åtgärden till IsAdmin = false (alla redigerade användare skulle förlora administratörsstatus). Om du vill förhindra uppdateringar av vissa egenskaper använder du någon av de andra metoderna ovan. Observera att vissa versioner av MVC-verktyg genererar styrenhetsklasser med [Bind(Inkludera ="â&euro;¦")] på Redigera åtgärder och innebär att om du tar bort en egenskap från den listan förhindras överpubliceringsattacker. Enligt beskrivningen ovan fungerar dock inte den metoden som avsett och återställer i stället data i de utelämnade egenskaperna till standardvärdena.</li><li>**För Skapa åtgärder, finns det några varningar som använder&euro;[Bind(Inkludera ="â ¦")] i stället för separata bindningsmodeller?** Ja. Först fungerar inte den här metoden för Redigeringsscenarier, vilket kräver att två separata metoder underhålls för att minska alla överpubliceringssäkerhetsproblem. För det andra, separata bindande modeller genomdriva separation av problem mellan den form som används för&euro;användarindata och den form som används för persistens, något [Bind (Inkludera = "â ¦")] inte gör. För det tredje, observera att [Bind(Inkludera ="â&euro;¦")] endast kan hantera toppnivåegenskaper. Du kan inte bara tillåta delar av underegenskaper (till exempel "Details.Name") i attributet. Slutligen, och kanske viktigast av allt, med hjälp&euro;av [Bind(Inkludera ="â ¦")] lägger till ett extra steg som måste komma ihåg varje gång klassen används för modellbindning. Om en ny åtgärdsmetod binder till dataklassen direkt och glömmer att&euro;inkludera attributet [Bind(Include ="â ¦")] kan den vara&euro;sårbar för överpubliceringsattacker, så metoden [Bind(Inkludera ="â ¦")] är något mindre säker som standard. Om du använder [Bind(Inkludera&euro;="â ¦")] ska du alltid komma ihåg att ange den varje gång dina dataklasser visas som åtgärdsmetodparametrar.</li><li>**För skapa åtgärder, vad sägs om att&euro;placera attributet [Bind(Include ="â ¦")] på själva modellklassen? Undviker inte detta tillvägagångssätt behovet av att komma ihåg att sätta attributet på varje åtgärdsmetod? -** Detta tillvägagångssätt fungerar i vissa fall. Om du använder [Bind(Include ="â&euro;¦")] på själva modelltypen (i stället för på åtgärdsparametrar med&euro;den här klassen) undviker du behovet av att komma ihåg att inkludera attributet [Bind(Include ="â ¦")] på varje åtgärdsmetod. Om du använder attributet direkt på klassen skapas effektivt en separat yta för den här klassen för modellbindningsändamål. Den här metoden tillåter dock endast en modellbindningsform per modellklass. Om en åtgärdsmetod behöver tillåta modellbindning av ett fält (till exempel en endast administratörsåtgärd som uppdaterar användarroller) och andra åtgärder måste förhindra modellbindning av det här fältet, fungerar inte den här metoden. Varje klass kan bara ha en modellbindningsform. Om olika åtgärder behöver olika modellbindningsformer måste de representera dessa separata former med hjälp&euro;av antingen separata modellbindningsklasser eller separata [Bind(Inkludera ="â ¦")] attribut på åtgärdsmetoderna.</li><li>**Vad är bindande modeller? Är de samma sak som visa modeller? -** Det här är två relaterade begrepp. Termen bindningsmodell refererar till en modellklass som används i en åtgärds parameterlista (formen skickades från MVC-modellbindning till åtgärdsmetoden). Termvymodellen refererar till en modellklass som skickas från en åtgärdsmetod till en vy. Att använda en vyspecifik modell är en vanlig metod för att skicka data från en åtgärdsmetod till en vy. Ofta är den här formen också lämplig för modellbindning, och termvymodellen kan användas för att referera till samma modell som används på båda ställena. För att vara exakt, detta förfarande talar specifikt om bindande modeller, med fokus på den form som gått till åtgärden, vilket är det som är viktigt för massa uppdrag ändamål.</li></ul>| 

## <a name="encode-untrusted-web-output-prior-to-rendering"></a><a id="rendering"></a>Koda ej betrodda webbutdata före rendering

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Generiska, webbformulär, MVC5, MVC6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Hur man förhindrar Cross-site scripting i ASP.NET](https://msdn.microsoft.com/library/ms998274.aspx), [Cross-site Scripting](https://cwe.mitre.org/data/definitions/79.html), [XSS (Cross Site Scripting) Förebyggande cheat sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) |
| **Steg** | Skript över flera webbplatser (ofta förkortat XSS) är en angreppsvektor för onlinetjänster eller program/komponent som förbrukar indata från webben. XSS-säkerhetsproblem kan göra det möjligt för en angripare att köra skript på en annan användares dator via ett sårbart webbprogram. Skadliga skript kan användas för att stjäla cookies och på annat sätt manipulera ett offers maskin via JavaScript. XSS förhindras genom att verifiera användarindata, se till att den är välformad och kodning innan den återges på en webbsida. Indatavalidering och utdatakodning kan göras med hjälp av Webbskyddsbiblioteket. För hanterad\#kod (C, VB.NET osv.) använder du en eller flera lämpliga kodningsmetoder från webbskyddsbiblioteket (Anti-XSS) beroende på i vilket sammanhang användarindatan visas:| 

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

## <a name="perform-input-validation-and-filtering-on-all-string-type-model-properties"></a><a id="typemodel"></a>Utföra indataverifiering och filtrering på alla egenskaper för strängtyp Modell

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Generisk, MVC5, MVC6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Lägga till validering,](https://www.asp.net/mvc/overview/getting-started/introduction/adding-validation) [validera modelldata i ett MVC-program,](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx) [vägledande principer för dina ASP.NET MVC-program](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Steg** | <p>Alla indataparametrar måste valideras innan de används i programmet för att säkerställa att programmet skyddas mot skadliga användarindata. Validera indatavärdena med hjälp av valideringar av reguljära uttryck på serversidan med en verifieringsstrategi för vitlista. Ohygienerade användarindata/parametrar som skickas till metoderna kan orsaka sårbarheter för kodinjektion.</p><p>För webbprogram kan startpunkter även innehålla formulärfält, QueryStrings, cookies, HTTP-huvuden och webbtjänstparametrar.</p><p>Följande valideringskontroller för indata måste utföras vid modellbindning:</p><ul><li>Modellegenskaperna ska kommenteras med RegularExpression-anteckning, för att acceptera tillåtna tecken och maximal tillåten längd</li><li>Styrenhetens metoder ska utföra ModelStates giltighet</li></ul>|

## <a name="sanitization-should-be-applied-on-form-fields-that-accept-all-characters-eg-rich-text-editor"></a><a id="richtext"></a>Sanitisering bör tillämpas i formulärfält som accepterar alla tecken, t.ex.

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Koda osäkra indata](https://msdn.microsoft.com/library/ff647397.aspx#paght000003_step3), [HTML-sanitizer](https://github.com/mganss/HtmlSanitizer) |
| **Steg** | <p>Identifiera alla statiska markeringstaggar som du vill använda. En vanlig metod är att begränsa formateringen `<b>` till säkra `<i>` HTML-element, till exempel (fetstil) och (kursiv).</p><p>Innan du skriver data, HTML-koda den. Detta gör alla skadliga skript säkra genom att det hanteras som text, inte som körbar kod.</p><ol><li>Inaktivera ASP.NET begäran validering genom att lägga till attributet ValidateRequest="false" i \@ siddirektivet</li><li>Koda strängindata med htmlencode-metoden</li><li>Använd en StringBuilder och anropa dess ersättmetod för att selektivt ta bort kodningen på de HTML-element som du vill tillåta</li></ol><p>Den sida-i referenserna inaktiverar ASP.NET begäran `ValidateRequest="false"`validering genom att ställa in . Det HTML-kodar indata och selektivt tillåter `<b>` och `<i>` alternativt kan ett .NET-bibliotek för HTML-sanering också användas.</p><p>HtmlSanitizer är ett .NET-bibliotek för rengöring av HTML-fragment och dokument från konstruktioner som kan leda till XSS-attacker. Den använder AngleSharp för att tolka, manipulera och återge HTML och CSS. HtmlSanitizer kan installeras som ett NuGet-paket och användarindata kan skickas via relevanta HTML- eller CSS-sanitiseringsmetoder, beroende på vad som är tillämpligt, på serversidan. Observera att sanering som säkerhetskontroll endast bör betraktas som ett sista alternativ.</p><p>Indatavalidering och utdatakodning anses vara bättre säkerhetskontroller.</p> |

## <a name="do-not-assign-dom-elements-to-sinks-that-do-not-have-inbuilt-encoding"></a><a id="inbuilt-encode"></a>Tilldela inte DOM-element till sänkor som inte har inbyggd kodning

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | Många javascript-funktioner gör inte kodning som standard. När du tilldelar ej betrodda indata till DOM-element via sådana funktioner kan det leda till XSS-körningar (Cross Site Script).| 

### <a name="example"></a>Exempel
Följande är osäkra exempel: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Använd `innerHtml`inte; använd `innerText`istället . På samma sätt, i stället för `$("#elm").html()`, använd`$("#elm").text()` 

## <a name="validate-all-redirects-within-the-application-are-closed-or-done-safely"></a><a id="redirect-safe"></a>Validera alla omdirigeringar i programmet är stängda eller görs på ett säkert sätt

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Oauth 2.0-auktoriseringsramverket - Öppna omdirigerare](https://tools.ietf.org/html/rfc6749#section-10.15) |
| **Steg** | <p>Programdesign som kräver omdirigering till en plats som tillhandahålls av användaren måste begränsa de möjliga omdirigeringsmålen till en fördefinierad "säker" lista över platser eller domäner. Alla omdirigeringar i programmet måste vara stängda/säkra.</p><p>Gör så här:</p><ul><li>Identifiera alla omdirigeringar</li><li>Implementera en lämplig begränsning för varje omdirigering. Lämpliga åtgärder inkluderar omdirigera vitlista eller användarbekräftelse. Om en webbplats eller tjänst med ett öppet omdirigeringssårbarhet använder Facebook/OAuth/OpenID-identitetsleverantörer kan en angripare stjäla en användares inloggningstoken och personifiera användaren. Detta är en inneboende risk när du använder OAuth, som dokumenteras i RFC 6749 "The OAuth 2.0 Authorization Framework", avsnitt 10.15 "Öppna omdirigeringar" På samma sätt kan användarnas autentiseringsuppgifter äventyras av spjutfiskeattacker med öppna omdirigeringar med öppna omdirigeringar</li></ul>|

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-controller-methods"></a><a id="string-method"></a>Implementera indataverifiering på alla strängtypsparametrar som accepteras av controller-metoder

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Generisk, MVC5, MVC6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Validera modelldata i ett MVC-program](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [vägledande principer för dina ASP.NET MVC-program](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Steg** | För metoder som bara accepterar primitiv datatyp, och inte modeller som argument, bör indatavalidering med reguljärt uttryck göras. Här Regex.IsMatch bör användas med ett giltigt regex mönster. Om indata inte matchar det angivna reguljära uttrycket bör kontrollen inte fortsätta ytterligare och en lämplig varning om valideringsfel ska visas.| 

## <a name="set-upper-limit-timeout-for-regular-expression-processing-to-prevent-dos-due-to-bad-regular-expressions"></a><a id="dos-expression"></a>Ange övre tidsgräns för bearbetning av reguljära uttryck för att förhindra DoS på grund av felaktiga reguljära uttryck

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Generiska, webbformulär, MVC5, MVC6  |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Egenskapen DefaultRegexMatchTimeout](https://msdn.microsoft.com/library/system.web.configuration.httpruntimesection.defaultregexmatchtimeout.aspx) |
| **Steg** | För att säkerställa dos-attacker mot dåligt skapade reguljära uttryck, som orsakar en hel del bakåtspårning, ställer in den globala standardtidsgränsen. Om bearbetningstiden tar längre tid än den definierade övre gränsen, skulle det kasta ett timeout-undantag. Om inget är konfigurerat skulle tidsgränsen vara oändlig.| 

### <a name="example"></a>Exempel
Följande konfiguration kommer till exempel att kasta en RegexMatchTimeoutException om bearbetningen tar mer än 5 sekunder: 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a name="avoid-using-htmlraw-in-razor-views"></a><a id="html-razor"></a>Undvik att använda Html.Raw i razor-vyer

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webbprogram | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | MVC5, MVC6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| Steg | ASP.NET -webbsidor (Razor) utför automatisk HTML-kodning. Alla strängar som skrivs ut av inbäddade kodklimpar (@ block) automatiskt HTML-kodas. Men när `HtmlHelper.Raw` metoden anropas returneras markering som inte är HTML-kodad. Om `Html.Raw()` hjälpmetoden används kringgås det automatiska kodningsskyddet som Razor tillhandahåller.|

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
Använd inte `Html.Raw()` om du inte behöver visa markering. Den här metoden utför inte utdatakodning implicit. Använd andra ASP.NET hjälpare t.ex.`@Html.DisplayFor()` 

## <a name="do-not-use-dynamic-queries-in-stored-procedures"></a><a id="stored-proc"></a>Använd inte dynamiska frågor i lagrade procedurer

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Databas | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>En SQL-injektionsattack utnyttjar sårbarheter i indataverifiering för att köra godtyckliga kommandon i databasen. Det kan inträffa när ditt program använder indata för att konstruera dynamiska SQL-satser för att komma åt databasen. Det kan också inträffa om koden använder lagrade procedurer som skickas strängar som innehåller rå användarindata. Med hjälp av SQL-injektionsattacken kan angriparen köra godtyckliga kommandon i databasen. Alla SQL-uttryck (inklusive SQL-satser i lagrade procedurer) måste parameteriseras. Parameteriserade SQL-uttryck accepterar tecken som har särskild betydelse för SQL (som enstaka citattecken) utan problem eftersom de är starkt maskinskrivna. |

### <a name="example"></a>Exempel
Följande är ett exempel på osäker dynamisk lagrad procedur: 

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
Följande är samma lagrade förfarande som genomförs på ett säkert sätt: 
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

## <a name="ensure-that-model-validation-is-done-on-web-api-methods"></a><a id="validation-api"></a>Kontrollera att modellvalidering görs på webb-API-metoder

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | MVC5, MVC6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Modellvalidering i webb-API ASP.NET](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Steg** | När en klient skickar data till ett webb-API är det obligatoriskt att validera data innan du utför någon bearbetning. För ASP.NET webb-API:er som accepterar modeller som indata använder du dataanteckningar på modeller för att ange valideringsregler för modellens egenskaper.|

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
I åtgärdsmetoden för API-styrenheterna måste modellens giltighet uttryckligen kontrolleras enligt nedan: 

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

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-web-api-methods"></a><a id="string-api"></a>Implementera indataverifiering på alla strängtypsparametrar som accepteras av webb-API-metoder

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Generisk, MVC 5, MVC 6 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Validera modelldata i ett MVC-program](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [vägledande principer för dina ASP.NET MVC-program](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Steg** | För metoder som bara accepterar primitiv datatyp, och inte modeller som argument, bör indatavalidering med reguljärt uttryck göras. Här Regex.IsMatch bör användas med ett giltigt regex mönster. Om indata inte matchar det angivna reguljära uttrycket bör kontrollen inte fortsätta ytterligare och en lämplig varning om valideringsfel ska visas.|

## <a name="ensure-that-type-safe-parameters-are-used-in-web-api-for-data-access"></a><a id="typesafe-api"></a>Kontrollera att typsäkra parametrar används i webb-API för dataåtkomst

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Webb-API | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | Ej tillämpligt  |
| **Steg** | <p>Om du använder samling parametrar behandlas indata som ett litteralt värde snarare än körbar kod. Samlingen Parametrar kan användas för att framtvinga typ- och längdbegränsningar för indata. Värden utanför intervallet utlöser ett undantag. Om typsäkra SQL-parametrar inte används kan angripare utföra injektionsattacker som är inbäddade i den ofiltrerade indata.</p><p>Använd typsäkra parametrar när du konstruerar SQL-frågor för att undvika eventuella SQL-injektionsattacker som kan uppstå med ofiltrerad indata. Du kan använda typsäkra parametrar med lagrade procedurer och med dynamiska SQL-uttryck. Parametrar behandlas som litterala värden av databasen och inte som körbar kod. Parametrar kontrolleras också för typ och längd.</p>|

### <a name="example"></a>Exempel
Följande kod visar hur du använder typsäkra parametrar med SqlParameterCollection när du anropar en lagrad procedur. 

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
I exemplet med föregående kod får indatavärdet inte vara längre än 11 tecken. Om data inte överensstämmer med den typ eller längd som definieras av parametern, genererar klassen SqlParameter ett undantag. 

## <a name="use-parameterized-sql-queries-for-cosmos-db"></a><a id="sql-docdb"></a>Använda parameteriserade SQL-frågor för Cosmos DB

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | Azure-dokument DB | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänna |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [Tillkännage SQL-parameterisering i Azure Cosmos DB](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Steg** | Även om Azure Cosmos DB endast stöder skrivskyddade frågor, är SQL-injektion fortfarande möjlig om frågor skapas genom att sammanfoga med användarindata. Det kan vara möjligt för en användare att få åtkomst till data som de inte bör komma åt i samma samling genom att skapa skadliga SQL-frågor. Använd parameteriserade SQL-frågor om frågor skapas baserat på användarindata. |

## <a name="wcf-input-validation-through-schema-binding"></a><a id="schema-binding"></a>WCF-indataverifiering via schemabindning

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänt, NET Framework 3 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff647820.aspx) |
| **Steg** | <p>Brist på validering leder till olika typ injektion attacker.</p><p>Meddelandevalidering representerar en försvarslinje i skyddet av ditt WCF-program. Med den här metoden validerar du meddelanden med scheman för att skydda WCF-tjänståtgärder från angrepp av en skadlig klient. Verifiera alla meddelanden som tas emot av klienten för att skydda klienten från angrepp av en skadlig tjänst. Meddelandeverifiering gör det möjligt att validera meddelanden när åtgärder förbrukar meddelandekontrakt eller datakontrakt, vilket inte kan göras med hjälp av parametervalidering. Med meddelandevalidering kan du skapa valideringslogik i scheman, vilket ger större flexibilitet och minskad utvecklingstid. Scheman kan återanvändas i olika program inom organisationen, vilket skapar standarder för datarepresentation. Dessutom kan meddelandevalidering du skydda åtgärder när de använder mer komplexa datatyper som involverar kontrakt som representerar affärslogik.</p><p>Om du vill utföra meddelandeverifiering skapar du först ett schema som representerar tjänstens åtgärder och de datatyper som förbrukas av dessa åtgärder. Du skapar sedan en .NET-klass som implementerar en anpassad klientmeddelandeinspektör och anpassad meddelandeinspektör för att validera meddelanden som skickas/tas emot till/från tjänsten. Därefter implementerar du ett anpassat slutpunktsbeteende för att aktivera meddelandeverifiering på både klienten och tjänsten. Slutligen implementerar du ett anpassat konfigurationselement i klassen som gör att du kan exponera det utökade anpassade slutpunktsbeteendet i konfigurationsfilen för tjänsten eller klienten"</p>|

## <a name="wcf--input-validation-through-parameter-inspectors"></a><a id="parameters"></a>WCF- Indatavalidering via parameterinspektörer

| Titel                   | Information      |
| ----------------------- | ------------ |
| **Komponent**               | WCF | 
| **SDL-fas**               | Utveckla |  
| **Tillämplig teknik** | Allmänt, NET Framework 3 |
| **Attribut**              | Ej tillämpligt  |
| **Referenser**              | [MSDN](https://msdn.microsoft.com/library/ff647875.aspx) |
| **Steg** | <p>Indata- och dataverifiering representerar en viktig försvarslinje för att skydda ditt WCF-program. Du bör validera alla parametrar som exponeras i WCF-tjänståtgärder för att skydda tjänsten från angrepp av en skadlig klient. Omvänt bör du också validera alla returvärden som tas emot av klienten för att skydda klienten från angrepp av en skadlig tjänst</p><p>WCF ger olika utökningspunkter som gör att du kan anpassa WCF-körningsbeteendet genom att skapa anpassade tillägg. Meddelandeinspektörer och parameterinspektörer är två utökningsmekanismer som används för att få större kontroll över data som skickas mellan en klient och en tjänst. Du bör bara använda parameterinspektörer för indataverifiering och använda meddelandeinspektörer när du behöver granska hela meddelandet som flödar in och ut ur en tjänst.</p><p>Om du vill utföra indataverifiering skapar du en .NET-klass och implementerar en anpassad parameterinspektör för att validera parametrar för åtgärder i tjänsten. Du implementerar sedan ett anpassat slutpunktsbeteende för att aktivera validering på både klienten och tjänsten. Slutligen implementerar du ett anpassat konfigurationselement i klassen som gör att du kan exponera det utökade anpassade slutpunktsbeteendet i konfigurationsfilen för tjänsten eller klienten</p>|
