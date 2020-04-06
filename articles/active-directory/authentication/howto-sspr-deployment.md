---
title: Distributionsöverväganden för azure Active Directory självbetjäningslösenordsåterställning
description: Lär dig mer om distributionsöverväganden och strategi för en lyckad implementering av Azure AD-återställning av lösenord för självbetjäning
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd5b9e1f2640e68f7c819a49ad34d9c051c582c5
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667320"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>Planera en azure Active Directory-distribution för återställning av lösenordsåterbeställning av Azure Active Directory

> [!IMPORTANT]
> Den här distributionsplanen innehåller vägledning och metodtips för distribution av Azure AD-självbetjäningslösenordsåterställning (SSPR).
>
> **Om du är och slutanvändare och behöver komma tillbaka [https://aka.ms/sspr](https://aka.ms/sspr)till ditt konto går du till **.

[SSPR (Self-Service Password Reset)](https://www.youtube.com/watch?v=tnb2Qf4hTP8) är en Azure Active Directory -funktion (AD) som gör det möjligt för användare att återställa sina lösenord utan att kontakta IT-personal för att få hjälp. Användarna kan snabbt häva blockeringen och fortsätta arbeta oavsett var de befinner sig eller tid på dagen. Genom att låta de anställda häva blockeringen kan din organisation minska den icke-produktiva tiden och de höga supportkostnaderna för de vanligaste lösenordsrelaterade problemen.

SSPR har följande nyckelfunktioner:

* Självbetjäning gör det möjligt för slutanvändare att återställa sina utgångna eller icke-utgångna lösenord utan att kontakta en administratör eller helpdesk för support.
* [Återställning av lösenord](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) gör det möjligt att hantera lokala lösenord och lösning av kontoutelåsning genom molnet.
* Aktivitetsrapporter för lösenordshantering ger administratörer insikt i lösenordsåterställning och registreringsaktivitet som förekommer i organisationen.

Den här distributionsguiden visar hur du planerar och sedan testar en SSPR-utrullning.

Så här snabbt kan du se SSPR i aktion och sedan komma tillbaka för att förstå ytterligare distributionsöverväganden:

> [!div class="nextstepaction"]
> [Aktivera återställning av lösenord för självbetjäning (SSPR)](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>Läs mer om SSPR

Läs mer om SSPR. Se [hur det fungerar: Azure AD självbetjäning lösenordsåterställning](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks).

### <a name="key-benefits"></a>Viktiga fördelar

De viktigaste fördelarna med att aktivera SSPR är:

* **Hantera kostnad**. SSPR minskar IT-supportkostnaderna genom att göra det möjligt för användare att återställa lösenord på egen hand. Det minskar också kostnaden för förlorad tid på grund av förlorade lösenord och lockouts. 

* **Intuitiv användarupplevelse**. Det ger en intuitiv engångsregistreringsprocess som tillåter användare att återställa lösenord och låsa upp konton på begäran från vilken enhet eller plats som helst. SSPR tillåter användare att komma tillbaka till arbetet snabbare och bli mer produktiva.

* **Flexibilitet och säkerhet**. SSPR gör det möjligt för företag att komma åt den säkerhet och flexibilitet som en molnplattform tillhandahåller. Administratörer kan ändra inställningar för att tillgodose nya säkerhetskrav och distribuera dessa ändringar till användare utan att störa deras inloggning.

* **Robust granskning och användningsspårning**. En organisation kan se till att affärssystemen förblir säkra medan användarna återställer sina egna lösenord. Robusta granskningsloggar innehåller information om varje steg i processen för återställning av lösenord. Dessa loggar är tillgängliga från ett API och gör det möjligt för användaren att importera data till ett SIEM-system (Security Incident and Event Monitoring).

### <a name="licensing"></a>Licensiering

Azure Active Directory licensieras per användare vilket innebär att varje användare kräver en lämplig licens för de funktioner de använder. Vi rekommenderar gruppbaserad licensiering för SSPR. 

Information om hur du jämför utgåvor och funktioner och aktiverar grupp- eller användarbaserad licensiering finns i [Licenskrav för azure AD-återställning av lösenord för självbetjäning](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-licensing).

Mer information om priser finns i [Azure Active Directory-priser](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites"></a>Krav

* En aktiv Azure AD-klientorganisation med minst en aktiverad utvärderingslicens. Om det behövs, [skapa en gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Ett konto med behörigheter som global administratör.


### <a name="training-resources"></a>Utbildningsresurser

| Resurser| Länk och beskrivning |
| - | - |
| Videoklipp| [Ge användarna bättre IT-skalbarhet](https://youtu.be/g9RpRnylxS8) 
| |[Vad återställs lösenord för självbetjäning?](https://youtu.be/hc97Yx5PJiM)|
| |[Distribuera återställning av lösenord för självbetjäning](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[Hur konfigurerar du återställning av lösenord för självbetjäning för användare i Azure AD?](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[Så här [förbereder du användare] registrera [sin] säkerhetsinformation för Azure Active Directory](https://youtu.be/gXuh0XS18wA) |
| Onlinekurser|[Hantera identiteter i Active Directory i Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) Använd SSPR för att ge användarna en modern, skyddad upplevelse. Se särskilt modulen "[Hantera Azure Active Directory -användare och grupper".](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents) |
|Pluralsight Betalda kurser |[Frågorna om identitets- och åtkomsthantering](https://www.pluralsight.com/courses/identity-access-management-issues) Lär dig mer om IAM och säkerhetsproblem att vara medveten om i din organisation. Se särskilt modulen "Andra autentiseringsmetoder".|
| |[Komma igång med Microsoft Enterprise Mobility Suite](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) Lär dig de bästa metoderna för att utöka lokala resurser till molnet på ett sätt som möjliggör autentisering, auktorisering, kryptering och en säker mobil upplevelse. Se särskilt modulen "Konfigurera avancerade funktioner i Microsoft Azure Active Directory Premium".
|Självstudier |[Utföra en pilotlansering av självbetjäning av lösenordsåterställning för Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot) |
| |[Aktivera tillbakaskrivning av lösenord](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback) |
| |[Återställning av Azure AD-lösenord från inloggningsskärmen för Windows 10](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows) |
| VANLIGA FRÅGOR OCH SVAR|[Vanliga frågor och svar om lösenordshantering](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) |


### <a name="solution-architecture"></a>Lösningsarkitektur

I följande exempel beskrivs lösningsarkitekturen för återställning av lösenord för vanliga hybridmiljöer.

![diagram över lösningsarkitektur](./media/howto-sspr-deployment//solutions-architecture.png)

Beskrivning av arbetsflödet

Om du vill återställa lösenordet går användarna till [portalen](https://aka.ms/sspr)för återställning av lösenord . De måste verifiera den tidigare registrerade autentiseringsmetoden eller metoderna för att bevisa sin identitet. Om de har återställt lösenordet börjar återställningsprocessen.

* För molnanvändare lagrar SSPR det nya lösenordet i Azure AD. 

* För hybridanvändare skriver SSPR tillbaka lösenordet till Active Directory på prem via Azure AD Connect-tjänsten. 

För användare som har [inaktiverad DISKH-synkronisering (PASSWORD hash-synkronisering)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) lagrar SSPR endast lösenorden i Active Directory on-prem.

### <a name="best-practices"></a>Bästa praxis

Du kan hjälpa användare att registrera sig snabbt genom att distribuera SSPR tillsammans med ett annat populärt program eller en annan tjänst i organisationen. Den här åtgärden genererar en stor mängd inloggningar och driver registreringen.

Innan du distribuerar SSPR kan du välja att bestämma antalet och den genomsnittliga kostnaden för varje lösenordsåterställningsanrop. Du kan använda den här datainläggsdistributionen för att visa värdet SSPR ger till organisationen.

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>Aktivera kombinerad registrering för SSPR och MFA

Microsoft rekommenderar att organisationer aktiverar den kombinerade registreringsupplevelsen för SSPR- och multifaktorautentisering. När du aktiverar den här kombinerade registreringsupplevelsen behöver användarna bara välja sin registreringsinformation en gång för att aktivera båda funktionerna.

Den kombinerade registreringsupplevelsen kräver inte organisationer för att aktivera både SSPR och Azure Multi-Factor Authentication. Kombinerad registrering ger organisationer en bättre användarupplevelse. Mer information finns i [Kombinerad registrering av säkerhetsinformation (förhandsversion)](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-deployment-project"></a>Planera distributionsprojektet

Tänk på dina organisationsbehov medan du bestämmer strategin för den här distributionen i din miljö.

### <a name="engage-the-right-stakeholders"></a>Engagera rätt intressenter

När teknikprojekt misslyckas gör de det vanligtvis på grund av felaktiga förväntningar på påverkan, resultat och ansvar. För att undvika dessa fallgropar, [se till att du engagerar rätt intressenter](https://aka.ms/deploymentplans) och att intressentrollerna i projektet är väl förstådda genom att dokumentera intressenterna och deras projektinmatning och kontoföring.

#### <a name="required-administrator-roles"></a>Obligatoriska administratörsroller


| Affärsroll/Persona| Azure AD-roll (om det behövs) |
| - | - |
| Support på nivå 1| Lösenordsadministratör |
| Support på nivå 2| Användaradministratör |
| SSPR-administratör| Global administratör |


### <a name="plan-communications"></a>Planera kommunikation

Kommunikation är avgörande för att en ny tjänst ska lyckas. Du bör proaktivt kommunicera med användarna hur deras upplevelse kommer att förändras, när den ändras och hur du får support om de får problem. Gå igenom [distributionsmaterialet för återställning av lösenord för självbetjäning på Microsofts nedladdningscenter](https://www.microsoft.com/download/details.aspx?id=56768) för idéer om hur du planerar din kommunikationsstrategi för slutanvändare.

### <a name="plan-a-pilot"></a>Planera en pilot

Vi rekommenderar att den första konfigurationen av SSPR är i en testmiljö. Börja med en pilotgrupp genom att aktivera SSPR för en delmängd användare i organisationen. Se [Metodtips för en pilot](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans).

Om du vill skapa en grupp läser du hur [du skapar en grupp och lägger till medlemmar i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal). 

## <a name="plan-configuration"></a>Konfiguration av plan

Följande inställningar krävs för att aktivera SSPR tillsammans med rekommenderade värden.

| Område | Inställning | Värde |
| --- | --- | --- |
| **Egenskaper för SSPR** | Återställning av lösenord med självbetjäning aktiverat | **Utvald** grupp för pilot / **Alla** för produktion |
| **Autentiseringsmetoder** | Autentiseringsmetoder som krävs för att registrera | Alltid 1 mer än vad som krävs för återställning |
|   | Autentiseringsmetoder som krävs för att återställa | En eller två |
| **Registrering** | Kräv att användare registrerar sig vid inloggning | Ja |
|   | Antal dagar innan användare uppmanas att bekräfta sin autentiseringsinformation | 90 – 180 dagar |
| **Meddelanden** | Meddela användare om lösenordsåterställning | Ja |
|   | Meddela alla administratörer när andra administratörer återställer sina lösenord | Ja |
| **Anpassning** | Anpassa hjälpdesk-länken | Ja |
|   | Anpassad e-post eller URL för helpdesk | Supportwebbplats eller e-postadress |
| **Lokal integration** | Skriv tillbaka lösenord till lokal AD | Ja |
|   | Tillåt användare att låsa upp kontot utan att återställa lösenord | Ja |

### <a name="sspr-properties"></a>Egenskaper för SSPR

När du aktiverar SSPR väljer du en lämplig säkerhetsgrupp i pilotmiljön.

* För att framtvinga SSPR-registrering för alla rekommenderar vi att du använder alternativet **Alla.**
* Annars väljer du lämplig Azure AD- eller AD-säkerhetsgrupp.

### <a name="authentication-methods"></a>Autentiseringsmetoder

När SSPR är aktiverat kan användare bara återställa sitt lösenord om de har data i de autentiseringsmetoder som administratören har aktiverat. Metoder inkluderar telefon, Authenticator app anmälan, säkerhetsfrågor, etc. Mer information finns i [Vad är autentiseringsmetoder?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods).

Vi rekommenderar följande inställningar för autentiseringsmetod:

* Ange de **autentiseringsmetoder som krävs för att registrera** minst en mer än det nummer som krävs för att återställa. Om du tillåter flera autentiseringar får användarna flexibilitet när de behöver återställas.

* Ange **antal metoder som krävs för att återställa** till en nivå som är lämplig för din organisation. En kräver minst friktion, medan två kan öka din säkerhet hållning. 

Användaren måste ha de autentiseringsmetoder som konfigurerats i [lösenordsprinciperna och begränsningarna i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy).

### <a name="registration-settings"></a>Registreringsinställningar

Ange **Kräv att användare registrerar sig när du loggar in på** **Ja**. Den här inställningen kräver att användarna registrerar sig när de loggar in, vilket säkerställer att alla användare är skyddade.

Ange **antal dagar innan användarna uppmanas att bekräfta sin autentiseringsinformation** till mellan **90** och **180** dagar, såvida inte din organisation har ett affärsbehov för en kortare tidsperiod.

### <a name="notifications-settings"></a>Inställningar för meddelanden

Konfigurera både **Meddela användare om återställning** av lösenord och meddela alla administratörer när andra administratörer **återställer sitt lösenord** till **Ja**. Om du väljer **Ja** på båda ökar säkerheten genom att användarna är medvetna om när deras lösenord återställs. Det säkerställer också att alla administratörer är medvetna om när en administratör ändrar ett lösenord. Om användare eller administratörer får ett meddelande och de inte har initierat ändringen kan de omedelbart rapportera ett potentiellt säkerhetsproblem.

### <a name="customization-settings"></a>Anpassningsinställningar

Det är viktigt att anpassa helpdesk-e-post eller webbadress för att säkerställa att användare som upplever problem kan få hjälp omedelbart. Ange det här alternativet till en gemensam e-postadress eller webbsida som användarna känner till. 

Mer information finns i [Anpassa Azure AD-funktionen för återställning av lösenord med självbetjäning](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization).

### <a name="password-writeback"></a>Återgång till lösenord

**Återställning av lösenord** är aktiverat med [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity) och skriver återställning av lösenord i molnet tillbaka till en befintlig lokal katalog i realtid. Mer information finns i [Vad är tillbakaskrivning av lösenord?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

Vi rekommenderar följande inställningar:

* Kontrollera att **Skriv tillbaka lösenord till lokala AD** är inställt på **Ja**. 
* Ställ in **tillåt användare att låsa upp kontot utan** att återställa lösenordet till **Ja**.

Som standard låser Azure AD upp konton när de utför en återställning av lösenord.

### <a name="administrator-password-setting"></a>Lösenordsinställning för administratör

Administratörskonton har förhöjda behörigheter. De lokala företags- eller domänadministratörerna kan inte återställa sina lösenord via SSPR. Lokala administratörskonton har följande begränsningar:

* kan bara ändra sitt lösenord i sin on-prem-miljö.
* kan aldrig använda hemliga frågor och svar som en metod för att återställa sitt lösenord.

Vi rekommenderar att du inte synkroniserar dina Active Directory-administratörskonton på prem med Azure AD.

### <a name="environments-with-multiple-identity-management-systems"></a>Miljöer med flera identitetshanteringssystem

Vissa miljöer har flera identitetshanteringssystem. Lokala identitetshanterare som Oracle AM och SiteMinder kräver synkronisering med AD för lösenord. Du kan göra detta med ett verktyg som PCNS (Password Change Notification Service) med Microsoft Identity Manager (MIM). Information om det här mer komplexa scenariot finns i artikeln [Distribuera TJÄNSTEN Distribuera TJÄNSTEN FÖR meddelande om MIM-lösenordsändring på en domänkontrollant](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-testing-and-support"></a>Planera testning och support

Se till att resultaten blir som förväntat i varje steg av distributionen från inledande pilotgrupper via hela organisationen.

### <a name="plan-testing"></a>Planera testning

För att säkerställa att distributionen fungerar som förväntat planerar du en uppsättning testfall för att validera implementeringen. För att bedöma testfallen behöver du en testanvändare som inte är administratör med ett lösenord. Om du behöver skapa en användare läser du [Lägga till nya användare i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

Följande tabell innehåller användbara testscenarier som du kan använda för att dokumentera dina organisationers förväntade resultat baserat på dina principer.
<br>


| Affärsfall| Förväntat resultat |
| - | - |
| SSPR-portalen är tillgänglig inifrån företagsnätverket| Bestäms av din organisation |
| SSPR-portalen är tillgänglig utanför företagsnätverket| Bestäms av din organisation |
| Återställa användarlösenord från webbläsaren när användaren inte är aktiverad för återställning av lösenord| Användaren kan inte komma åt flödet för återställning av lösenord |
| Återställa användarlösenord från webbläsaren när användaren inte har registrerat sig för återställning av lösenord| Användaren kan inte komma åt flödet för återställning av lösenord |
| Användaren loggar in när den tillämpas för att göra registrering om återställning av lösenord| Uppmanar användaren att registrera säkerhetsinformation |
| Användaren loggar in när registreringen om återställning av lösenord är klar| Uppmanar användaren att registrera säkerhetsinformation |
| SSPR-portalen är tillgänglig när användaren inte har någon licens| Är tillgänglig |
| Återställa användarlösenord från Windows 10 Azure AD-ansluten eller hybrid-Azure AD-ansluten enhetslåsskärm| Användaren kan återställa lösenord |
| SSPR-registrerings- och användningsdata är tillgängliga för administratörer i nära realtid| Är tillgänglig via granskningsloggar |

Du kan också referera till [Slutför en Azure AD-självbetjäningspilotrulle för lösenordsåterställning](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot). I den här självstudien aktiverar du en pilot-distribution av SSPR i organisationen och testar med ett icke-administratörskonto.

### <a name="plan-support"></a>Planera support

SSPR skapar vanligtvis inte användarproblem, men det är viktigt att förbereda supportpersonalen för att hantera problem som kan uppstå. En administratör kan återställa lösenordet för slutanvändare via Azure AD-portalen, men det är bättre att lösa problemet via en supportprocess med självbetjäning.

För att möjliggöra supportteamets framgång kan du skapa en faq baserat på frågor du får från användarna. Några exempel:

| Scenarier| Beskrivning |
| - | - |
| Användaren har inga registrerade autentiseringsmetoder tillgängliga| En användare försöker återställa sitt lösenord men har inte någon av de autentiseringsmetoder som de registrerade tillgängliga (Exempel: de lämnade sin mobiltelefon hemma och kan inte komma åt e-post) |
| Användaren får inget sms eller samtal på sitt kontor eller sin mobiltelefon| En användare försöker verifiera sin identitet via sms eller samtal men får inget sms/samtal. |
| Användaren kan inte komma åt portalen för återställning av lösenord| En användare vill återställa sitt lösenord men är inte aktiverad för återställning av lösenord och kan inte komma åt sidan för att uppdatera lösenord. |
| Användaren kan inte ange ett nytt lösenord| En användare slutför verifieringen under flödet för återställning av lösenord men kan inte ange ett nytt lösenord. |
| Användaren ser inte länken Återställ lösenord på en Windows 10-enhet| En användare försöker återställa lösenordet från låsskärmen i Windows 10, men enheten är antingen inte ansluten till Azure AD eller så är Intune-enhetsprincipen inte aktiverad |

### <a name="plan-rollback"></a>Återställ plan

Så här återställer du distributionen:

* för en enskild användare tar du bort användaren från säkerhetsgruppen 

* för en grupp tar du bort gruppen från SSPR-konfigurationen

* Inaktivera SSPR för Azure AD-klienten för alla

## <a name="deploy-sspr"></a>Distribuera SSPR

Innan du distribuerar, se till att du har gjort följande:

1. Skapade och började genomföra din [kommunikationsplan.](#plan-communications)

1. Fastställ lämpliga [konfigurationsinställningar](#plan-configuration).

1. Identifierade användare och grupper för [pilot-](#plan-a-pilot) och produktionsmiljöer.

1. [Bestämda konfigurationsinställningar](#plan-configuration) för registrering och självbetjäning.

1. [Konfigurerad återställning av lösenord](#password-writeback) om du har en hybridmiljö.


**Du är nu redo att distribuera SSPR!**

Se [Aktivera återställning av lösenord för självbetjäning](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot#enable-self-service-password-reset) för fullständiga steg-för-steg-anvisningar för att konfigurera följande områden.

1. [Autentiseringsmetoder](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

1. [Registreringsinställningar](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)

1. [Inställningar för meddelanden](#notifications-settings)

1. [Anpassningsinställningar](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

1. [Lokal integration](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback)

### <a name="enable-sspr-in-windows"></a>Aktivera SSPR i Windows
För datorer som kör Windows 7, 8, 8.1 och 10 kan du [göra det möjligt för användare att återställa sitt lösenord på inloggningsskärmen i Windows](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-windows)

## <a name="manage-sspr"></a>Hantera SSPR

Azure AD kan ge ytterligare information om din SSPR-prestanda via granskningar och rapporter.

### <a name="password-management-activity-reports"></a>Aktivitetsrapporter för lösenordshantering 

Du kan använda färdiga rapporter på Azure-portalen för att mäta SSPR-prestanda. Om du är licensierad på rätt sätt kan du också skapa anpassade frågor. Mer information finns i [Rapporteringsalternativ för Azure AD-lösenordshantering](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting)

> [!NOTE]
>  Du måste vara [global administratör](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)och du måste välja att dessa data ska samlas in för din organisation. Om du vill anmäla dig måste du besöka fliken Rapportering eller granskningsloggarna på Azure Portal minst en gång. Fram till dess samlas inte data in för din organisation.

Granskningsloggar för registrering och återställning av lösenord är tillgängliga i 30 dagar. Om säkerhetsgranskning inom ditt företag kräver längre kvarhållning måste loggarna exporteras och förbrukas till ett SIEM-verktyg som [Azure Sentinel,](https://docs.microsoft.com/azure/sentinel/connect-azure-active-directory)Splunk eller ArcSight.

![SSPR Rapportering skärmdump](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>Autentiseringsmetoder- Användning och insikter

[Med användning och insikter](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights) kan du förstå hur autentiseringsmetoder för funktioner som Azure MFA och SSPR fungerar i din organisation. Den här rapporteringsfunktionen ger din organisation möjlighet att förstå vilka metoder som registrerar och hur de ska användas.

### <a name="troubleshoot"></a>Felsöka

* Se [Felsöka återställning av lösenord för självbetjäning](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-troubleshoot) 

* Följ [vanliga frågor och svar om lösenordshantering](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) 

### <a name="helpful-documentation"></a>Användbar dokumentation

* [Vad är autentiseringsmetoder?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

* [Hur fungerar det: Azure AD självbetjäning lösenordsåterställning?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)

* [Anpassa Azure AD-funktionen för återställning av lösenord med självbetjäning](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

* [Lösenordsprinciper och begränsningar i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)

* [Vad är tillbakaskrivning av lösenord?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

## <a name="next-steps"></a>Nästa steg

* Information om hur du kommer igång med distributionen av SSPR finns i [Aktivera azure ad-återställning av lösenord för självbetjäning](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-sspr.md)

* [Överväg att implementera Azure AD-lösenordsskydd](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)

* [Överväg att implementera Azure AD Smart Lockout](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)