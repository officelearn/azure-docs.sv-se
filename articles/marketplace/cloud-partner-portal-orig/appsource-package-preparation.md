---
title: Förberedelse av AppSource-paket | Azure Marketplace
description: Explanaion för att förbereda och skapa AppSource-paket.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 76f8cbd6cb16b585a7dbda7b2ffa5eeeeb1b68d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280617"
---
# <a name="appsource-package-preparation"></a>Förberedelse av AppSource-paket

Förutom en lösning. zip-fil behöver du ett **AppSource-paket**. Detta är en. zip-fil som innehåller alla till gångar som behövs för att automatisera processen för att distribuera din lösning till kundernas CRM-miljö. **AppSource-paketet** innehåller följande komponenter

* Paket för paket distributionen
* **Content_Types. XML-** fil med de till gångar som du använder
* XML-fil med dina AppData
* 32 x 32-logotyp som visas med din lista i administrations centret
* Licens villkor, sekretess policy

Stegen nedan hjälper dig att skapa ditt AppSource-paket.

## <a name="a-create-a-package-for-the-package-deployer"></a>a. Skapa ett paket för paket distributionen

Paketet för paket distributionen är en del av AppSource-paketet.

Använd följande instruktioner om du vill skapa ett paket för paket distributionen: [https://msdn.microsoft.com/library/dn688182.aspx](https://msdn.microsoft.com/library/dn688182.aspx). När det är klart kommer ditt paket att bestå av till gångarna nedan:

1. Paketera mapp: innehåller alla lösningar, konfigurations data, flata filer och innehållet för paketet. _Obs! i exemplet nedan kommer vi att anta att Package-mappen kallas "PkgFolder"._
2. DLL: sammansättningen innehåller den anpassade koden för ditt paket. _Obs! i exemplet nedan kommer vi att anta att filen heter "MicrosoftSample. dll"._

Nu måste du skapa en fil med namnet "**Content_Types. XML**" den här filen visar alla till gångs tillägg som ingår i paketet. Här är exempel koden för filen.

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

Det sista steget är att zippa in följande i en enda fil. Anropa IT **Package. zip**. Den kommer att innehålla

1. PkgFolder (inklusive allt i mappen)
2. DLL
3. **Content_Types. XML**

Steg för att skapa Package. zip:

1. Lägg till din Package-mapp, **Content_Types. XML-** fil och PackageName. dll i en katalog.

![CRMScreenShot2](media/CRMScreenShot2.png)

1. Markera alla objekt i mappen, högerklicka och välj Skicka till komprimerad mapp (zip)

![CRMScreenShot3](media/CRMScreenShot3.png)

1. Ändra namnet till Package. zip

![CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. Skapa ett AppSource-paket

AppSource-paketet kräver ytterligare filer.

1. jpg (32 x 32-lösning)
2. HTML (HTML-formaterad fil)
3. **Content_Types. XML** (samma som ovan)
4. xml

Här är exempel koden för indata. xml. Se definitioner i tabellen nedan.

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
 
**Vilken**

|Field|Information|
|---|---|
|ProviderName|Vilken lösning kommer från. Om du har ett Microsoft-team bör det vara Microsoft.|
|PackageFile |Paket distributions till gångar zippade samman med\_en innehålls typ. XML-fil. Den här zip-filen ska innehålla paketets distributions paket och mappen med paket distributions resurserna. Det vill säga Package. zip|
|SolutionAnchorName |Namnet på zip-filen för lösningen i paket distributionen som används som visnings namn och beskrivning av lösnings till gångar.|
| StartDate| Detta är det datum då lösnings paketet blir tillgängligt. Formatet är MM/DD/ÅÅÅÅ|
|EndDate|Detta är det datum då lösnings paketet upphör att vara tillgängligt. Formatet är MM/DD/ÅÅÅÅ |
|SupportedCountries |Det här är en kommaavgränsad lista över länder/regioner som ska se det här paketet. Kontakta onlinetjänster om du vill visa en lista över alla aktuella lands koder. I samband med att du skriver listan var: AE, AL, AM, AO, P.A., AT, AU, AZ, BA, BB, BD, BG BH, BM, BN, BO, BR, av, CA, CH, CI, CL, CM, CO, CR, ka, FV, CY, CZ, L', DK,, DZ, EC, EE, t, ES, s, FR, GB, GE,, GR, GT, HK, HN, HR, HU, ID, IE, IL , I, ÄR SWEETIQ, JM, EVA, JP, KE, KG, KN, KR, KW, KY, KZ, LB, LK, LT, LU, LV, LY, MA, MC, MD, ME, MK, MN, MO, MT, MU, MX, MY, NG, NI, NL, NO, NZ, OM, PA, PE, PH, PK, PL, PR, PS, PT, PY, FRÅGOR OCH SVAR, RO, RS, RU, RW, SA, SE, SG, SI, SK, SN, SA, TH, TM, TN , TR, TT, TW, UA, US, UY, UZ, VE, VI, VN, ZA, ZW |
|LearnMoreLink | URL till sidan mer information för det här paketet. |
|Språk|En instans av den här noden för varje UX-språk som du vill stödja i den primära lösningens UX. Den här noden innehåller underordnade som beskriver språk, logo typ och villkor för varje språk|
|Språk: PackageLocale. Code|LCID för språket för den här noden. Exempel: US English är 1033|
|Språk: PackageLocale. IsDefault|Anger att detta är standard språket. Detta används som fortsättnings språk om det UX-språk som valts av kunden inte är tillgängligt.|
|Språk: Logo typ|Detta om den logo typ som du vill använda för det här paketet. Ikonens storlek är 32 x 32. Tillåtna format är PNG och JPG|
|Språk: villkor: PackageTerm. File|Detta är fil namnet på HTML-dokumentet som innehåller dina licens villkor.|

Här visas logo typen:

![CRMScreenShot5](media/CRMScreenShot5.png)

Det sista steget är att zippa in följande i en enda fil.

1. zip (skapat tidigare)
2. **Content_Types. XML**
3. xml
4. png
5. html

![CRMScreenShot6](media/CRMScreenShot6.png)

Byt namn på filen till något lämpligt för din app. Vi rekommenderar att du inkluderar ditt företags namn och ditt namn på appen. Exempel: **_Microsoft_SamplePackage. zip**.
