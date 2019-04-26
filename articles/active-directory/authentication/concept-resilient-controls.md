---
title: Skapa en flexibel hanteringsstrategi för åtkomstkontroll – Azure Active Directory
description: Det här dokumentet innehåller vägledning om strategier för en organisation ska använda för att ge ökad flexibilitet för att minska risken för kontoutelåsning under oförutsedda avbrott
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 12/19/2018
ms.author: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e1fa72f8c7edf76ec46663fd62ee40a3a16e8cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60414961"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Skapa en flexibel hanteringsstrategi för åtkomstkontroll med Azure Active Directory

>[!NOTE]
> Informationen i det här dokumentet återspeglar den aktuella vyn tillhör Microsoft Corporation på problemet vid datumet för publiceringen. Eftersom Microsoft måste svara på förändrade marknadsvillkor, ska det inte tolkas som ett löfte från Microsofts sida och Microsoft kan inte garantera att informationen efter datumet för publiceringen.

Organisationer som förlitar sig på en enda access control, till exempel multifaktorautentisering (MFA) eller en enda nätverksplats, att skydda sina IT-system kan vara utsatta för åtkomstfel till sina appar och resurser om enda åtkomstkontrollen blir otillgänglig eller är felkonfigurerad. Till exempel kan en naturkatastrof resultera i stora segment av telekommunikation infrastruktur eller företagsnätverk otillgänglig. Sådana störningar kan förhindra att slutanvändare och administratörer från att kunna logga in.

Det här dokumentet innehåller vägledning om strategier för en organisation ska använda för att ge ökad flexibilitet för att minska risken för kontoutelåsning under oförutsedda avbrott med följande scenarier:

 1. Organisationer kan öka deras återhämtning för att minska risken för kontoutelåsning **innan ett avbrott** genom att implementera begränsningsstrategier eller beredskapsplaner.
 2. Organisationer kan fortsätta att få åtkomst till appar och resurser som de väljer **under ett avbrott** genom att begränsningsstrategier och beredskapsplaner på plats.
 3. Organisationer bör se till att de bevara information, till exempel loggar, **efter ett avbrott** och innan de är lättare att återställa alla risker som de har implementerats.
 4. Organisationer som inte har implementerats förebyggande strategier eller alternativa planer kanske kan implementera **nödfall alternativ** utan störningar.

## <a name="key-guidance"></a>Viktiga riktlinjer

Det finns fyra viktiga takeaways i det här dokumentet:

* Undvik att administratören kontoutelåsning genom att använda för åtkomst vid akutfall.
* Implementera MFA med villkorlig åtkomst (CA) i stället för MFA per användare.
* Minimera kontoutelåsning för användare med hjälp av flera kontroller för villkorlig åtkomst (CA).
* Minimera användaren kontoutelåsning genom att etablera flera autentiseringsmetoder eller motsvarigheter för varje användare.

## <a name="before-a-disruption"></a>Innan ett avbrott

Åtgärda ett avbrott i faktiska måste vara en organisations primära fokus med åtkomstkontroll problem som kan uppstå. Minimera innehåller planering för en faktisk händelse samt implementera strategier för att se till att åtkomstkontroller och åtgärder som inte påverkas under avbrott.

### <a name="why-do-you-need-resilient-access-control"></a>Varför behöver du elastiska åtkomstkontroll?

 Identiteten är kontrollplanet för användare med åtkomst till appar och resurser. Identitetssystemet styr vilka användare och på vilka villkor, till exempel åtkomstkontroller eller autentiseringskrav, användare får åtkomst till program. När en eller flera autentiserings- eller krav på åtkomstkontroll inte är tillgänglig för användare att autentiseras på grund av oförutsedda omständigheter kan kan organisationer uppstå en eller båda av följande problem:

* **Administratören kontoutelåsning:** Administratörer kan inte hantera klient eller tjänster.
* **Kontoutelåsning för användaren:** Användare kan inte komma åt appar eller resurser.

### <a name="administrator-lockout-contingency"></a>Administratören kontoutelåsning contingency

För att låsa upp administratörsåtkomst till din klient, bör du skapa för åtkomst vid akutfall. Dessa konton för åtkomst vid akutfall, även känt som *nödkonto* konton, tillåter åtkomst att hantera Azure AD-konfiguration om procedurer för åtkomst av normal privilegierat konto inte är tillgängliga. Minst två konton för åtkomst vid akutfall ska skapas efter den [rekommendationer för åtkomst vid akutfall]( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

### <a name="mitigating-user-lockout"></a>Åtgärda användare kontoutelåsning

 För att minska risken för kontoutelåsning för användare att använda principer för villkorlig åtkomst med flera kontroller för att ge användarna möjlighet att välja hur de kommer åt appar och resurser. Genom att ge en användare valet mellan, till exempel logga in med MFA **eller** loggar in från en hanterad enhet **eller** inloggning från företagsnätverket, om en av åtkomstkontroller är tillgänglig som användaren har andra alternativ för att fortsätta att fungera.

#### <a name="microsoft-recommendations"></a>Microsoft-rekommendationer

Lägga till följande åtkomstkontroller i de befintliga principerna för villkorlig åtkomst för organisation:

1. Etablera flera autentiseringsmetoder för varje användare som förlitar sig på olika kommunikationskanaler, till exempel Microsoft Authenticator-appen (Internetbaserad), OATH-token (genererade på enheten) och SMS (telephonic).
2. Distribuera Windows Hello för företag på Windows 10-enheter att uppfylla kraven på MFA direkt från enhetsinloggning.
3. Använda betrodda enheter via [Hybrid för Azure AD-anslutning](https://docs.microsoft.com/azure/active-directory/devices/overview) eller [Microsoft Intune-hanterade enheter](https://docs.microsoft.com/intune/planning-guide). Betrodda enheter kommer att förbättra användarupplevelsen eftersom den betrodda enheten själva kan uppfylla starka autentiseringskrav för princip utan en MFA-kontrollen för användaren. MFA måste sedan utföras när de registrerar en ny enhet och få åtkomst till appar eller resurser från ej betrodda enheter.
4. Använda Azure AD identity protection riskbaserade principer som förhindrar åtkomst när användaren eller logga in är i fara i stället för fast MFA-principer.

>[!NOTE]
> Riskbaserade principer kräver [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) licenser.

I följande exempel beskrivs principer som du måste skapa för att tillhandahålla en flexibel åtkomstkontroll för användare att komma åt sina appar och resurser. I det här exemplet behöver du en säkerhetsgrupp **AppUsers** med målanvändare som du vill ge åtkomst till, en grupp med namnet **CoreAdmins** med core-administratörer och en grupp med namnet  **EmergencyAccess** med åtkomst vid akutfall.
Det här exemplet principuppsättningen beviljar valda användare i **AppUsers**, åtkomst till valda appar om de ansluter från en betrodd enhet eller ger en stark autentisering, till exempel MFA. Den omfattar inte vid akutfall konton och core-administratörer.

**Ange principer för minskning av CA: N:**

* Principen 1: Blockera åtkomst till personer utanför målgrupper
  * Användare och grupper: Inkludera alla användare. Exkludera AppUsers och CoreAdmins EmergencyAccess
  * Appar i molnet: Inkludera alla appar
  * Villkor: (Ingen)
  * Ge kontroll: Blockera
* Princip för 2: Bevilja åtkomst till AppUsers att kräva MFA eller betrodd enhet.
  * Användare och grupper: Inkludera AppUsers. Undanta CoreAdmins och EmergencyAccess
  * Appar i molnet: Inkludera alla appar
  * Villkor: (Ingen)
  * Ge kontroll: Bevilja åtkomst, kräva multifaktorautentisering, Kräv att enheten ska vara kompatibel. För flera kontroller: Begär en av de valda kontrollerna.

### <a name="contingencies-for-user-lockout"></a>Risker för kontoutelåsning för användare

Din organisation kan också även skapa principer för oförutsedda händelser. Om du vill skapa principer för oförutsedda händelser, måste du definiera villkor för kompromiss mellan kontinuitet för företag, driftskostnaderna, ekonomisk kostnad och säkerhetsrisker. Du kan till exempel aktivera en princip för oförutsedda händelser endast till en delmängd av användare för en delmängd av appar för en delmängd av klienter, eller från en delmängd av platser. Principer för oförutsedda händelser ger administratörer och slutanvändare åtkomst till appar och resurser, under ett avbrott när ingen minskning metod har implementerats.
Förstå din exponering vid avbrott bidrar till att minska risken och är en viktig del av planeringsprocessen. Om du vill skapa din nödplan först bestämma följande affärsbehoven i organisationen:

1. Bestämma dina verksamhetskritiska kritiska appar förväg: Vad är appar måste du ge åtkomst till, även med en lägre risk/säkerhetsposition? Skapa en lista över de här apparna och kontrollera att din andra intressenter (business, säkerhet, juridiska och ledarskap) alla godkänner att om alla åtkomstkontroll är borta kan de här apparna fortfarande måste fortsätta att köras. Du sannolikt kommer att slutet med kategorier av:
   * **Kategori 1 verksamhetskritiska affärskritiska appar** som inte var tillgänglig i mer än ett par minuter, till exempel appar som direkt påverkar intäkter på organisationen.
   * **Kategori 2 viktiga appar** att företaget måste vara tillgänglig inom några timmar.
   * **Kategori 3 med låg prioritet appar** som klarar att ett avbrott i några få dagar.
2. För appar i kategori 1 och 2 rekommenderar Microsoft att du planerar inför vilken typ av åtkomstnivå som du vill tillåta:
   * Vill du tillåta fullständig åtkomst eller begränsad session, som begränsar nedladdningar?
   * Vill du tillåta åtkomst till delar av appen, men inte hela appen?
   * Vill du tillåta åtkomst till arbete och blockera administratörsåtkomst tills åtkomstkontrollen återställs?
3. För dessa appar, Microsoft rekommenderar också att du planerar vilka vägar av åtkomst du avsiktligt öppnas och vilka som du kommer att stängas:
   * Vill du tillåta webbläsaren endast åtkomst och blockera omfattande klienter som kan spara data offline?
   * Vill du tillåta åtkomst endast för användare i företagsnätverket och hålla utanför användare blockerats?
   * Vill du tillåta åtkomst från vissa länder eller regioner endast under störningar?
   * Vill du principer för oförutsedda händelser principer, särskilt för verksamhetskritiska affärskritiska appar att misslyckas eller lyckas om ett alternativt åtkomstkontroll inte är tillgängligt?

#### <a name="microsoft-recommendations"></a>Microsoft-rekommendationer

Principer för villkorlig åtkomst för oförutsedda händelser är en **inaktiverad princip** som utesluter Azure MFA, tredje parts MFA riskbaserad eller enhetsbaserad kontroller. När din organisation bestämmer sig för att aktivera din nödplan, kan administratörer aktivera principen och inaktivera regelbundna kontroll-baserade policys.

>[!IMPORTANT]
> Inaktivera principer som höja säkerheten för dina användare, även tillfälligt minskar din säkerhetsposition medan nödplan är på plats.

* Konfigurera en uppsättning principer för återställning om ett avbrott i en typ av autentiseringsuppgift eller en kontroll mekanism påverkan åtkomst till dina appar. Konfigurera en princip i inaktiverat tillstånd som kräver domänanslutning som en kontroll, som en säkerhetskopia för en aktiv princip som kräver att en tredje parts MFA-provider.
* Minska risken för obehöriga gissa lösenord, när MFA inte krävs, genom att följa metoder i den [lösenord vägledning](https://aka.ms/passwordguidance) white paper om.
* Distribuera [Azure AD Self-Service lösenord återställer (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) och [Azure AD-lösenordsskydd](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) att kontrollera att användare inte använda vanliga lösenord och villkor som du vill förbjuda.
* Använda principer som begränsar åtkomst i apparna om en viss autentiseringsnivå inte uppnås i stället för bara återgång till fullständig åtkomst. Exempel:
  * Konfigurera en princip för säkerhetskopiering som skickar begränsad session anspråket till Exchange och SharePoint.
  * Om din organisation använder Microsoft Cloud App Security kan du överväga att återgång till en princip som engagerar MCAS och sedan MCAS tillåter läsbehörighet men inte laddar upp.
* Namnge dina principer för att se till att det är enkelt att hitta dem under ett avbrott. Inkludera följande element i principens namn:
  * En *märka nummer* för principen.
  * Text att visa den här principen avser endast nödsituationer. Exempel: **AKTIVERA I NÖDFALL**
  * Den *avbrott* som gäller för. Exempel: **Under avbrott i MFA**
  * En *sekvensnummer* för att visa ordningen du måste aktivera principerna.
  * Den *appar* som gäller för.
  * Den *kontroller* gäller.
  * Den *villkor* krävs.
  
Den här namngivningsstandarden för oförutsedda händelser principer kommer att på följande sätt: 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

I följande exempel: **Exempel A – Contingency CA: N för att återställa åtkomst till verksamhetskritiska Samarbetsappar**, är en typisk företagets reservplan. Organisationen kräver vanligtvis MFA för all åtkomst för Exchange Online och SharePoint Online i det här scenariot och kan avbrott i det här fallet är MFA-provider för kunden har ett avbrott (om Azure MFA, lokala MFA-provider eller tredje parts MFA). Den här principen minskar risken för avbrottet genom att tillåta specifika målanvändare åtkomst till de här apparna från betrodda Windows-enheter bara när de använder appen från betrodda företagets nätverk. Det kommer också utesluta nödfall konton och core administratörer från dessa begränsningar. Målanvändarna kommer sedan att få åtkomst till Exchange Online och SharePoint Online, medan andra användare inte kommer fortfarande har åtkomst till appar på grund av avbrottet. Det här exemplet kräver en namngiven nätverksplats **CorpNetwork** och en säkerhetsgrupp **ContingencyAccess** med målanvändare, en grupp med namnet **CoreAdmins** med den Core-administratörer och en grupp med namnet **EmergencyAccess** med åtkomst vid akutfall. Contingency kräver fyra principer för att ange att lägga till. 

**Exempel A – Contingency CA-principer för att återställa åtkomst till verksamhetskritiska Samarbetsappar:**

* Principen 1: Kräv att domänanslutna enheter för Exchange och SharePoint
  * Namn: EM001 - AKTIVERA I NÖDFALL: MFA Disruption[1/4] - Exchange SharePoint - Require Hybrid Azure AD Join
  * Användare och grupper: Inkludera ContingencyAccess. Undanta CoreAdmins och EmergencyAccess
  * Appar i molnet: Exchange Online och SharePoint Online
  * Villkor: Alla
  * Ge kontroll: Kräv domänansluten
  * Status: Disabled
* Princip för 2: Blockera andra plattformar än Windows
  * Namn: EM002 - AKTIVERA I NÖDFALL: MFA-avbrott [2/4] - Exchange SharePoint – blockera åtkomst utom Windows
  * Användare och grupper: Inkludera alla användare. Undanta CoreAdmins och EmergencyAccess
  * Appar i molnet: Exchange Online och SharePoint Online
  * Villkor: Plattformen omfattar alla Enhetsplattformar, undanta Windows
  * Ge kontroll: Blockera
  * Status: Disabled
* Princip 3: Blockera nätverk än CorpNetwork
  * Namn: EM003 - AKTIVERA I NÖDFALL: MFA-avbrott [3 och 4] - Exchange SharePoint – blockera åtkomst förutom företagets nätverk
  * Användare och grupper: Inkludera alla användare. Undanta CoreAdmins och EmergencyAccess
  * Appar i molnet: Exchange Online och SharePoint Online
  * Villkor: Platser är valfri plats, undanta CorpNetwork
  * Ge kontroll: Blockera
  * Status: Disabled
* Princip 4: Blockera EAS uttryckligen
  * Namn: EM004 - AKTIVERA I NÖDFALL: MFA-avbrott [4/4] - Exchange - Block EAS för alla användare
  * Användare och grupper: Inkludera alla användare
  * Appar i molnet: Innehåller Exchange Online
  * Villkor: Klientappar: Exchange Active Sync
  * Ge kontroll: Blockera
  * Status: Disabled

Ordningen för aktivering:

1. Undanta ContingencyAccess och CoreAdmins EmergencyAccess från den befintliga MFA-principen. Kontrollera att en användare i ContingencyAccess kan komma åt SharePoint Online och Exchange Online.
2. Aktivera principen 1: Kontrollera på domänanslutna enheter som inte är i exkludera grupper kan komma åt Exchange Online och SharePoint Online. Verifiera användare i gruppen exkludera kan komma åt SharePoint Online och Exchange från valfri enhet.
3. Aktivera princip för 2: Kontrollera användare som inte är i gruppen exkludera inte kan hämta till SharePoint Online och Exchange Online från sina mobila enheter. Verifiera användare i gruppen exkludera kan komma åt SharePoint och Exchange från valfri enhet (Windows/iOS/Android).
4. Aktivera princip för 3: Kontrollera användare som inte är i exkludera grupper kan inte komma åt SharePoint och Exchange av företagets nätverk, även med en domän ansluten dator. Verifiera användare i gruppen exkludera kan komma åt SharePoint och Exchange från alla nätverk.
5. Aktivera princip för 4: Kontrollera att alla användare inte kan hämta Exchange Online från interna e-program på mobila enheter.
6. Inaktivera den befintliga MFA-principen för SharePoint Online och Exchange Online.

I den här nästa exempel **exempel B - Contingency CA-principer för att tillåta mobil åtkomst till Salesforce**, en business-app-åtkomst har återställts. I det här scenariot kräver kunden normalt åtkomsten säljpersonal till Salesforce (som konfigurerats för enkel inloggning på med Azure AD) från mobila enheter endast tillåtas från kompatibla enheter. Avbrott i det här fallet är att det finns ett problem med att utvärdera enhetens efterlevnad och driftstörningarna som händer i taget känsliga där försäljningen team behöver åtkomst till Salesforce och gör affärer. Dessa principer för oförutsedda händelser så beviljas kritiska användare åtkomst till Salesforce från en mobil enhet så att de kan fortsätta att Stäng erbjudanden och avbryter inte verksamheten. I det här exemplet **SalesforceContingency** innehåller alla säljpersonal behöva behålla åtkomsten och **SalesAdmins** innehåller nödvändiga Salesforce-administratörer.

**Exempel B - Contingency CA-principer:**

* Principen 1: Blockera alla inte i SalesContingency-teamet
  * Namn: EM001 - AKTIVERA I NÖDFALL: Enhetens efterlevnad avbrott [1/2] - Salesforce - Block alla användare utom SalesforceContingency
  * Användare och grupper: Inkludera alla användare. Undanta SalesAdmins och SalesforceContingency
  * Appar i molnet: Salesforce.
  * Villkor: Ingen
  * Ge kontroll: Blockera
  * Status: Disabled
* Princip för 2: Blockera säljteamet från valfri plattform än mobile (för att minska ytan på attack)
  * Namn: EM002 - AKTIVERA I NÖDFALL: Enhetens efterlevnad avbrott [2/2] - Salesforce - Block på alla plattformar förutom iOS och Android
  * Användare och grupper: Inkludera SalesforceContingency. Exkludera SalesAdmins
  * Appar i molnet: Salesforce
  * Villkor: Plattformen omfattar alla Enhetsplattformar, undanta iOS och Android
  * Ge kontroll: Blockera
  * Status: Disabled

Ordningen för aktivering:

1. Undanta SalesAdmins och SalesforceContingency från den befintliga policyn för efterlevnad för Salesforce. Kontrollera att en användare i gruppen SalesforceContingency har åtkomst till Salesforce.
2. Aktivera principen 1: Kontrollera användare utanför SalesContingency har inte åtkomst till Salesforce. Verifiera användare i SalesAdmins och SalesforceContingency har åtkomst till Salesforce.
3. Aktivera princip för 2: Verifiera användare i gruppen SalesContingency kan komma åt Salesforce från sina bärbara Windows/Mac-datorer men kan fortfarande komma åt från sina mobila enheter. Kontrollera SalesAdmin har fortfarande åtkomst till Salesforce från valfri enhet.
4. Inaktivera den befintliga policyn för efterlevnad för Salesforce.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Distribuera lösenordshashsynkronisering även om de är externa eller användning av direktautentisering

Användaren kontoutelåsning kan också inträffa om följande villkor är uppfyllda:

- Din organisation använder en hybrididentitetslösning med direktautentisering eller federation.
- Din lokala identitetssystem (till exempel Active Directory, AD FS eller ingen beroende komponent) är inte tillgängliga. 
 
För att vara mer motståndskraftiga, din organisation bör [aktivera lösenordshashsynkronisering](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), eftersom det gör det möjligt för dig att [istället använda lösenordshashsynkronisering](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin) om ditt lokala identitetssystem är otillgängliga.

#### <a name="microsoft-recommendations"></a>Microsoft-rekommendationer
 Aktivera lösenordshashsynkronisering med hjälp av Azure AD Connect-guiden, oavsett om din organisation använder federation eller direktautentisering.

>[!IMPORTANT]
> Det krävs inte att konvertera användare från federerad för hanterad autentisering du använder lösenordets hash-synkronisering.

## <a name="during-a-disruption"></a>Under ett avbrott

Om du valt för att implementera en plan för minskning kommer du att kunna överleva automatiskt ett avbrott för enkel åtkomstkontroll. Om du har valt för att skapa en nödplan, kommer du att kunna aktivera dina principer för oförutsedda händelser under åtkomstkontroll avbrott:

1. Aktivera din oförutsedda händelser principer för att bevilja målanvändare, åtkomst till specifika appar från vissa nätverk.
2. Inaktivera din vanliga kontroll-baserade policys.

### <a name="microsoft-recommendations"></a>Microsoft-rekommendationer

Beroende på vilka åtgärder eller risker används under ett avbrott, kan din organisation bevilja åtkomst med bara lösenord. Inga skyddet är en stor säkerhetsrisk som måste vägas noggrant. Organisationer måste:

1. Som en del av din strategi för kontroll av ändringen, dokumentera varje ändring och det tidigare tillståndet för att kunna återställa eventuella risker som du har implementerat så snart åtkomstkontroller är fullt fungerande.
2. Anta att skadliga aktörer ska försöka inhämta lösenord via lösenord sprutar eller phishing-attacker medan du har inaktiverat MFA. Illvilliga aktörer kanske redan har lösenord som tidigare inte medgav åtkomst till alla resurser som kan utföras under den här perioden. För kritiska användare, till exempel chefer minska du delvis denna risk genom att återställa sina lösenord innan du inaktiverar MFA för dessa.
3. Arkivera alla inloggningsaktiviteter för att identifiera vem som har åtkomst till vad under den tid som MFA har inaktiverats.
4. [Prioritering som rapporterats av alla riskhändelser](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) under den här perioden.

## <a name="after-a-disruption"></a>Efter ett avbrott

Ångra ändringarna som en del av aktiverad nödplan när tjänsten har återställts som orsakade avbrott. 

1. Aktivera de vanliga principerna
2. Inaktivera principer för oförutsedda händelser. 
3. Återställa alla ändringar du gjort och dokumenterade under kan avbrott.
4. Kom ihåg att återskapa autentiseringsuppgifter och fysiskt skydda nya autentiseringsuppgifter information som en del av ditt konto för åtkomst vid akutfall-procedurer om du har använt ett åtkomstkonto för nödläge.
5. Fortsätta att [prioritering som rapporterats av alla riskhändelser](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) efter avbrott för misstänkt aktivitet.
6. Återkalla alla uppdateringstokens som har utfärdats [med hjälp av PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) att rikta en uppsättning användare. Återkalla alla uppdateringstokens är viktig för privilegierade konton som används under avbrott och göra det tvingar dem att autentiseras på nytt och uppfylla kontroll över de återställda principerna.

## <a name="emergency-options"></a>Alternativ för nödläge

 Vid nödfall och din organisation inte tidigare implementera en lösning eller nödplan och sedan följa rekommendationerna i den [risker för användaren kontoutelåsning](#contingencies-for-user-lockout) avsnittet om de redan använder villkorlig åtkomst principer för att använda MFA.
Om din organisation använder äldre principer för MFA för per användare, kan du överväga följande alternativ:

1. Om du har den utgående IP-adressen för företagets nätverk, kan du lägga till dem som betrodda IP-adresser att aktivera autentisering endast till företagets nätverk.
   1. Om du inte har med lagret för utgående IP-adresser, eller du krävs för att aktivera åtkomst i och utanför företagets nätverk, kan du lägga till hela IPv4-adressutrymmet som tillförlitliga IP-adresser genom att ange 0.0.0.0/1 och 128.0.0.0/1.

>[!IMPORTANT]
 > Om du bredda de betrodda IP-adresserna för att låsa upp åtkomst genereras inte riskhändelser som är associerade med IP-adresser (till exempel omöjlig resa eller okända platser).

>[!NOTE]
 > Konfigurera [tillförlitliga IP-adresser](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings) för Azure MFA är bara tillgänglig med [Azure AD Premium-licenser](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-licensing).

## <a name="learn-more"></a>Läs mer

* [Dokumentation om Azure AD-autentisering](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-iis)
* [Hantera nödfall-åtkomst till administratörskonton i Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)
* [Konfigurera namngivna platser i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)
  * [Set-MsolDomainFederationSettings](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [Konfigurera hybrid Azure Active Directory-anslutna enheter](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
* [Distributionsguide för Windows Hello för företag](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [Riktlinjer för lösenord – Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Vad är villkor i Azure Active Directory villkorlig åtkomst?](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)
* [Vad är åtkomstkontroller i Azure Active Directory villkorlig åtkomst?](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)
