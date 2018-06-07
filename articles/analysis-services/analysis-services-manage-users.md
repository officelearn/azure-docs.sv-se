---
title: Autentisering och användarbehörigheter i Azure Analysis Services | Microsoft Docs
description: Lär dig mer om autentisering och användare i Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 47846af015bf02940da7a7b8a4154113aab57b08
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597638"
---
# <a name="authentication-and-user-permissions"></a>Autentisering och användarbehörigheter
Azure Analysis Services använder Azure Active Directory (Azure AD) för identity management- och användarautentisering. Alla användare att skapa, hantera eller ansluta till en Azure Analysis Services server måste ha ett giltigt användar-ID i ett [Azure AD-klient](../active-directory/active-directory-administer.md) i samma prenumeration.

Azure Analysis Services stöder [Azure AD B2B-samarbete](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md). Med B2B, kan användare utanför organisationen bjudas in som gästanvändare i en Azure AD-katalog. Gäster kan vara från en annan katalog i Azure AD-klient eller en giltig e-postadress. Uppmanas en gång och att användaren godkänner inbjudan som skickas via e-post från Azure, användaridentitet har lagts till i klientkatalogen. Dessa identiteter kan läggas till säkerhetsgrupper eller som medlemmar i en serverroll som administratör eller databasen.

![Arkitektur för Azure Analysis Services-autentisering](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Autentisering
Alla program och verktyg som ska använda en eller flera av Analysis Services [klientbiblioteken](analysis-services-data-providers.md) (AMO MSOLAP, ADOMD) för att ansluta till en server. 

Alla tre klientbibliotek stöder både Azure AD interaktiva flödet och icke-interaktiv autentiseringsmetoder. Två metoder för icke-interaktiv, Active Directory-lösenord och Active Directory-integrerad autentisering metoder kan användas i program som använder AMOMD och MSOLAP. Dessa två metoder innebär aldrig popup-dialogrutor.

Program som Excel och Power BI Desktop och verktyg som SSMS och SSDT installera de senaste versionerna av bibliotek när uppdateras till den senaste versionen. Power BI Desktop SSMS och SSDT uppdateras varje månad. Excel är [uppdateras med Office 365](https://support.office.com/en-us/article/When-do-I-get-the-newest-features-in-Office-2016-for-Office-365-da36192c-58b9-4bc9-8d51-bb6eed468516). Office 365-uppdateringar som är mer sällan och vissa organisationer använder uppskjutna kanalen, betydelse uppdateringar är uppskjutna upp till tre månader.

Beroende på den klientprogrammet eller verktyg som används för vara typ av autentisering och hur du loggar in olika. Varje program stöder olika funktioner för att ansluta till molntjänster som Azure Analysis Services.

Power BI Desktop och SSDT SSMS stöd för Active Directory Universal-autentisering, en interaktiv metod som också stöder Azure Multi-Factor Authentication (MFA). Azure MFA hjälper till att skydda åtkomst till data och program och ger en process för enkel inloggning. Den ger stark autentisering med flera alternativ för verifiering (telefonsamtal, textmeddelande, smartkort och PIN-kod eller mobilapp). Interaktiv MFA med Azure AD kan resultera i en dialogruta för verifiering. **Universal autentisering rekommenderas**.

Logga in till Azure genom att använda ett Windows-konto och Universal autentisering har valts eller tillgängliga (Excel), [Active Directory Federation Services (AD FS)](../active-directory/connect/active-directory-aadconnect-azure-adfs.md) krävs. Med Federation, Azure AD och Office 365-användare som autentiseras med hjälp av lokala autentiseringsuppgifter och har åtkomst till Azure-resurser.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)
Azure Analysis Services-servrar som stöder anslutningar från [SSMS V17.1](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) och senare med hjälp av Windows-autentisering, Active Directory-lösenordsautentisering och Active Directory Universal-autentisering. I allmänhet bör du använda Active Directory Universal autentisering eftersom:

*  Stöder interaktiv och icke-interaktiva autentiseringsmetoder.

*  Stöder Azure B2B gästanvändare bjudits in till Azure AS-klient. När du ansluter till en server Välj gästanvändare Active Directory Universal autentisering när du ansluter till servern.

*  Stöder Multifaktorautentisering (MFA). Azure MFA hjälper till att skydda åtkomst till data och program med en uppsättning alternativ för verifiering: telefonsamtal, textmeddelande, smartkort och PIN-kod eller meddelande i mobilappen. Interaktiv MFA med Azure AD kan resultera i en dialogruta för verifiering.

### <a name="sql-server-data-tools-ssdt"></a>SQL Server Data Tools (SSDT)
SSDT ansluter till Azure Analysis Services med hjälp av Active Directory Universal autentisering med stöd för MFA. Användarna uppmanas att logga in på Azure på den första distributionen. Användarna måste logga in på Azure med ett konto med administratörsbehörighet för servern på servern de distribuerar till. När du loggar in till Azure första gången, tilldelas en token. SSDT cachelagrar det token i minnet för framtida Återanslutningar till.

### <a name="power-bi-desktop"></a>Power BI Desktop
Power BI Desktop ansluter till Azure Analysis Services genom att använda Active Directory Universal med stöd för MFA. Användarna uppmanas att logga in på Azure på den första anslutningen. Användarna måste logga in på Azure med ett konto som ingår i en serveradministratör eller en databasroll.

### <a name="excel"></a>Excel
Excel-användare kan ansluta till en server med hjälp av ett Windows-konto, en organisations-ID (e-postadress) eller en extern e-postadress. Externa e-identiteter måste finnas i Azure AD som gästanvändare.

## <a name="user-permissions"></a>Användarbehörigheter

**Serveradministratörer** är specifika för ett Azure Analysis Services-serverinstansen. De ansluter med verktyg som Azure-portalen, SSMS och SSDT för att utföra uppgifter som att lägga till databaser och hantera användarroller. Som standard läggs den användare som skapar servern automatiskt som administratör för Analysis Services-server. Andra administratörer kan läggas till med hjälp av Azure-portalen eller SSMS. Server-administratörer måste ha ett konto i Azure AD-klient på samma prenumeration. Läs mer i [hantera serveradministratörer](analysis-services-server-admins.md). 

**Databasen användare** ansluta till modellen databaser med hjälp av klientprogram som Excel eller Power BI. Användare måste läggas till databasroller. Databasroller definiera administratör, en process eller läsbehörighet för en databas. Det är viktigt att förstå databasanvändare i en roll med administratörsbehörighet skiljer sig från server-administratörer. Som standard är serveradministratörer dock också databasadministratörer. Läs mer i [hantera databasroller och användare](analysis-services-database-users.md).

**Azure-resurs-ägare**. Resursägare hantera resurser för en Azure-prenumeration. Resursägare kan lägga till Azure AD-användaridentiteter till ägare eller deltagare roller inom en prenumeration med hjälp av **åtkomstkontroll** i Azure-portalen eller med Azure Resource Manager-mallar. 

![Åtkomstkontroll i Azure-portalen](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Roller på den här nivån gälla för användare och konton som behöver för att utföra uppgifter som kan utföras i portalen eller med hjälp av Azure Resource Manager-mallar. Läs mer i [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md). 


## <a name="database-roles"></a>Databasroller

 Rollerna definieras för en tabellmodell är databasroller. Att rollerna innehåller medlemmar som består av Azure AD-användare och säkerhetsgrupper som har specifika behörigheter som definierar åtgärden medlemmarna kan ha en modelldatabasen. En databasroll skapas som ett separat objekt i databasen och gäller endast för databasen som rollen har skapats.   
  
 Som standard när du skapar ett nytt projekt i tabellmodell har projektet modellen inte några roller. Roller kan definieras med hjälp av dialogrutan rollhanteraren i SSDT. När rollerna har definierats under modellen projektets utformning används de endast för arbetsytan modelldatabasen. När modellen har distribuerats, används samma roller till distribuerade modellen. När en modell har distribuerats, kan server och databasadministratörer hantera roller och medlemmar med hjälp av SSMS. Läs mer i [hantera databasroller och användare](analysis-services-database-users.md).
  


## <a name="next-steps"></a>Nästa steg

[Hantera åtkomst till resurser med Azure Active Directory-grupper](../active-directory/active-directory-manage-groups.md)   
[Hantera databasroller och användare](analysis-services-database-users.md)  
[Hantera serveradministratörer](analysis-services-server-admins.md)  
[Rollbaserad åtkomstkontroll](../role-based-access-control/overview.md)  