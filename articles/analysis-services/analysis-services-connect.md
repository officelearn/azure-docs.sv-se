---
title: Ansluta till Azure Analysis Services-servrar| Microsoft-dokument
description: Lär dig hur du ansluter till och hämtar data från en Analysis Services-server i Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: cc671629934d80b3e727ca69f9026f534d05e160
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676651"
---
# <a name="connecting-to-servers"></a>Ansluta till servrar

I den hÃ¤r artikeln beskrivs anslutning till en server med datamodellering och datahanteringsprogram som SQL Server Management Studio (SSMS) eller Visual Studio with Analysis Services-projekt, eller med klientrapporteringsprogram som Microsoft Excel, Power BI Desktop eller anpassade program. Anslutningar till Azure Analysis Services använder HTTPS.

## <a name="client-libraries"></a>Klientbibliotek

[Hämta de senaste klientbiblioteken](analysis-services-data-providers.md)

Alla anslutningar till en server, oavsett typ, kräver uppdaterade AMO-, ADOMD.NET- och OLEDB-klientbibliotek för att ansluta till och samverka med en Analysis Services-server. För SSMS, Visual Studio, Excel 2016 och senare, och Power BI, installeras eller uppdateras de senaste klientbiblioteken med månadsversioner. Men i vissa fall är det möjligt att ett program kanske inte har det senaste. Till exempel när principer fördröjer uppdateringar eller Office 365-uppdateringar finns på uppskjuten kanal.

> [!NOTE]
> Klientbiblioteken kan inte ansluta till Azure Analysis Services via proxyservrar som kräver ett användarnamn och lösenord. 

## <a name="server-name"></a>servernamn

När du skapar en Analysis Services-server i Azure anger du ett unikt namn och den region där servern ska skapas. När du anger servernamnet i en anslutning är servernamngivningsschemat:

```
<protocol>://<region>/<servername>
```
 Där protokollet är sträng **asazure**är regionen Uri där servern skapades (till exempel westus.asazure.windows.net) och servernamn är namnet på din unika server i regionen.

### <a name="get-the-server-name"></a>Hämta servernamnet

Kopiera hela servernamnet i **Azure portal** > server >**Översiktsservernamn.** **Overview** >  Om andra användare i organisationen också ansluter till den här servern kan du dela det här servernamnet med dem. När du anger ett servernamn måste hela sökvägen användas.

![Hämta servernamnet i Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> Protokollet för östra USA 2 regionen är **aspaaseastus2**.

## <a name="connection-string"></a>Anslutningssträng

När du ansluter till Azure Analysis Services med tabellobjektmodellen använder du följande anslutningssträngformat:

###### <a name="integrated-azure-active-directory-authentication"></a>Integrerad Azure Active Directory-autentisering

Integrerad autentisering hämtar Azure Active Directory-autentiseringsuppgifterna om det är tillgängligt. Om inte visas Azure-inloggningsfönstret.

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

## <a name="connect-using-an-odc-file"></a>Ansluta med en ODC-fil

Med äldre versioner av Excel kan användare ansluta till en Azure Analysis Services-server med hjälp av en Office Data Connection-fil (.odc). Mer information finns i [Skapa en Office Data Connection -fil (.odc).](analysis-services-odc.md)


## <a name="next-steps"></a>Nästa steg

[Anslut med Excel](analysis-services-connect-excel.md)    
[Anslut med Power BI](analysis-services-connect-pbi.md)   
[Hantera servern](analysis-services-manage.md)   

