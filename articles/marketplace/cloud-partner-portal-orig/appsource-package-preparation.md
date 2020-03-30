---
title: Förberedelse av paketpaket för appsource | Azure Marketplace
description: Explanaion i hur man förbereder och bygger AppSource-paket .
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 76f8cbd6cb16b585a7dbda7b2ffa5eeeeb1b68d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280617"
---
# <a name="appsource-package-preparation"></a>Förberedelse av paketpaket för AppSource

Förutom en solution.zip-fil behöver du ett **AppSource-paket**. Detta är en ZIP-fil som innehåller alla tillgångar som behövs för att automatisera processen att distribuera din lösning i kundernas CRM-miljö. **AppSource-paketet** har följande komponenter

* Paket för paket deployer
* **Content_Types.xml-fil** med de resurser du använder
* xml-fil med appspecifika data
* 32x32 logotyp som visas med dina uppgifter i Admin Center
* Licensvillkor, sekretesspolicy

Stegen nedan hjälper dig att skapa ditt AppSource-paket.

## <a name="a-create-a-package-for-the-package-deployer"></a>a. Skapa ett paket för paketde deployer

Paketet för paketde deployer är en del av AppSource-paketet.

Om du vill skapa ett paket för paketdestruktören använder du följande instruktioner: [https://msdn.microsoft.com/library/dn688182.aspx](https://msdn.microsoft.com/library/dn688182.aspx). När det är klart kommer ditt paket att bestå av tillgångarna nedan:

1. Paketmapp: innehåller alla lösningar, konfigurationsdata, platta filer och innehållet för paketet. _Obs: I exemplet som följer kommer vi att anta att paketmappen heter "PkgFolder"_
2. dll: Sammansättningen innehåller den anpassade koden för ditt paket. _I exemplet som följer antar vi att den här filen heter "MicrosoftSample.dll"._

Nu måste du skapa en fil som heter "**Content_Types.xml**" Den här filen kommer att lista alla tillgångar tillägg som ingår i ditt paket. Här är exempelkoden för filen.

    <?xml version="1.0" encoding="utf-8"?>
        <Types xmlns="http://schemas.openxmlformats.org/package/2006/content-types">
        <Default Extension="xml" ContentType="application/octet-stream" />
        <Default Extension="xaml" ContentType="application/octet-stream" />
        <Default Extension="dll" ContentType="application/octet-stream" />
        <Default Extension="zip" ContentType="application/octet-stream" />
        <Default Extension="jpg" ContentType="application/octet-stream" />
        <Default Extension="gif" ContentType="application/octet-stream" />
        <Default Extension="png" ContentType="application/octet-stream" />
        <Default Extension="htm" ContentType="application/octet-stream" />
        <Default Extension="html" ContentType="application/octet-stream" />
        <Default Extension="db" ContentType="application/octet-stream" />
        <Default Extension="css" ContentType="application/octet-stream" />
    </Types>

Det sista steget är att zip följande i en fil. Kalla det **package.zip**. Det kommer att innehålla

1. PkgFolder (inklusive allt i mappen)
2. Dll
3. **Content_Types.xml**

Steg för att skapa package.zip:

1. Placera paketmappen, **filen Content_Types.xml** och PackageName.dll i en katalog.

![CRMScreenShot2](media/CRMScreenShot2.png)

1. Markera alla objekt i mappen, högerklicka och välj Skicka till komprimerad mapp (zip)

![CRMScreenShot3](media/CRMScreenShot3.png)

1. Ändra namnet till package.zip

![CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. Skapa ett AppSource-paket

AppSource-paketet kräver några ytterligare filer.

1. jpg (32x32 upplösning)
2. html (HTML-formaterad fil)
3. **Content_Types.xml** (samma som ovan)
4. xml

Här är exempelkod för input.xml. Se definitioner i tabellen nedan.

    <PvsPackageData>
        <ProviderName>Microsoft</ProviderName>
        <PackageFile>package.zip</PackageFile>
        <SolutionAnchorName>SampleSolution.zip</SolutionAnchorName>
        <StartDate>01/01/2016</StartDate>
        <EndDate>01/01/2021</EndDate>
        <SupportedCountries>US,CA</SupportedCountries>
        <LearnMoreLink>https://www.microsoft.com</LearnMoreLink>
        <Locales>
        <PackageLocale Code="1033" IsDefault="true">
        <Logo>logo32x32.png</Logo>
        <Terms>
        <PackageTerm File="TermsOfUse.html" />
        </Terms>
        </PackageLocale>
        </Locales>
    </PvsPackageData>
 
**Där:**

|Field|Information|
|---|---|
|ProviderName|Vem är lösningen som kommer ifrån. Om ett Microsoft-team, bör detta vara Microsoft.|
|PackageFile |Package Deployer-resurser zippade\_tillsammans med en content types.xml-fil. Den här zip-filen ska innehålla paket deployer-sammansättningen och mappen med paket deployer-tillgångarna. Det vill än, package.zip|
|SolutionAnchorName |Namn på zip-filen lösning i paketdispertören som används för visningsnamnet och beskrivningen av lösningstillgångar.|
| StartDate| Detta är det datum då lösningspaketet blir tillgängligt. Formatet är MM/DD/YYYY|
|EndDate (EndDate)|Detta är det datum då lösningspaketet slutar vara tillgängligt. Formatet är MM/DD/YYYY |
|Länder som stöds |Detta är en kommaavgränsad lista över länder/regioner som bör se det här paketet. Kontakta onlinetjänster för en lista över alla aktuella landskoder. Vid den tidpunkten var förteckningen: AE,AL,AM,AO,AR, AT,AU,AZ,BA, BB, BD, BE, BG,BH,BM,BN,BO,BR,BY,CA,CH,CI,CL,CM,CO,CR,CV,CW,CY,CZ,DE,DK,DO,DZ,EC, EE, EG ,ES,FI,FR,GB,GE,GH,GR,GT,HK,HN,HR,HU,ID,IE,IL,IN,IQ,IS,IT,JM,JO,JP,KE,KN,KN,KR,KW,KY,KZ,LB,LK,LT,LU,LV,LY,MA,MC,MD,ME,MK,MN,MO, MT,MU,MX,MY ,NG,NI,NL,NO,NZ,OM,PA,PE,PH,PK,PL,PR,PS,PT,PY,QA,RO,RS,RU,RW,SA,SE,SG,SI,SK,SN,SV,TH,TM,TN,TR,TT,TW,UA,US,UY,UZ,VE,VI,VN,ZA,ZW |
|Lär merLänk | URL till mer info sida för detta paket. |
|Locales|En instans av den här noden för varje UX-språk som du vill stödja i UX-lösningen preferred solution. Den här noden innehåller underordnade som beskriver språk, logotyp och termer för varje språk|
|Språk: PackageLocale.Code|LCID av språket för den här noden. Exempel: amerikansk engelska är 1033|
|Språk: PackageLocale.IsDefault|Anger att detta är standardspråket. Detta används som reservspråk om det UX-språk som kunden valt inte är tillgängligt.|
|Språk: Logotyp|Detta om logotypen du vill använda för detta paket. Storleken för Icon är 32x32. Format som tillåts är PNG och JPG|
|Språk:Villkor: PackageTerm.File|Det här är filnamnet på HTML-dokumentet som innehåller dina licensvillkor.|

Här är där logotypen kommer att visas:

![CRMScreenShot5](media/CRMScreenShot5.png)

Det sista steget är att zip följande i en fil.

1. zip (skapad tidigare)
2. **Content_Types.xml**
3. xml
4. png
5. html

![CRMScreenShot6](media/CRMScreenShot6.png)

Byt namn på filen till något som är lämpligt för din app. Vi föredrar att du inkluderar ditt företagsnamn och ditt appnamn. Till exempel: **_Microsoft_SamplePackage.zip**.
