---
title: Admin övertagande av en ohanterat katalog - Azure AD | Microsoft-dokument
description: Så här tar du över ett DNS-domännamn i en ohanterad Azure AD-organisation (skuggklient).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09012d93a1f9fd24427cb8b3937b3a36cf75d9e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834173"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Ta över en ohanterat katalog som administratör i Azure Active Directory

I den här artikeln beskrivs två sätt att ta över ett DNS-domännamn i en ohanterat katalog i Azure Active Directory (Azure AD). När en självbetjäningsanvändare registrerar sig för en molntjänst som använder Azure AD läggs de till i en ohanterad Azure AD-katalog baserat på e-postdomän. Mer information om självbetjäning eller "viral" registrering för en tjänst finns i [Vad är självbetjäningsanmäla för Azure Active Directory?](directory-self-service-signup.md)

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Bestäm hur du vill ta över en ohanterlig katalog
Under processen för adminövertagande kan du bevisa ägarskapet enligt instruktionerna i [Add a custom domain name to Azure AD](../fundamentals/add-custom-domain.md) (Lägga till ett anpassat domännamn i Azure AD). I nästa avsnitt beskrivs administratörsupplevelsen mer detaljerat, men här följer en sammanfattning:

* När du utför ett [”internt” administratörsövertagande](#internal-admin-takeover) av en ohanterad Azure-katalog läggs du till som global administratör för den ohanterade katalogen. Inga användare, domäner eller tjänstplaner migreras till en annan katalog som du administrerar.

* När du utför ett [”externt” administratörsövertagande](#external-admin-takeover) av en ohanterad Azure-katalog lägger du till DNS-domännamnet på den ohanterade katalogen till din hanterade Azure-katalog. När du lägger till domännamnet skapas en mappning av användare till resurser i din hanterade Azure-katalog så att användare kan fortsätta att använda tjänsterna utan avbrott. 

## <a name="internal-admin-takeover"></a>Internt administrationsövertagande

Vissa produkter som innehåller SharePoint och OneDrive, till exempel Office 365, stöder inte externt uppköp. Om det är ditt scenario, eller om du är administratör och vill ta över en ohanterad eller "skugga" klient skapa av användare som använde självbetjäning registrering, kan du göra detta med en intern admin övertagande.

1. Skapa en användarkontext i den ohanterade klienten genom att registrera dig för Power BI. För att underlätta för exempel, dessa steg förutsätter att vägen.

2. Öppna [Power BI-webbplatsen](https://powerbi.com) och välj **Startfri**. Ange ett användarkonto som använder organisationens domännamn. till exempel `admin@fourthcoffee.xyz`. När du har angett i verifieringskoden kontrollerar du bekräftelsekoden i din e-postadress.

3. I bekräftelsemeddelandet från Power BI väljer du **Ja, det är jag.**

4. Logga in på [Microsoft 365-administrationscentret](https://portal.office.com/admintakeover) med Power BI-användarkontot. Du får ett meddelande som instruerar dig att **bli administratör** för domännamnet som redan har verifierats i den ohanterat klienten. välj **Ja, jag vill vara admin**.
  
   ![första skärmdump för Bli Admin](./media/domains-admin-takeover/become-admin-first.png)
  
5. Lägg till TXT-posten för att bevisa att du äger domännamnet **fourthcoffee.xyz** på din domännamnsregistratorer. I det här exemplet är det GoDaddy.com.
  
   ![Lägga till en txt-post för domännamnet](./media/domains-admin-takeover/become-admin-txt-record.png)

När DNS TXT-posterna verifieras hos din domännamnsregistrator kan du hantera Azure AD-klienten.

När du har slutfört föregående steg är du nu global administratör för den fjärde kaffeklienten i Office 365. Om du vill integrera domännamnet med dina andra Azure-tjänster kan du ta bort det från Office 365 och lägga till det i en annan hanterad klientorganisation i Azure.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>Lägga till domännamnet till en hanterad klientorganisation i Azure AD

1. Öppna [administrationscentret för Microsoft 365](https://admin.microsoft.com).
2. Välj fliken **Användare** och skapa ett nytt användarkonto med ett namn som *fourthcoffeexyz.onmicrosoft.com\@* som inte använder det anpassade domännamnet. 
3. Kontrollera att det nya användarkontot har globala administratörsbehörighet för Azure AD-klienten.
4. Öppna fliken Domäner i **administrationscentret** för Microsoft 365, välj domännamnet och välj **Ta bort**. 
  
   ![ta bort domännamnet från Office 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Om du har några användare eller grupper i Office 365 som refererar till det borttagna domännamnet måste de byta namn till domänen .onmicrosoft.com. Om du tvingar bort domännamnet byter alla användare automatiskt namn till *användar\@fourthcoffeexyz.onmicrosoft.com*.
  
6. Logga in på [Azure AD-administrationscentret](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) med ett konto som är den globala administratören för Azure AD-klienten.
  
7. Välj **Anpassade domännamn**och lägg sedan till domännamnet. Du måste ange DNS TXT-posterna för att verifiera ägarskapet av domännamnet. 
  
   ![domän som verifierats som tillagd i Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Alla användare av Power BI- eller Azure Rights Management-tjänst som har licenser tilldelade i Office 365-klienten måste spara sina instrumentpaneler om domännamnet tas bort. De måste logga in med ett användarnamn som *fourthcoffeexyz.onmicrosoft.com\@* i stället *för användaren\@fourthcoffee.xyz*.

## <a name="external-admin-takeover"></a>Externt administrationsövertagande

Om du redan hanterar en klient med Azure-tjänster eller Office 365 kan du inte lägga till ett anpassat domännamn om det redan har verifierats i en annan Azure AD-klientorganisation. Från din hanterade klientorganisation i Azure AD kan du dock ta över en ohanterad klientorganisation som ett externt administrationsövertagande. Den allmänna proceduren följer artikeln [Lägg till en anpassad domän i Azure AD](../fundamentals/add-custom-domain.md).

När du verifierar ägarskapet av domännamnet tar Azure AD bort domännamnet från den ohanterat klienten och flyttar det till din befintliga klientorganisation. Extern admin övertagande av en ohanterlig katalog kräver samma DNS TXT valideringsprocessen som interna admin övertagande. Skillnaden är att följande också flyttas över med domännamnet:

- Användare
- Prenumerationer
- Licenstilldelningar

### <a name="support-for-external-admin-takeover"></a>Stöd för externt administrationsövertagande
Externt administrationsövertagande stöds av följande onlinetjänster:

- Azure Rights Management
- exchange online

Serviceplanerna som stöds omfattar:

- PowerApps gratis
- PowerFlow gratis
- RMS för personer
- Microsoft Stream
- Dynamics 365 gratis provperiod

Externt administrationsövertagande stöds inte för någon tjänst som har tjänstplaner som inkluderar SharePoint, OneDrive eller Skype för företag. till exempel genom en Office-kostnadsfri prenumeration. 

Du kan också använda alternativet [ **ForceTakeover** ](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) för att ta bort domännamnet från den ohanterat klienten och verifiera det på önskad klient. 

#### <a name="more-information-about-rms-for-individuals"></a>Mer information om RMS för enskilda personer

För [RMS för enskilda personer](/azure/information-protection/rms-for-individuals), när den ohanterade klienten finns i samma region som klienten som du äger, flyttas automatiskt skapade Azure Information [Protection-klientnyckeln](/azure/information-protection/plan-implement-tenant-key) och [standardskyddsmallarna](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) dessutom över med domännamnet.

Nyckeln och mallarna flyttas inte över när den ohanterat klienten finns i en annan region. Till exempel om den ohanterat hyresgästen finns i Europa och den organisation som du äger är i Nordamerika.

Även om RMS för enskilda personer är utformat för att stödja Azure AD-autentisering för att öppna skyddat innehåll, hindrar det inte användare från att också skydda innehåll. Om användarna skyddade innehåll med RMS för enskilda personer-prenumerationen och nyckeln och mallarna inte flyttades över, är innehållet inte tillgängligt efter domänövertagandet.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Azure AD PowerShell-cmdletar för alternativet ForceTakeover
Du kan se dessa cmdlets som används i [PowerShell-exempel](#powershell-example).

cmdlet | Användning
------- | -------
`connect-msolservice` | Logga in på din hanterade klient.
`get-msoldomain` | Visar dina domännamn som är associerade med den aktuella klienten.
`new-msoldomain –name <domainname>` | Lägger till domännamnet i klienten som Overifierad (ingen DNS-verifiering har utförts ännu).
`get-msoldomain` | Domännamnet ingår nu i listan över domännamn som är associerade med din hanterade klient, men visas som **Overifierad**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Innehåller den information som ska läggas in i den nya DNS TXT-posten för domänen (MS=xxxxx). Verifiering kanske inte sker omedelbart eftersom det tar lite tid för TXT-posten att spridas, så vänta några minuter innan du överväger alternativet **-ForceTakeover.** 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Om ditt domännamn fortfarande inte är verifierat kan du fortsätta med alternativet **-ForceTakeover.** Den verifierar att TXT-posten skapades och startar uppköpsprocessen.<li>**Alternativet -ForceTakeover** bör endast läggas till i cmdleten när du tvingar fram ett externt administrationsövertagande, till exempel när den ohanterade klienten har Office 365-tjänster som blockerar övertagandet.
`get-msoldomain` | Domänlistan visar nu domännamnet som **Verifierat**.

> [!NOTE]
> Den ohanterade Azure AD-organisationen tas bort 10 dagar efter att du har utnyttjat alternativet extern uppköpskraft.

### <a name="powershell-example"></a>PowerShell-exempel

1. Anslut till Azure AD med de autentiseringsuppgifter som användes för att svara på självbetjäningserbjudandet:
   ```powershell
   Install-Module -Name MSOnline
   $msolcred = get-credential
    
   connect-msolservice -credential $msolcred
   ```
2. Få en lista över domäner:
  
   ```powershell
   Get-MsolDomain
   ```
3. Kör Cmdlet Get-MsolDomainVerificationDns för att skapa en utmaning:
   ```powershell
   Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
   ```
    Ett exempel:
   ```
   Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Kopiera värdet (utmaningen) som returneras från det här kommandot. Ett exempel:
   ```powershell
   MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. Skapa en DNS-txt-post som innehåller värdet som du kopierade i föregående steg i det offentliga DNS-namnområdet. Namnet på den här posten är namnet på den överordnade domänen, så om du skapar den här resursposten med hjälp av DNS-rollen från Windows Server lämnar du postnamnet tomt och klistrar bara in värdet i rutan Text.
6. Kör cmdleten Confirm-MsolDomain för att verifiera utmaningen:
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   Ett exempel:
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

En lyckad utmaning returnerar dig till prompten utan fel.

## <a name="next-steps"></a>Nästa steg

* [Lägga till ett anpassat domännamn i Azure AD](../fundamentals/add-custom-domain.md)
* [Hur du installerar och konfigurerar Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Cmdlet-referens](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
