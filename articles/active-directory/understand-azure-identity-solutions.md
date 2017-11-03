---
title: "Förstå Azure Identity | Microsoft Docs"
description: "Hämta en grundläggande förståelse för Microsoft Azure identitet lösning villkoren, begrepp och rekommendationer att göra det bästa identity styrning beslutet för din organisation."
keywords: 
author: jeffgilb
manager: femila
ms.reviewer: jsnow
ms.author: jeffgilb
ms.date: 7/17/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.custom: it-pro
ms.openlocfilehash: e368f14638c480a632afa7c17023aa8ae4c8833f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="understand-azure-identity-solutions"></a>Förstå identitetslösningar i Azure
Microsoft Azure Active Directory (AD Azure) är ett identitets- och molnet hanteringslösning som tillhandahåller katalogtjänster, identitet styrning och hantering av åtkomst. Azure AD snabbt [aktiverar enkel inloggning (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) till 1 000 är förintegrerade kommersiella och anpassade appar i den [Azure AD application gallery](https://azure.microsoft.com/marketplace/active-directory/all/). Många av de här apparna som du förmodligen redan använder till exempel Office 365, Salesforce.com, rutan, ServiceNow och Workday.

En enda Azure AD-katalog associeras automatiskt med en Azure-prenumeration när den skapas. Som tjänsten identitet i Azure innehåller Azure AD sedan alla Identitetshantering och access control-funktioner för molnbaserade resurser. Dessa resurser kan innehålla användare, appar och grupper för en enskild klientorganisation (organisation) som visas i följande diagram:

![Azure Active Directory](./media/understand-azure-identity-solutions/azure-ad.png)

Microsoft Azure tillhandahåller flera olika sätt att utnyttja identitet som en tjänst (IDaaS) med olika behörighetsnivåer komplexitet för att uppfylla behoven för din organisation. Resten av den här artikeln hjälper dig att förstå grundläggande Azure identity termer och begrepp samt rekommendationer om du vill göra det bästa valet från de tillgängliga alternativen.

## <a name="terms-to-know"></a>Termer att känna till

Innan du kan välja i en Azure identity-lösningen för din organisation, behöver du en grundläggande förståelse av de termer som används när man talar om Azure identity services.

|Termen känna till| Beskrivning|
|-----|-----|
|Azure-prenumeration |Prenumerationer som används för att betala för Azure-molntjänster och är vanligtvis kopplad till ett kreditkort. Du kan ha flera prenumerationer, men det kan vara svårt att dela resurser mellan prenumerationer.|
|Azure-klient | En Azure AD-klient är av en enda organisation. Det är en dedikerad, betrodda instans av Azure AD som skapas automatiskt när en organisation registrerar sig för en Microsoft cloud service-prenumeration som Azure, Intune och Office 365. Klienter kan få åtkomst till tjänster antingen i en dedikerad miljö (enstaka klient) eller i en miljö med delad med andra organisationer (multitenant).|
|Azure AD-katalog | Varje Azure-klient har en dedikerad, betrodda Azure AD-katalog som innehåller klientens användare, grupper och program. Den används för att utföra identitet och åtkomst till hanteringsfunktioner för klientresurser. Eftersom en unik Azure AD-katalog etableras automatiskt för att representera organisationen när du registrerar dig för en Microsoft-molntjänst som Azure, Microsoft Intune eller Office 365, ser du ibland villkoren *klient*,  *Azure AD*, och *Azure AD-katalog* utbytbara. |
|Egen domän | När du först registrerar dig för en prenumeration på Microsoft cloud tjänster, din klient (organisation) använder en *. onmicrosoft.com* domännamn. Dock de flesta organisationer har en eller flera domännamn som används för att göra företag och som slutanvändare använder för åtkomst till företagsresurser. Du kan lägga till ditt domännamn till Azure AD så att domännamnet är dina användare som  *alice@contoso.com*  i stället för  *alice@contoso.onmicrosoft.com* . |
|Azure AD-konto | Dessa är identiteter som skapas med hjälp av Azure AD eller en annan Microsoft-molntjänst som Office 365. De är lagrade i Azure AD och tillgänglig för alla prenumerationer på molntjänster i organisationen. |
|Administratör för Azure-prenumeration| Kontoadministratören är den person som registrerade sig för eller har köpt Azure-prenumerationen. De kan använda den [Kontocenter](https://account.azure.com/Subscriptions) för att utföra olika hanteringsuppgifter som att skapa prenumerationer avbryta prenumerationer, ändra faktureringen för en prenumeration eller ändra den tjänstadministratör. |
|Global administratör för Azure AD | Azure AD globala administratörer har fullständig åtkomst till alla administrativa funktioner i Azure AD. En global administratör blir den person som registrerar sig för en prenumeration på Microsoft molnet service automatiskt som standard. Du kan ha fler än en global administratör, men endast globala administratörer kan tilldela någon av [andra administratörsroller](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) till användare. |
|Microsoft-konto | Microsoft-konton (som skapats av du för personligt bruk) ger åtkomst till konsumentinriktade Microsoft-produkter och molntjänster, t.ex. Outlook (Hotmail), OneDrive, Xbox LIVE eller Office 365. Dessa identiteter skapas och lagras i Microsoft-konto konsumentidentitetssystemet som körs av Microsoft.|
|Arbets-eller skolkonton | Arbets- eller skolkonto konton (som utfärdats av en administratör för företag/academic) ger åtkomst till företagets affärsnivå Microsofts molntjänster, till exempel Azure, Intune och Office 365.|


## <a name="concepts-to-understand"></a>Begrepp att förstå

Nu när du vet villkoren grundläggande Azure identitet, bör du läsa mer om dessa Azure identity-begrepp som hjälper dig fatta ett välgrundat Azure identity service beslut.

|Konceptet att förstå |Beskrivning|
|-----|-----|
|[Hur Azure-prenumerationer är associerade med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) |Alla Azure-prenumerationer har en förtroenderelation med en Azure AD-katalog för att autentisera användare, tjänster och enheter. *Flera prenumerationer kan lita på samma Azure AD-katalog, men en prenumeration litar bara på en enda Azure AD-katalog*. Den här förtroenderelationen skiljer sig från relationen som en prenumeration har med andra Azure-resurser (webbplatser, databaser och så vidare), som är mer som underordnade resurser till en prenumeration. Om en prenumeration upphör att gälla sedan stoppas åtkomst till resurser som är associerade med prenumerationen än Azure AD också. Dock fortfarande Azure AD-katalog i Azure, så att du kan associera en annan prenumeration med katalogen och fortsätta att hantera klientresurser.|
|[Hur Azure AD-licensiering fungerar](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-get-started-azure-portal) | När du köper eller aktiverar Enterprise Mobility Suite, Azure AD Premium eller Azure AD Basic uppdateras katalogen med prenumeration, inklusive dess giltighetstid och förbetalda licenser. När prenumerationen är aktiv, tjänsten hanteras av Azure AD globala administratörer och används av licensierade användare. Din prenumerationsinformation antalet tilldelade eller tillgängliga licenser är tillgängliga i Azure-portalen från den **Azure Active Directory** > **licenser** bladet. Detta är det enklast att hantera din licens.|
|[Rollbaserad åtkomstkontroll i Azure-portalen](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)|Azure rollbaserad åtkomstkontroll (RBAC) ger detaljerad åtkomsthantering för Azure-resurser. För många behörigheter kan exponera och konto för attacker. För få behörigheter innebär att anställda kan få arbetet gjort effektivt. Med RBAC kan du ge anställda exakt behörigheter de behöver baserat på tre grundläggande roller som gäller för alla resursgrupper: ägare, deltagare och läsare. Du kan också skapa upp till 2 000 egen [anpassade RBAC-roller](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) dina specifika behov. |
|[Hybrididentitet](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)|Hybrididentitet uppnås genom att integrera dina lokala Windows Server Active Directory (AD DS) med Azure AD med hjälp av [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). På så sätt kan du ange en gemensam identitet för dina användare för Office 365, Azure, och lokala appar eller SaaS-program som är integrerade med Azure AD. Med hybrid identity utöka du effektivt din lokala miljö till molnet för identitets- och.|

### <a name="the-difference-between-windows-server-ad-ds-and-azure-ad"></a>Skillnaden mellan Windows Server AD DS och AD Azure
Både Azure Active Directory (Azure AD) och lokala Active Directory (AD DS eller Active Directory Domain Services) är system som lagrar katalogdata och hanterar kommunikation mellan användare och resurser, inklusive användarprocesser inloggning, autentisering, och katalogsökningar.

Om du redan är bekant med lokala Windows Server Active Directory Domain Services (AD DS) introducerades med Windows 2000 Server och sedan du förmodligen förstår det grundläggande konceptet att en identity-tjänsten. Dock är det också viktigt att förstå att Azure AD inte är bara en domänkontrollant i molnet. Det är ett helt nytt sätt för att tillhandahålla identitet som en tjänst (IDaaS) i Azure som kräver ett helt nytt sätt att tänker fullständigt omfatta molnbaserade funktioner och skyddar din organisation från aktuella hot. 

AD DS är en serverroll på Windows Server, vilket innebär att den kan användas på fysiska eller virtuella datorer. Den har en hierarkisk struktur utifrån X.500. DNS används för att hitta objekt, kan vara har åtgärdat med LDAP och främst används Kerberos för autentisering. Active Directory kan organisationsenheter (OU) och grupprincipobjekt (GPO) utöver ansluta datorer till domänen och skapas förtroenden mellan domäner.

IT har skyddat deras säkerhet perimeternätverk för år med hjälp av AD DS, men moderna, perimeter mindre företag stöder identitet behov för anställda, kunder och partner kräver en ny kontrollplan. Azure AD är detta plan för kontroll av identiteten. Säkerhet har flyttas utanför företagets brandvägg till molnet där Azure AD skyddar företagets resurser och åtkomst genom att tillhandahålla en gemensam identitet för användare (antingen lokalt eller i molnet). Detta ger användarna möjlighet att på ett säkert sätt få åtkomst till appar som de behöver för att utföra sitt arbete från nästan alla enheter. Sömlös risk-baserade data protection kontroller, backas upp av machine learning-funktioner och detaljerad rapportering finns också som IT-behov att hålla företagets data är säkra.

Azure AD är en offentlig katalogtjänst för flera kunder vilket innebär att du kan skapa en klient för molnservrar och program, till exempel Office 365 i Azure AD. Användare och grupper skapas i en platt struktur utan organisationsenheter eller grupprincipobjekt. Autentiseringen utförs via protokoll, till exempel SAML WS-Federation och OAuth. Det går att fråga Azure AD, men i stället för att använda LDAP måste du använda en REST-API som kallas AD Graph API. Dessa alla fungerar över HTTP och HTTPS.

### <a name="extend-office-365-management-and-security-capabilities"></a>Utöka Office 365-funktioner för hantering och säkerhet
Redan använder Office 365? Du kan påskynda din digitala transformation genom att utöka inbyggda Office 365-funktioner med Azure AD att skydda dina resurser, aktivera säker produktivitet för personalen hela. När du använder Azure AD och Office 365-funktioner förutom att, kan du skydda din portfölj för hela programmet med en identitet som möjliggör enkel inloggning för alla appar. Du kan expandera din funktioner för villkorlig åtkomst utifrån inte bara enheten tillstånd, men användaren, plats och program samt risk. Multifaktorautentisering (MFA) ger ytterligare skydd när du behöver den. Du får ytterligare tillsyn av behörigheter och ge på begäran, just-in-time administrativ åtkomst till. Användarna bli mer produktiva och skapa färre supportavdelningen biljetter, tack vare självbetjäning funktionerna Azure AD innehåller som återställer bortglömda lösenord, programförfrågningar, och skapa och hantera grupper.

> [!TIP]
> Om du vill veta mer om hur du använder Azure AD identity management med Office 365? [Hämta e-bok](https://info.microsoft.com/Extend-Office-365-security-with-EMS.html).

## <a name="microsoft-azure-identity-solutions"></a>Identitetslösningar i Microsoft Azure

Microsoft Azure tillhandahåller flera olika sätt att hantera identiteter för dina användare om de underhålls fullständigt lokalt, i molnet eller även någonstans i mellan. Dessa alternativ inkluderar: själv (DIY) AD DS i Azure, Azure Active Directory (Azure AD), Hybrididentitet och Azure AD Domain Services.

### <a name="do-it-yourself-diy-ad-ds"></a>Själv (själv) AD DS
För företag som behöver en kompakta i molnet, **själv (DIY) AD DS** i Azure kan användas. Det här alternativet stöder många scenarier för Windows Server AD DS som lämpar sig väl för distribution som virtuella datorer (VM) på Azure. Du kan till exempel skapa en virtuell dator i Azure som en domänkontrollant som körs i ett avlägset datacenter som är ansluten till fjärrnätverket. Därifrån kan skulle den virtuella datorn kunna stödja autentiseringsbegäranden från externa användare och förbättra prestanda. Det här alternativet är också väl lämpade en relativt billig ersättning till annars kostsamma katastrofåterställningsplatser genom att lägga upp ett litet antal domänkontrollanter och ett enda virtuellt nätverk i Azure. Slutligen kan du behöva distribuera ett program på Azure, t.ex SharePoint, som kräver Windows Server AD DS, men har inga beroende på det lokala nätverket eller företagets Windows Server Active Directory. I det här fallet kan du distribuera en isolerad skog i Azure för att uppfylla kraven för SharePoint-servergruppen. Det finns också stöd för att distribuera nätverksprogram som kräver anslutning till lokalt nätverk och lokala Active Directory.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (AD Azure)
**Azure AD-fristående** är en helt molnbaserade identitets- och åtkomsthantering som en tjänst (IDaaS). Azure AD innehåller kraftfulla och stabila funktioner för att hantera användare och grupper. Det hjälper till att skydda åtkomsten till både lokala program och program i molnet, däribland webbtjänster från Microsoft som Office 365 och många SaaS-program som inte kommer från Microsoft. Azure AD finns i tre versioner: ledigt, grundläggande och Premium. Azure AD förstärker företagets effektivitet och utökar säkerhet utöver gränsbrandvägg till en ny kontroll skyddas av Azure machine learning och andra avancerade säkerhetsfunktioner.

### <a name="hybrid-identity"></a>Hybrid-identitet
I stället för att välja mellan lokala eller molnbaserade identitets-lösningar, utöka många vanlig tänka IT-chefer och företag som har börjat förutseende företagets framtidens, sina lokala kataloger till molnet via **hybrididentitet** lösningar. Med hybrid identity får du en verkligen global identitet och åtkomst hanteringslösning som ger säker och effektiv åtkomst till program som användarna behöver för att utföra sitt arbete.

> [!TIP]
> Om du vill veta mer om hur IT-chefer har gjort Azure Active Directory en central del av sina IT-strategier kan hämta den [its Guide till Azure Active Directory](https://aka.ms/AzureADCIOGuide).

### <a name="azure-ad-domain-services"></a>Azure AD Domain Services
**Azure AD Domain Services** ger en molnbaserad möjlighet att använda AD DS för kontroll av lightweight Azure VM-konfiguration och ett sätt att uppfylla kraven för lokal identitet för nätverksutveckling och testning. Azure AD Domain Services är inte avsedd att lyfta och flytta dina lokala AD DS-infrastruktur till virtuella Azure-datorer som hanteras av Azure AD Domain Services. I stället ska Azure virtuella datorer i hanterade domäner användas för att stödja utveckling, testning och flytt av lokala program som kräver autentiseringsmetoder för AD DS till molnet.

## <a name="common-scenarios-and-recommendations"></a>Vanliga scenarier och rekommendationer

Här följer några vanliga scenarier för identitets- och rekommendationer om vilka Azure identitet kan vara mest lämpliga för varje.

|Scenario med identiteter| Rekommendation|
|-----|-----|
|Min organisation har gjort stora investeringar i lokala Windows Server Active Directory, men vi vill utöka identitet till molnet.| Vanligaste Azure identity-lösningen är [hybrididentitet](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview). Om du redan gjort investeringar i lokala AD DS, kan du enkelt utöka identitet till molnet med Azure AD Connect.|
|Mitt företag föddes i molnet och vi har inga investeringar i lokala identitetslösningar.| [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) är det bästa valet för endast molnbaserad företag utan lokal investeringar.|
|Jag behöver lightweight Azure VM-konfiguration och kontroll för att uppfylla identitetskrav på lokal för app-utveckling och testning.|[Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) är bra om du behöver använda AD DS för kontroll av lightweight Azure VM-konfiguration eller vill utveckla eller migrera äldre, katalog-medvetna lokala program till molnet.|  
|Jag behöver stöd för några virtuella datorer i Azure, men mitt företag fortfarande kraftigt har investerat i lokala Active Directory (AD DS).|Använd [själv AD DS](https://msdn.microsoft.com/library/azure/jj156090.aspx) att använda virtuella Azure-datorer när du behöver stöd för några virtuella datorer och har stor AD DS-investeringar lokalt. |

## <a name="where-can-i-learn-more"></a>Var kan jag lära sig mer?
Vi har massor av bra resurser online för att du lär dig allt om Azure AD. Här är en lista över bra artiklar för att komma igång:

* [Aktivera din katalog för hybridhantering av med Azure AD Connect](active-directory-aadconnect.md)
* [Ytterligare säkerhet för ett någonsin anslutna world](../multi-factor-authentication/multi-factor-authentication.md)
* [Automatisera användaren etablering och avetablering för SaaS-program med Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Komma igång med Azure AD Reporting](active-directory-reporting-getting-started.md)
* [Hantera lösenord från valfri plats](active-directory-passwords.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Automatisera användaren etablering och avetablering för SaaS-program med Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Hur du ger säker fjärråtkomst till lokala program](active-directory-application-proxy-get-started.md)
* [Hantera åtkomst till resurser med Azure Active Directory-grupper](active-directory-manage-groups.md)
* [Vad är Microsoft Azure Active Directory-licensiering?](active-directory-licensing-what-is.md)
* [Hur kan identifiera ej sanktionerade molnappar som används inom organisationen](active-directory-cloudappdiscovery-whatis.md)

## <a name="next-steps"></a>Nästa steg

Nu när du förstår Azure identity begrepp och alternativ som du kan du kan använda följande resurser för att komma igång med att implementera det alternativ som du har valt:

[Lär dig mer om Azure hybrididentitetslösningar](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution)

[Läs mer i en Azure Proof of Concept-miljö](https://aka.ms/aad-poc)

[Distribuera Azure AD i produktion](https://aka.ms/aad-onboard)
