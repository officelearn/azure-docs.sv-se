---
title: Autentisering och användarbehörigheter för Azure Analysis Services| Microsoft-dokument
description: I den här artikeln beskrivs hur Azure Analysis Services använder Azure Active Directory (Azure AD) för identitetshantering och användarautentisering.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 4a054c3c042e18f1679acd75e5ba5ad74f66edff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572754"
---
# <a name="authentication-and-user-permissions"></a>Autentisering och användarbehörigheter

Azure Analysis Services använder Azure Active Directory (Azure AD) för identitetshantering och användarautentisering. Alla användare som skapar, hanterar eller ansluter till en Azure Analysis Services-server måste ha en giltig användaridentitet i en [Azure AD-klient](../active-directory/fundamentals/active-directory-administer.md) i samma prenumeration.

Azure Analysis Services stöder [Azure AD B2B-samarbete](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md). Med B2B kan användare utanför en organisation bjudas in som gästanvändare i en Azure AD-katalog. Gäster kan komma från en annan Azure AD-klientkatalog eller en giltig e-postadress. När den har bjudits in och användaren har accepterat inbjudan som skickas via e-post från Azure läggs användaridentiteten till i klientkatalogen. Dessa identiteter kan läggas till i säkerhetsgrupper eller som medlemmar i en serveradministratör eller databasroll.

![Azure Analysis Services-autentiseringsarkitektur](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Autentisering

Alla klientprogram och verktyg använder ett eller flera av Analysis [Services-klientbiblioteken](analysis-services-data-providers.md) (AMO, MSOLAP, ADOMD) för att ansluta till en server. 

Alla tre klientbiblioteken stöder både azure AD interaktivt flöde och icke-interaktiva autentiseringsmetoder. De två icke-interaktiva metoderna, Active Directory Password och Active Directory Integrated Authentication metoder kan användas i program som använder AMOMD och MSOLAP. Dessa två metoder resulterar aldrig i popup-dialogrutor.

Klientprogram som Excel och Power BI Desktop och verktyg som SSMS och Analysis Services-projekttillägg för Visual Studio installerar de senaste versionerna av biblioteken när de uppdateras till den senaste versionen. Power BI Desktop-, SSMS- och Analysis Services-projekttillägget uppdateras varje månad. Excel [uppdateras med Office 365](https://support.office.com/article/When-do-I-get-the-newest-features-in-Office-2016-for-Office-365-da36192c-58b9-4bc9-8d51-bb6eed468516). Office 365-uppdateringar är mindre vanliga och vissa organisationer använder den uppskjutna kanalen, vilket innebär att uppdateringarna skjuts upp upp till tre månader.

Beroende på vilket klientprogram eller verktyg du använder kan typen av autentisering och hur du loggar in vara olika. Varje program kan ha stöd för olika funktioner för anslutning till molntjänster som Azure Analysis Services.

Power BI Desktop, Visual Studio och SSMS stöder Universell Active Directory-autentisering, en interaktiv metod som också stöder Azure Multi-Factor Authentication (MFA). Azure MFA hjälper till att skydda åtkomsten till data och program samtidigt som den tillhandahåller en enkel inloggningsprocess. Den ger stark autentisering med flera verifieringsalternativ (telefonsamtal, sms, smartkort med pin eller mobilappsavisering). Interaktiv MFA med Azure AD kan resultera i en popup-dialogruta för validering. **Universell autentisering rekommenderas**.

Om inloggning till Azure med hjälp av ett Windows-konto och universell autentisering inte är markerat eller tillgängligt (Excel), krävs [Active Directory Federation Services (AD FS).](../active-directory/hybrid/how-to-connect-fed-azure-adfs.md) Med Federation, Azure AD och Office 365-användare autentiseras med lokala autentiseringsuppgifter och kan komma åt Azure-resurser.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

Azure Analysis Services-servrar stöder anslutningar från [SSMS V17.1](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) och senare med hjälp av Windows-autentisering, Active Directory-lösenordsautentisering och Universell Active Directory-autentisering. I allmänhet rekommenderar vi att du använder Universell Active Directory-autentisering eftersom:

*  Stöder interaktiva och icke-interaktiva autentiseringsmetoder.

*  Stöder Azure B2B-gästanvändare som bjudits in till Azure AS-klienten. När du ansluter till en server måste gästanvändare välja Universell Active Directory-autentisering när de ansluter till servern.

*  Stöder MFA (Multi Factor Authentication). Azure MFA hjälper till att skydda åtkomsten till data och program med en rad olika verifieringsalternativ: telefonsamtal, sms, smartkort med pin eller avisering av mobilappar. Interaktiv MFA med Azure AD kan resultera i en popup-dialogruta för validering.

### <a name="visual-studio"></a>Visual Studio

Visual Studio ansluter till Azure Analysis Services med hjälp av Active Directory Universal Authentication med MFA-stöd. Användare uppmanas att logga in på Azure på den första distributionen. Användare måste logga in på Azure med ett konto med serveradministratörsbehörigheter på den server som de distribuerar till. När du loggar in på Azure första gången tilldelas en token. Token cachelagras i minnet för framtida återanslutningar.

### <a name="power-bi-desktop"></a>Power BI Desktop

Power BI Desktop ansluter till Azure Analysis Services med Hjälp av Active Directory Universal Authentication med MFA-stöd. Användare uppmanas att logga in på Azure på den första anslutningen. Användare måste logga in på Azure med ett konto som ingår i en serveradministratör eller databasroll.

### <a name="excel"></a>Excel

Excel-användare kan ansluta till en server med hjälp av ett Windows-konto, ett organisations-ID (e-postadress) eller en extern e-postadress. Externa e-postidentiteter måste finnas i Azure AD som gästanvändare.

## <a name="user-permissions"></a>Användarbehörigheter

**Serveradministratörer** är specifika för en Azure Analysis Services-serverinstans. De ansluter med verktyg som Azure portal, SSMS och Visual Studio för att utföra uppgifter som att lägga till databaser och hantera användarroller. Som standard läggs användaren som skapar servern automatiskt till som serveradministratör för Analysis Services. Andra administratörer kan läggas till med hjälp av Azure Portal eller SSMS. Serveradministratörer måste ha ett konto i Azure AD-klienten i samma prenumeration. Mer information finns i [Hantera serveradministratörer](analysis-services-server-admins.md). 

**Databasanvändare** ansluter till modelldatabaser med hjälp av klientprogram som Excel eller Power BI. Användare måste läggas till i databasroller. Databasroller definierar administratörs-, process- eller läsbehörigheter för en databas. Det är viktigt att förstå databasanvändare i en roll med administratörsbehörighet skiljer sig från serveradministratörer. Som standard är dock serveradministratörer också databasadministratörer. Mer information finns i [Hantera databasroller och användare](analysis-services-database-users.md).

**Azure-resursägare**. Resursägare hanterar resurser för en Azure-prenumeration. Resursägare kan lägga till Azure AD-användaridentiteter i ägar- eller deltagarroller i en prenumeration med hjälp av **åtkomstkontroll** i Azure-portalen eller med Azure Resource Manager-mallar. 

![Åtkomstkontroll i Azure-portalen](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Roller på den här nivån gäller för användare eller konton som behöver utföra uppgifter som kan slutföras i portalen eller med hjälp av Azure Resource Manager-mallar. Mer information finns i [Rollbaserad åtkomstkontroll](../role-based-access-control/overview.md). 

## <a name="database-roles"></a>Databasroller

 Roller som definierats för en tabellmodell är databasroller. Det vill än, rollerna innehåller medlemmar som består av Azure AD-användare och säkerhetsgrupper som har specifika behörigheter som definierar vilken åtgärd som dessa medlemmar kan vidta på en modelldatabas. En databasroll skapas som ett separat objekt i databasen och gäller endast för databasen som rollen har skapats i.   
  
 När du skapar ett nytt tabellmodellprojekt har modellprojektet som standard inga roller. Roller kan definieras med hjälp av dialogrutan Rollhanteraren i Visual Studio. När roller definieras under modellprojektdesign tillämpas de bara på modellarbetsytadatabasen. När modellen distribueras tillämpas samma roller på den distribuerade modellen. När en modell har distribuerats kan server- och databasadministratörer hantera roller och medlemmar med hjälp av SSMS. Mer information finns i [Hantera databasroller och användare](analysis-services-database-users.md).
  
## <a name="next-steps"></a>Nästa steg

[Hantera åtkomst till resurser med Azure Active Directory-grupper](../active-directory/fundamentals/active-directory-manage-groups.md)   
[Hantera databasroller och användare](analysis-services-database-users.md)  
[Hantera serveradministratörer](analysis-services-server-admins.md)  
[Rollbaserad åtkomstkontroll](../role-based-access-control/overview.md)  