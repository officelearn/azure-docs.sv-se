---
title: Skapa en strategi för hantering av flexibel åtkomstkontroll – Azure AD
description: Detta dokument ger vägledning om strategier som en organisation bör anta för att ge motståndskraft för att minska risken för lockout vid oförutsedda störningar
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ca5817e744ff81efcd549bc328d7ce5eeedb2d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908742"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Skapa en strategi för hantering av flexibel åtkomstkontroll med Azure Active Directory

>[!NOTE]
> The information contained in this document represents the current view of Microsoft Corporation on the issues discussed as of the date of publication. Eftersom Microsoft måste svara på förändrade marknadsvillkor bör det inte tolkas som ett åtagande från Microsofts sida, och Microsoft kan inte garantera riktigheten i all information som presenteras efter publiceringsdatumet.

Organisationer som förlitar sig på en enda åtkomstkontroll, till exempel MFA (Multi Factor Authentication) eller en enda nätverksplats, för att skydda sina IT-system är känsliga för åtkomstfel till sina appar och resurser om den enda åtkomstkontrollen blir otillgänglig eller felkonfigurerad. En naturkatastrof kan till exempel leda till att stora segment av telekommunikationsinfrastruktur eller företagsnät inte är tillgängliga. En sådan störning kan hindra slutanvändare och administratörer från att logga in.

Det här dokumentet innehåller vägledning om strategier som en organisation bör anta för att ge motståndskraft för att minska risken för utelåsning vid oförutsedda störningar med följande scenarier:

 1. Organisationer kan öka sin motståndskraft för att minska risken för lockout **innan en störning** genom att implementera begränsningsstrategier eller beredskapsplaner.
 2. Organisationer kan fortsätta att komma åt appar och resurser som de väljer **under en störning** genom att ha begränsningsstrategier och beredskapsplaner på plats.
 3. Organisationer bör se till att de bevarar information, till exempel loggar, **efter ett avbrott** och innan de återställer alla oförutsedda händelser som de implementerade.
 4. Organisationer som inte har genomfört förebyggande strategier eller alternativa planer kan kunna genomföra **nödalternativ** för att hantera störningar.

## <a name="key-guidance"></a>Viktig vägledning

Det finns fyra viktiga takeaways i detta dokument:

* Undvik administratörsutelåsning med hjälp av konton för nödåtkomst.
* Implementera MFA med hjälp av villkorad åtkomst (CA) i stället för mfa per användare.
* Minska användarnas utelåsning genom att använda flera kontroller för villkorlig åtkomst (CA).
* Minska användarnas utelåsning genom att etablera flera autentiseringsmetoder eller motsvarigheter för varje användare.

## <a name="before-a-disruption"></a>Innan ett avbrott

Förmildrande en faktisk störning måste vara en organisations primära fokus i hanteringen av åtkomstkontrollfrågor som kan uppstå. Förmildrande inkluderar planering för en faktisk händelse plus implementera strategier för att se till att åtkomstkontroller och åtgärder påverkas inte vid störningar.

### <a name="why-do-you-need-resilient-access-control"></a>Varför behöver du flexibel åtkomstkontroll?

 Identitet är kontrollplanet för användare som använder appar och resurser. Ditt identitetssystem styr vilka användare och under vilka villkor, till exempel åtkomstkontroller eller autentiseringskrav, användare får åtkomst till programmen. När ett eller flera autentiserings- eller åtkomstkontrollkrav inte är tillgängliga för användare att autentisera på grund av oförutsedda omständigheter kan organisationer uppleva ett eller båda av följande problem:

* **Administratörsutelåsning:** Administratörer kan inte hantera klienten eller tjänsterna.
* **Utelåsning av användare:** Användare kan inte komma åt appar eller resurser.

### <a name="administrator-lockout-contingency"></a>Administratörsutelåsning oförutsedda

Om du vill låsa upp administratörsåtkomst till din klientorganisation bör du skapa konton för nödåtkomst. Dessa konton för nödåtkomst, även kallade *break glass-konton,* ger åtkomst till hantering av Azure AD-konfiguration när normala privilegierade kontoåtkomstprocedurer inte är tillgängliga. Minst två konton för nödåtkomst bör skapas enligt rekommendationerna för [kontot för nödåtkomst.]( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)

### <a name="mitigating-user-lockout"></a>Förmildrande användarutelåsning

 Om du vill minska risken för att användarenutelåsning ska vara, använder du principer för villkorlig åtkomst med flera kontroller för att ge användarna möjlighet att välja hur de ska komma åt appar och resurser. Genom att ge en användare möjlighet att välja mellan till exempel logga in med MFA **eller** logga in från en hanterad enhet **eller** logga in från företagsnätverket, om en av åtkomstkontrollerna inte är tillgänglig har användaren andra alternativ för att fortsätta arbeta.

#### <a name="microsoft-recommendations"></a>Microsofts rekommendationer

Införliva följande åtkomstkontroller i dina befintliga principer för villkorlig åtkomst för organisation:

1. Etablera flera autentiseringsmetoder för varje användare som förlitar sig på olika kommunikationskanaler, till exempel Microsoft Authenticator-appen (internetbaserad), OATH-token (genererad på enheten) och SMS (telefon).
2. Distribuera Windows Hello för företag på Windows 10-enheter för att uppfylla MFA-krav direkt från enhetsinmatning.
3. Använd betrodda enheter via [Azure AD Hybrid Join](https://docs.microsoft.com/azure/active-directory/devices/overview) eller Microsoft [Intune Managed-enheter](https://docs.microsoft.com/intune/planning-guide). Betrodda enheter förbättrar användarupplevelsen eftersom själva den betrodda enheten kan uppfylla de starka autentiseringskraven för principen utan en MFA-utmaning för användaren. MFA kommer sedan att krävas när du registrerar en ny enhet och när du öppnar appar eller resurser från ej betrodda enheter.
4. Använd Azure AD-identitetsskyddsriskbaserade principer som förhindrar åtkomst när användaren eller inloggningen är i riskzonen i stället för fasta MFA-principer.

>[!NOTE]
> Riskbaserade principer kräver [Azure AD Premium P2-licenser.](https://azure.microsoft.com/pricing/details/active-directory/)

I följande exempel beskrivs principer som du måste skapa för att tillhandahålla en flexibel åtkomstkontroll för att användaren ska kunna komma åt sina appar och resurser. I det här exemplet behöver du en säkerhetsgrupp **AppUsers** med de målanvändare du vill ge åtkomst till, en grupp med namnet **CoreAdmins** med kärnadministratörerna och en grupp med namnet **EmergencyAccess** med konton för nödåtkomst.
Den här exempelprincipuppsättningen ger valda användare i **AppUsers**åtkomst till valda appar om de ansluter från en betrodd enhet eller ger stark autentisering, till exempel MFA. Det utesluter nödkonton och kärnadministratörer.

**Principer för begränsning av certifikatutfärdare har angetts:**

* Politik 1: Blockera åtkomst till personer utanför målgrupper
  * Användare och grupper: Inkludera alla användare. Exkludera AppUsers, CoreAdmins och EmergencyAccess
  * Molnappar: Inkludera alla appar
  * Villkor: (Ingen)
  * Bidragskontroll: Blockera
* Princip 2: Bevilja åtkomst till appusers som kräver MFA ELLER betrodd enhet.
  * Användare och grupper: Inkludera appusers. Exkludera CoreAdmins och EmergencyAccess
  * Molnappar: Inkludera alla appar
  * Villkor: (Ingen)
  * Bevilja kontroll: Bevilja åtkomst, kräver multifaktorautentisering, kräver att enheten är kompatibel. För flera kontroller: Kräv en av de markerade kontrollerna.

### <a name="contingencies-for-user-lockout"></a>Oförutsedda händelser för användarutelåsning

Alternativt kan din organisation också skapa principer för oförutsedda händelser. Om du vill skapa principer för oförutsedda händelser måste du definiera kompromisskriterier mellan affärskontinuitet, driftskostnader, ekonomiska kostnader och säkerhetsrisker. Du kan till exempel aktivera en princip för oförutsedda händelser endast för en delmängd av användare, för en delmängd av appar, för en delmängd av klienter eller från en delmängd av platser. Principer för oförutsedda händelser ger administratörer och slutanvändare åtkomst till appar och resurser under ett avbrott när ingen begränsningsmetod implementerades.
Att förstå din exponering under ett avbrott bidrar till att minska risken och är en viktig del av din planeringsprocess. Om du vill skapa en beredskapsplan bestämmer du först följande affärskrav för din organisation:

1. Fastställ dina verksamhetskritiska appar i förväg: Vilka appar måste du ha tillgång till, även med en lägre risk/säkerhetsposition? Skapa en lista över dessa appar och se till att dina andra intressenter (företag, säkerhet, juridik, ledarskap) alla är överens om att om all åtkomstkontroll försvinner måste dessa appar fortfarande fortsätta att köras. Du kommer sannolikt att sluta med kategorier av:
   * **Verksamhetskritiska appar i kategori 1** som inte kan vara otillgängliga i mer än några minuter, till exempel Appar som direkt påverkar organisationens intäkter.
   * **Kategori 2 viktiga appar** som verksamheten behöver för att vara tillgänglig inom några timmar.
   * **Appar med låg prioritet i kategori 3** som klarar ett avbrott på några dagar.
2. För appar i kategori 1 och 2 rekommenderar Microsoft att du i förplanerar vilken typ av åtkomstnivå du vill tillåta:
   * Vill du tillåta full åtkomst eller begränsad session, som att begränsa nedladdningar?
   * Vill du tillåta åtkomst till en del av appen men inte hela appen?
   * Vill du tillåta åtkomst till informationsarbetare och blockera administratörsåtkomst tills åtkomstkontrollen återställs?
3. För dessa appar rekommenderar Microsoft också att du planerar vilka åtkomstvägar du avsiktligt öppnar och vilka du stänger:
   * Vill du tillåta att webbläsare bara kommer åt och blockerar avancerade klienter som kan spara offlinedata?
   * Vill du bara tillåta åtkomst för användare i företagsnätverket och hålla externa användare blockerade?
   * Vill du bara tillåta tillträde från vissa länder eller regioner under avbrottet?
   * Vill du att principerna för beredskapsprinciperna, särskilt för verksamhetskritiska appar, ska misslyckas eller lyckas om en alternativ åtkomstkontroll inte är tillgänglig?

#### <a name="microsoft-recommendations"></a>Microsofts rekommendationer

En princip för villkorlig åtkomst för oförutsedda utgifter är en **inaktiverad princip** som utelämnar Azure MFA, MFA från tredje part, riskbaserade kontroller eller enhetsbaserade kontroller. När organisationen sedan bestämmer sig för att aktivera din beredskapsplan kan administratörer aktivera principen och inaktivera de vanliga kontrollbaserade principerna.

>[!IMPORTANT]
> Om du inaktiverar principer som upprätthåller säkerheten för användarna, även tillfälligt, minskar din säkerhetsposition medan beredskapsplanen är på plats.

* Konfigurera en uppsättning återgångsprinciper om ett avbrott i en autentiseringsuppgifterstyp eller en åtkomstkontrollmekanism påverkar åtkomsten till dina appar. Konfigurera en princip i inaktiverat tillstånd som kräver domänkoppling som en kontroll, som en säkerhetskopia för en aktiv princip som kräver en MFA-provider från tredje part.
* Minska risken för att dåliga aktörer gissar lösenord, när MFA inte krävs, genom att följa metoderna i faktabladet [om lösenordsvägledning.](https://aka.ms/passwordguidance)
* Distribuera [Azure AD Self-Service Password Reset (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) och [Azure AD Password Protection](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) för att se till att användarna inte använder vanliga lösenord och termer som du väljer att förbjuda.
* Använd principer som begränsar åtkomsten i apparna om en viss autentiseringsnivå inte uppnås i stället för att helt enkelt falla tillbaka till full åtkomst. Ett exempel:
  * Konfigurera en princip för säkerhetskopiering som skickar det begränsade sessionsanspråket till Exchange och SharePoint.
  * Om din organisation använder Microsoft Cloud App Security kan du överväga att falla tillbaka till en princip som engagerar MCAS och sedan MCAS Tillåter skrivskyddad åtkomst men inte uppladdningar.
* Namnge dina principer för att se till att det är lätt att hitta dem under en störning. Inkludera följande element i principnamnet:
  * Ett *etikettnummer* för principen.
  * Text att visa, är denna policy endast för nödsituationer. Till exempel: **AKTIVERA I NÖDLÄGE**
  * De *störningar* det gäller. Till exempel: **Under MFA Disruption**
  * Ett *sekvensnummer* för att visa den ordning som du måste aktivera principerna.
  * De *appar* som gäller.
  * De *kontroller* som den kommer att gälla.
  * De *villkor* det kräver.
  
Denna namngivningsstandard för beredskapsprinciperna kommer att vara följande: 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

Följande exempel: **Exempel A - Policy för oförutsedda certifikatutfärdare för att återställa åtkomst till verksamhetskritiska samarbetsappar**är en typisk företagsförutsevärde. I det här fallet kräver organisationen vanligtvis MFA för all Exchange Online- och SharePoint Online-åtkomst, och störningen i det här fallet är att MFA-leverantören för kunden har ett avbrott (oavsett om Azure MFA, lokal MFA-leverantör eller MFA från tredje part). Den här principen minskar det här avbrottet genom att specifika riktade användare endast får åtkomst till dessa appar från betrodda Windows-enheter när de kommer åt appen från sitt betrodda företagsnätverk. Det kommer också att utesluta nödkonton och kärnadministratörer från dessa begränsningar. De riktade användarna får sedan tillgång till Exchange Online och SharePoint Online, medan andra användare fortfarande inte har tillgång till apparna på grund av avbrottet. Det här exemplet kräver en namngiven nätverksplats **CorpNetwork** och en säkerhetsgrupp **ContingencyAccess** med målanvändarna, en grupp med namnet **CoreAdmins** med kärnadministratörerna och en grupp med namnet **EmergencyAccess** med kontona för nödåtkomst. Oförutsedda utgifter kräver fyra principer för att ge önskad åtkomst. 

**Exempel A - Principer för certifikatutfärdare för oförutsedda utgifter för att återställa åtkomst till verksamhetskritiska samarbetsappar:**

* Princip 1: Kräv domänanslutna enheter för Exchange och SharePoint
  * Namn: EM001 - AKTIVERA I NÖDLÄGE: MFA-avbrott[1/4] - Exchange SharePoint - Kräv Hybrid Azure AD-anslutning
  * Användare och grupper: Inkludera ContingencyAccess. Exkludera CoreAdmins och EmergencyAccess
  * Molnappar: Exchange Online och SharePoint Online
  * Villkor: Alla
  * Bidragskontroll: Kräv domänansluten
  * Tillstånd: Inaktiverat
* Princip 2: Blockera andra plattformar än Windows
  * Namn: EM002 - AKTIVERA I NÖDLÄGE: MFA Disruption[2/4] - Exchange SharePoint - Blockera åtkomst utom Windows
  * Användare och grupper: Inkludera alla användare. Exkludera CoreAdmins och EmergencyAccess
  * Molnappar: Exchange Online och SharePoint Online
  * Villkor: Enhetsplattform inkluderar alla plattformar, exkluderar Windows
  * Bidragskontroll: Blockera
  * Tillstånd: Inaktiverat
* Policy 3: Blockera andra nätverk än CorpNetwork
  * Namn: EM003 - AKTIVERA I NÖDLÄGE: MFA Disruption[3/4] - Exchange SharePoint - Block access except Corporate Network
  * Användare och grupper: Inkludera alla användare. Exkludera CoreAdmins och EmergencyAccess
  * Molnappar: Exchange Online och SharePoint Online
  * Villkor: Platser Inkludera valfri plats, exkluderar CorpNetwork
  * Bidragskontroll: Blockera
  * Tillstånd: Inaktiverat
* Policy 4: Blockera EAS uttryckligen
  * Namn: EM004 - AKTIVERA I NÖD: MFA Disruption[4/4] - Exchange - Blockera EAS för alla användare
  * Användare och grupper: Inkludera alla användare
  * Molnappar: Inkludera Exchange Online
  * Villkor: Klientappar: Exchange Active Sync
  * Bidragskontroll: Blockera
  * Tillstånd: Inaktiverat

Aktiveringsordning:

1. Uteslut ContingencyAccess, CoreAdmins och EmergencyAccess från den befintliga MFA-principen. Verifiera en användare i ContingencyAccess kan komma åt SharePoint Online och Exchange Online.
2. Aktivera princip 1: Verifiera användare på domänanslutna enheter som inte finns i undantagsgrupperna kan komma åt Exchange Online och SharePoint Online. Verifiera användare i gruppen Uteslut kan komma åt SharePoint Online och Exchange från vilken enhet som helst.
3. Aktivera princip 2: Kontrollera att användare som inte ingår i undantagsgruppen inte kan komma åt SharePoint Online och Exchange Online från sina mobila enheter. Verifiera användare i gruppen Uteslut kan komma åt SharePoint och Exchange från valfri enhet (Windows/iOS/Android).
4. Aktivera princip 3: Kontrollera att användare som inte ingår i undantagsgrupperna inte kan komma åt SharePoint och Exchange utanför företagsnätverket, även med en domänansluten dator. Verifiera användare i gruppen Uteslut kan komma åt SharePoint och Exchange från alla nätverk.
5. Aktivera princip 4: Kontrollera att alla användare inte kan få Exchange Online från de inbyggda e-postprogrammen på mobila enheter.
6. Inaktivera den befintliga MFA-principen för SharePoint Online och Exchange Online.

I nästa exempel återställs **exempel B - principer för oförutsedda certifikatutfärdare för att tillåta mobil åtkomst till Salesforce**, en företagsapps åtkomst. I det här fallet kräver kunden vanligtvis sina försäljningsanställda åtkomst till Salesforce (konfigurerad för enkel inloggning med Azure AD) från mobila enheter för att endast tillåtas från kompatibla enheter. Störningen i det här fallet är att det finns ett problem med att utvärdera enhetens efterlevnad och avbrottet sker vid en känslig tidpunkt där säljteamet behöver tillgång till Salesforce för att avsluta affärer. Dessa principer för oförutsedda händelser ger kritiska användare åtkomst till Salesforce från en mobil enhet så att de kan fortsätta att avsluta affärer och inte störa verksamheten. I det här exemplet innehåller **SalesforceContingency** alla försäljningsanställda som behöver behålla åtkomst och **SalesAdmins** innehåller nödvändiga administratörer av Salesforce.

**Exempel B - Principer för certifikatutfärdare för oförutsedda utgifter:**

* Policy 1: Blockera alla som inte ingår i salescontingency-teamet
  * Namn: EM001 - AKTIVERA I NÖDLÄGE: Störningar i enhetsefterlevnad[1/2] - Salesforce - Blockera alla användare utom SalesforceContingency
  * Användare och grupper: Inkludera alla användare. Exkludera SalesAdmins och SalesforceContingency
  * Molnappar: Salesforce.
  * Villkor: Inga
  * Bidragskontroll: Blockera
  * Tillstånd: Inaktiverat
* Policy 2: Blockera säljteamet från någon annan plattform än mobil (för att minska angreppets yta)
  * Namn: EM002 - AKTIVERA I NÖDLÄGE: Störningar i enhetsefterlevnad[2/2] - Salesforce - Blockera alla plattformar utom iOS och Android
  * Användare och grupper: Inkludera SalesforceContingency. Exkludera SalesAdmins
  * Molnappar: Salesforce
  * Villkor: Enhetsplattform inkluderar alla plattformar, exkluderar iOS och Android
  * Bidragskontroll: Blockera
  * Tillstånd: Inaktiverat

Aktiveringsordning:

1. Exkludera SalesAdmins och SalesforceContingency från den befintliga enhetsefterlevnadsprincipen för Salesforce. Verifiera att en användare i gruppen SalesforceContingency kan komma åt Salesforce.
2. Aktivera princip 1: Kontrollera att användare utanför SalesContingency inte kan komma åt Salesforce. Verifiera användare i SalesAdmins och SalesforceContingency kan komma åt Salesforce.
3. Aktivera princip 2: Kontrollera att användare i gruppen SalesContingency inte kan komma åt Salesforce från sina bärbara Windows/Mac-datorer men kan fortfarande komma åt från sina mobila enheter. Verifiera att SalesAdmin fortfarande kan komma åt Salesforce från vilken enhet som helst.
4. Inaktivera den befintliga enhetsefterlevnadsprincipen för Salesforce.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Distribuera synkronisering av lösenordsh hash även om du är federerad eller använder direktautentisering

Användarutelåsning kan också uppstå om följande villkor är uppfyllda:

- Din organisation använder en hybrididentitetslösning med direktautentisering eller federation.
- Dina lokala identitetssystem (till exempel Active Directory, AD FS eller en beroende komponent) är inte tillgängliga. 
 
För att vara mer flexibel bör din organisation [aktivera synkronisering av lösenordsh hash,](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)eftersom det gör att du kan [växla till att använda synkronisering av lösenord hash-enheter](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin) om dina lokala identitetssystem är nere.

#### <a name="microsoft-recommendations"></a>Microsofts rekommendationer
 Aktivera synkronisering av lösenordsh hash med hjälp av Azure AD Connect-guiden, oavsett om din organisation använder federation eller direktautentisering.

>[!IMPORTANT]
> Det krävs inte att konvertera användare från federerade till hanterad autentisering för att använda lösenord hash-synkronisering.

## <a name="during-a-disruption"></a>Vid en störning

Om du har valt att implementera en begränsningsplan kan du automatiskt överleva ett enda avbrott i åtkomstkontrollen. Men om du har valt att skapa en beredskapsplan kan du aktivera dina principer för oförutsedda händelser under avbrott i åtkomstkontrollen:

1. Aktivera dina principer för oförutsedda händelser som ger riktade användare åtkomst till specifika appar från specifika nätverk.
2. Inaktivera dina vanliga kontrollbaserade principer.

### <a name="microsoft-recommendations"></a>Microsofts rekommendationer

Beroende på vilka mildrande åtgärder eller oförutsedda händelser som används vid ett avbrott kan din organisation bevilja åtkomst med bara lösenord. Ingen skyddsåtgärd är en betydande säkerhetsrisk som måste vägas noggrant. Organisationer måste

1. Som en del av din strategi för ändringskontroll dokumenterar du varje ändring och föregående tillstånd för att kunna återställa alla oförutsedda händelser som du har implementerat så snart åtkomstkontrollerna är i full drift.
2. Anta att skadliga aktörer kommer att försöka skörda lösenord genom lösenord spray eller phishing-attacker medan du inaktiverat MFA. Dessutom kan felaktiga aktörer redan ha lösenord som tidigare inte gav åtkomst till någon resurs som kan försökas under det här fönstret. För kritiska användare, till exempel chefer, kan du delvis minska den här risken genom att återställa deras lösenord innan du inaktiverar MFA för dem.
3. Arkivera alla inloggningsaktiviteter för att identifiera vem som kommer åt vad under den tid då MFA inaktiverades.
4. [Triage alla riskdetekteringar rapporteras](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) under detta fönster.

## <a name="after-a-disruption"></a>Efter ett avbrott

Ångra de ändringar du har gjort som en del av den aktiverade beredskapsplanen när tjänsten har återställts som orsakade avbrottet. 

1. Aktivera de vanliga principerna
2. Inaktivera dina principer för oförutsedda händelser. 
3. Återställ alla andra ändringar som du har gjort och dokumenterat under avbrottet.
4. Om du har använt ett nödåtkomstkonto bör du komma ihåg att återskapa autentiseringsuppgifter och fysiskt skydda de nya autentiseringsuppgifterna som en del av dina rutiner för nödåtkomstkonto.
5. Fortsätt att [triage alla riskidentifieringar som rapporterats](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) efter störningar för misstänkt aktivitet.
6. Återkalla alla uppdateringstoken som har utfärdats [med PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) för att rikta en uppsättning användare. Att återkalla alla uppdateringstoken är viktigt för privilegierade konton som används under avbrottet och gör det kommer att tvinga dem att omauktorisera och uppfylla kontrollen av de återställda principerna.

## <a name="emergency-options"></a>Alternativ för nödsituationer

 I händelse av en nödsituation och din organisation inte tidigare implementerat en begränsnings- eller beredskapsplan följer du rekommendationerna i avsnittet [Oförutsedda händelser för användarutelåsning](#contingencies-for-user-lockout) om de redan använder principer för villkorlig åtkomst för att genomdriva MFA.
Om din organisation använder äldre principer för MFA per användare kan du överväga följande alternativ:

1. Om du har den utgående IP-adressen för företagsnätverket kan du lägga till dem som betrodda IP-adresser för att aktivera autentisering endast i företagsnätverket.
   1. Om du inte har inventeringen av utgående IP-adresser, eller om du måste aktivera åtkomst i och utanför företagsnätverket, kan du lägga till hela IPv4-adressutrymmet som betrodda IP-adresser genom att ange 0.0.0.0/1 och 128.0.0.0/1.

>[!IMPORTANT]
 > Om du breddar de betrodda IP-adresserna för att avblockera åtkomsten genereras inte riskidentifieringar som är associerade med IP-adresser (till exempel omöjliga resor eller okända platser).

>[!NOTE]
 > Konfigurera [betrodda IPs](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings) för Azure MFA är endast tillgängligt med [Azure AD Premium-licenser](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-licensing).

## <a name="learn-more"></a>Läs mer

* [Dokumentation om Azure AD-autentisering](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-iis)
* [Hantera administrativa konton för nödåtkomst i Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)
* [Konfigurera namngivna platser i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)
  * [Set-MsolDomainFederationSettings](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [Konfigurera hybrid-Azure Active Directory-anslutna enheter](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
* [Distributionsguide för Windows Hello för företag](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [Lösenordsvägledning - Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Vad är villkor i Azure Active Directory Villkorlig åtkomst?](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)
* [Vad är åtkomstkontroller i Azure Active Directory Villkorlig åtkomst?](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)
