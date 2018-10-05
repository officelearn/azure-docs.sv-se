---
title: Förberedelse av paketet i AppSource | Microsoft Docs
description: Explanaion i hur du förbereder och skapa AppSource-paket.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 0b24c5d2f174c9a656e81d0c85e12b589d7d7799
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48811633"
---
# <a name="appsource-package-preparation"></a>Förberedelse av AppSource-paket

Förutom en solution.zip-fil, måste en **AppSource paketet**. Det här är en .zip-fil som innehåller alla de resurser som behövs för att automatisera processen för att distribuera din lösning till kundernas CRM-miljö. Den **AppSource paketet** har följande komponenter

* Paket för Package Deployer
* **Content_Types.XML** fil med de resurser som du använder
* XML-fil med dina appspecifika-data
* 32 x 32 logotyp som visas med din registrering i administrationscentret
* Licensvillkoren sekretesspolicy

Stegen nedan hjälper dig att skapa din AppSource-paket.

## <a name="a-create-a-package-for-the-package-deployer"></a>a. Skapa ett paket för Package Deployer

Paket för Package Deployer är en del av AppSource-paketet.

Använd följande instruktioner för att skapa ett paket för Package Deployer: [ https://msdn.microsoft.com/library/dn688182.aspx ](https://msdn.microsoft.com/library/dn688182.aspx). När du är klar kommer ditt paket bestå av tillgångar nedan:

1. Paketmappen: innehåller alla lösningar, konfigurationsdata, flata filer och innehåll för ditt paket. _Obs: I följande exempel förutsätter vi att paketmappen kallas ”PkgFolder”_
2. DLL-filen: sammansättningen innehåller anpassad kod för ditt paket. _Obs: I följande exempel förutsätter vi att den här filen kallas ”MicrosoftSample.dll”._

Nu kan du behöva skapa en fil med namnet ”**Content_Types.xml**” den här filen visar en lista över alla tillgångar-tillägg som ingår i paketet. Här är exempelkod för filen.

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

Det sista steget är att zip-följande till en fil. Anropa den **package.zip**. Den innehåller

1. PkgFolder (inklusive allt inuti mappen)
2. DLL-filen
3. **Content_Types.XML**

Steg för att skapa package.zip:

1. Placera din paketmappen **Content_Types.xml** fil- och PackageName.dll i en katalog.

![CRMScreenShot2](media/CRMScreenShot2.png)

2. Markera alla objekt i mappen, högerklicka och välj Skicka till komprimerade (zip) mappen

![CRMScreenShot3](media/CRMScreenShot3.png)

3. Ändra namnet till package.zip

![CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. Skapa ett paket med AppSource

AppSource-paketet kräver några ytterligare filer.

1. JPG (32 x 32 upplösning)
2. HTML (HTML-formaterad fil)
3. **Content_Types.XML** (samma som ovan)
4. xml

Här är exempelkod för Input.XML är. Se definitioner i den tabellen nedan.

    <PvsPackageData>
        <ProviderName>Microsoft</ProviderName>
        <PackageFile>package.zip</PackageFile>
        <SolutionAnchorName>SampleSolution.zip</SolutionAnchorName>
        <StartDate>01/01/2016</StartDate>
        <EndDate>01/01/2021</EndDate>
        <SupportedCountries>US,CA</SupportedCountries>
        <LearnMoreLink>http://www.microsoft.com</LearnMoreLink>
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

|Fält|Information|
|---|---|
|ProviderName|Vem är den lösning som kommer från. Om ett Microsoft-team, bör detta vara Microsoft.|
|PackageFile |Paketera Deployer tillgångar zippade tillsammans med en innehåll\_types.xml fil. Den här zipfilen ska innehålla package deployer sammansättning och mappen med package deployer tillgångar. Det vill säga package.zip|
|SolutionAnchorName |Namnet på lösningen zip-filen i package deployer som används för namn och beskrivning av lösning tillgångar.|
| Startdatum| Det här är det datum då lösningspaketet blir tillgängliga. Formatet är åååå-MM-DD|
|endDate|Detta är det datum att lösningspaketet stoppar som det är tillgängligt. Formatet är åååå-MM-DD |
|SupportedCountries |Det här är en kommaavgränsad lista över länder som bör se det här paketet. Kontakta online services för en lista över alla aktuella landskoder. Då detta skriver listan var: AE, AL, AM, AO, AR, AT, Australien, AZ, BA, BB, BD, BE, BG, BH, BM, BN, BO, Brasilien, BY, CA, CH, CI, CL, CM, CO, CR, KA, CW, CY, CZ, DE, DK, gör, DZ, EG, EE, EG , ES, FI, FR, GB, GE, HÖ, GR, GT, HK, HN, HR, HU, ID, INTERNET EXPLORER, IL, IN-, IQ, ÄR DET, JM, JO, JP, KE, KG, KN, KOREA, KW, KY, KZ, LB, LK, LT, LU, LV, LY, MA, MC, MD, MIG, MK, MN, MÅNAD, MT, MU, MX, MIN , NG, NI, NL, NEJ, NZ, OM, PA, PE, PH, PK, PL, PULL-BEGÄRAN, PS, PT, PY, QA, RO, RS, RU, RW-SKIVOR, SA, SE, SG, SI, SK, SN, SA, TH, TM, TN, TR, TT, TW, UA, USA, UY, UZ, STÖTT, VI, VN, ZA, ZW |
|LearnMoreLink | URL till informationslagersidan för mer för det här paketet. |
|Nationella inställningar|En instans av den här noden för varje UX-språk som du vill stödja i prioriterade lösningen UX. Den här noden innehåller underordnade som beskriver de nationella inställningar, logotyp och villkoren för varje språk|
|Nationella inställningar: PackageLocale.Code|LCID för de för den här noden. Exempel: Svenska är 1033|
|Nationella inställningar: PackageLocale.IsDefault|Anger att det här är standardspråket. Detta används som höst tillbaka språk om det valda kunden UX-språket inte är tillgänglig.|
|Nationella inställningar: logotyp|Detta om logotypen som du vill använda för det här paketet. Ikonen storleken är 32 x 32. Format som tillåts är PNG och JPG|
|Nationella inställningar: termer: PackageTerm.File|Det här är namnet på HTML-dokument som innehåller dina licensvillkor.|

Här är där logotypen visas:

![CRMScreenShot5](media/CRMScreenShot5.png)

Det sista steget är att zip-följande till en fil.

1. ZIP (som skapats tidigare)
2. **Content_Types.XML**
3. xml
4. PNG
5. HTML

![CRMScreenShot6](media/CRMScreenShot6.png)

Byt namn på filen till något som är lämplig för din app. Vi föredrar att du inkluderar företagets namn och namn. Till exempel: **_Microsoft_SamplePackage.zip**.
