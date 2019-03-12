---
title: Viktig information om Azure Data Catalog
description: Viktig information om Azure Data Catalog.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: 3aca9c49-45a4-4352-92e6-bd25ee3eacf7
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 60c5b7b55e417a5703010ea34cf75dcb20146c37
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531687"
---
# <a name="azure-data-catalog-release-notes"></a>Viktig information om Azure Data Catalog
## <a name="notes-for-the-november-20-2015-release-of-azure-data-catalog"></a>För 20 November 2015-versionen av Azure Data Catalog
### <a name="opening-data-sources-in-power-bi-desktop"></a>Öppna datakällor i Power BI Desktop
När du använder alternativet ”Öppna i Power BI Desktop” från den **Azure Data Catalog** portal, kan användarna få ett av två problemen i Power BI Desktop-programmet:

* En dialogruta med rubriken ”det gick inte att öppna dokumentet” visas
* Power BI Desktop-program öppnas, men filen verkar vara tomt

För vardera situation, kan problemet lösas genom att hämta och installera den senaste versionen av Power BI Desktop från [PowerBI.com](https://powerbi.com).

## <a name="notes-for-the-november-13-2015-release-of-azure-data-catalog"></a>För den 13 November 2015-versionen av Azure Data Catalog
### <a name="registering-and-connecting-to-teradata"></a>Registrera och ansluta till Teradata
När du ansluter till Teradata-datakällor användare måste ha installerat rätt Teradata ODBC-drivrutin som matchar bitarna (32-bitars eller 64-bitars) av programvara som används.

Från och med den här ADC versionsdatum, den senaste [Teradata ODBC-drivrutin för windows (version 15.10)](https://downloads.teradata.com/download/connectivity/odbc-driver/windows) är kompatibel med Office 2013, men inte med Office 2016.

## <a name="notes-for-the-july-13-2015-release-of-azure-data-catalog"></a>För 13 juli 2015-versionen av Azure Data Catalog
### <a name="registering-and-connecting-to-oracle-database"></a>Registrera och ansluta till Oracle-databas
När du ansluter till Oracle Database-datakällor som användarna måste ha installerat rätt Oracle-drivrutiner som matchar bitarna (32-bitars eller 64-bitars) av programvara som används.

* När du registrerar Oracle-datakällor på en dator med 32-bitars Windows ska 32-bitars Oracle drivrutiner användas
* När du registrerar Oracle-datakällor på en dator som kör 64-bitars Windows ska 64-bitars Oracle drivrutiner användas
* När du ansluter till Oracle-datakällor i Excel på en dator med 32-bitarsversionen av Microsoft Office, ska inklusive på 64-bitars Windows 32-bitars Oracle drivrutiner användas
* När du ansluter till Oracle-datakällor i Excel på en dator som kör 64-bitarsversion av Microsoft Office, ska 64-bitars Oracle drivrutiner användas

### <a name="registering-and-connecting-to-sql-server-reporting-services"></a>Registrera och ansluta till SQL Server Reporting Services
Stöd för SQL Server Reporting Services (SSRS)-datakällor är för närvarande begränsad till endast servrar för enhetligt läge. Stöd för SSRS i SharePoint-läge kommer att läggas till i en senare version.

### <a name="opening-data-assets-in-excel"></a>Öppna datatillgångar i Excel
När du öppnar du datatillgångar i Microsoft Excel från den **Azure Data Catalog** portal, användarna får en fråga med en **Microsoft Excel-säkerhetsmeddelande** dialogrutan. Det här är standard, förväntat beteende och användare kan välja **aktivera** att fortsätta.

Mer information finns i [aktivera eller inaktivera säkerhetsaviseringar om länkar och filer från misstänkta webbplatser](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

### <a name="proxy-and-policy-configuration-and-data-source-registration"></a>Proxy och princip för konfiguration och data för registrering av datakälla
Användare kan uppstå en situation där de kan logga in till Azure Data Catalog-portalen, men när de försöker logga in till registreringsverktyget som de får ett felmeddelande som hindrar dem från att logga in.

Det finns två möjliga orsaker till detta problem:

**Orsak 1: Active Directory Federation Services-konfigurering** registreringsverktyget använder formulärautentisering för att validera användarinloggningar mot Active Directory. För lyckad inloggning måste formulär för autentisering aktiveras i den globala autentiseringsprincipen av en Active Directory-administratör.

I vissa situationer kan kan det här felet inträffa endast när användaren är ansluten till företagets nätverk eller bara när användaren ansluter från utanför företagets nätverk. Den globala autentiseringsprincipen kan autentiseringsmetoder aktiveras separat för intranät och extranät-anslutningar. Inloggningsfel kan uppstå om formulärautentisering inte är aktiverat för det virtuella nätverket som användaren ansluter.

Mer information finns i [konfigurera autentiseringsprinciper](https://technet.microsoft.com/library/dn486781.aspx).

**Orsak 2: Nätverkskonfiguration för proxy** om företagsnätverket använder en proxyserver, registreringsverktyget kanske inte kan ansluta till Azure Active Directory via proxy. Användare kan se till att registreringsverktyget genom att redigera konfigurationsfilen för verktyget, lägger till det här avsnittet i filen:

      <system.net>
        <defaultProxy useDefaultCredentials="true" enabled="true">
          <proxy usesystemdefault="True"
                          proxyaddress="http://<your corporate network proxy url>"
                          bypassonlocal="True"/>
        </defaultProxy>
      </system.net>


Starta registreringsverktyget för att hitta filen RegistrationTool.exe.config, och sedan öppna Aktivitetshanteraren i Windows-verktyget. På fliken information i Aktivitetshanteraren högerklickar du på RegistrationTool.exe och välj Öppna filens mapp på snabbmenyn.
