---
title: Använda AAD-autentisering med flera faktorer
description: Synapse SQL stöder anslutningar från SQL Server Management Studio (SSMS) med hjälp av Universell Active Directory-autentisering.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 8e8f17f54f52aef6f552b7c211aa8f0e498b94df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424925"
---
# <a name="using-multi-factor-aad-authentication-with-synapse-sql-ssms-support-for-mfa"></a>Använda Multifaktor AAD-autentisering med Synapse SQL (SSMS-stöd för MFA)

Synapse SQL stöder anslutningar från SQL Server Management Studio (SSMS) med hjälp av *Universell Active Directory-autentisering*. 

I den här artikeln beskrivs skillnaderna mellan de olika autentiseringsalternativen och även de begränsningar som är förknippade med att använda universell autentisering. 

**Ladda ner den senaste SSMS** - Ladda ner den senaste versionen av SSMS på klientdatorn, från [Download SQL Server Management Studio (SSMS).](https://msdn.microsoft.com/library/mt238290.aspx) 

**Ladda ner den senaste SSMS** - Ladda ner den senaste versionen av SSMS på klientdatorn, från [Download SQL Server Management Studio (SSMS).](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

För alla funktioner som beskrivs i den här artikeln, använd åtminstone juli 2017, version 17.2.  Den senaste anslutningsdialogrutan bör se ut ungefär som följande bild:

![1mfa-universal-anslut](./media/mfa-authentication/1mfa-universal-connect.png "Slutför rutan Användarnamn.")  

## <a name="the-five-authentication-options"></a>De fem autentiseringsalternativen  

Active Directory Universell autentisering stöder de två icke-interaktiva autentiseringsmetoderna:
    - `Active Directory - Password`Autentisering
    - `Active Directory - Integrated`Autentisering

Det finns två icke-interaktiva autentiseringsmodeller också, som kan användas i många olika applikationer (ADO.NET, JDCB, ODC, etc.). Dessa två metoder resulterar aldrig i popup-dialogrutor:

- `Active Directory - Password`
- `Active Directory - Integrated`

Den interaktiva metoden är att även stöder Azure multifaktorautentisering (MFA) är:

- `Active Directory - Universal with MFA`

Azures MFA bidrar till att skydda åtkomsten till data och program och tillgodoser samtidigt användarens önskemål om en enkel inloggningsprocess. Det ger stark autentisering med en rad enkla verifieringsalternativ (telefonsamtal, sms, smartkort med stift eller mobilappsavisering), så att användarna kan välja den metod de föredrar. Interaktiv MFA med Azure AD kan resultera i en popup-dialogruta för validering.

En beskrivning av multifaktorautentisering finns i [Multifaktorautentisering](../../active-directory/authentication//concept-mfa-howitworks.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD-domännamn eller klient-ID-parameter

Från och med [SSMS version 17](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)kan användare som importeras till den aktuella Active Directory från andra Azure Active-kataloger som gästanvändare ange Azure AD-domännamnet eller klient-ID när de ansluter. 

Gästanvändare inkluderar användare som bjudits in från andra Azure-AD:er, Microsoft-konton som outlook.com, hotmail.com, live.com eller andra konton som gmail.com. Den här informationen gör det möjligt för **Active Directory Universal med MFA-autentisering** att identifiera rätt autentiseringsutfärdare. Det här alternativet krävs också för att stödja Microsoft-konton (MSA) till exempel outlook.com, hotmail.com, live.com eller icke-MSA-konton. 

Alla dessa användare som vill autentiseras med universell autentisering måste ange sitt Azure AD-domännamn eller klient-ID. Den här parametern representerar det aktuella Azure AD-domännamnet/klient-ID som Azure Server är länkat till. 

Om Azure Server till exempel är `contosotest.onmicrosoft.com` associerat `joe@contosodev.onmicrosoft.com` med Azure AD-domän där `contosodev.onmicrosoft.com`användaren är värd för som importerad användare från Azure AD-domänen, är `contosotest.onmicrosoft.com`domännamnet som krävs för att autentisera den här användaren . 

När användaren är en inbyggd användare av Azure AD som är länkad till Azure Server och inte är ett MSA-konto krävs inget domännamn eller klient-ID. 

Om du vill ange parametern (som börjar med SSMS version 17.2) fyller du i dialogrutan Anslut till databas i dialogrutan **Anslut till databas,** väljer **Active Directory - Universal med MFA-autentisering,** klickar på **Alternativ,** slutför rutan **Användarnamn** och klickar sedan på fliken **Anslutningsegenskaper.** 

Markera rutan **AD-domännamn eller klient-ID** och ange autentiseringsbefogningsbefogingsmyndighet, till exempel domännamnet (**contosotest.onmicrosoft.com**) eller GUID för klient-ID.  

   ![mfa-hyresgäst-ssms](./media/mfa-authentication/mfa-tenant-ssms.png)

Om du kör SSMS 18.x eller senare behövs inte längre AD-domännamnet eller klient-ID:t för gästanvändare eftersom 18.x eller senare automatiskt känner igen det.

   ![mfa-hyresgäst-ssms](./media/mfa-authentication/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Azure AD-support för företag   
Azure AD-användare som stöds för Azure AD B2B-scenarier som gästanvändare (se [Vad är Azure B2B-samarbete](../../active-directory/b2b/what-is-b2b.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) kan endast ansluta till Synapse `CREATE USER` SQL som en del av medlemmar i en grupp som skapats i aktuella Azure AD och mappas manuellt med transact-SQL-uttrycket i en viss databas. 

Om till `steve@gmail.com` exempel inbjudna `contosotest` till Azure AD `contosotest.onmicrosoft.com`(med Azure Ad-domänen) `usergroup` måste en Azure AD-grupp, till exempel skapas i Azure AD som innehåller `steve@gmail.com` medlemmen. Sedan måste den här gruppen skapas för en specifik databas (det vill säga MyDatabase) av Azure `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` AD SQL-administratör eller Azure AD DBO genom att köra ett Transact-SQL-uttryck. 

När databasanvändaren har skapats `steve@gmail.com` kan användaren `MyDatabase` logga in på `Active Directory – Universal with MFA support`alternativet SSMS-autentisering . 

Användargruppen har som standard endast anslutningsbehörigheten och all ytterligare dataåtkomst som måste beviljas på vanligt sätt. 

Observera att `steve@gmail.com` användaren som gästanvändare måste markera kryssrutan `contosotest.onmicrosoft.com` och lägga till AD-domännamnet i dialogrutan SSMS-anslutningsegenskap. **Connection Property** **AD-domännamnet eller klient-ID-alternativet** stöds endast för Universal med MFA-anslutningsalternativ, annars är det nedtonat.

## <a name="universal-authentication-limitations-for-synapse-sql"></a>Universella autentiseringsbegränsningar för Synapse SQL

- SSMS och SqlPackage.exe är de enda verktyg som för närvarande är aktiverade för MFA via Active Directory Universal Authentication.
- SSMS version 17.2 stöder samtidig åtkomst för flera användare med universell autentisering med MFA. Version 17.0 och 17.1, begränsade en inloggning för en instans av SSMS med universell autentisering till ett enda Azure Active Directory-konto. Om du vill logga in som ett annat Azure AD-konto måste du använda en annan instans av SSMS. (Den här begränsningen är begränsad till universell active directory-autentisering, du kan logga in på olika servrar med Active Directory-lösenordsautentisering, Active Directory-integrerad autentisering eller SQL Server-autentisering).
- SSMS stöder Visualisering av Active Directory Universal Authentication for Object Explorer, Query Editor och Query Store.
- SSMS version 17.2 ger DacFx Wizard stöd för export/extrahera/distribuera datadatabas. När en viss användare har autentiserats via den första autentiseringsdialogrutan med universell autentisering fungerar DacFx-guiden på samma sätt som för alla andra autentiseringsmetoder.
- SSMS-tabelldesignern stöder inte universell autentisering.
- Det finns inga ytterligare programvarukrav för Universell Active Directory-autentisering förutom att du måste använda en version av SSMS som stöds.  
- ADAL-versionen (Active Directory Authentication Library) för universell autentisering uppdaterades till den senaste versionen av ADAL.dll 3.13.9 som är tillgänglig. Se [Active Directory Authentication Library 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Nästa steg
Mer information finns i artikeln [Anslut till Synapse SQL med SQL Server Management Studio.](get-started-ssms.md) 

