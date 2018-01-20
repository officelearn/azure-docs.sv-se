---
title: "Viktig information för Azure Data Catalog | Microsoft Docs"
description: "Viktig information för Azure Data Catalog."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 3aca9c49-45a4-4352-92e6-bd25ee3eacf7
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: 249c3a690638bb91d060443d591d8e52791f50ae
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="azure-data-catalog-release-notes"></a>Azure Data Catalog viktig information
## <a name="notes-for-the-november-20-2015-release-of-azure-data-catalog"></a>För 20 November 2015-versionen av Azure Data Catalog
### <a name="opening-data-sources-in-power-bi-desktop"></a>Öppna datakällor i Power BI Desktop
När du använder alternativet ”Öppna i Power BI Desktop” från den **Azure Data Catalog** Företagsportalen kan användare kan stöta på något av två problem i Power BI Desktop-program:

* En dialogruta visas med rubriken ”det gick inte att öppna dokumentet” visas
* Power BI Desktop-programmet öppnas, men filen verkar vara tomt

För varje fall kan problemet lösas genom att hämta och installera den senaste versionen av Power BI Desktop från [PowerBI.com](https://powerbi.com).

## <a name="notes-for-the-november-13-2015-release-of-azure-data-catalog"></a>För 13 November 2015-versionen av Azure Data Catalog
### <a name="registering-and-connecting-to-teradata"></a>Registrera och ansluta till Teradata
När du ansluter till Teradata datakällor användare måste ha installerat rätt Teradata ODBC-drivrutin som matchar bitness (32-bitars eller 64-bitars) för den programvara som används.

Från och med den här ADC lanseringsdatumet, den senaste [Teradata ODBC-drivrutin för windows (version 15.10)](http://downloads.teradata.com/download/connectivity/odbc-driver/windows) är kompatibel med Office 2013, men inte med Office 2016.

## <a name="notes-for-the-july-13-2015-release-of-azure-data-catalog"></a>För 13 juli 2015-versionen av Azure Data Catalog
### <a name="registering-and-connecting-to-oracle-database"></a>Registrera och ansluta till Oracle-databas
När du ansluter till datakällor i Oracle-databas måste användare har installerat rätt Oracle drivrutiner som matchar bitness (32-bitars eller 64-bitars) för den programvara som används.

* När du registrerar Oracle-datakällor på en dator med 32-bitars Windows kommer 32-bitars Oracle drivrutiner att användas
* När du registrerar Oracle-datakällor på en dator som kör 64-bitars Windows kommer 64-bitars Oracle drivrutiner att användas
* När du ansluter till Oracle-datakällor på en dator med 32-bitars version av Microsoft Office Excel, kommer inklusive på 64-bitars Windows 32-bitars Oracle drivrutiner att användas
* 64-bitars Oracle drivrutiner kommer att användas vid anslutning till Oracle-datakällor på en dator som kör 64-bitarsversionen av Microsoft Office Excel

### <a name="registering-and-connecting-to-sql-server-reporting-services"></a>Registrera och ansluta till SQL Server Reporting Services
Stöd för SQL Server Reporting Services (SSRS) datakällor är för närvarande begränsad till enhetligt läge-servrar. Stöd för SSRS i SharePoint-läge ska läggas till i en senare version.

### <a name="opening-data-assets-in-excel"></a>Öppna datatillgångar i Excel
När du öppnar datatillgångar i Microsoft Excel från den **Azure Data Catalog** Företagsportalen kan användare uppmanas med en **säkerhetsmeddelande om Microsoft Excel** dialogrutan. Detta är standard, förväntat beteende och användare kan välja **aktivera** att fortsätta.

Mer information finns i [aktivera eller inaktivera säkerhetsvarningar om länkar och filer från misstänkta webbplatser](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

### <a name="proxy-and-policy-configuration-and-data-source-registration"></a>Proxy- och konfiguration och data för registrering av datakälla
Användare kan stöta på en situation där de kan logga in till Azure Data Catalog-portalen, men när de försöker logga in på det registreringsverktyget för datakällor de får ett felmeddelande som hindrar dem från att logga in.

Det finns två möjliga orsaker till detta problem:

**Orsak 1: Active Directory Federation Services configuration** registreringsverktyget för datakällor använder formulärautentisering för att verifiera användarinloggningar mot Active Directory. För lyckad inloggning måste formulär för autentisering aktiveras i den globala autentiseringsprincipen av en Active Directory-administratör.

I vissa situationer kan kan det här felet inträffa endast när användaren är på företagets nätverk, eller endast när användaren ansluter från utanför företagets nätverk. Global autentiseringsprincip tillåter autentiseringsmetoder aktiveras separat för intranät och extranät-anslutningar. Inloggningsfel kan uppstå om formulär för autentisering inte har aktiverats för nätverket som användaren ansluter.

Mer information finns i [konfigurera autentiseringsprinciper](https://technet.microsoft.com/library/dn486781.aspx).

**Orsak 2: Proxy-nätverkskonfigurationen** om företagets nätverk använder en proxyserver, registreringsverktyget kanske inte kan ansluta till Azure Active Directory via proxy. Användare kan se till att registreringsverktyget genom att redigera verktygets konfigurationsfil, lägger till det här avsnittet i filen:

      <system.net>
        <defaultProxy useDefaultCredentials="true" enabled="true">
          <proxy usesystemdefault="True"
                          proxyaddress="http://<your corporate network proxy url>"
                          bypassonlocal="True"/>
        </defaultProxy>
      </system.net>


Starta registreringsverktyget för att hitta filen RegistrationTool.exe.config och sedan öppna Aktivitetshanteraren-verktyget. Högerklicka på RegistrationTool.exe och välj Öppna filplats på popup-menyn på fliken information i Aktivitetshanteraren.
