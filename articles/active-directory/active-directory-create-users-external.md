---
title: "Lägga till användare från andra kataloger eller partnerföretag i Azure Active Directory | Microsoft Docs"
description: "Beskriver hur du lägger till användare eller ändrar användarinformation i Azure Active Directory, inklusive externa användare och gästanvändare."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 564a04ec-53c1-470b-9ab9-f3db57da0a89
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/22/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3f2578288bcf15c6f64bba453f82751bb2d3d772


---
# <a name="add-users-from-other-directories-or-partner-companies-in-azure-active-directory"></a>Lägga till användare från andra kataloger eller partnerföretag i Azure Active Directory
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-users-create-external-azure-portal.md)
> * [Klassisk Azure-portal](active-directory-create-users-external.md)
> 
> 

Den här artikeln förklarar hur du lägger till användare från andra kataloger i Azure Active Directory och hur du lägger till användare från partnerföretag. Information om hur du lägger till nya användare i din organisation och hur du lägger till användare som har Microsoft-konton finns i [Lägga till nya användare i Azure Active Directory](active-directory-create-users.md). Tillagda användare har inte administratörsbehörighet som standard, men du kan tilldela roller till dem när som helst.

## <a name="add-a-user"></a>Lägga till en användare
1. Logga in på [den klassiska Azure-portalen](https://manage.windowsazure.com) med ett konto som är en global administratör för katalogen.
2. Välj **Active Directory** och öppna katalogen.
3. Välj fliken **Användare** och sedan **Lägg till användare** i kommandofältet.
4. På sidan **Berätta om den här användaren** under **Typ av användare** väljer du antingen:
   
   * **Användare i en annan Azure AD-katalog** – lägger till ett användarkonto i katalogen som kommer från en annan Azure AD-katalog. Du kan bara välja en användare i en annan katalog om du är medlem i den katalogen.
   * **Användare i partnerföretag** – om du vill bjuda in och lägga till användare från ett partnerföretag i din katalog (se [Azure Active Directory B2B-samarbete](active-directory-b2b-what-is-azure-ad-b2b.md)). Du måste [ladda upp en CSV-fil med e-postadresser](active-directory-b2b-references-csv-file-format.md).
5. På användarens **profilsida** anger du ett för- och efternamn, ett användarvänligt namn och en användarroll från listan **Roller**. Mer information om användar- och administratörsroller finns i [Tilldela administratörsroller i Azure AD](active-directory-assign-admin-roles.md). Ange om du vill **aktivera Multi-Factor Authentication** för användaren.
6. På sidan **Skaffa tillfälligt lösenord** väljer du **Skapa**.

> [!IMPORTANT]
> Om din organisation använder mer än en domän bör du vara medveten om följande problem när du lägger till ett användarkonto:
> 
> * Om du vill lägga till användarkonton med samma UPN (användarens huvudnamn) i olika domäner lägger du **först** till exempelvis geoffgrisso@contoso.onmicrosoft.com, ** följt av** geoffgrisso@contoso.com.
> * **Lägg inte till** geoffgrisso@contoso.com innan du läger till geoffgrisso@contoso.onmicrosoft.com. Den här ordningen är viktig och kan vara krånglig att ångra.
> 
> 

Om du ändrar informationen för en användare vars identitet synkroniseras med din lokala Active Directory-tjänst kan du inte ändra användarinformation på den klassiska Azure-portalen. Om du vill ändra användarinformationen använder du de lokala Active Directory-hanteringsverktygen.

## <a name="add-external-users"></a>Lägga till externa användare
Du kan också lägga till användare från en annan Azure AD-katalog som du tillhör, eller från partnerföretag genom att ladda upp en CSV-fil. Om du vill lägga till en extern användare väljer du **Användare i en annan Microsoft Azure AD-katalog** eller **Användare i partnerföretag** för **Typ av användare**.

Båda typer av användare kommer från en annan katalog och läggs till som **externa användare**. Externa användare kan samarbeta med andra användare i en katalog utan att nya konton och autentiseringsuppgifter behöver läggas till. Externa användare autentiseras med deras hemkatalog när de loggar in och autentiseringen fungerar med alla andra kataloger som de har lagts till i.

## <a name="external-user-management-and-limitations"></a>Extern användarhantering och begränsningar
När du lägger till en användare från en annan katalog till din katalog är den användaren en extern användare i din katalog. Visningsnamnet och användarnamnet kopieras från hemkatalogen och används för den externa användaren i din katalog. Därefter är egenskaperna för det externa användarkontot helt oberoende. Om egenskapsändringar görs för användaren i dennes hemkatalog sprids inte dessa ändringar till det externa användarkontot i din katalog.

Den enda kopplingen mellan de två kontona är att användaren alltid autentiseras mot hans eller hennes hemkatalog eller Microsoft-konto. Det är därför du inte ser något alternativ för att återställa lösenordet eller aktivera Multi-Factor Authentication för en extern användare. För närvarande är autentiseringsprincipen för hemkatalogen eller Microsoft-kontot det enda som utvärderas när användaren loggar in.

> [!NOTE]
> Du kan dock inaktivera den externa användaren i katalogen, vilket blockerar åtkomsten till din katalog.
> 
> 

Om en användare tas bort i sin hemkatalog eller om användaren säger upp sitt Microsoft-konto finns den externa användaren fortfarande kvar i din katalog. Dock kan användare i katalogen inte komma åt resurser eftersom de inte kan autentiseras med en hemkatalog eller ett Microsoft-konto.

### <a name="services-that-currently-support-access-by-azure-ad-external-users"></a>Tjänster som för närvarande stöder åtkomst av externa Azure AD-användare
* **Den klassiska Azure-portalen**: Om en extern användare är administratör för flera kataloger kan han eller hon hantera alla dessa kataloger här.
* **SharePoint Online**: Om extern delning är aktiverat kan en extern användare komma åt tillåtna resurser i SharePoint Online.
* **Dynamics CRM**: Om användaren är licensierad via PowerShell kan en extern användare komma åt tillåtna resurser i Dynamics CRM.
* **Dynamics AX**: Om användaren är licensierad via PowerShell kan en extern användare komma åt tillåtna resurser i Dynamics CRM. Begränsningarna för [externa Azure AD-användare](#known-limitations-of-azure-ad-external-users) gäller även för externa användare i Dynamics AX.

### <a name="known-limitations-of-azure-ad-external-users"></a>Kända begränsningar för externa Azure AD-användare
* Externa användare som är administratörer kan inte lägga till användare från partnerföretag i kataloger (B2B-samarbete) utanför deras hemkatalog.
* Externa användare kan inte samtycka till program med flera klienter utanför deras hemkatalog.
* Power BI stöder inte åtkomst av externa användare för närvarande.
* Office-portalen stöder inte licensiering av externa användare.
* Med hänsyn till Azure AD PowerShell är externa användare inloggade i deras hemkatalog och kan inte hantera kataloger som de är externa användare i.

## <a name="whats-next"></a>Nästa steg
* [Lägga till nya användare i Azure Active Directory](active-directory-create-users.md)
* [Administrera Azure AD](active-directory-administer.md)
* [Hantera lösenord i Azure AD](active-directory-manage-passwords.md)
* [Hantera grupper i Azure AD](active-directory-manage-groups.md)




<!--HONumber=Nov16_HO2-->


