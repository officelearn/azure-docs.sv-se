---
title: Så här fungerar förtroenden för Azure AD Domain Services | Microsoft-dokument
description: Läs mer om hur skogsförtroende fungerar med Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 903881a1d15c1f043e381f50e5b69d661cd08192
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476434"
---
# <a name="how-trust-relationships-work-for-resource-forests-in-azure-active-directory-domain-services"></a>Så här fungerar förtroenderelationer för resursskogar i Azure Active Directory Domain Services

Ad DS (Active Directory Domain Services) ger säkerhet över flera domäner eller skogar via domän- och skogsförtroenderelationer. Innan autentisering kan ske över förtroenden måste Windows först kontrollera om domänen som begärs av en användare, dator eller tjänst har en förtroenderelation med domänen för det begärande kontot.

Om du vill söka efter den här förtroenderelationen beräknar Windows-säkerhetssystemet en förtroendesökväg mellan domänkontrollanten (DC) för den server som tar emot begäran och en domänkontrollant i domänen för det begärande kontot.

De åtkomstkontrollmekanismer som tillhandahålls av AD DS och den distribuerade säkerhetsmodellen för Windows ger en miljö för drift av domän- och skogsförtroende. För att dessa förtroenden ska fungera korrekt måste varje resurs eller dator ha en direkt förtroendesökväg till en domänkontrollant i domänen där den finns.

Förtroendesökvägen implementeras av tjänsten Net Logon med hjälp av en autentiserat RPC-anslutning (Remote Procedure Call) till den betrodda domänmyndigheten. En säker kanal sträcker sig också till andra AD DS-domäner via förtroenderelationer mellan domäner. Den här säkra kanalen används för att hämta och verifiera säkerhetsinformation, inklusive säkerhetsidentifierare (SID) för användare och grupper.

## <a name="trust-relationship-flows"></a>Förtroenderelationsflöden

Flödet av säker kommunikation över truster bestämmer elasticiteten i ett förtroende. Hur du skapar eller konfigurerar ett förtroende avgör hur långt kommunikationen sträcker sig inom eller mellan skogar.

Flödet av kommunikation över förtroenden bestäms av inriktningen av förtroendet. Truster kan vara enkelriktade eller tvåvägs och kan vara transitiva eller icke-transitiva.

Följande diagram visar att alla domäner i *träd 1* och *träd 2* har transitiva förtroenderelationer som standard. Därför kan användare i *träd 1* komma åt resurser i domäner i *träd 2* och användare i *träd 1* kan komma åt resurser i *träd 2*när rätt behörigheter tilldelas vid resursen.

![Diagram över förtroenderelationer mellan två skogar](./media/concepts-forest-trust/trust-relationships.png)

### <a name="one-way-and-two-way-trusts"></a>Enkelriktade och tvåvägstruster

Förtroenderelationer som möjliggör åtkomst till resurser kan vara antingen enkelriktade eller tvåvägs.

Ett enkelriktat förtroende är en enkelriktad autentiseringssökväg som skapats mellan två domäner. I ett enkelriktad förtroende mellan *domän A* och *domän B*kan användare i domän *A* komma åt resurser i *domän B*. Användare i *domän B* kan dock inte komma åt resurser i *domän A*.

Vissa enkelriktade förtroenden kan vara antingen icke-transitiva eller transitiva beroende på vilken typ av förtroende som skapas.

I ett tvåvägsförtroende litar *domän A* på *domän B* och domän *B* litar på *domän A*. Den här konfigurationen innebär att autentiseringsbegäranden kan skickas mellan de två domänerna i båda riktningarna. Vissa tvåvägsrelationer kan vara icke-transitiva eller transitiva beroende på vilken typ av förtroende som skapas.

Alla domänförtroende i en AD DS-skog är dubbelriktade, transitiva förtroenden. När en ny underordnad domän skapas skapas automatiskt ett dubbelriktad transitivt förtroende mellan den nya underordnade domänen och den överordnade domänen.

### <a name="transitive-and-non-transitive-trusts"></a>Transitiva och icke-transitiva truster

Transitivity avgör om ett förtroende kan utökas utanför de två domäner som det bildades med.

* Ett transitivt förtroende kan användas för att utöka förtroenderelationer med andra domäner.
* Ett icke-transitivt förtroende kan användas för att neka förtroenderelationer med andra domäner.

Varje gång du skapar en ny domän i en skog skapas automatiskt en dubbelriktad transitiv förtroenderelation mellan den nya domänen och dess överordnade domän. Om underordnade domäner läggs till i den nya domänen flödar förtroendesökvägen uppåt genom domänhierarkin som utökar den ursprungliga förtroendesökvägen som skapats mellan den nya domänen och dess överordnade domän. Transitiva förtroenderelationer flödar uppåt genom ett domänträd när det skapas, vilket skapar transitiva förtroenden mellan alla domäner i domänträdet.

Autentiseringsbegäranden följer dessa förtroendesökvägar, så konton från alla domäner i skogen kan autentiseras av någon annan domän i skogen. Med en enda inloggningsprocess kan konton med rätt behörighet komma åt resurser i alla domäner i skogen.

## <a name="forest-trusts"></a>Skogsförtroenden

Skogsförtroende hjälper dig att hantera en segmenterad AD DS-infrastruktur och stödja åtkomst till resurser och andra objekt i flera skogar. Skogsfonder är användbara för tjänsteleverantörer, företag som genomgår fusioner eller förvärv, samarbetsföretag extranät och företag som söker en lösning för administrativ autonomi.

Med hjälp av skogsförtroende kan du länka två olika skogar för att skapa en enkelriktad eller dubbelriktad transitiv förtroenderelation. Med ett skogsförtroende kan administratörer koppla samman två AD DS-skogar med en enda förtroenderelation för att ge en sömlös autentiserings- och auktoriseringsupplevelse över skogarna.

Ett skogsförtroende kan bara skapas mellan en skogsrotdomän i en skog och en skogsrotdomän i en annan skog. Skogsförtroende kan bara skapas mellan två skogar och kan inte implicit utökas till en tredje skog. Det här beteendet innebär att om ett skogsförtroende skapas mellan *Skog 1* och *Skog 2*och ett annat skogsförtroende skapas mellan *Skog 2* och *Skog 3*har *Skog 1* inte ett implicit förtroende med *Skog 3*.

Följande diagram visar två separata skogsförtroenderelationer mellan tre AD DS-skogar i en enda organisation.

![Diagram över relationer mellan skog och förtroenden inom en enda organisation](./media/concepts-forest-trust/forest-trusts.png)

Den här exempelkonfigurationen ger följande åtkomst:

* Användare i *Skog 2* kan komma åt resurser i alla domäner i *skog 1* eller *skog 3*
* Användare i *Skog 3* kan komma åt resurser i alla domäner i *Skog 2*
* Användare i *Skog 1* kan komma åt resurser i alla domäner i *Skog 2*

Den här konfigurationen tillåter inte användare i *Skog 1* att komma åt resurser i *Skog 3* eller vice versa. Om du vill att användare i både *Skog 1* och *Skog 3* ska kunna dela resurser måste ett dubbelriktad transitivt förtroende skapas mellan de två skogarna.

Om ett envägs skogsförtroende skapas mellan två skogar kan medlemmar i den betrodda skogen använda resurser som finns i den betrodda skogen. Förtroendet fungerar dock bara i en riktning.

Till exempel när ett enkelriktad, skogsförtroende skapas mellan *Skog 1* (den betrodda skogen) och *Skog 2* (den förtroendefulla skogen):

* Medlemmar i *Skog 1* kan komma åt resurser i *Skog 2*.
* Medlemmar i *Skog 2* kan inte komma åt resurser som finns i *Skog 1* med samma förtroende.

> [!IMPORTANT]
> Azure AD Domain Services resursskog stöder endast ett enkelriktat skogsförtroende till lokala Active Directory.

### <a name="forest-trust-requirements"></a>Krav på skogsförtroende

Innan du kan skapa ett skogsförtroende måste du kontrollera att du har rätt DNS-infrastruktur (Domain Name System) på plats. Skogsförtroende kan bara skapas när någon av följande DNS-konfigurationer är tillgänglig:

* En enda rot-DNS-server är rot-DNS-servern för båda skogens DNS-namnområden - rotzonen innehåller delegering för var och en av DNS-namnområdena och rottipsen för alla DNS-servrar inkluderar rot-DNS-servern.
* Där det inte finns någon delad rot-DNS-server och rot-DNS-servrarna för varje skog DNS-namnområde använder DNS-villkorsriktare för varje DNS-namnområde för att dirigera frågor om namn i det andra namnområdet.

    > [!IMPORTANT]
    > Azure AD Domain Services resursskog måste använda den här DNS-konfigurationen. Att vara värd för ett annat DNS-namnområde än DNS-namnområdet för resursskogen är inte en funktion i Azure AD Domain Services. Villkorade vidarebefordrare är rätt konfiguration.

* Om det inte finns någon delad rot-DNS-server och rot-DNS-servrarna för varje skogs-DNS-namnområde används konfigureras DNS-sekundära zoner i varje DNS-namnområde för att dirigera frågor om namn i det andra namnområdet.

Om du vill skapa ett skogsförtroende måste du vara medlem i gruppen Domänadministratörer (i skogsrotdomänen) eller gruppen Företagsadministratörer i Active Directory. Varje förtroende tilldelas ett lösenord som administratörerna i båda skogarna måste känna till. Medlemmar i Företagsadministratörer i båda skogarna kan skapa förtroenden i båda skogarna samtidigt och i det här scenariot genereras ett lösenord som är kryptografiskt slumpmässigt automatiskt och skrivs för båda skogarna.

Det utgående skogsförtroendet för Azure AD Domain Services skapas i Azure-portalen. Du skapar inte förtroendet manuellt med själva den hanterade domänen. Det inkommande skogsförtroendet måste konfigureras av en användare med de privilegier som tidigare noterats i den lokala Active Directory.

## <a name="trust-processes-and-interactions"></a>Förtroendeprocesser och interaktioner

Många transaktioner mellan domäner och mellan skogar är beroende av domän- eller skogsförtroende för att kunna utföra olika uppgifter. I det här avsnittet beskrivs de processer och interaktioner som uppstår när resurser används över förtroenden och autentiseringsreferenser utvärderas.

### <a name="overview-of-authentication-referral-processing"></a>Översikt över bearbetning av autentiseringsreferens

När en begäran om autentisering refereras till en domän måste domänkontrollanten i den domänen avgöra om det finns en förtroenderelation med den domän som begäran kommer från. Förtroendets riktning och om förtroendet är transitivt eller icke-transitivt måste också bestämmas innan den autentiserar användaren för åtkomst till resurser i domänen. Autentiseringsprocessen som sker mellan betrodda domäner varierar beroende på vilket autentiseringsprotokoll som används. Kerberos V5- och NTLM-protokollen bearbetar referenser för autentisering till en domän på ett annat sätt

### <a name="kerberos-v5-referral-processing"></a>Kerberos V5 remiss bearbetning

Kerberos V5-autentiseringsprotokollet är beroende av net-inloggningstjänsten på domänkontrollanter för klientautentisering och auktoriseringsinformation. Kerberos-protokollet ansluter till ett KDC (Key Distribution Center) och Active Directory-kontoarkivet för sessionsbiljetter.

Kerberos-protokollet använder också förtroenden för tgs -tjänster (Cross-realm ticket-granting services) och för att validera behörighetsattributcertifikat (PACs) över en säker kanal. Kerberos-protokollet utför autentisering över hela sfären endast med Kerberos-sfärer som inte är windows-varumärken, till exempel en MIT Kerberos-sfär och behöver inte interagera med tjänsten Net Logon.

Om klienten använder Kerberos V5 för autentisering begär den en biljett till servern i måldomänen från en domänkontrollant i dess kontodomän. Kerberos KDC fungerar som en betrodd mellanhand mellan klienten och servern och tillhandahåller en sessionsnyckel som gör det möjligt för de två parterna att autentisera varandra. Om måldomänen skiljer sig från den aktuella domänen följer KDC en logisk process för att avgöra om en autentiseringsbegäran kan refereras:

1. Är den aktuella domänen betrodd direkt av domänen för den server som begärs?
    * Om ja skickar du en hänvisning till den begärda domänen till klienten.
    * Om nej, gå till nästa steg.

2. Finns det en transitiv förtroenderelation mellan den aktuella domänen och nästa domän på förtroendesökvägen?
    * Om ja skickar du en hänvisning till nästa domän till klienten i förtroendesökvägen.
    * Om nej skickar du ett meddelande som nekats inloggning till klienten.

### <a name="ntlm-referral-processing"></a>BEARBETNING AV NTLM-hänvisningar

NTLM-autentiseringsprotokollet är beroende av net-inloggningstjänsten på domänkontrollanter för klientautentisering och auktoriseringsinformation. Det här protokollet autentiserar klienter som inte använder Kerberos-autentisering. NTLM använder förtroenden för att skicka autentiseringsbegäranden mellan domäner.

Om klienten använder NTLM för autentisering går den första begäran om autentisering direkt från klienten till resursservern i måldomänen. Den här servern skapar en utmaning som klienten svarar på. Servern skickar sedan användarens svar till en domänkontrollant i sin datorkontodomän. Den här domänkontrollanten kontrollerar användarkontot mot dess databas för säkerhetskonton.

Om kontot inte finns i databasen avgör domänkontrollanten om direktautentisering ska utföras, vidarebefordra begäran eller neka begäran med hjälp av följande logik:

1. Har den aktuella domänen en direkt förtroenderelation med användarens domän?
    * Om ja skickar domänkontrollanten klientens autentiseringsuppgifter till en domänkontrollant i användarens domän för direktautentisering.
    * Om nej, gå till nästa steg.

2. Har den aktuella domänen en transitiv förtroenderelation med användarens domän?
    * Om ja skickar du autentiseringsbegäran vidare till nästa domän i förtroendesökvägen. Den här domänkontrollanten upprepar processen genom att kontrollera användarens autentiseringsuppgifter mot sin egen databas för säkerhetskonton.
    * Om nej skickar du ett meddelande som nekats inloggning till klienten.

### <a name="kerberos-based-processing-of-authentication-requests-over-forest-trusts"></a>Kerberos-baserad bearbetning av autentiseringsbegäranden över skogsförtroenden

När två skogar är anslutna av ett skogsförtroende kan autentiseringsbegäranden som görs med Kerberos V5- eller NTLM-protokollen dirigeras mellan skogar för att ge åtkomst till resurser i båda skogarna.

När ett skogsförtroende upprättas samlar varje skog in alla betrodda namnområden i sin partnerskog och lagrar informationen i ett [betrott domänobjekt](#trusted-domain-object). Betrodda namnområden inkluderar domännamn, UPN-suffix (User Principal Name), SPN-suffix (Service Principal Name) och SID-namnområden (Security ID) som används i den andra skogen. TDO-objekt replikeras till den globala katalogen.

Innan autentiseringsprotokoll kan följa sökvägen till skogen måste resursdatorns huvudnamn (SPN) matchas till en plats i den andra skogen. Ett SPN kan vara något av följande:

* DNS-namnet på en värd.
* DNS-namnet på en domän.
* Det unika namnet på ett tjänstanslutningspunktsobjekt.

När en arbetsstation i en skog försöker komma åt data på en resursdator i en annan skog kontaktar Kerberos-autentiseringsprocessen domänkontrollanten för en tjänstbiljett till resursdatorns SPN. När domänkontrollanten frågar den globala katalogen och fastställer att SPN inte finns i samma skog som domänkontrollanten skickar domänkontrollanten en hänvisning för den överordnade domänen tillbaka till arbetsstationen. Då frågar arbetsstationen den överordnade domänen för tjänstbiljetten och fortsätter att följa referenskedjan tills den når domänen där resursen finns.

Följande diagram och steg ger en detaljerad beskrivning av Kerberos-autentiseringsprocessen som används när datorer som kör Windows försöker komma åt resurser från en dator som finns i en annan skog.

![Diagram över Kerberos-processen över ett skogsförtroende](media/concepts-forest-trust/kerberos-over-forest-trust-process.png)

1. *Användare1* loggar in på *Workstation1* med autentiseringsuppgifter från *europe.tailspintoys.com* domänen. Användaren försöker sedan komma åt en delad resurs på *FileServer1* som finns i *usa.wingtiptoys.com* skogen.

2. *Workstation1* kontaktar Kerberos KDC på en domänkontrollant i domänen *ChildDC1*och begär en tjänstbiljett för *SpN för FileServer1.*

3. *ChildDC1* hittar inte SPN i domändatabasen och frågar den globala katalogen för att se om några domäner i tailspintoys.com skogen innehåller det här SPN.ChildDC1 does not find the SPN in its domain database and queries the global catalog to see if any domains in the *tailspintoys.com* forest contain this SPN. Eftersom en global katalog är begränsad till sin egen skog hittas inte SPN.Because a global catalog is limited to its own forest, the SPN is not found.

    Den globala katalogen kontrollerar sedan sin databas efter information om alla skogsförtroende som har upprättats med dess skog. Om den hittas jämför den namnsuffixen i det betrodda domänobjektet (Forest Trust Trusted Domain Object) med suffixet för målsuffixet för att hitta en matchning. När en matchning har hittats ger den globala katalogen en routningstips tillbaka till *ChildDC1*.

    Routningstips hjälper till att rikta autentiseringsbegäranden mot målskogen. Tips används bara när alla traditionella autentiseringskanaler, till exempel lokal domänkontrollant och sedan global katalog, inte kan hitta ett SPN.

4. *ChildDC1* skickar en hänvisning för den överordnade domänen tillbaka till *Workstation1*.

5. *Workstation1* kontaktar en domänkontrollant i *ForestRootDC1* (dess överordnade domän) för en hänvisning till en domänkontrollant (*ForestRootDC2*) i skogsrotdomänen för *wingtiptoys.com* skogen.

6. *Workstation1* kontaktar *ForestRootDC2* i *skogen wingtiptoys.com* för en servicebiljett till den begärda tjänsten.

7. *ForestRootDC2* kontaktar sin globala katalog för att hitta SPN och den globala katalogen hittar en matchning för SPN och skickar tillbaka den till *ForestRootDC2*.

8. *ForestRootDC2* skickar sedan remissen till *usa.wingtiptoys.com* tillbaka till *Workstation1*.

9. *Workstation1* kontaktar KDC på *ChildDC2* och förhandlar om biljetten för *User1* för att få tillgång till *FileServer1*.

10. När *Workstation1* har en tjänstbiljett skickar den servicebiljetten till *FileServer1*, som läser *User1:s*säkerhetsreferenser och konstruerar en åtkomsttoken därefter.

## <a name="trusted-domain-object"></a>Objekt för betrodd domän

Varje domän- eller skogsförtroende inom en organisation representeras av ett TDO (Trusted Domain Object) som lagras i *systembehållaren* i domänen.

### <a name="tdo-contents"></a>TDO-innehåll

Informationen i en TDO varierar beroende på om en TDO har skapats av ett domänförtroende eller av ett skogsförtroende.

När ett domänförtroende skapas representeras attribut som DNS-domännamn, domän-SID, förtroendetyp, förtroendetransitivitet och det ömsesidiga domännamnet i TDO. Skogsförtroende-TDOs lagrar ytterligare attribut för att identifiera alla betrodda namnområden från partnerskogen. Dessa attribut omfattar domännamn i domänträd, UPN-suffix (User Principal Name), SPN-suffix (Service Principal Name) och SID-namnområden (Security ID).

Eftersom förtroenden lagras i Active Directory som TDOs har alla domäner i en skog kunskap om förtroenderelationer som finns i hela skogen. På samma sätt, när två eller flera skogar sammanfogas genom skogsförtroende, har skogsrotdomänerna i varje skog kunskap om förtroenderelationer som finns i alla domäner i betrodda skogar.

### <a name="tdo-password-changes"></a>TDO lösenordsändringar

Båda domänerna i en förtroenderelation delar ett lösenord som lagras i TDO-objektet i Active Directory. Som en del av kontounderhållsprocessen ändrar den betrodda domänkontrollanten lösenordet som lagras i TDO var 30:e dag. Eftersom alla tvåvägstruster faktiskt är två enkelriktade truster som går i motsatta riktningar, sker processen två gånger för tvåvägsförtroende.

Ett förtroende har en tillitsfull och en betrodd sida. På den betrodda sidan kan alla skrivbara domänkontrollanter användas för processen. På förtroendesidan utför PDC-emulatorn lösenordsändringen.

Om du vill ändra ett lösenord slutför domänkontrollanterna följande process:

1. Pdc-emulatorn (Primary Domain Controller) i den betrodda domänen skapar ett nytt lösenord. En domänkontrollant i den betrodda domänen initierar aldrig lösenordsändringen. Det initieras alltid av den betrodda domänen PDC-emulatorn.

2. PDC-emulatorn i den betrodda domänen anger *OldPassword-fältet* för TDO-objektet till det aktuella *NewPassword-fältet.*

3. PDC-emulatorn i den betrodda domänen anger *NewPassword-fältet* för TDO-objektet till det nya lösenordet. Om du behåller en kopia av det tidigare lösenordet kan du återgå till det gamla lösenordet om domänkontrollanten i den betrodda domänen inte tar emot ändringen eller om ändringen inte replikeras innan en begäran görs som använder det nya förtroendelösenordet.

4. PDC-emulatorn i den betrodda domänen gör ett fjärranrop till en domänkontrollant i den betrodda domänen och ber den att ange lösenordet på förtroendekontot till det nya lösenordet.

5. Domänkontrollanten i den betrodda domänen ändrar förtroendelösenordet till det nya lösenordet.

6. På varje sida av förtroendet replikeras uppdateringarna till de andra domänkontrollanterna i domänen. I domänen för förtroende utlöser ändringen en brådskande replikering av det betrodda domänobjektet.

Lösenordet har nu ändrats på båda domänkontrollanterna. Normal replikering distribuerar TDO-objekten till de andra domänkontrollanterna i domänen. Det är dock möjligt för domänkontrollanten i domänen att lita på att ändra lösenordet utan att uppdatera en domänkontrollant i den betrodda domänen. Det här scenariot kan bero på att en säker kanal, som krävs för att bearbeta lösenordsändringen, inte kunde upprättas. Det är också möjligt att domänkontrollanten i den betrodda domänen kanske inte är tillgänglig någon gång under processen och kanske inte får det uppdaterade lösenordet.

För att hantera situationer där lösenordsändringen inte kommuniceras ändrar domänkontrollanten i domänen förtroende aldrig det nya lösenordet om det inte har autentiserats (konfigurerat en säker kanal) med det nya lösenordet. Det här problemet är anledningen till att både gamla och nya lösenord hålls i TDO-objektet för den betrodda domänen.

En lösenordsändring slutförs inte förrän autentiseringen med lösenordet lyckas. Det gamla, lagrade lösenordet kan användas över den säkrade kanalen tills domänkontrollanten i den betrodda domänen tar emot det nya lösenordet, vilket möjliggör oavbruten tjänst.

Om autentiseringen med det nya lösenordet misslyckas på grund av att lösenordet är ogiltigt försöker den betrodda domänkontrollanten autentisera med det gamla lösenordet. Om den autentiserar med det gamla lösenordet återupptas lösenordsändringsprocessen inom 15 minuter.

Förtroende lösenord uppdateringar måste replikera till domänkontrollanter på båda sidor av förtroende inom 30 dagar. Om förtroendelösenordet ändras efter 30 dagar och en domänkontrollant då bara har N-2-lösenordet, kan det inte använda förtroendet från förtroendesidan och kan inte skapa en säker kanal på den betrodda sidan.

## <a name="network-ports-used-by-trusts"></a>Nätverksportar som används av förtroenden

Eftersom förtroenden måste distribueras över olika nätverksgränser kan de behöva sträcka sig över en eller flera brandväggar. När så är fallet kan du antingen tunnel lita på trafik över en brandvägg eller öppna specifika portar i brandväggen så att trafiken kan passera.

> [!IMPORTANT]
> Active Directory Domain Services stöder inte att Active Directory RPC-trafik begränsas till specifika portar.

Läs avsnittet **Windows Server 2008 och senare versioner** i Microsoft Support-artikeln Så här [konfigurerar du en brandvägg för Active Directory-domäner och -förtroenden för](https://support.microsoft.com/help/179442/how-to-configure-a-firewall-for-domains-and-trusts) att lära dig mer om de portar som behövs för ett skogsförtroende.

## <a name="supporting-services-and-tools"></a>Stödtjänster och verktyg

För att stödja förtroenden och autentisering används vissa ytterligare funktioner och hanteringsverktyg.

### <a name="net-logon"></a>Netto inloggning

Net Logon-tjänsten underhåller en säker kanal från en Windows-baserad dator till en domänkontrollant. Det används också i följande förtroenderelaterade processer:

* Förtroendeinställningar och hantering - Net Logon hjälper till att upprätthålla förtroendelösenord, samlar in förtroendeinformation och verifierar förtroenden genom att interagera med LSA-processen och TDO.

    För Skogsförtroenden innehåller förtroendeinformationen posten Forest Trust Information (*FTInfo),* som innehåller den uppsättning namnområden som en betrodd skog påstår sig hantera, kommenterade med ett fält som anger om varje anspråk är betrodd av den betrodda skogen.

* Autentisering – Tillhandahåller användarautentiseringsuppgifter över en säker kanal till en domänkontrollant och returnerar domän-SID och användarrättigheter för användaren.

* Domänkontrollantplats – Hjälper till att hitta eller hitta domänkontrollanter i en domän eller mellan domäner.

* Direktvalidering – Autentiseringsuppgifter för användare i andra domäner bearbetas av Net Logon. När en betrodd domän behöver verifiera en användares identitet skickar den användarens autentiseringsuppgifter via Net Logon till den betrodda domänen för verifiering.

* PAC-verifiering (Privilege Attribute Certificate) – När en server som använder Kerberos-protokollet för autentisering behöver verifiera PAC i en tjänstbiljett skickar den PAC över den säkra kanalen till domänkontrollanten för verifiering.

### <a name="local-security-authority"></a>Lokal säkerhetskontroll

Den lokala säkerhetsmyndigheten (LSA) är ett skyddat delsystem som upprätthåller information om alla aspekter av lokal säkerhet i ett system. LSA, som kallas lokal säkerhetsprincip, tillhandahåller tillsammans olika tjänster för översättning mellan namn och identifierare.

LSA-säkerhetsundersystemet tillhandahåller tjänster i både kernel-läge och användarläge för att validera åtkomst till objekt, kontrollera användarbehörighet och generera granskningsmeddelanden. LSA ansvarar för att kontrollera giltigheten av alla sessionsbiljetter som presenteras av tjänster i betrodda eller ej betrodda domäner.

### <a name="management-tools"></a>Hanteringsverktyg

Administratörer kan använda *Active Directory-domäner och förtroenden*, *Netdom* och *Nltest* för att visa, skapa, ta bort eller ändra förtroenden.

* *Active Directory-domäner och förtroenden* är Microsoft Management Console (MMC) som används för att administrera domänförtroende, domän- och skogsfunktionsnivåer och användarens huvudnamnsuffix.
* Kommandoradsverktygen *Netdom* och *Nltest* kan användas för att hitta, visa, skapa och hantera förtroenden. Dessa verktyg kommunicerar direkt med LSA-behörigheten på en domänkontrollant.

## <a name="next-steps"></a>Nästa steg

Mer information om resursskogar finns [i Hur fungerar skogsförtroende i Azure AD DS?][concepts-trust]

Information om hur du skapar en Azure AD DS-hanterad domän med en resursskog finns i [Skapa och konfigurera en Azure AD DS-hanterad domän][tutorial-create-advanced]. Du kan sedan [skapa ett utgående skogsförtroende till en lokal domän (förhandsversion).][create-forest-trust]

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
