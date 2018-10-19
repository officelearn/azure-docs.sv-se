---
title: Autentisering och användarbehörigheter i Azure Analysis Services | Microsoft Docs
description: Läs mer om autentisering och användarbehörigheter i Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6280544a42d0d5012b01446ec8c3bc386ef861dd
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49428427"
---
# <a name="authentication-and-user-permissions"></a>Autentisering och användarbehörigheter
Azure Analysis Services använder Azure Active Directory (Azure AD) för identity management- och användarautentisering. Alla användare skapa, hantera eller ansluta till en Azure Analysis Services server måste ha en giltig användaridentitet i en [Azure AD-klient](../active-directory/fundamentals/active-directory-administer.md) i samma prenumeration.

Azure Analysis Services stöder [Azure AD B2B-samarbete](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md). Med B2B, kan användare utanför en organisation bjudas in som gästanvändare i Azure AD-katalog. Gäster kan vara från en annan katalog i Azure AD-klient eller någon giltig e-postadress. Inbjuden en gång och att användaren godkänner skicka inbjudan via e-post från Azure, användar-ID läggs till i klientkatalog. Dessa identiteter kan läggas till säkerhetsgrupper eller som medlemmar i en serverroll för administratör eller databasen.

![Arkitektur för Azure Analysis Services-autentisering](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Autentisering
Alla program och verktyg använder en eller flera av Analysis Services [klientbibliotek](analysis-services-data-providers.md) (AMO MSOLAP, ADOMD) för att ansluta till en server. 

Samtliga tre klientbibliotek stöder både Azure AD interaktiva flödet och icke-interaktiv autentiseringsmetoder. Två metoder för icke-interaktivt, Active Directory-lösenord och Active Directory-integrerad autentisering metoder kan användas i program som använder AMOMD och MSOLAP. Dessa två metoder innebär aldrig popup-dialogrutor.

Klientprogram som Excel och Power BI Desktop och verktyg som SSMS och SSDT installera de senaste versionerna av bibliotek när har uppdaterats till den senaste versionen. Power BI Desktop, SSMS och SSDT uppdateras varje månad. Excel är [uppdateras med Office 365](https://support.office.com/article/When-do-I-get-the-newest-features-in-Office-2016-for-Office-365-da36192c-58b9-4bc9-8d51-bb6eed468516). Office 365-uppdateringar är uppstår mer sällan, och vissa organisationer använder den uppskjutna kanalen, vilket innebär ska skjutas upp upp till tre månader.

Beroende på klientprogram eller verktyg som du använder, skilja vilken typ av autentisering och hur du loggar in sig. Varje program stöder olika funktioner för att ansluta till molntjänster som Azure Analysis Services.

Power BI Desktop och SSDT SSMS stöd för Active Directory Universal-autentisering, en interaktiv metod som också har stöd för Azure Multi-Factor Authentication (MFA). Azure MFA hjälper till att skydda åtkomsten till data och program samtidigt som du tillhandahåller en enkel inloggningsprocess. Du får stark autentisering med flera alternativ för verifiering (telefonsamtal, textmeddelande, smartkort och PIN-kod eller mobilapp). Interaktiv MFA med Azure AD kan resultera i en popup-dialogruta för verifiering. **Universell autentisering rekommenderas**.

Om du loggar in i Azure med hjälp av ett Windows-konto och universell autentisering inte har valts eller tillgängliga (Excel), [Active Directory Federation Services (AD FS)](../active-directory/hybrid/how-to-connect-fed-azure-adfs.md) krävs. Med Federation, Azure AD och Office 365-användare som autentiseras med hjälp av lokala autentiseringsuppgifter och har åtkomst till Azure-resurser.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)
Azure Analysis Services-servrarna har stöd för anslutningar från [SSMS V17.1](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) och senare med hjälp av Windows-autentisering, Active Directory-lösenordsautentisering och Active Directory Universal-autentisering. I allmänhet bör du använda Active Directory Universal-autentisering eftersom:

*  Stöder interaktiv och icke-interaktiva autentiseringsmetoder.

*  Stöder Azure B2B-gästanvändare som bjuds in till Azure AS-klient. När du ansluter till en server, Välj gästanvändare Active Directory Universal-autentisering när du ansluter till servern.

*  Stöder Multifaktorautentisering (MFA). Azure MFA hjälper till att skydda åtkomsten till data och program med en mängd alternativ för verifiering: telefonsamtal, textmeddelande, smartkort och PIN-kod eller mobilapp. Interaktiv MFA med Azure AD kan resultera i en popup-dialogruta för verifiering.

### <a name="sql-server-data-tools-ssdt"></a>SQL Server Data Tools (SSDT)
SSDT ansluter till Azure Analysis Services med hjälp av Active Directory Universal-autentisering med stöd för MFA. Användare uppmanas att logga in på Azure på den första distributionen. Användarna måste logga in på Azure med ett konto med administratörsbehörighet för servern på servern de distribuerar till. När du loggar in till Azure första gången, tilldelas en token. SSDT cachelagrar det token i minnet för framtida Återanslutningar till.

### <a name="power-bi-desktop"></a>Power BI Desktop
Power BI Desktop ansluter till Azure Analysis Services med Active Directory Universal-autentisering med stöd för MFA. Användare uppmanas att logga in på Azure på den första anslutningen. Användarna måste logga in på Azure med ett konto som ingår i en serveradministratör eller databasrollen.

### <a name="excel"></a>Excel
Excel-användare kan ansluta till en server med hjälp av ett Windows-konto, en organisations-ID (e-postadress) eller en extern e-postadress. Externa e-identiteter måste finnas i Azure AD som gästanvändare.

## <a name="user-permissions"></a>Användarbehörigheter

**Serveradministratörer** är specifika för en Azure Analysis Services-serverinstansen. De ansluter med verktyg som Azure-portalen, SSMS och SSDT för att utföra uppgifter som att lägga till databaser och hantera användarroller. Som standard läggs användaren som skapas automatiskt som en administratör för Analysis Services-server. Andra administratörer kan läggas till med hjälp av Azure-portalen eller SSMS. Server-administratörer måste ha ett konto i Azure AD-klient i samma prenumeration. Mer information finns i [hantera serveradministratörer](analysis-services-server-admins.md). 

**Databasen användare** ansluta till databaser med hjälp av klientprogram som Excel eller Power BI. Användare måste läggas till databasroller. Databasroller definierar administratören, process eller Läs-och skrivrättigheter för en databas. Det är viktigt att förstå databasanvändare i en roll med administratörsbehörighet skiljer sig från server-administratörer. Som standard är serveradministratörer dock också databasadministratörer. Mer information finns i [hanterar databasroller och användare](analysis-services-database-users.md).

**Azure resursägare**. Resursägare hantera resurser för en Azure-prenumeration. Resursägare kan lägga till Azure AD-användaridentiteter till ägare eller Deltagarroller inom en prenumeration med hjälp av **åtkomstkontroll** i Azure portal eller med Azure Resource Manager-mallar. 

![Åtkomstkontroll i Azure-portalen](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Roller på den här nivån gäller för användare eller konton som behöver för att utföra uppgifter som kan utföras i portalen eller med hjälp av Azure Resource Manager-mallar. Mer information finns i [Role-Based Access Control](../role-based-access-control/overview.md). 


## <a name="database-roles"></a>Databasroller

 Roller som definierats för en tabellmodell är databasroller. Det vill säga rollerna innehåller medlemmar som består av Azure AD-användare och säkerhetsgrupper som har specifika behörigheter som definierar åtgärden dessa medlemmar kan ha en modelldatabasen. En databasroll skapas som ett separat objekt i databasen och gäller endast för databasen som rollen har skapats i.   
  
 Som standard när du skapar ett nytt tabellmodellprojekt har modellprojektet inte några roller. Roller kan definieras med hjälp av dialogrutan rollhanteraren i SSDT. När roller definieras under modellen Projektdesign tillämpas de endast på arbetsytan modelldatabasen. När modellen har distribuerats kan tillämpas samma roller på distribuerad modell. När en modell har distribuerats, server och databasadministratörer kan hantera roller och medlemmar med hjälp av SSMS. Mer information finns i [hanterar databasroller och användare](analysis-services-database-users.md).
  


## <a name="next-steps"></a>Nästa steg

[Hantera åtkomst till resurser med Azure Active Directory-grupper](../active-directory/fundamentals/active-directory-manage-groups.md)   
[Hantera databasroller och användare](analysis-services-database-users.md)  
[Hantera serveradministratörer](analysis-services-server-admins.md)  
[Rollbaserad åtkomstkontroll](../role-based-access-control/overview.md)  