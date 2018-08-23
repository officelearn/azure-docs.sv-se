---
title: Administratören övertagande av en ohanterad katalog eller shadow-klient i Azure Active Directory | Microsoft Docs
description: Så här att ta över en DNS-domännamnet i en ohanterad katalog (shadow klient) i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 04/06/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 210526e105793820a2e8a80a11b356b1d7d764da
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42057368"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Ta över en ohanterad katalog som administratör i Azure Active Directory
Den här artikeln beskrivs två sätt att ta över en DNS-domännamnet i en ohanterad katalog i Azure Active Directory (AD Azure). När en självbetjäningsanvändare registrerar sig för en molntjänst som använder Azure AD läggs de till i en ohanterad Azure AD-katalog baserat på e-postdomän. Mer information om självbetjäning eller ”viral” registrering för en tjänst finns i [vad är självbetjäningsregistrering för Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-signup)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Bestäm hur du vill ta över en ohanterad katalog
Under processen för adminövertagande kan du bevisa ägarskapet enligt instruktionerna i [Add a custom domain name to Azure AD](../fundamentals/add-custom-domain.md) (Lägga till ett anpassat domännamn i Azure AD). I nästa avsnitt beskrivs administratörsupplevelsen mer detaljerat, men här följer en sammanfattning:

* När du utför ett [”internt” adminövertagande](#internal-admin-takeover) av en ohanterad Azure-katalog läggs du till som global administratör för den ohanterade katalogen. Inga användare, domäner eller tjänstplaner migreras till en annan katalog som du administrerar.

* När du utför ett [”externt” adminövertagande](#external-admin-takeover) av en ohanterad Azure-katalog lägger du till DNS-domännamnet på den ohanterade katalogen till din hanterade Azure-katalog. När du lägger till domännamnet skapas en mappning av användare till resurser i din hanterade Azure-katalog så att användare kan fortsätta att använda tjänsterna utan avbrott. 

## <a name="internal-admin-takeover"></a>Intern adminövertagande

Vissa produkter som innehåller SharePoint och OneDrive, till exempel Office 365 har inte stöd för extern övertagning. Om det är ditt scenario, eller om du är administratör och vill ta över en ohanterad eller ”shadow” klient skapa av användare som har använt självbetjäningsregistrering, kan du göra detta med en intern adminövertagande.

1. Skapa en användarkontext i ohanterad klient genom registreringen med till exempel Power BI. Dessa instruktioner förutsätter att sökvägen för att underlätta för till exempel.

2. Öppna den [Power BI-webbplatsen](https://powerbi.com) och välj **starta kostnadsfri**. Ange ett användarkonto som använder domännamnet för organisationen, till exempel `admin@fourthcoffee.xyz`. Kontrollera din e-post för bekräftelsekoden när du har angett i verifieringskoden.

3. I e-postbekräftelsen från Power BI, väljer **Ja, det är jag**.

4. Logga in på den [Office 365 Administrationscenter](https://portal.office.com/adminportal/Home) med Power BI-konto. Du får ett meddelande där du uppmanas att **bli administratör** för det domännamn som har redan verifierats i en ohanterad klient. Välj **Ja, jag vill att administratören**.
  
  ![första skärmbild för bli administratör](./media/domains-admin-takeover/become-admin-first.png)
  
5. Lägg till TXT-posten för att bevisa att du äger domännamnet **fourthcoffee.xyz** på din domänregistrator. I det här exemplet är det GoDaddy.com.
  
  ![Lägg till en txt-post för domännamnet](./media/domains-admin-takeover/become-admin-txt-record.png)

Du kan hantera Azure AD-klienten när DNS TXT-poster har verifierats i din domännamnsregistrator.

När du har slutfört föregående steg, men du är nu global administratör för den fjärde kaffe klienten i Office 365. Om du vill integrera domännamnet med dina andra Azure-tjänster, kan du ta bort den från Office 365 och lägga till den i en annan hanterad klient i Azure.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>Att lägga till domännamnet till en hanterad klient i Azure AD 

1. Öppna den [Office 365 Administrationscenter](https://portal.office.com/adminportal/Home).
2. Välj **användare** fliken och skapa ett nytt användarkonto med ett namn som liknar *user@fourthcoffeexyz.onmicrosoft.com* som inte använder det anpassade domännamnet. 
3. Kontrollera att det nya användarkontot har globala administratörsrättigheter för Azure AD-klient.
4. Öppna **domäner** fliken i Office 365 Administrationscenter, Välj domännamnet och välj **ta bort**. 
  
  ![ta bort domännamnet från Office 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Om du har några användare eller grupper i Office 365 som refererar till borttagna domännamnet, måste de ändras till den. onmicrosoft.com-domän. Om du tvingar ta bort domännamnet, alla användare får automatiskt ett nytt namn, i det här exemplet till *user@fourthcoffeexyz.onmicrosoft.com*.
  
6. Logga in på den [Azure AD administratörscenter](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) med ett konto som är global administratör för Azure AD-klient.
  
7. Välj **anpassade domännamn**, lägga till domännamnet. Du måste ange DNS TXT-poster för att verifiera ditt ägarskap till domännamnet. 
  
  ![domän som har lagts till i Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Alla användare av Power BI eller Azure Rights Management-tjänsten som har licenser som tilldelats i Office 365-klient måste spara sina instrumentpaneler om domännamnet har tagits bort. De måste logga in med ett användarnamn som *user@fourthcoffeexyz.onmicrosoft.com* snarare än *user@fourthcoffee.xyz*.

## <a name="external-admin-takeover"></a>Externa adminövertagande

Om du redan hanterar en klient med Azure-tjänster eller Office 365 kan du inte lägga till ett anpassat domännamn om det redan är verifierat i en annan Azure AD-klient. Från din hanterade klient i Azure AD ta du dock över en ohanterad klient som en extern adminövertagande. Allmänna förfarandet följer artikeln [lägga till en anpassad domän till Azure AD](../fundamentals/add-custom-domain.md).

När du verifiera ditt ägarskap till domännamnet Azure AD tar bort domännamnet från ohanterad klient och flyttar det till din befintliga klient. Externa adminövertagande av en ohanterad katalog kräver samma DNS TXT-verifieringsprocessen som interna adminövertagande. Skillnaden är att följande också flyttas med domännamn:

- Användare
- Prenumerationer
- Licenstilldelningar

### <a name="support-for-external-admin-takeover"></a>Stöd för externa adminövertagande
Externa adminövertagande stöds av följande onlinetjänster:

- Power BI
- Azure Rights Management
- exchange online

Stöds service-planerna inkluderar:

- Powerbi kostnadsfri
- Power BI Pro
- Kostnadsfria PowerApps
- PowerFlow kostnadsfritt
- RMS för enskilda användare
- Microsoft Stream
- Kostnadsfri utvärderingsversion av Dynamics 365

Externa adminövertagande stöds inte för alla tjänster som har service-planer med SharePoint, OneDrive eller Skype för företag; till exempel via en kostnadsfri Office-prenumeration eller Office grundläggande SKU. Alternativt kan du använda den [ **ForceTakeover** alternativet](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) för att ta bort domännamnet från ohanterad klient och verifiera den på den önskade klienten. Det här alternativet om ForceTakeover ska inte flytta över användare eller behålla åtkomst till prenumerationen. I stället flyttas det här alternativet bara domännamnet. 

#### <a name="more-information-about-rms-for-individuals"></a>Mer information om RMS för enskilda användare

För [RMS för enskilda användare](/azure/information-protection/rms-for-individuals), när ohanterad klient är i samma region som klientorganisationen att du äger, den automatiskt skapade [Azure Information Protection-klientnyckel](/azure/information-protection/plan-implement-tenant-key) och [standard skyddsmallar](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) dessutom flyttas över domänens namn. 

Nyckel och mallar flyttas inte när ohanterad klient är i en annan region. Ohanterad klient är till exempel i Europa och den klient som du äger är i nordamerikanska. 

Även om RMS för enskilda användare är utformat för att stödja Azure AD-autentisering för att öppna skyddat innehåll, det inte förhindra att användarna även skyddar innehåll. Om användare skydda innehåll med RMS för enskild prenumeration och nyckel och mallar har inte flyttats, kommer innehållet inte att komma åt efter domän övertagande.    

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Azure AD PowerShell-cmdlets för alternativet ForceTakeover
Du kan se dessa cmdletar som används i [PowerShell-exempel](#powershell-example).


Cmdlet: | Användning 
------- | -------
`connect-msolservice` | När du uppmanas logga in på din hanterad klient.
`get-msoldomain` | Visar dina domännamn som är associerade med den aktuella klienten.
`new-msoldomain –name <domainname>` | Lägger till domännamnet till klient som inte verifierad (ingen DNS-verifiering har utförts ännu).
`get-msoldomain` | Domännamnet ingår nu i listan över domännamn som är associerade med din hanterad klient, men visas som **inte verifierad**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Innehåller informationen som att lägga till nya DNS TXT-post för domänen (MS = xxxxx). Verifiering kan inte utföras direkt eftersom det tar lite tid för TXT-posten att spridas, så Vänta några minuter innan den **- ForceTakeover** alternativet. 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Om ditt domännamn är fortfarande inte verifieras, du kan fortsätta med den **- ForceTakeover** alternativet. Verifierar att TXT-posten har skapats och inleder gäller processen.<li>Den **- ForceTakeover** alternativet ska läggas till cmdlet: en endast när tvinga en extern adminövertagande, till exempel när ohanterad klient har Office 365-tjänster som blockerar övertagande.
`get-msoldomain` | Listan visar nu domännamnet som **verifierad**.

### <a name="powershell-example"></a>PowerShell-exempel

1. Anslut till Azure AD med de autentiseringsuppgifter som användes för att svara på självbetjäning erbjudandet:
  ````
    import-module MSOnline
    $msolcred = get-credential
    
    connect-msolservice -credential $msolcred
  ````
2. Hämta en lista över domäner:
  
  ````
    Get-MsolDomain
  ````
3. Kör cmdleten Get-MsolDomainVerificationDns för att skapa en utmaning:
  ````
    Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
  
    For example:
  
    Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
  ````

4. Kopiera värdet (utmaningen) som returneras från det här kommandot. Exempel:
  ````
    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
  ````
5. Skapa en DNS txt-post som innehåller värdet som du kopierade i föregående steg i ditt offentliga DNS-namnområde. Namn för den här posten är namnet på den överordnade domänen, så om du skapar denna resurspost med hjälp av DNS-roll från Windows Server, post namn tomt och bara klistra in värdet i textrutan.
6. Kör cmdleten Confirm-MsolDomain för att verifiera utmaningen:
  
  ````
    Confirm-MsolEmailVerifiedDomain -DomainName *your_domain_name*
  ````
  
  Exempel:
  
  ````
    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com
  ````

En lyckad utmaning återgår till Kommandotolken utan fel.

## <a name="next-steps"></a>Nästa steg
* [Lägga till ett anpassat domännamn i Azure AD](../fundamentals/add-custom-domain.md)
* [Installera och konfigurera Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Cmdlet-referens](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
