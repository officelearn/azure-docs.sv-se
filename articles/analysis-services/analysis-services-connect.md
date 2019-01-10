---
title: Ansluta till Azure Analysis Services-servrar | Microsoft Docs
description: Lär dig mer om att ansluta till och hämta data från en Analysis Services-server i Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: e2c60d48595f2c3687bd178e0f4eed010f14b568
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191538"
---
# <a name="connecting-to-servers"></a>Ansluta till servrar

Den här artikeln beskriver ansluter till en server med hjälp av datamodellering och av hanteringsprogram som SQL Server Management Studio (SSMS) eller SQL Server Data Tools (SSDT). Eller, med klienten rapporterar program som Microsoft Excel, Power BI Desktop eller anpassade program. Anslutningar till Azure Analysis Services använder HTTPS.

## <a name="client-libraries"></a>Klientbibliotek

[Hämta de senaste klientbibliotek](analysis-services-data-providers.md)

Alla anslutningar till en server, oavsett typ, kräver uppdaterade AMO och ADOMD.NET OLEDB klientbibliotek för att ansluta till och gränssnitt med en Analysis Services-server. För SSMS, SSDT, Excel 2016 och senare och Power BI, senaste klientbibliotek installeras eller uppdateras med månatliga versioner. I vissa fall kan är det dock ett program kan inte ha den senaste versionen. Till exempel när principer fördröjning uppdaterar eller Office 365-uppdateringar som är på uppskjuten kanalen.

## <a name="server-name"></a>servernamn

När du skapar en Analysis Services-server i Azure kan ange du ett unikt namn och den region där servern är som ska skapas. När du anger servernamnet i en anslutning är namngivningsschemat för servern:

```
<protocol>://<region>/<servername>
```
 Där protokollet är sträng **asazure**, regionen är URI: N där servern skapades (till exempel westus.asazure.windows.net) och servername är namnet på din unika server för regionen.

### <a name="get-the-server-name"></a>Hämta namnet på

I **Azure-portalen** > server > **översikt** > **servernamn**, kopiera hela servernamnet. Om andra användare i din organisation ansluter till den här servern för, kan du dela det här Servernamnet med dem. När du anger ett servernamn måste hela sökvägen användas.

![Hämta servernamnet i Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)


## <a name="connection-string"></a>Anslutningssträng

När du ansluter till Azure Analysis Services format med hjälp av Tabellobjektsmodellen, Använd följande anslutningssträng:

###### <a name="integrated-azure-active-directory-authentication"></a>Azure Active Directory-integrerad autentisering

Integrerad autentisering hämtar den Azure Active Directory cacheminnet om det är tillgängligt. Om inte, visas fönstret för Azure-inloggning.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Azure Active Directory-autentisering med användarnamn och lösenord

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Windows-autentisering (integrerad säkerhet)

Använd Windows-kontot som kör den aktuella processen.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>Ansluta med hjälp av en ODC-fil

Med äldre versioner av Excel kan användarna ansluta till en Azure Analysis Services-server med hjälp av en fil Office Data Connection (.odc). Mer information finns i [skapa en fil Office Data Connection (.odc)](analysis-services-odc.md).


## <a name="next-steps"></a>Nästa steg

[Anslut till Excel](analysis-services-connect-excel.md)    
[Anslut till Powerbi](analysis-services-connect-pbi.md)   
[Hantera servern](analysis-services-manage.md)   

