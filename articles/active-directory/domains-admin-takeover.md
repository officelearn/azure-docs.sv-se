---
title: Administratören övertag för en ohanterad katalog eller klient för skuggkopior i Azure Active Directory | Microsoft Docs
description: Så här tar över ett DNS-namn i en ohanterad katalog (shadow klient) i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: b9f01876-29d1-4ab8-8b74-04d43d532f4b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/06/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: cd11ea68f298395236abf83295b939462ba00964
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Ta över en ohanterad katalog som administratör i Azure Active Directory
Den här artikeln beskrivs två sätt att ta över ett DNS-namn i en ohanterad katalog i Azure Active Directory (AD Azure). När en Självbetjäningsanvändare som registrerar sig för en molnbaserad tjänst som använder Azure AD, läggs de till en ohanterad Azure AD-katalog baserat på deras e-postdomän. Mer information om självbetjäning eller ”viral” registreringen för en tjänst finns [vad är självbetjäningsregistrering för Azure Active Directory?]()

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Bestäm hur du vill ta över en ohanterad katalog
Under processen med admin övertag, du kan bevisa att du äger enligt beskrivningen i [lägga till ett anpassat domännamn i Azure AD](add-custom-domain.md). I nästa avsnitt beskrivs administratörsupplevelse i detalj, men här är en sammanfattning:

* När du utför en [”interna” admin övertag](#internal-admin-takeover) av en ohanterad Azure-katalogen du läggs till som global administratör för katalogen ohanterad. Inga användare, domäner eller serviceplaner migreras till valfri katalog som du administrerar.

* När du utför en [”externa” admin övertag](#external-admin-takeover) en ohanterad Azure-katalogen du lägga till DNS-domännamnet för den ohanterade katalogen hanterad Azure-katalogen. När du lägger till namnet på en domän, skapas en koppling av användare till resurser i hanterad Azure-katalogen så att användarna kan fortsätta att komma åt tjänster utan avbrott. 

## <a name="internal-admin-takeover"></a>Internt admin övertag

Vissa produkter som innehåller SharePoint och OneDrive, till exempel Office 365, stöder inte externa övertag. Om det är ditt scenario, eller om du är administratör och vill ta över en ohanterad eller ”shadow” klient skapas av användare som används för självbetjäning registrering, kan du göra detta med ett internt admin övertag.

1. Skapa en användarkontext i ohanterad klient genom att logga med, till exempel Power BI. Dessa instruktioner förutsätter att sökvägen för underlätta för exempel.

2. Öppna den [Power BI-platsen](https://powerbi.com) och välj **starta ledigt**. Ange ett användarkonto som använder domännamnet för organisationen; till exempel `admin@fourthcoffee.xyz`. När du anger i verifieringskoden, kontrollera din e-post för bekräftelsekoden.

3. Välj i postbekräftelsen från Power BI **Ja, det är jag**.

4. Logga in på den [Office 365 Admin center](https://portal.office.com/adminportal/Home) med Power BI-användarkonto. Ett meddelande om att du uppmanas att **blir administratören** domännamnet har redan verifierats i ohanterad-klient. Välj **Ja, jag vill att administratören**.
  
  ![första skärmbild för bli administratören](./media/domains-admin-takeover/become-admin-first.png)
  
5. Lägg till TXT-posten för att bevisa att du äger domännamnet **fourthcoffee.xyz** hos din domänregistrator namn. I det här exemplet är det GoDaddy.com.
  
  ![Lägg till en txt-posten för domännamnet](./media/domains-admin-takeover/become-admin-txt-record.png)

När DNS TXT-poster har verifierats hos din domänregistrator namn, kan du hantera Azure AD-klient.

När du har slutfört föregående steg, men du är nu global administratör för fjärde kaffe klient i Office 365. Om du vill integrera domännamnet med andra Azure-tjänster kan du ta bort den från Office 365 och lägga till den i en annan hanterad klient i Azure.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>Lägga till domännamnet i en hanterad klient i Azure AD 

1. Öppna den [Office 365 Admin center](https://portal.office.com/adminportal/Home).
2. Välj **användare** fliken och skapa ett nytt konto med namnet *user@fourthcoffeexyz.onmicrosoft.com* som inte använder det anpassade domännamnet. 
3. Kontrollera att det nya användarkontot har globala administratörsrättigheter för Azure AD-klient.
4. Öppna **domäner** i administrationscentret för Office 365, väljer du domännamnet på och välj **ta bort**. 
  
  ![ta bort domännamnet från Office 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Om du har några användare eller grupper i Office 365 som refererar till det borttagna domännamnet kan de ändras till den. onmicrosoft.com-domän. Om du tvingar ta bort domännamnet, alla användare automatiskt ändras, i det här exemplet att *user@fourthcoffeexyz.onmicrosoft.com*.
  
6. Logga in på den [administrationscentret för Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) med ett konto som är global administratör för Azure AD-klient.
  
7. Välj **anpassade domännamn**, lägga till domännamnet. Du måste ange DNS TXT-poster för att verifiera ditt ägarskap till domännamnet. 
  
  ![domän som läggs till Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Alla användare av Power BI eller Azure Rights Management-tjänsten som har licenser som tilldelats i Office 365-klienten måste spara sina instrumentpaneler om domännamnet har tagits bort. De måste logga in med ett användarnamn som *user@fourthcoffeexyz.onmicrosoft.com* snarare än *user@fourthcoffee.xyz*.

## <a name="external-admin-takeover"></a>Externa administratören övertag

Om du redan hanterar en klient med Azure-tjänster eller Office 365 kan du lägga till ett anpassat domännamn om det redan har verifierats i en annan Azure AD-klient. Dock från en hanterad klient i Azure AD kan du ta över en ohanterad-klient som en extern administratör övertag. Det allmänna förfarandet följer artikeln [lägga till en anpassad domän till Azure AD](add-custom-domain.md).

När du verifiera ditt ägarskap till domännamnet Azure AD tar bort domännamnet från ohanterad-klient och flyttar det till din befintliga klientorganisation. Externa administratören övertag till en ohanterad katalog kräver samma DNS TXT-valideringen som internt admin övertag. Skillnaden är att följande också flyttas med domännamn:

- Användare
- Prenumerationer
- Licenstilldelning

### <a name="support-for-external-admin-takeover"></a>Stöd för externa administratören övertag
Externa administratören övertag stöds av följande onlinetjänster:

- Power BI
- Azure Rights Management
- exchange online

Stöds serviceplaner inkluderar:

- Powerbi ledigt
- Power BI Pro
- PowerApps ledigt
- PowerFlow ledigt
- RMS för enskilda användare
- Microsoft Stream
- Dynamics 365 kostnadsfri utvärderingsversion

Externa administratören övertag stöds inte för alla tjänster som har serviceplaner som innehåller SharePoint, OneDrive eller Skype för företag; till exempel genom en kostnadsfri prenumeration Office eller Office grundläggande SKU: N. Du kan eventuellt använda den [ **ForceTakeover** alternativet](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) för att ta bort domännamnet från ohanterad-klient och verifiera den önskade innehavaren. Det här alternativet om ForceTakeover kommer inte flytta över användare eller få åtkomst till prenumerationen. I stället flyttas det här alternativet endast domännamnet. 

#### <a name="more-information-about-rms-for-individuals"></a>Mer information om RMS för enskilda användare

För [RMS för enskilda användare](/information-protection/understand-explore/rms-for-individuals)när ohanterad innehavaren är i samma region som innehavare för att du äger, den automatiskt skapade [klientnyckel för Azure Information Protection](/information-protection/plan-design/plan-implement-tenant-key) och [standard skydd mallar](/information-protection/deploy-use/configure-usage-rights#rights-included-in-the-default-templates) dessutom flyttas över domänens namn. 

Nyckeln och mallar flyttas inte när ohanterad klient finns i en annan region. Ohanterad-klient är till exempel i Europa och den klient som du äger är i Nordamerika. 

Även om RMS för enskilda användare är utformat för att stödja Azure AD-autentisering för att öppna skyddat innehåll, det inte förhindra att användare också skydda innehåll. Om användarna skydda innehåll med den prenumeration på RMS för enskilda användare och nyckeln och mallar inte har flyttats, kommer innehållet inte att komma åt efter domän övertag.    

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Azure AD PowerShell-cmdlets för alternativet ForceTakeover
Du kan se dessa cmdlets som används i [exempel PowerShell](#powershell-example).


Cmdlet: | Användning 
------- | -------
`connect-msolservice` | När du uppmanas logga in på din hanterad klient.
`get-msoldomain` | Visar dina domännamn som är associerade med den aktuella innehavaren.
`new-msoldomain –name <domainname>` | Lägger till domännamnet i klient som ej verifierat (ingen DNS-verifiering har utförts ännu).
`get-msoldomain` | Domännamnet ingår nu i listan över domännamn som är associerade med din hanterade klient, men visas som **Ej verifierat**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Innehåller information för att lägga till nya DNS TXT-post för domänen (MS = xxxxx). Kontrollen kan inte hända omedelbart eftersom tar det lite tid för TXT-posten ska spridas, så Vänta några minuter innan den **- ForceTakeover** alternativet. 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Om domännamnet inte är fortfarande kontrolleras, du kan fortsätta med den **- ForceTakeover** alternativet. Verifierar att TXT-posten har skapats och systemtillstånd aktiveras gäller processen.<li>Den **- ForceTakeover** alternativet ska läggas till cmdleten endast när tvinga en extern administratör övertag, till exempel när ohanterad-klient har Office 365-tjänster som blockerar övertag.
`get-msoldomain` | Listan innehåller nu domännamnet som **verifierad**.

### <a name="powershell-example"></a>PowerShell-exempel

1. Anslut till Azure AD med hjälp av autentiseringsuppgifterna som användes för att svara på självbetjäning-erbjudande:
  ````
    import-module MSOnline
    $msolcred = get-credential
    
    connect-msolservice -credential $msolcred
  ````
2. Hämta en lista över domäner:
  
  ````
    Get-MsolDomain
  ````
3. Kör cmdleten Get-MsolDomainVerificationDns om du vill skapa en förfrågan:
  ````
    Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
  
    For example:
  
    Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
  ````

4. Kopiera värdet (challenge) som returneras från det här kommandot. Exempel:
  ````
    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
  ````
5. Skapa en DNS txt-post som innehåller värdet som du kopierade i föregående steg i det offentliga DNS-namnområdet. Namnet för den här posten är namnet på den överordnade domänen, om du skapar denna resurspost med hjälp av DNS-serverrollen från Windows Server, se till att posten namn är tomt och bara klistra in värdet i textrutan.
6. Kör cmdleten bekräfta MsolDomain för att verifiera utmaningen:
  
  ````
    Confirm-MsolEmailVerifiedDomain -DomainName *your_domain_name*
  ````
  
  Exempel:
  
  ````
    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com
  ````

En lyckad utmaning returnerar du uppmaningen utan fel.

## <a name="next-steps"></a>Nästa steg
* [Lägga till ett anpassat domännamn i Azure AD](add-custom-domain.md)
* [Installera och konfigurera Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Cmdlet-referens](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
