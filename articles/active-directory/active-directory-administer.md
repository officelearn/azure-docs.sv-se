---
title: Administrera Azure AD-katalogen | Microsoft Docs
description: "I den här artikeln lär du dig vad en Azure AD-klient är och hur du hanterar Azure via Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
writer: markvi
manager: femila
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/06/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 001ffc0f9c7465552392a9848ef1487a4c0eafce
ms.lasthandoff: 12/07/2016


---
# <a name="administer-your-azure-ad-directory"></a>Administrera Azure AD-katalogen
## <a name="what-is-an-azure-ad-tenant"></a>Vad är en Azure AD-klientorganisation eller Azure AD-klient?
På en fysisk arbetsplats kan en organisation vara en grupp eller ett företag som är inrymd i en byggnad. Till exempel kanske ditt företag äger kontorsutrymme i en byggnad. Den här byggnaden kan ligga på en gata med många andra företag. I vår kontext är ditt företag en klientorganisation, eller klient, i byggnaden. Den här byggnaden är en tillgång för din organisation och ger säkerhet och ser till att ni kan bedriva er verksamhet på ett säkert sätt. Den är också åtskild från andra företag på er gata. Detta säkerställer att din organisation och resurserna där isoleras från andra företag.

På en arbetsplats i molnet kan en klientorganisation definieras som en klient eller organisation som äger och hanterar en specifik instans av molntjänsten. Med identitetsplattformen som tillhandahålls av Microsoft Azure är en klient helt enkelt en dedikerad instans av Azure AD (Active Directory Azure) som din organisation tilldelas och äger när den registrerar sig för en Microsoft-molntjänst som Azure eller Office 365.

Varje Azure AD-katalog är separat och åtskild från andra Azure AD-kataloger. Precis som ett företags kontorsbyggnad är en säker resurs som är specifik för din organisation har även Azure AD-katalogen utformats för att vara en säker tillgång för exklusiv användning av din organisation. Azure AD-arkitekturen håller isär kunddata och identitetsinformation. Det innebär att användare och administratörer av en Azure AD-katalog inte oavsiktligt eller illvilligt kan komma åt data i en annan katalog.

![Hantera Azure Active Directory][1]

## <a name="how-can-i-get-an-azure-ad-directory"></a>Hur kan jag skaffa en Azure AD-katalog?
Azure AD innehåller de grundläggande katalog- och identitetshanteringsfunktionerna bakom de flesta av Microsofts molntjänster, inklusive:

* Azure
* Microsoft Office 365
* Microsoft Dynamics CRM Online
* Microsoft Intune

Du får en Azure AD-katalog när du registrerar dig för någon av dessa Microsoft-molntjänster. Du kan skapa ytterligare kataloger efter behov. Du kan till exempel skapa din första katalog som en produktionskatalog och sedan skapa en annan katalog för testning eller mellanlagring.

> [!NOTE]
> När du har registrerat dig för din första tjänst rekommenderar vi att du använder samma administratörskonto som associerats med din organisation när du registrerar dig för andra Microsoft-molntjänster.
> 
> 

Första gången du registrerar dig för en Microsoft-molntjänst uppmanas du att ange information om din organisation och din organisations domännamnsregistrering på Internet. Den här informationen används sedan för att skapa en ny Azure AD-kataloginstans för din organisation. Samma katalog används för att autentisera inloggningsförsök när du prenumererar på flera Microsoft-molntjänster.

De ytterligare tjänsterna utnyttjar fullt ut eventuella befintliga användarkonton, principer, inställningar eller lokal katalogintegrering som du konfigurerar för att förbättra effektiviteten mellan din organisations identitetsinfrastruktur lokalt och Azure AD.

Om du ursprungligen registrerade dig för en Microsoft Intune-prenumeration och slutförde de nödvändiga stegen för att ytterligare integrera din lokala Active Directory med Azure AD-katalogen genom att distribuera katalogsynkronisering och/eller servrar med enkel inloggning, kan du registrera dig för en annan Microsoft-molntjänst som Office 365 som också kan dra nytta av samma fördelar med katalogintegreringen som du nu använder med Microsoft Intune.

Mer information om hur du integrerar din lokala katalog med Azure AD finns i [Katalogintegrering](active-directory-aadconnect.md).

### <a name="associate-an-azure-ad-directory-with-a-new-azure-subscription"></a>Associera en Azure AD-katalog med en ny Azure-prenumeration
Du kan associera en ny Azure-prenumeration med samma katalog som autentiserar inloggningen för en befintlig Office 365- eller Microsoft Intune-prenumeration. Logga in på Azure-hanteringsportalen med ditt arbets- eller skolkonto. Hanteringsportalen returnerar ett meddelande om att det inte gick att hitta några prenumerationer för kontot. Välj **Registrera dig för Azure** så är din katalog tillgänglig för administration på portalen. Mer information finns i [Hantera katalogen för din Office 365-prenumeration i Azure](active-directory-how-subscriptions-associated-directory.md#manage-the-directory-for-your-office-365-subscription-in-azure).

En video om vanliga användningsfrågor för Azure AD finns i [Azure Active Directory – Vanliga frågor om registrering, inloggning och användning](http://channel9.msdn.com/Series/Windows-Azure-Active-Directory/WAADCommonSignupsigninquestions).

### <a name="create-an-azure-ad-directory-by-signing-up-for-a-microsoft-cloud-service-as-an-organization"></a>Skapa en Azure AD-katalog genom att registrera dig för en Microsoft-molntjänst som en organisation
Om du inte har någon prenumeration på en Microsoft-molntjänst än kan du använda en av länkarna nedan för att registrera dig. När du registrerar dig för din första tjänst skapas en Azure AD-katalog automatiskt.

* [Microsoft Azure](https://account.windowsazure.com/organization)
* [Office 365](http://products.office.com/business/compare-office-365-for-business-plans/)
* [Microsoft Intune](https://account.manage.microsoft.com/Signup/MainSignUp.aspx?OfferId=40BE278A-DFD1-470a-9EF7-9F2596EA7FF9&ali=1)

### <a name="manage-an-azure-provisioned-default-directory"></a>Hantera en Azure-etablerad standardkatalog
I dag skapas en katalog automatiskt när du registrerar dig för Azure och din prenumeration associeras med den katalogen. Men om du ursprungligen registrerade dig för Azure före oktober 2013 skapades ingen katalog automatiskt. I så fall kan det hända att Azure etablerade en standardkatalog för ditt konto i efterhand. Din prenumeration kopplades i så fall till den standardkatalogen.

Den här typen av katalogetablering gjordes i oktober 2013 som en del av en övergripande förbättring av säkerhetsmodellen för Azure. Modellen tillhandahåller organisationsidentitetsfunktioner för alla Azure-kunder och ser till att alla Azure-resurser är tillgängliga i kontexten för en användare i katalogen. Du kan inte använda Azure utan en katalog. Det var nödvändigt att skapa en katalog för alla användare som registrerade sig före den 7 juli 2013 men som inte hade någon katalog. Om du redan hade skapat en katalog associerades din prenumeration med den katalogen.

Det kostar inget att använda Azure AD. Katalogen är en kostnadsfri resurs. Det finns en till Azure Active Directory Premium-nivå som licensieras separat och som ger tillgång till ytterligare funktioner, till exempel företagsanpassning och lösenordsåterställning via självbetjäning.

Om du vill byta katalogens visningsnamn klickar du på katalogen på portalen och sedan på **Konfigurera**. Som du ser senare i det här avsnittet kan du lägga till en ny katalog eller ta bort en katalog som du inte längre behöver. Om du vill associera prenumerationen med en annan katalog klickar du på tillägget **Inställningar** i det vänstra navigeringsfönstret och sedan på **Redigera katalog** längst ned på sidan **Prenumerationer**. Du kan också skapa en anpassad domän med DNS-namnet som du har registrerat i stället för standarddomänen *.onmicrosoft.com, som kan vara bättre med en tjänst som SharePoint Online.

## <a name="how-can-i-manage-directory-data"></a>Hur hanterar jag katalogdata?
Som administratör för en eller flera Microsoft-molntjänstprenumerationer kan du antingen använda Azure-hanteringsportalen, Microsoft Intune-kontoportalen eller administrationscenter för Office 365 för att hantera organisationens katalogdata. Du kan också ladda ned och köra [Microsoft Azure Active Directory-modulen för Windows PowerShell-cmdlets](https://msdn.microsoft.com/library/azure/jj151815.aspx) för att hantera data som lagras i Azure AD.

Från någon av dessa portaler (eller cmdlets) kan du:

* Skapa och hantera användar- och gruppkonton
* Hantera relaterade molntjänster som din organisation prenumererar på
* Konfigurera lokal integrering med din katalogtjänst

Azure-hanteringsportalen, administrationscenter för Office 365, Microsoft Intune-kontoportalen och Azure AD-cmdlets läser alla från och skriver till en enda delad instans av Azure AD som associeras med din organisations katalog, som du ser i följande bild. På så vis fungerar portaler (eller cmdlets) som ett frontend-gränssnitt som hämtar in och/eller ändrar dina katalogdata.

![][2]

Dessa kontoportaler och de associerade Azure AD PowerShell-cmdlets som används för att hantera användare och grupper har utvecklats på Azure AD-plattformen.

Om du gör en ändring i organisationens data med hjälp av någon av portalerna (eller cmdlets) när du är inloggad i någon av dessa tjänster så visas ändringen även på de andra portalerna nästa gång du loggar in till den tjänsten eftersom dessa data delas mellan de Microsoft-molntjänster som du prenumererar på.
Om du till exempel blockerar en användare via administrationscenter för Office 365 så kan användaren inte heller logga in till någon annan tjänst som din organisation prenumererar på. Om du visar samma användares konto på Microsoft Intune-kontoportalen ser du att användaren är blockerad.

## <a name="how-can-i-add-and-manage-multiple-directories"></a>Hur kan jag lägga till och hantera flera kataloger?
Du kan lägga till en Azure AD-katalog på Azure-hanteringsportalen. Välj tillägget **Active Directory** till vänster och klicka på **Lägg till**.

Du kan hantera alla kataloger som helt oberoende resurser. Varje katalog är en peer med en fullständig funktionsuppsättning som är logiskt oberoende av andra kataloger som du hanterar. Det finns ingen överordnad-underordnad-relation mellan kataloger. Detta oberoende mellan kataloger gäller resursoberoende, administrativt oberoende och synkroniseringsoberoende.

* **Resursoberoende**. Om du skapar eller tar bort en resurs i en katalog påverkas inte resurser i andra kataloger, delvis med undantag av externa användare, som beskrivs nedan. Om du använder en anpassad ”contoso.com”-domän med en katalog kan den inte användas med någon annan katalog.
* **Administrativt oberoende**.  Om en icke-administrativ användare av katalogen ”Contoso” skapar testkatalogen ”Test”:
  
  * Använd katalogsynkroniseringsverktyget om du vill synkronisera data med en enda AD-skog.
  * Administratörerna av Contoso-katalogen har inte någon direkt administratörsbehörighet till katalogen Test såvida inte någon av administratörerna av Test-katalogen uttryckligen beviljar dem sådan behörighet. ”Contoso”-administratörerna kan kontrollera åtkomsten till katalogen ”Test” tack vare deras kontroll över användarkontot som skapade ”Test”.
    
    Om du ändrar (lägger till eller tar bort) en administratörsroll för en användare i en katalog så påverkar inte ändringen administratörsrollen som användaren kan ha i en annan katalog.
* **Synkroniseringsoberoende**. Du kan konfigurera varje Azure AD separat om du vill synkronisera data från en enskild instans av antingen:
  
  * Katalogsynkroniseringsverktyget för att synkronisera data med en enda AD-skog
  * Azure Active Directory Connector för Forefront Identity Manager för att synkronisera data med en eller flera lokala skogar och/eller andra datakällor än AD-datakällor.

Observera att dina kataloger, till skillnad från andra Azure-resurser, inte är underordnade resurser till en Azure-prenumeration. Så om du avbryter eller låter din Azure-prenumeration förfalla kan du fortfarande komma åt dina katalogdata med Azure AD PowerShell, Azure Graph-API:et eller andra gränssnitt, till exempel administrationscenter för Office 365. Du kan även associera en annan prenumeration med katalogen.

## <a name="how-can-i-delete-an-azure-ad-directory"></a>Hur kan jag ta bort en Azure AD-katalog?
En global administratör kan ta bort en Azure AD-katalog från portalen. När en katalog tas bort tas även alla resurser som ingår i katalogen bort. Därför är det viktigt att du är helt säker på att du inte behöver katalogen innan du tar bort den.

> [!NOTE]
> Om användaren har loggat in med ett arbets- eller skolkonto kan han eller hon inte ta bort sin hemkatalog. Om användaren till exempel är inloggad som joe@contoso.onmicrosoft.com, kan denna användare inte ta bort katalogen som har contoso.onmicrosoft.com som standarddomän.
> 
> 

### <a name="conditions-that-must-be-met-to-delete-an-azure-ad-directory"></a>Villkor som måste vara uppfyllda innan en Azure AD-katalog kan tas bort
Azure AD kräver att vissa villkor är uppfyllda innan du kan ta bort en katalog. Detta minskar risken för att borttagningen av en katalog påverkar användare eller program negativt, till exempel möjligheten för användare att logga in i Office 365 eller att komma åt resurser i Azure. Om en katalog för en prenumeration tas bort av misstag kan användaren inte komma åt Azure-resurser för den prenumerationen.

Följande villkor kontrolleras:

* Den enda användaren i katalogen är den globala administratören som ska ta bort katalogen. Andra användare måste tas bort innan katalogen kan tas bort. Om användarna synkroniseras lokalt måste synkroniseringen inaktiveras och användarna måste tas bort i molnkatalogen med hjälp av hanteringsportalen eller Azure-modulen för Windows PowerShell. Det finns inga krav på att ta bort grupper eller kontakter, till exempel kontakter som lagts till från administrationscenter för Office 365.
* Det får inte finnas några program i katalogen. Alla program måste tas bort innan katalogen kan tas bort.
* Det får inte finnas några prenumerationer för Microsoft Online Services, till exempel Microsoft Azure, Office 365 eller Azure AD Premium, som är kopplade till katalogen. Om en standardkatalog skapades för dig i Azure kan du inte ta bort den katalogen om din Azure-prenumeration fortfarande är beroende av den här katalogen för autentisering. På liknande sätt kan du inte ta bort en katalog om en annan användare har associerat en prenumeration med den. Om du vill associera din prenumeration med en annan katalog loggar du in på Azure-hanteringsportalen och klickar på **Inställningar** i det vänstra navigeringsfönstret. Klicka sedan på **Redigera katalog** längst ned på sidan **Prenumerationer**. Mer information om Azure-prenumerationer finns i [Hur Azure-prenumerationer är kopplade till Azure AD](active-directory-how-subscriptions-associated-directory.md).

> [!NOTE]
> Om användaren har loggat in med ett arbets- eller skolkonto kan han eller hon inte ta bort sin hemkatalog. Om användaren till exempel är inloggad som joe@contoso.onmicrosoft.com, kan denna användare inte ta bort katalogen som har contoso.onmicrosoft.com som standarddomän.
> 
> 

* Inga Multi-Factor Authentication-providers får vara kopplade till katalogen.

## <a name="additional-resources"></a>Ytterligare resurser
* [Azure AD-forum](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD)
* [Azure Multi-Factor Authentication-forum](https://social.msdn.microsoft.com/Forums/home?forum=windowsazureactiveauthentication)
* [StackOverflow](http://stackoverflow.com/questions/tagged/azure)
* [Registrera dig för Azure som en organisation](sign-up-organization.md)
* [Hantera Azure AD med hjälp av Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
* [Tilldela administratörsroller i Azure AD](active-directory-assign-admin-roles.md)

<!--Image references-->
[1]: ./media/active-directory-administer/aad_portals.png
[2]: ./media/active-directory-administer/azure_tenants.png

