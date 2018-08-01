---
title: 'Azure AD Connect: Versionshistorik | Microsoft Docs'
description: Den här artikeln visar en lista över alla versioner av Azure AD Connect och Azure AD Sync
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 6ca32d51a52cf636b1c41667e20872cfe49fa7e2
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390161"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: Versionshistorik
Azure Active Directory (Azure AD)-teamet uppdaterar regelbundet Azure AD Connect med nya funktioner. Inte alla tillägg gäller för alla målgrupper.


Den här artikeln är utformad för att hålla reda på de versioner som har lanserats och förstå vad ändringarna visas i den senaste versionen.

Den här tabellen är en lista över närliggande ämnen:

Avsnitt |  Information
--------- | --------- |
Steg för att uppgradera från Azure AD Connect | Olika metoder för att [uppgradera från en tidigare version till senast](active-directory-aadconnect-upgrade-previous-version.md) Azure AD Connect-versionen.
Nödvändiga behörigheter | Behörigheter som krävs för att tillämpa en uppdatering, se [konton och behörigheter](./active-directory-aadconnect-accounts-permissions.md#upgrade).

Ladda ned | [Hämta Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>Versionsstatus

7/20/2018: släppts för automatisk uppgradering. Versionen för att ladda ned följer inom kort.

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

- Ping federera-integrering i Azure AD Connect är nu tillgängligt för allmän tillgänglighet. [Mer information om hur du federerade Azure AD med Ping federera](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#federation-with-pingfederate)
- Säkerhetskopiering av Azure AD-förtroendet i AD FS skapar nu i Azure AD Connect varje gång en uppdatering görs och lagrar den i en separat fil för enkel återställning om det behövs. [Lär dig mer om nya funktioner och Azure AD litar management i Azure AD Connect ](https://aka.ms/fedtrustinaadconnect).
- Nya verktyg för felsökning kan du felsöka ändrar primära e-postadress och dölja konto från den globala adresslistan
- Azure AD Connect har uppdaterats för att inkludera den senaste SQL Server 2012 Native Client
- När du växlar användare logga in till Hashsynkronisering för lösenord eller direktautentisering i aktiviteten ”ändra användarinloggning” är kryssrutan sömlös enkel inloggning aktiverat som standard.
- Stöd har lagts till för Windows Server Essentials 2019
- Azure AD Connect Health-agenten har uppdaterats till den senaste versionen 3.1.7.0
- Vid en uppgradering om installationsprogrammet identifierar ändringar av Synkroniseringsregler standard ombeds administratören med en varning innan du skriver över de ändrade reglerna. Detta gör att användarna att vidta åtgärder och återuppta senare. Gamla beteendet: Om det fanns ingen ändrade out-of-box-regel sedan manuell uppgradering skriver över dessa regler utan att ge alla varningar för användaren och synkroniseringsschemaläggaren inaktiverades utan att meddela användaren. Nya beteendet: Användaren uppmanas med varning innan du skriver över de ändrade out-of-box Synkroniseringsregler. Användaren har möjlighet att uppgraderingen och återupptas senare efter att vidta korrigerande åtgärder.
- Ge en bättre hantering av FIPS-kompatibilitetsproblem, vilket ger ett felmeddelande för genereringen av hashvärden för MD5 i en FIPS-kompatibel miljö och en länk till dokumentationen som tillhandahåller en lösning för det här problemet.
- Användargränssnittet uppdatera för att förbättra federation uppgifter i guiden, som nu finns under en separat sub-grupp för federation. 
- Alla ytterligare uppgifter för federation är nu grupperade under en enda undermeny för enkel användning.
- En ny gjort om ADSyncConfig Posh modulen (AdSyncConfig.psm1) med den nya AD-behörigheter funktioner flyttas från den gamla ADSyncPrep.psm1 (som kan vara inaktuell snart)

### <a name="fixed-issues"></a>Åtgärdade problem 

- Ett fel har åtgärdats där AAD Connect-servern skulle visa hög CPU-användning efter uppgraderingen till .net 4.7.2
- Ett fel som skulle periodvis producerar ett felmeddelande visas för ett automatiskt löst SQL deadlock problem har åtgärdats
- Åtgärdat problem med flera för Regelredigeraren synkronisering och synkronisering av Service Manager  
- Ett fel har åtgärdats där Azure AD Connect inte kan få inställningsinformation för registret
- En bugg som skapade problem när användaren går framåt/tillbaka i guiden
- Ett fel för att förhindra att ett fel som sker på grund av felaktig multi tråd vid i guiden har åtgärdats
- När gruppen Synkroniseringsfiltrering sidan påträffar ett LDAP-fel när löses säkerhetsgrupper, returnerar undantag med fullständig exakthet nu i Azure AD Connect.  Den grundläggande orsaken för hänvisning undantaget är fortfarande okänd och kommer att åtgärdas av ett annat fel.
-  Ett fel har åtgärdats där behörigheter för STK och NGC nycklar (msDS-KeyCredentialLink attribut för användare/enhet objekt för WHfB) har inte ställts in korrekt.     
- Ett fel har åtgärdats där ”Set-ADSyncRestrictedPermissions' anropades inte korrekt
-  Lägger till stöd för beviljar på tillbakaskrivning av grupp i installationsguiden för Aadconnects behörighet
- När du ändrar inloggningen metod från Lösenordshashsynkronisering till AD FS, har lösenordets Hash-synkronisering inte inaktiverats.
- Har lagts till verifiering för IPv6-adresser i AD FS-konfiguration
- Uppdatera meddelande om att det finns en befintlig konfiguration.
- Tillbakaskrivning av enhet inte kan upptäcka behållare i ej betrodd skog. Detta har uppdaterats för att ge ett bättre felmeddelande visas och en länk till motsvarande dokumentation
- Avmarkera en Organisationsenhet och synkronisering/tillbakaskrivning av motsvarar att OU ger ett allmänt synkroniseringsfel. Detta har ändrats för att skapa ett mer begriplig felmeddelande.

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>Versionsstatus

5/14/2018: släpps för automatisk uppgradering och nedladdning.

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

Nya funktioner och förbättringar

- Den här versionen innehåller den offentliga förhandsversionen av integreringen av PingFederate i Azure AD Connect. Den här versionen kan kunder kan enkelt och på ett tillförlitligt sätt konfigurera deras Azure Active Directory-miljö för att använda PingFederate som deras federationsleverantör. Om du vill veta mer om hur du använder den här nya funktionen kan du besöka vår [onlinedokumentation](active-directory-aadconnect-user-signin.md#federation-with-pingfederate). 
- Uppdatera ett Azure AD Connect guiden felsökning för där den nu analyserar fler fel scenario, till exempel länkade postlådor och dynamiska grupper i AD. Läs mer om verktyget felsökning [här](active-directory-aadconnect-troubleshoot-objectsync.md).
- Konfiguration för tillbakaskrivning av enhet hanteras nu endast i den Azure AD Connect-guiden.
- En ny PowerShell-modul kallas ADSyncTools.psm1 har lagts till som kan användas för att felsöka anslutningsproblem med SQL och olika andra verktyg för felsökning. Läs mer om modulen ADSyncTools [här](active-directory-aadconnect-tshoot-sql-connectivity.md). 
- En ny ytterligare uppgift ”konfigurera Enhetsalternativ” har lagts till. Du kan använda för att konfigurera följande två åtgärder: 
    -   **Hybrid Azure AD-anslutning**: om din miljö har en lokal AD-fotavtryck och du även vill utnyttja funktionerna i Azure Active Directory, du kan implementera hybrid Azure AD-anslutna enheter. Enheter är enheter som är både, ansluten till din lokala Active Directory och Azure Active Directory.
    -   **Tillbakaskrivning av enhet**: tillbakaskrivning av enhet som används för att aktivera villkorlig åtkomst baserat på enheter till AD FS (2012 R2 eller senare) skyddade enheter

   >[!NOTE] 
   > - Möjlighet att aktivera tillbakaskrivning av enheter från anpassa synkroniseringsalternativ nedtonade. 
   > -  PowerShell-modulen för ADPrep är föråldrad med den här versionen.



### <a name="fixed-issues"></a>Åtgärdade problem 

- Den här versionen uppdaterar SQL Server Express-installationen till SQL Server 2012 SP4, som bland annat innehåller korrigeringar för flera säkerhetsrisker.  Se [här](https://support.microsoft.com/en-ca/help/4018073/sql-server-2012-service-pack-4-release-information) för mer information om SQL Server 2012 SP4.
- Synkronisera Regelbearbetning: utgående anslutning till Synkroniseringsregler utan delta villkor ska vara ej tillämpade om överordnad synkronisering regeln inte längre är tillämplig
- Flera hjälpmedel korrigeringar har tillämpats på Synchronization Service Manager-UI och Regelredigeraren synkronisering
- Azure AD Connect-guiden: Fel vid skapande av AD-anslutningskontot när Azure AD Connect finns i en arbetsgrupp
- Azure AD Connect-guiden: På den Azure AD-inloggningssida visas kryssrutan verifiering när det finns någon matchning av datatyp i AD-domäner och domäner för Azure AD-verifierad
- Automatisk Klientuppgradering PowerShell åtgärda ska ställas in automatiskt uppgraderingstillståndet korrekt i vissa fall när försök gjordes att automatiskt uppgradera.
- Azure AD Connect-guiden: Uppdatera telemetri för att samla in information som tidigare saknas
- Azure AD Connect-guiden: Följande ändringar har gjorts när du använder den **ändra användarinloggning** uppgift att växla från AD FS till direktautentisering:
    - Autentiseringsagenten för direktautentisering är installerad på Azure AD Connect-servern och direktautentisering-funktionen är aktiverad, innan det konvertera domänerna från federerad som hanteras.
    - Användare konverteras inte längre från federerad som hanteras. Endast domäner konverteras.
- Azure AD Connect-guiden: AD FS med flera domän Regex är inte korrekt när användare UPN har ' specialtecken Regex uppdatering som stöder specialtecken
- Azure AD Connect-guiden: Ta bort falska ”konfigurera källfästpunktattribut”-meddelande när ingen ändring 
- Azure AD Connect-guiden: AD FS stöd för scenariot med dubbla federation
- Azure AD Connect-guiden: AD FS anspråk uppdateras inte för har lagts till domänen vid konvertering av en hanterad domän till federerad
- Azure AD Connect-guiden: Under identifieringen av installerade paket vi hitta inaktuella Dirsync/Azure AD-synkronisering/Azure AD Connect relaterade produkter. Vi kommer nu att försöka avinstallera inaktuella produkter.
- Azure AD Connect-guiden: Rätt fel meddelande mappning när installationen av genomströmning autentiseringsagent misslyckas
- Azure AD Connect-guiden: Bort ”konfigurationsbehållare” från domänen OU-filtrering sidan
- Installera Synkroniseringsmotorn: ta bort onödiga äldre logik som ibland inte från Synkroniseringsmotorn installera msi
- Azure AD Connect-guiden: Hjälptext åtgärda popup-fönstret på sidan för valfria funktioner för synkronisering av lösenords-Hash
- Synkronisera motorn för körning: åtgärda scenariot där en CS-objekt har en importerade delete och Synkroniseringsregler försöker etablera om objektet.
- Synkronisera motorn för körning: lägga till hjälp länk för Online anslutning felsökningsguide i händelseloggen för ett importfel
- Synkronisera motorn för körning: minskad minnesanvändning av Sync Scheduler vid uppräkning av kopplingar
- Azure AD Connect-guiden: Åtgärda ett problem som löser en anpassad synkroniseringstjänstkontot som har inga AD läsprivilegier
- Azure AD Connect-guiden: Förbättra loggning av domän och Organisationsenhet filtrering val
- Azure AD Connect-guiden: Lägg till AD FS standard anspråk till federationsförtroende som skapats för MFA-scenariot
- Azure AD Connect-guiden: Distribuera AD FS-WAP: lägga till servern inte kan använda nya certifikat
- Azure AD Connect-guiden: DSSO undantag när onPremCredentials inte initierats för en domän 
- Företrädesvis flödar AD-attribut för unikt namn från Active användarobjektet.
- Fast ett rent kosmetiskt programfel har prioriteten för den första regeln för OOB-synkronisering har angetts till 99 i stället för 100



## <a name="117510"></a>1.1.751.0
Status för 4/12/2018: lanserats för nedladdning enbart

>[!NOTE]
>Den här versionen är en snabbkorrigering för Azure AD Connect

### <a name="azure-ad-connect-sync"></a>Azure AD Connect-synkronisering
#### <a name="fixed-issues"></a>Åtgärdade problem
Korrigera ett problem har instansen för Azure-automatisk identifiering för Kina klienter Ibland misslyckas.  

### <a name="ad-fs-management"></a>AD FS-hantering
#### <a name="fixed-issues"></a>Åtgärdade problem

Det uppstod ett problem i konfigurationen omprövningslogiken som skulle resultera i en ArgumentException om ”ett objekt med samma nyckel har redan lagts till”.  Detta innebär att alla försök åtgärder att misslyckas.

## <a name="117500"></a>1.1.750.0
Status 3/22/2018: släpps för automatiska uppgraderingar och nedladdning.
>[!NOTE]
>När uppgraderingen till den nya versionen utlöses automatiskt en fullständig synkronisering och fullständig import för Azure AD-anslutningsappen och en fullständig synkronisering för AD-koppling. Kontrollera att du har vidtagit nödvändiga åtgärder för att stödja detta eller håll om hur du uppgraderar förrän du har hittat en lämplig tidpunkt att göra det eftersom det kan ta en stund, beroende på storleken på din Azure AD Connect-miljö.

>[!NOTE]
>”AutoUpgrade funktioner har felaktigt inaktiverats för vissa klienter som distribueras versioner senare än 1.1.524.0. För att säkerställa att din Azure AD Connect-instans är fortfarande berättigade till AutoUpgrade, kör du följande PowerShell-cmdlet: ”Set-ADSyncAutoUpgrade - AutoupGradeState aktiverad”


### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Åtgärdade problem

* Cmdlet Set-ADSyncAutoUpgrade skulle tidigare blockera Autoupgrade om automatisk Klientuppgradering status är inställd på pausad. Den här funktionen har ändrats så att den inte blockerar AutoUpgrade i framtida versioner.
* Ändra den **användarinloggning** sidan alternativet ”Lösenordssynkronisering” till ”Lösenordshashsynkronisering”.  Azure AD Connect synkroniserar lösenords-hash, inte lösenord, så att det överensstämmer med vad faktiskt inträffar.  Mer information finns i [implementera lösenordshashsynkronisering med Azure AD Connect-synkronisering](active-directory-aadconnectsync-implement-password-hash-synchronization.md)

## <a name="117490"></a>1.1.749.0
Status: Frigöras för att välja kunder

>[!NOTE]
>När uppgraderingen till den nya versionen utlöses automatiskt en fullständig synkronisering och fullständig import för Azure AD-anslutningsappen och en fullständig synkronisering för AD-koppling. Eftersom det kan ta en stund, beroende på storleken på din Azure AD Connect-miljö, se till att du har vidtagit nödvändiga åtgärder för att stödja detta eller håll om hur du uppgraderar förrän du har hittat en lämplig tidpunkt att göra detta.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Åtgärdade problem
* Åtgärda tidsinställning fönstret på bakgrundsaktiviteter för Partitionsfiltrering sidan när du växlar till nästa sida.

* Ett fel som orsakade åtkomstfel när den anpassade åtgärden ConfigDB har åtgärdats.

* En bugg att komma tillrätta med tidsgräns för SQL-anslutning.

* Ett fel har åtgärdats där kontrollen av förutsättningar misslyckades i certifikat med SAN-jokertecken.

* Ett fel som orsakar miiserver.exe kraschar vid en export för Azure AD-koppling har åtgärdats.

* En bugg som felaktiga lösenord försök loggas på Domänkontrollant när du kör Azure AD Connect-guiden för att ändra konfigurationen.


#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

* Lägger till inställningar för allmänna Dataskyddsförordningen (GDPR).  Mer information finns i artikeln [här](active-directory-aadconnect-gdpr.md).

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* programtelemetri - administratören kan växla den här klassen av data på/av när du vill

* Azure AD Health-data – administratören måste besök health-portalen för att styra inställningar för deras hälsotillstånd.
   När tjänsten principen har ändrats, läser agenterna och använda den.

* Har lagts till enheten återskrivning konfigurationsåtgärder och en förloppsindikator för att initiera dialogrutan

* Förbättrad allmänna diagnostik HTML-rapport och en fullständig datainsamling i en ZIP-Text / HTML-rapport

* Förbättrad tillförlitlighet för automatisk uppgradering och har lagts till ytterligare telemetri för att se till att du kan kontrollera hälsotillståndet för servern

* Begränsa åtkomstbehörighet till Privilegierade konton för AD-anslutningskontot

  * För nya installationer kommer guiden att begränsa de behörigheter som Privilegierade konton har för MSOL-kontot när du har skapat MSOL-konto.

Ändringarna tar hand om följande:
1. Express installationer
2. Anpassade installationer med Skapa automatiskt konto
3. Ändra installationsprogrammet så att det ingen krävs SA privilegiet ren installation av Azure AD Connect

* Lägga till ett nytt verktyg för att felsöka synkroniseringsproblem för ett specifikt objekt. Den finns under ”Felsök synkronisering av objektet' alternativet för Azure AD Connect guiden Felsöka ytterligare-uppgiften. Verktyget kontrollerar för närvarande för följande:

  * UserPrincipalName matchningsfel mellan synkroniserade användarobjektet och användarkontot i Azure AD-klient.
  * Om objektet är filtrerad från synkronisering på grund av filtrering av domän
  * Om objektet är filtrerad från synkronisering på grund av organisationsenhet (OU) filtrering

* Lägga till ett nytt verktyg för att synkronisera den aktuella lösenords-hash som lagras i en lokal Active Directory för ett visst användarkonto.

Verktyget kräver inte en lösenordsändring. Den finns under ”Felsök synkronisering av Lösenordshash' alternativet för Azure AD Connect guiden Felsöka ytterligare-uppgiften.






## <a name="116540"></a>1.1.654.0
Status: 12: e December 2017

>[!NOTE]
>Den här versionen är en rekommenderad relaterade snabbkorrigering för Azure AD Connect

### <a name="azure-ad-connect"></a>Azure AD Connect
En förbättring har lagts till Azure AD Connect version 1.1.654.0 (och efter) så att behörigheten rekommenderade ändringar som beskrivs under avsnittet [låsa åtkomsten till AD DS-kontot](#lock) tillämpas automatiskt när Azure AD Ansluta skapar AD DS-kontot. 

- När du konfigurerar Azure AD Connect, kan installera administratören antingen ange ett befintligt AD DS-konto eller låta Azure AD Connect automatiskt skapa kontot. Ändrade behörigheter tillämpas automatiskt på AD DS-kontot som skapas under installationen av Azure AD Connect. De tillämpas inte på befintliga AD DS-konto som tillhandahålls av administratören installera.
- För kunder som har uppgraderat från en äldre version av Azure AD Connect till 1.1.654.0 (eller efter) behörigheten tillämpas ändringar inte retroaktivt på befintliga AD DS-konton som skapats före uppgraderingen. De kommer endast tillämpas på nya AD DS-konton som skapats efter uppgraderingen. Detta inträffar när du lägger till nya AD-skogar som ska synkroniseras till Azure AD.

>[!NOTE]
>Den här versionen tar endast bort sårbarheten för nya installationer av Azure AD Connect där kontot skapas genom installationsprocessen. För befintliga installationer, eller i fall där du skapar kontot själv, bör du kontrollera att problemet inte finns.

#### <a name="lock"></a> Låsa åtkomsten till AD DS-kontot
Låsa åtkomsten till AD DS-kontot genom att implementera följande behörighetsändringar i lokalt AD:  

*   Inaktivera arv på det angivna-objektet
*   Ta bort alla åtkomstkontrollposter på specifika objekt, utom åtkomstkontrollposter som är specifika för SJÄLVBETJÄNINGSPORTALEN. Vi vill hålla standardbehörigheterna intakt när det gäller att själv.
*   Tilldela specifika behörigheter:

Typ     | Namn                          | Access               | Gäller
---------|-------------------------------|----------------------|--------------|
Tillåt    | SYSTEM                        | Fullständig behörighet         | Det här objektet  |
Tillåt    | Företagsadministratörer             | Fullständig behörighet         | Det här objektet  |
Tillåt    | Domänadministratörer                 | Fullständig behörighet         | Det här objektet  |
Tillåt    | Administratörer                | Fullständig behörighet         | Det här objektet  |
Tillåt    | Företagets domänkontrollanter | Lista innehåll        | Det här objektet  |
Tillåt    | Företagets domänkontrollanter | Läsa alla egenskaper  | Det här objektet  |
Tillåt    | Företagets domänkontrollanter | Läsbehörighet     | Det här objektet  |
Tillåt    | Autentiserade användare           | Lista innehåll        | Det här objektet  |
Tillåt    | Autentiserade användare           | Läsa alla egenskaper  | Det här objektet  |
Tillåt    | Autentiserade användare           | Läsbehörighet     | Det här objektet  |

Att öka inställningarna för AD DS-konto som du kan köra [den här PowerShell.skript](https://gallery.technet.microsoft.com/Prepare-Active-Directory-ef20d978). PowerShell-skriptet tilldelar behörigheter som nämns ovan till AD DS-kontot.

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>PowerShell-skript för att öka ett befintligt tjänstkonto

Du använder PowerShell-skript för att tillämpa de här inställningarna till en befintlig AD DS-konto (ether tillhandahålls av din organisation eller som skapats i en tidigare installation av Azure AD Connect, ladda ned skriptet från den angivna länken ovan.

##### <a name="usage"></a>Användning:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Var 

**$ObjectDN** = Active Directory-konto vars behörigheter måste höjas.

**$Credential** = administratörsautentiseringsuppgifter som har rätt behörighet för att begränsa behörigheten för kontot $ObjectDN. Dessa privilegier hålls vanligtvis av Enterprise- eller domänadministratören. Det fullständigt kvalificerade domännamnet för administratörskontot Undvik att använda lookup kontofel. Exempel: contoso.com\admin.

>[!NOTE] 
>$credential. Användarnamnet måste vara i formatet för FQDN\username. Exempel: contoso.com\admin 

##### <a name="example"></a>Exempel:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>Denna säkerhetsrisk används för att få obehörig åtkomst?

Om du vill se om problemet har använts för att kompromettera din Azure AD återställa Connect-konfigurationen bör du kontrollera att det sista lösenordet datum för kontot.  Om tidsstämpeln i oväntat, ytterligare undersökningar, via i händelseloggen för att återställa event som lösenord, bör göras.

Mer information finns i [Microsoft Security Advisory 4056318](https://technet.microsoft.com/library/security/4056318)

## <a name="116490"></a>1.1.649.0
Status: Oktober 27 2017

>[!NOTE]
>Den här versionen är inte tillgängligt för kunder via funktionen Azure AD Connect automatiskt uppgradera.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Problem har åtgärdats
* Fasta ett version kompatibilitetsproblem mellan Azure AD Connect och Azure AD Connect Health Agent (för sync). Det här problemet påverkar kunder som utför Azure AD Connect uppgradering till version 1.1.647.0, men för närvarande har Hälsoagenten version 3.0.127.0. Efter uppgraderingen måste skicka Hälsoagenten inte längre hälsotillståndsdata om Azure AD Connect-synkroniseringstjänsten till Azure AD Health-tjänsten. Med den här snabbkorrigeringen installeras Hälsoagenten version 3.0.129.0 under Azure AD Connect-uppgradering. Hälsoagenten version 3.0.129.0 saknar kompatibilitetsproblem med Azure AD Connect version 1.1.649.0.


## <a name="116470"></a>1.1.647.0
Status: Oktober 19-2017

> [!IMPORTANT]
> Det finns ett känt kompatibilitetsproblem mellan Azure AD Connect version 1.1.647.0 och Azure AD Connect Health Agent (för sync) version 3.0.127.0. Det här problemet förhindrar att Health-agenten skickar hälsotillståndsdata om den Azure AD Connect-synkroniseringstjänsten (inklusive objektet synkroniseringsfel och körningshistorik för data) till Azure AD Health-tjänsten. Innan du uppgraderar din Azure AD Connect-distribution till version 1.1.647.0 manuellt kontrollera den aktuella versionen av Azure AD Connect Health Agent installerad på din Azure AD Connect-servern. Du kan göra det genom att gå till *Kontrollpanelen → Lägg till/ta bort program* och leta efter application *Microsoft Azure AD Connect Health Agent för Sync*. Om versionen är 3.0.127.0, rekommenderar vi att du väntar på nästa Azure AD Connect-version ska vara tillgängliga innan uppgraderingen. Om Hälsoagenten-version inte 3.0.127.0, är det OK att fortsätta med uppgraderingen manuell, på plats. Observera att det här problemet inte påverkar swing-uppgradering eller kunder som gör ny installation av Azure AD Connect.
>
>

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Åtgärdade problem
* Åtgärdat ett problem med den *ändra användarinloggning* aktivitet i Azure AD Connect-guiden:

  * Problemet uppstår när du har en befintlig Azure AD Connect-distribution med Lösenordssynkronisering **aktiverat**, och du försöker ange metoden användare logga in som *direktautentisering*. Innan ändringen tillämpas i guiden felaktigt visar den ”*inaktivera lösenordssynkronisering*” fråga. Lösenordssynkronisering förblir aktiv efter att ändringen gäller. Med den här snabbkorrigeringen visas i guiden inte längre prompten.

  * Enligt design, guiden inaktiverar inte Lösenordssynkronisering när du uppdaterar användarens inloggningsmetod med den *ändra användarinloggning* uppgift. Det här är att undvika avbrott för kunder som vill behålla Lösenordssynkronisering, även om de aktiverar direktautentisering eller federation som primär användare logga in metod.
  
  * Om du vill inaktivera lösenordssynkronisering när du har uppdaterat inloggningsmetod för användare, måste du köra den *anpassa synkroniseringskonfigurationen* uppgift i guiden. När du navigerar till den *valfria funktioner* sidan, avmarkera de *Lösenordssynkronisering* alternativet.
  
  * Observera att samma problem inträffar också vid försök att aktivera/inaktivera sömlös enkel inloggning. Mer specifikt du har en befintlig Azure AD Connect-distribution med aktiverat Lösenordssynkronisering och inloggningsmetod för användare har redan konfigurerats som *direktautentisering*. Med hjälp av den *ändra användarinloggning* uppgiften som du försöker markera/avmarkera den *Aktivera sömlös enkel inloggning* alternativet medan inloggningsmetod för användare som är konfigurerade som ”direktautentisering”. Innan ändringen tillämpas i guiden felaktigt visar den ”*inaktivera lösenordssynkronisering*” fråga. Lösenordssynkronisering förblir aktiv efter att ändringen gäller. Med den här snabbkorrigeringen visas i guiden inte längre prompten.

* Åtgärdat ett problem med den *ändra användarinloggning* aktivitet i Azure AD Connect-guiden:

   * Problemet uppstår när du har en befintlig Azure AD Connect-distribution med Lösenordssynkronisering **inaktiverad**, och du försöker ange metoden användare logga in som *direktautentisering*. När ändringen används aktiverar till att guiden både direktautentisering och Lösenordssynkronisering. Med den här snabbkorrigeringen kan guiden inte längre Lösenordssynkronisering.

  * Synkronisering av lösenord var tidigare en förutsättning för att aktivera direktautentisering. När du anger att användaren loggar in metod som *direktautentisering*, guiden skulle aktivera både direktautentisering och Lösenordssynkronisering. Lösenordssynkronisering har nyligen tagits bort som en förutsättning. Som en del av Azure AD Connect version 1.1.557.0 kan en ändring har gjorts till Azure AD Connect att inte aktivera Lösenordssynkronisering när du ställer in användaren logga in metod som *direktautentisering*. Ändringen har dock endast tillämpats på Azure AD Connect-installationen. Med den här snabbkorrigeringen samma ändring gäller även de *ändra användarinloggning* uppgift.
  
  * Observera att samma problem inträffar också vid försök att aktivera/inaktivera sömlös enkel inloggning. Mer specifikt kan du har en befintlig Azure AD Connect-distribution med Lösenordssynkronisering inaktiveras och inloggningsmetod för användare har redan konfigurerats som *direktautentisering*. Med hjälp av den *ändra användarinloggning* uppgiften som du försöker markera/avmarkera den *Aktivera sömlös enkel inloggning* alternativet medan inloggningsmetod för användare som är konfigurerade som ”direktautentisering”. När ändringen används aktiverar till att guiden synkronisering av lösenord. Med den här snabbkorrigeringen kan guiden inte längre Lösenordssynkronisering. 

* Ett problem som orsakade Azure AD Connect-uppgradering misslyckas med felet har åtgärdats ”*det går inte att uppgradera synkroniseringstjänsten*”. Dessutom kan synkroniseringstjänsten inte längre starta med fel i händelse ”*tjänsten kunde inte starta eftersom versionen av databasen är nyare än versionen av binärfilerna installerat*”. Problemet uppstår när administratören utför uppgraderingen saknar sysadmin-behörighet till SQLServer som används av Azure AD Connect. Med den här snabbkorrigeringen kräver Azure AD Connect endast administratören har db_owner behörighet att ADSync-databas under uppgraderingen.

* Ett problem som påverkar kunder som har aktiverat Azure AD Connect uppgradera har åtgärdats [sömlös enkel inloggning](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). När Azure AD Connect har uppgraderats visas sömlös enkel inloggning felaktigt som inaktiverade i Azure AD Connect-guiden, även om funktionen är aktiverad och helt funktionella. Med den här snabbkorrigeringen visas funktionen nu korrekt som aktiverats i guiden.

* Ett problem som orsakade den Azure AD Connect-guiden för att alltid visa har åtgärdats i ”*konfigurera Källankare*” fråga på den *redo att konfigurera* även om inga ändringar som rör Källfästpunkt har gjorts.

* När du utför en manuell uppgradering på plats av Azure AD Connect, måste kunden ange autentiseringsuppgifterna som Global administratör för motsvarande Azure AD-klienten. Tidigare uppgraderingen kan fortsätta trots att de globala autentiseringsuppgifterna tillhör en annan Azure AD-klient. Medan uppgraderingen visas har slutförts, sparas inte vissa konfigurationer korrekt med uppgraderingen. Med den här ändringen förhindrar guiden uppgraderingen fortsätter om Azure AD-klienten inte överensstämmer med de angivna autentiseringsuppgifterna.

* Ta bort redundant logik som startats onödan om Azure AD Connect Health-tjänsten i början av en manuell uppgradering.


#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* Har lagts till logik för att förenkla de steg som krävs för att konfigurera Azure AD Connect med Microsoft-molnet för Tyskland. Tidigare var behöver du uppdatera särskilda registernycklar på Azure AD Connect-servern att fungera korrekt med Microsoft-molnet för Tyskland, enligt beskrivningen i den här artikeln. Nu kan kan Azure AD Connect identifieras automatiskt om din klient i Microsoft-molnet för Tyskland baseras på de globala autentiseringsuppgifterna som angetts under installationen.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
>[!NOTE]
> Obs: Synkroniseringstjänsten har ett WMI-gränssnitt som du kan utveckla din egen anpassade scheduler. Det här gränssnittet är nu inaktuellt och kommer att tas bort från framtida versioner av Azure AD Connect levererats efter den 30 juni 2018. Kunder som vill anpassa synkroniseringsschema ska använda den [inbyggda scheduler (https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-scheduler).

#### <a name="fixed-issues"></a>Åtgärdade problem
* När Azure AD Connect-guiden skapar AD Connector-kontot som krävs för att synkronisera ändringar från lokala Active Directory kan tilldelar den korrekt inte kontot behörighet att läsa PublicFolder objekt. Det här problemet påverkar både snabbinstallationen och anpassad installation. Den här ändringen åtgärdar problemet.

* Ett problem som orsakade sidan för felsökning för Azure AD Connect-guiden att renderas inte korrekt för administratörer som körs från Windows Server 2016 har åtgärdats.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* När du felsöker synkronisering av lösenord med hjälp av Azure AD Connect-guiden felsökningssida returnerar felsökning sidan nu domänspecifika status.

* Tidigare, om du försöker aktivera Lösenordshashsynkronisering Azure AD Connect kontrollerar inte om AD-anslutningskontot krävs behörighet för att synkronisera lösenords-hash från lokala AD. Azure AD Connect-guiden kommer nu, verifiera och varna dig om AD-anslutningskontot inte har tillräckliga behörigheter.

### <a name="ad-fs-management"></a>AD FS-hantering
#### <a name="fixed-issue"></a>Problem har åtgärdats
* Ett problem som rör användningen av har åtgärdats [msDS-ConsistencyGuid som Källankare](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funktionen. Det här problemet påverkar kunder som har konfigurerat *Federation med AD FS* som inloggningsmetod för användare. När du kör *konfigurera Källankare* uppgift i guiden, Azure AD Connect växlar till med hjälp av * ms-DS-ConsistencyGuid som källattributet för immutableId. Som en del av den här ändringen försöker Azure AD Connect uppdaterar anspråksreglerna för ImmutableId i AD FS. Men detta steg misslyckades eftersom Azure AD Connect inte har administratörsbehörighet krävs för att konfigurera AD FS. Med den här snabbkorrigeringen Azure AD Connect nu uppmanas du att ange administratörsautentiseringsuppgifter för för AD FS när du kör den *konfigurera Källankare* uppgift.



## <a name="116140"></a>1.1.614.0
Status: September 05 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Kända problem
* Det finns ett känt problem som orsakar Azure AD Connect-uppgradering misslyckas med felet ”*det går inte att uppgradera synkroniseringstjänsten*”. Dessutom kan synkroniseringstjänsten inte längre starta med fel i händelse ”*tjänsten kunde inte starta eftersom versionen av databasen är nyare än versionen av binärfilerna installerat*”. Problemet uppstår när administratören utför uppgraderingen saknar sysadmin-behörighet till SQLServer som används av Azure AD Connect. Dbo-behörigheter är inte tillräckliga.

* Det finns ett känt problem med Azure AD Connect uppgradera som påverkar kunder som har aktiverat [sömlös enkel inloggning](active-directory-aadconnect-sso.md). När Azure AD Connect har uppgraderats visas funktionen som inaktiverad i guiden, även om funktionen hålls aktiverad. En korrigering för det här problemet ska anges i framtiden att släppa. Kunder som är orolig för problemet kan manuellt åtgärda det genom att aktivera sömlös enkel inloggning i guiden.

#### <a name="fixed-issues"></a>Åtgärdade problem
* Åtgärdat ett problem som förhindrade att Azure AD Connect uppdaterar anspråksregler i lokala AD FS samtidigt som den [msDS-ConsistencyGuid som Källankare](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funktionen. Problemet uppstår om du försöker aktivera funktionen för en befintlig Azure AD Connect-distribution som har AD FS som konfigurerats som metod för inloggning. Problemet uppstår eftersom guiden inte frågar om AD FS-autentiseringsuppgifter innan du försöker uppdatera anspråksregler i AD FS.
* Åtgärdat ett problem som orsakas av Azure AD Connect misslyckas installationen om lokalt AD-skogen har NTLM har inaktiverats. Problemet beror på att Azure AD Connect-guiden inte ge fullständiga autentiseringsuppgifter när du skapar security-kontexter som krävs för Kerberos-autentisering. Detta leder till Azure AD Connect-guiden till återgår till att använda NTLM och Kerberos-autentiseringen misslyckas.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
#### <a name="fixed-issues"></a>Åtgärdade problem
* Ett problem har åtgärdats där nya synkroniseringsregeln går inte att skapa om attributet taggen har inte fyllts i.
* Åtgärdat ett problem som orsakas av Azure AD Connect för att ansluta till en lokal AD synkronisering av lösenord med hjälp av NTLM, även om Kerberos finns. Det här problemet uppstår om lokalt AD-topologi har en eller flera domänkontrollanter som har återställts från en säkerhetskopia.
* Ett problem som orsakade fullständig synkroniseringssteg ska ske onödigt efter uppgraderingen har åtgärdats. I allmänhet krävs kör fullständig synkroniseringssteg efter uppgradering om ändringar i out-of-box Synkroniseringsregler. Problemet uppstod på grund av ändring-identifieringslogik som felaktigt identifierats ändras när den påträffar synkronisering Regeluttryck med tecken för ny rad. Radmatningstecken infogas i synkronisering Regeluttryck lättlästa.
* Ett problem som kan göra att Azure AD Connect-servern fungerar korrekt när automatisk uppgradering har åtgärdats. Det här problemet påverkar Azure AD Connect-servrar med version 1.1.443.0 (eller tidigare). Mer information om problemet finns i artikeln [Azure AD Connect inte fungerar korrekt när du har en automatisk uppgradering](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* Ett problem som kan orsaka automatisk uppgradering till göras var femte minut när fel uppstår har åtgärdats. Med åtgärden, automatisk uppgradering av återförsök med exponentiell backoff när fel uppstår.
* Ett problem har åtgärdats där lösenordet synkroniseringshändelse 611 felaktigt visas i Windows-program händelseloggar som **endast i informationssyfte** i stället för **fel**. Händelsen 611 skapas när Lösenordssynkronisering påträffar ett problem. 
* Ett problem har åtgärdats i Azure AD Connect-guiden som gör att funktionen för tillbakaskrivning av grupp aktiveras utan att välja en Organisationsenhet som krävs för tillbakaskrivning av grupp.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* Lägga till en Felsök uppgift till Azure AD Connect-guiden under ytterligare aktiviteter. Kunder kan använda den här aktiviteten för att felsöka problem med synkronisering av lösenord och samla in allmänna diagnostik. I framtiden, utökas Felsök uppgiften för att inkludera andra directory synkronisering-relaterade problem.
* Azure AD Connect nu stöder en ny installationsläge kallas **använda befintlig databas**. Det här installationsläget kan kunderna installerar Azure AD Connect som anger en befintlig ADSync-databas. Mer information om den här funktionen finns i artikeln [Använd en befintlig databas](active-directory-aadconnect-existing-database.md).
* För förbättrad säkerhet, Azure AD Connect nu som standard med TLS1.2 för att ansluta till Azure AD för katalogsynkronisering. Tidigare var standard TLS 1.0.
* När Azure AD Connect lösenord synkronisering agenten startas, försöker den ansluta till Azure AD-välkända slutpunkten för Lösenordssynkronisering. Vid lyckad anslutning omdirigeras till en slutpunkt för regionspecifika. Tidigare cachelagrar lösenord Synkroniseringsagenten regionspecifika slutpunkten tills den startas. Nu rensar agenten cacheminnet och försök med välkända slutpunkten om den stöter på problem med anslutning till regionspecifika-slutpunkten. Den här ändringen gör att Lösenordssynkronisering kan redundansväxla till en annan regionspecifika slutpunkt när cachelagrade regionspecifika slutpunkten inte längre är tillgänglig.
* Om du vill synkronisera ändringar från en lokal AD-skog krävs ett AD DS-konto. Du kan antingen (i) skapa AD DS själv och ange dess autentiseringsuppgifter till Azure AD Connect eller (ii) ange autentiseringsuppgifter för en företagsadministratör och låta Azure AD Connect skapar AD DS-kontot åt dig. Tidigare är (i) standardalternativet i Azure AD Connect-guiden. Nu, (ii) är standardalternativet.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* Stöd har lagts till för Microsoft Azure Government-molnet och Microsoft Cloud Tyskland.

### <a name="ad-fs-management"></a>AD FS-hantering
#### <a name="fixed-issues"></a>Åtgärdade problem
* Initiera ADSyncNGCKeysWriteBack-cmdlet i Förberedelseverktyg powershell-modulen AD felaktigt tillämpade ACL: er till behållaren för enhetsregistrering och skulle därför bara ärvas befintliga behörigheter.  Detta har uppdaterats så att sync-tjänstkontot har rätt behörigheter.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* AAD Connect verifiera AD FS-inloggning aktiviteten har uppdaterats så att den verifierar inloggningar mot Microsoft Online och inte bara token hämtning från AD FS.
* När du konfigurerar en ny AD FS-servergrupp med hjälp av AAD Connect flyttades sidan ADFS informationen så att det inträffar innan användaren uppmanas att ange AD FS och WAP-servrar.  På så sätt kan AAD Connect att kontrollera att det angivna kontot har rätt behörigheter.
* Under uppgraderingen AAD Connect kommer misslyckas vi inte längre en uppgradering om AD FS AAD förtroende kan inte uppdatera.  Om det händer visas ett lämpligt varningsmeddelande användaren och bör fortsätta att återställa förtroendet via AAD Connect ytterligare uppgiften.

### <a name="seamless-single-sign-on"></a>Sömlös enkel inloggning
#### <a name="fixed-issues"></a>Åtgärdade problem
* Ett problem som orsakade den Azure AD Connect-guiden att returnera ett fel om du försöker aktivera har åtgärdats [sömlös enkel inloggning](active-directory-aadconnect-sso.md). Felmeddelandet är *”konfiguration av Microsoft Azure AD Connect-Autentiseringsagenten misslyckades”.* Det här problemet påverkar befintliga kunder som hade uppgraderat förhandsversionen av Autentiseringsagenter för manuellt [direktautentisering](active-directory-aadconnect-sso.md) baserat på stegen som beskrivs i det här [artikeln](active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).


## <a name="115610"></a>1.1.561.0
Status: Juli 23 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problem har åtgärdats

* Ett problem som orsakade synkroniseringsregel för out of box ”ut till AD - användare ImmutableId” har åtgärdats som ska tas bort:

  * Problemet uppstår när Azure AD Connect har uppgraderats, eller när aktivitet *uppdatera synkroniseringskonfigurationen* i Azure AD Connect guiden som används för att uppdatera konfigurationen för Azure AD Connect-synkronisering.
  
  * Den här synkroniseringsregeln kan användas för kunder som har aktiverat den [msDS-ConsistencyGuid som Källankare funktion](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor). Den här funktionen introducerades i version 1.1.524.0 och när. När synkroniseringsregeln tas bort, Azure AD Connect inte längre kan fylla i en lokal AD ms-DS-ConsistencyGuid attributet med värdet för attributet ObjectGuid. Den förhindrar inte att nya användare håller på att etableras i Azure AD.
  
  * Korrigeringen säkerställer att synkroniseringsregeln inte längre tas bort under uppgraderingen, eller under konfigurationsändring, förutsatt att funktionen är aktiverad. För befintliga kunder som har drabbats av det här problemet, korrigeringen ser också till att synkroniseringsregeln har lagts till igen när du har uppgraderat till den här versionen av Azure AD Connect.

* Ett problem som orsakar out-of-box Synkroniseringsregler har prioritetsvärde som är mindre än 100 har åtgärdats:

  * I allmänhet är prioritet 0 - 99 reserverade för av anpassade Synkroniseringsregler. Under uppgraderingen uppdateras prioritet värdena för out-of-box Synkroniseringsregler för att hantera synkronisera ändringar för regeln. På grund av det här problemet tilldelas out-of-box Synkroniseringsregler prioritetsvärde som är mindre än 100.
  
  * Korrigeringen förhindrar att problemet uppstår under uppgraderingen. Men återställs det inte prioritet värden för befintliga kunder som har drabbats av problemet. En separat korrigering ges i framtiden för att underlätta återställningen.

* Ett problem har åtgärdats där den [domän- och OU-filtrering skärmen](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) i Azure AD Connect guiden visar *synkronisera alla domäner och organisationsenheter* alternativ som markerats, även om OU-baserad filtrering är aktiverat.

*   Ett problem har åtgärdats som orsakade den [konfigurera katalogpartitioner skärmen](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) i hanteraren för synkroniseringstjänsten att returnera ett fel om de *uppdatera* klickar på knappen. Felmeddelandet är *”ett fel inträffade under uppdateringen domäner: Det går inte att typkonvertera objekt av typen System.Collections.ArrayList om du skriver” Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject ”.* Felet uppstår när nya AD-domänen har lagts till en befintlig AD-skog och du försöker uppdatera Azure AD Connect med uppdatera-knappen.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

* [Automatiska](active-directory-aadconnect-feature-automatic-upgrade.md) har utökats för att stödja kunder med följande konfigurationer:
  * Du har aktiverat funktionen för tillbakaskrivning av enhet.
  * Du har aktiverat funktionen för tillbakaskrivning av grupp.
  * Installationen är inte en standardinställningar eller en DirSync-uppgradering.
  * Du har fler än 100 000 objekt i metaversum.
  * Du ansluter till mer än en skog. Snabbinstallation av ansluter bara till en skog.
  * AD-anslutningskontot är inte standardkontot för MSOL_ längre.
  * Servern anges ska vara i mellanlagringsläge.
  * Du har aktiverat funktionen för tillbakaskrivning av användare.
  
  >[!NOTE]
  >Scope-expansion av funktionen för automatisk uppgradering påverkar kunder med Azure AD Connect version 1.1.105.0 och efter. Om du inte vill att din Azure AD Connect-server som ska uppgraderas automatiskt, måste du köra följande cmdlet på din Azure AD Connect-server: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Mer information om aktivering/inaktivering automatisk uppgradering finns i artikeln [Azure AD Connect: automatisk uppgradering](active-directory-aadconnect-feature-automatic-upgrade.md).

## <a name="115580"></a>1.1.558.0
Status: Släpps inte. Ändringar i den här versionen ingår i versionen 1.1.561.0.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problem har åtgärdats

* Ett problem som orsakade synkroniseringsregel för out of box ”ut till AD - användare ImmutableId” har åtgärdats som ska tas bort när OU-baserad filtrering konfiguration har uppdaterats. Den här synkroniseringsregeln krävs för den [msDS-ConsistencyGuid som Källankare funktion](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor).

* Ett problem har åtgärdats där den [domän- och OU-filtrering skärmen](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) i Azure AD Connect guiden visar *synkronisera alla domäner och organisationsenheter* alternativ som markerats, även om OU-baserad filtrering är aktiverat.

*   Ett problem har åtgärdats som orsakade den [konfigurera katalogpartitioner skärmen](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) i hanteraren för synkroniseringstjänsten att returnera ett fel om de *uppdatera* klickar på knappen. Felmeddelandet är *”ett fel inträffade under uppdateringen domäner: Det går inte att typkonvertera objekt av typen System.Collections.ArrayList om du skriver” Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject ”.* Felet uppstår när nya AD-domänen har lagts till en befintlig AD-skog och du försöker uppdatera Azure AD Connect med uppdatera-knappen.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

* [Automatiska](active-directory-aadconnect-feature-automatic-upgrade.md) har utökats för att stödja kunder med följande konfigurationer:
  * Du har aktiverat funktionen för tillbakaskrivning av enhet.
  * Du har aktiverat funktionen för tillbakaskrivning av grupp.
  * Installationen är inte en standardinställningar eller en DirSync-uppgradering.
  * Du har fler än 100 000 objekt i metaversum.
  * Du ansluter till mer än en skog. Snabbinstallation av ansluter bara till en skog.
  * AD-anslutningskontot är inte standardkontot för MSOL_ längre.
  * Servern anges ska vara i mellanlagringsläge.
  * Du har aktiverat funktionen för tillbakaskrivning av användare.
  
  >[!NOTE]
  >Scope-expansion av funktionen för automatisk uppgradering påverkar kunder med Azure AD Connect version 1.1.105.0 och efter. Om du inte vill att din Azure AD Connect-server som ska uppgraderas automatiskt, måste du köra följande cmdlet på din Azure AD Connect-server: `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`. Mer information om aktivering/inaktivering automatisk uppgradering finns i artikeln [Azure AD Connect: automatisk uppgradering](active-directory-aadconnect-feature-automatic-upgrade.md).

## <a name="115570"></a>1.1.557.0
Status: Juli 2017

>[!NOTE]
>Den här versionen är inte tillgängligt för kunder via funktionen Azure AD Connect automatiskt uppgradera.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Problem har åtgärdats
* Ett problem har åtgärdats med cmdleten initiera ADSyncDomainJoinedComputerSync som orsakade den verifierade domänen som konfigurerats på det befintliga objektet ändras även om det fortfarande är en giltig domän punkt för anslutning av tjänsten. Det här problemet inträffar när Azure AD-klienten har mer än en verifierade domäner som kan användas för att konfigurera tjänstanslutningspunkten.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* Tillbakaskrivning av lösenord är nu tillgängligt som förhandsversion med Microsoft Azure Government-molnet och Microsoft Cloud Tyskland. Mer information om Azure AD Connect-stöd för olika tjänstinstanser i artikeln [Azure AD Connect: tänka på för instanser](active-directory-aadconnect-instances.md).

* Initiera ADSyncDomainJoinedComputerSync cmdleten har nu en ny valfri parameter med namnet AzureADDomain. Den här parametern kan du ange vilket verifierad domän som ska användas för att konfigurera tjänstanslutningspunkten.

### <a name="pass-through-authentication"></a>Direktautentisering

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* Namnet på agenten som krävs för direktautentisering har ändrats från *Microsoft Azure AD Application Proxy Connector* till *Microsoft Azure AD Connect-Autentiseringsagenten*.

* Aktiverar direktautentisering längre aktiverar synkronisering av Lösenordshash som standard.


## <a name="115530"></a>1.1.553.0
Status: Juni 2017

> [!IMPORTANT]
> Det finns schema- och synkronisera regeländringar som införs i den här versionen. Azure AD Connect-synkroniseringstjänsten utlöser fullständig Import och fullständig synkronisering steg efter uppgradering. Information om ändringarna beskrivs nedan. För att tillfälligt vill skjuta upp fullständig Import och fullständig synkronisering steg efter uppgradering finns i artikeln [hur du skjuta upp fullständig synkronisering efter uppgradering](active-directory-aadconnect-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).
>
>

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync

#### <a name="known-issue"></a>Kända problem
* Det finns ett problem som påverkar kunder som använder [OU-baserad filtrering](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) med Azure AD Connect-synkronisering. När du navigerar till den [domän- och OU-filtrering sidan](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) i Azure AD Connect-guiden på följande förväntas:
  * Om OU-baserad filtrering är aktiverat i **synkronisera utvalda domäner och organisationsenheter** alternativet är markerat.
  * I annat fall den **synkronisera alla domäner och organisationsenheter** alternativet är markerat.

Det problem som uppstår är att den **synkronisera alla domäner och organisationsenheter alternativet** är alltid markerat när du kör guiden.  Detta inträffar även om OU-baserad filtrering konfigurerats tidigare. Innan du sparar ändringar i konfigurationen AAD Connect, se till att den **synkronisera valda domäner och organisationsenheter alternativet** och bekräfta att alla organisationsenheter som behöver synkronisera aktiveras igen. I annat fall kommer OU-baserad filtrering att inaktiveras.

#### <a name="fixed-issues"></a>Åtgärdade problem

* Ett problem har åtgärdats med tillbakaskrivning av lösenord som gör att en Azure AD-administratör att återställa lösenordet för en lokal AD privilegierad användarkonto. Problemet uppstår när Azure AD Connect beviljas behörighet för återställning av lösenord via det privilegierade kontot. Problemet åtgärdas i den här versionen av Azure AD Connect genom att inte tillåta en Azure AD-administratör att återställa lösenordet för ett godtyckligt lokalt AD privilegierad användarkonto om inte administratören är ägaren av det kontot. Mer information finns i [Security Advisory 4033453](https://technet.microsoft.com/library/security/4033453).

* Ett problem som rör har åtgärdats i [msDS-ConsistencyGuid som Källankare](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funktion där Azure AD Connect har inte tillbakaskrivning till lokala AD-attribut msDS-ConsistencyGuid. Problemet uppstår när det finns flera lokala AD-skogar som lagts till i Azure AD Connect och *användaridentiteter finns i flera kataloger alternativet* har valts. När sådan konfiguration används, de resulterande synkroniseringsreglerna inte att uppdatera attributet sourceAnchorBinary i metaversum. SourceAnchorBinary attributet används som källattributet för attributet msDS-ConsistencyGuid. Därför uppstår inte tillbakaskrivning till attributet ms-DSConsistencyGuid. Följande Synkroniseringsregler har uppdaterats för att säkerställa att attributet sourceAnchorBinary i metaversum fylls alltid för att åtgärda problemet:
  * I från AD - InetOrgPerson AccountEnabled.xml
  * I från AD - InetOrgPerson Common.xml
  * I från AD - användare AccountEnabled.xml
  * I från AD - användare Common.xml
  * I från AD - användare ansluta-SOAInAAD.xml

* Tidigare, även om den [msDS-ConsistencyGuid som Källankare](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funktionen inte är aktiverad synkroniseringsregeln ”ut till AD – ImmutableId för användaren” fortfarande läggs till i Azure AD Connect. Effekten är ofarliga och orsakar inte tillbakaskrivning av attributet msDS-ConsistencyGuid ska ske. För att undvika förvirring har logic lagts till till att synkronisera regeln läggs endast när funktionen är aktiverad.

* Ett problem som orsakade synkronisering av lösenordshash misslyckas med felhändelse 611 har åtgärdats. Det här problemet uppstår när du har en eller flera domain domänkontrollanter har tagits bort från den lokala AD. I slutet av varje cykel för synkronisering av lösenord synkronisering cookien utfärdat av en lokal AD innehåller anrops-ID för de borttagna domänkontrollanterna med USN (Update Sequence Number) värdet 0. Synkroniseringshanteraren för lösenord går inte att spara synkronisering cookie som innehåller USN-värde på 0 och misslyckas med felhändelse 611. Under nästa synkroniseringscykel återanvänder Lösenordshanteraren för synkronisering av senaste beständiga synkronisering cookien som inte innehåller USN-värde på 0. Detta leder till samma lösenordsändringar synkroniseras om. Med den här snabbkorrigeringen kvarstår synkronisering Lösenordshanteraren synkronisering cookien korrekt.

* Tidigare, även om automatisk uppgradering har inaktiverats med hjälp av cmdleten Set-ADSyncAutoUpgrade, den automatisk uppgradering av processen fortsätter att söka efter uppgradera regelbundet och förlitar sig på det nedladdade installationsprogrammet måste respektera avstängning. Med den här snabbkorrigeringen kontrollerar automatisk uppgradering av processen inte längre för uppgradering med jämna mellanrum. Korrigeringen tillämpas automatiskt när uppgraderingen installationsprogrammet för den här Azure AD Connect-versionen körs en gång.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

* Tidigare den [msDS-ConsistencyGuid som Källankare](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-msds-consistencyguid-as-sourceanchor) funktionen är tillgänglig för nya distributioner. Det är nu tillgängliga för befintliga distributioner. Mer specifikt:
  * Starta Azure AD Connect-guiden för att komma åt funktionen och välj den *uppdatering Källfästpunkt* alternativet.
  * Det här alternativet är bara synliga för befintliga distributioner som använder objectGuid som sourceAnchor-attribut.
  * När du konfigurerar alternativet, verifierar guiden tillståndet för attributet msDS-ConsistencyGuid i din lokala Active Directory. Om attributet inte är konfigurerat för alla användarobjekt i katalogen, använder guiden msDS-ConsistencyGuid som sourceAnchor-attribut. Om attributet har konfigurerats för en eller flera objekt i katalogen, avslutar guiden attributet som används av andra applikationer och är inte lämplig som sourceAnchor-attribut och tillåter inte ändringen Källfästpunkt att fortsätta. Om du är säker på att attributet inte är används av befintliga program som du behöver kontakta supporten för information om hur du ignorera felet.

* Specifika för **userCertificate** attributet enhetsobjekt, Azure AD Connect nu söker efter certifikat värden som krävs för [ansluta domänanslutna enheter till Azure AD för Windows 10-upplevelse](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) och filtrerar bort resten innan du synkroniserar till Azure AD. Om du vill aktivera det här beteendet synkroniseringsregel för out of box ”ut till AAD – enhet ansluta SOAInAD” har uppdaterats.

* Azure AD Connect nu stöder tillbakaskrivning av Exchange Online **cloudPublicDelegates** attributet lokala AD **publicDelegates** attribut. Detta gör scenariot där en Exchange Online-postlåda kan tilldelas SendOnBehalfTo rättigheter för användare med lokala Exchange-postlåda. Stöd för den här funktionen, en ny synkroniseringsregel av out of box ”ut till AD – tillbakaskrivning av användare Exchange Hybrid PublicDelegates” har lagts till. Den här synkroniseringsregel läggs endast till Azure AD Connect när Exchange-Hybrid-funktionen är aktiverad.

*   Azure AD Connect nu stöder synkronisering av den **altRecipient** attribut från Azure AD. För att stödja den här ändringen, har följande out-of-box Synkroniseringsregler uppdaterats med det obligatoriska attributflödet:
  * I från AD – användaren Exchange
  * På AAD – användaren ExchangeOnline
  
* Den **cloudSOAExchMailbox** attribut i metaversum anger om en viss användare har Exchange Online-postlåda eller inte. Den aktuella definitionen har uppdaterats för att inkludera ytterligare Exchange Online RecipientDisplayTypes som sådan utrustning och konferensrum postlådor. Om du vill aktivera den här ändringen, har definitionen av attributet cloudSOAExchMailbox, som finns under synkroniseringsregel för out of box ”i från AAD – användaren Exchange-Hybrid”, uppdaterats från:

```
CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
```

... till följande:

```
CBool(
  IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
    IIF([cloudMSExchRecipientDisplayType]=0,True,(
      IIF([cloudMSExchRecipientDisplayType]=2,True,(
        IIF([cloudMSExchRecipientDisplayType]=7,True,(
          IIF([cloudMSExchRecipientDisplayType]=8,True,(
            IIF([cloudMSExchRecipientDisplayType]=10,True,(
              IIF([cloudMSExchRecipientDisplayType]=16,True,(
                IIF([cloudMSExchRecipientDisplayType]=17,True,(
                  IIF([cloudMSExchRecipientDisplayType]=18,True,(
                    IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                       IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))

```

* Lägga till följande uppsättning X509Certificate2-kompatibla funktioner för att skapa synkronisering Regeluttryck att hantera för certifikatvärden i attributet userCertificate:

    ||||
    | --- | --- | --- |
    |CertSubject|CertIssuer|CertKeyAlgorithm|
    |CertSubjectNameDN|CertIssuerOid|CertNameInfo|
    |CertSubjectNameOid|CertIssuerDN|IsCert|
    |CertFriendlyName|certThumbprint|CertExtensionOids|
    |CertFormat|CertNotAfter|CertPublicKeyOid|
    |CertSerialNumber|CertNotBefore|CertPublicKeyParametersOid|
    |CertVersion|CertSignatureAlgorithmOid|Välj|
    |CertKeyAlgorithmParams|CertHashString|Var|
    |||Med|

* Följande schemaändringar har införts för att kunder kan skapa anpassade Synkroniseringsregler för att flöda sAMAccountName, domainNetBios och domainFQDN för grupp-objekt, samt distinguishedName för objekt:

  * Följande attribut har lagts till MV-schemat:
    * Grupp: AccountName
    * Grupp: domainNetBios
    * Grupp: domainFQDN
    * Person: distinguishedName

  * Följande attribut har lagts till Azure AD Connector schemat:
    * Grupp: OnPremisesSamAccountName
    * Grupp: NetBIOS-namn
    * Grupp: DNS-domännamn
    * Användare: OnPremisesDistinguishedName

* Skriptet ADSyncDomainJoinedComputerSync cmdleten har nu en ny valfri parameter med namnet AzureEnvironment. Parametern används för att ange vilken region som den motsvarande Azure Active Directory-klienten finns i. Giltiga värden är:
  * AzureCloud (standard)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USGovernment
 
* Uppdaterade synkronisering Rule Editor att använda ansluta (i stället för att tillhandahålla) eftersom standardvärdet för länktyp under skapande av synkronisering.

### <a name="ad-fs-management"></a>AD FS-hantering

#### <a name="issues-fixed"></a>Problem som åtgärdas

* Följande URL: er är ny WS-Federation-slutpunkter som introducerades av Azure AD för att förbättra skydd mot avbrott i autentisering och kommer att läggas till en lokal AD FS svarande part förtroende-konfiguration:
  * https://ests.login.microsoftonline.com/login.srf
  * https://stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf
  
* Ett problem som orsakade AD FS för att generera felaktig anspråksvärdet för IssuerID har åtgärdats. Problemet uppstår om det finns flera verifierade domäner i Azure AD-klient och domänsuffixet för attributet userPrincipalName som används för att generera IssuerID-anspråket är minst 3 nivåer djup (till exempel johndoe@us.contoso.com). Problemet löses genom att uppdatera regex som används av anspråksreglerna.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* Tidigare kan funktionen ADFS certifikathantering från Azure AD Connect endast användas med AD FS-servergrupper som hanteras via Azure AD Connect. Nu kan du använda funktionen med AD FS-servergrupper som inte hanteras med Azure AD Connect.

## <a name="115240"></a>1.1.524.0
Utgiven: Maj 2017

> [!IMPORTANT]
> Det finns schema- och synkronisera regeländringar som införs i den här versionen. Azure AD Connect-synkroniseringstjänsten utlöser fullständig Import och en fullständig synkronisering steg efter uppgradering. Information om ändringarna beskrivs nedan.
>
>

**Åtgärdade problem:**

Azure AD Connect-synkronisering

* Ett problem som orsakar automatisk uppgradering av ska ske på Azure AD Connect-servern, även om kunden har inaktiverat funktionen med hjälp av cmdleten Set-ADSyncAutoUpgrade har åtgärdats. Med denna snabbkorrigering automatisk uppgradering av processen på servern söker fortfarande efter uppgraderingen med jämna mellanrum, men det nedladdade installationsprogrammet godkänner automatisk uppgradering av konfigurationen.
* Under uppgradering på plats för DirSync skapar Azure AD Connect ett Azure AD-tjänstkonto som ska användas av Azure AD-anslutningsappen för att synkronisera med Azure AD. När kontot har skapats, autentiserar Azure AD Connect med Azure AD med hjälp av kontot. Ibland autentisering misslyckas på grund av tillfälliga problem, vilket i sin tur gör att DirSync uppgradering att misslyckas med fel *”ett fel har uppstått aktiviteten slutfördes konfigurera AAD Sync: AADSTS50034: Om du vill logga in på det här programmet, måste kontot vara lägga till katalogen xxx.onmicrosoft.com ”.* Förbättrad återhämtning av DirSync-uppgradering, Azure AD Connect nu autentisering försöker med steget igen.
* Det uppstod ett problem med build 443 som gör att DirSync uppgradering ska lyckas men skapas inte körning av profiler krävs för katalogsynkronisering. Återställning logic ingår i den här versionen av Azure AD Connect. När kunden uppgraderas till den här versionen, identifierar saknas körningsprofiler Azure AD Connect och de skapas.
* Ett problem som orsakar processen för synkronisering av lösenord inte kunde starta med händelse-ID 6900 och felet har åtgärdats *”ett objekt med samma nyckel har redan lagts”*. Det här problemet uppstår om du uppdaterar OU filtrering konfiguration för att inkludera AD-konfigurationens partition. För att åtgärda problemet, synkroniserar processen för Lösenordssynkronisering nu lösenordsändringar från AD-domänpartitioner. Icke-domänpartitioner, till exempel konfigurationspartitionen hoppas över.
* Under snabbinstallationen, Azure AD Connect skapar en lokal AD DS-konto som används av AD-koppling för att kommunicera med lokala AD. Tidigare kontot har skapats med flaggan PASSWD_NOTREQD inställd på user Account Control-attribut och ett slumpmässigt lösenord har angetts för kontot. Nu tar Azure AD Connect anges uttryckligen bort flaggan PASSWD_NOTREQD när lösenordet har ställts in på kontot.
* Ett problem som orsakar DirSync-uppgradering misslyckas med felet har åtgärdats *”deadlock-läge uppstod i SQLServer som försöker hämta ett programlås”* när mailNickname-attributet finns i lokalt AD-schema, men begränsas inte till objektklass AD-användare.
* Ett problem som orsakar funktionen för tillbakaskrivning av enhet inaktiveras automatiskt när en administratör uppdaterar Azure AD Connect sync-konfiguration med hjälp av Azure AD Connect-guiden har åtgärdats. Det här problemet orsakas av guiden utför en kontroll som krävs för den befintliga enheten tillbakaskrivning av konfigurationen i lokala AD och kontrollen misslyckas. Korrigeringen är att hoppa över kontrollen om tillbakaskrivning av enhet har redan aktiverats tidigare.
* Om du vill konfigurera OU-filtrering, kan du antingen använda Azure AD Connect-guiden eller hanteraren för synkroniseringstjänsten. Tidigare, om du använder Azure AD Connect-guiden för att konfigurera OU-filtrering, nya organisationsenheter som skapats efteråt ingår för katalogsynkronisering. Om du inte vill att nya organisationsenheter som ska tas med, måste du konfigurera OU-filtrering med hjälp av hanteraren för synkroniseringstjänsten. Du kan nu få samma beteende med hjälp av Azure AD Connect-guiden.
* Ett problem som orsakar lagrade procedurer som krävs av Azure AD Connect som ska skapas under schemat för installation-administratör kan i stället för under dbo-schemat har åtgärdats.
* Ett problem som orsakar attributet spårnings-ID som returneras av Azure AD för att utelämna i AAD Connect serverns händelselogg har åtgärdats. Problemet uppstår om Azure AD Connect tar emot ett meddelande om omdirigering från Azure AD och Azure AD Connect kan inte ansluta till den tillhandahållna slutpunkten. TrackingId används av Support-tekniker för att korrelera med sida tjänstloggar under felsökningen.
* När Azure AD Connect får LargeObject-fel från Azure AD, Azure AD Connect genererar en händelse med händelse-ID 6941 och meddelandet *”objektet är för stor. Begränsa antalet attributvärden för det här objektet ”.* På samma gång, genererar Azure AD Connect även en vilseledande händelse med händelse-ID 6900 och meddelandet *”Microsoft.Online.Coexistence.ProvisionRetryException: Det går inte att kommunicera med Windows Azure Active Directory-tjänst”.* För att minimera missförstånden genererar Azure AD Connect inte längre det senare fallet när LargeObject-fel som tas emot.
* Ett problem som gör att Hanteraren för synkroniseringstjänsten inte svarar när du försöker uppdatera konfigurationen för allmän LDAP connector har åtgärdats.

**Nya funktioner/förbättringar:**

Azure AD Connect-synkronisering
* Synkronisera regeländringar – följande synkronisering regeländringar har införts:
  * Synkroniseringsregel för uppdaterade standard inställd inte exportera attribut **userCertificate** och **userSMIMECertificate** om attribut har fler än 15 värden.
  * AD-attribut **employeeID** och **msExchBypassModerationLink** ingår nu i regeluppsättningen för standard-synkronisering.
  * AD-attribut **foto** har tagits bort från standard synkronisering regeluppsättning.
  * Lagt till **preferredDataLocation** metaversumschema och AAD-koppling schemat. Kunder som vill uppdatera antingen attribut i Azure AD kan implementera av anpassade Synkroniseringsregler för att göra detta. 
  * Lagt till **userType** metaversumschema och AAD-koppling schemat. Kunder som vill uppdatera antingen attribut i Azure AD kan implementera av anpassade Synkroniseringsregler för att göra detta.

* Azure AD Connect nu automatiskt kan du använda ConsistencyGuid attribut som källfästpunktsattribut för lokal AD-objekt. Ytterligare, Azure AD Connect fyller ConsistencyGuid attributet med värdet för attributet objectGuid om den är tom. Den här funktionen gäller för ny distribution. Om du vill veta mer om den här funktionen, läser du avsnittet [Azure AD Connect: Designbegreppen - med msDS-ConsistencyGuid som sourceAnchor](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor).
* Nya felsökningsvyer cmdlet har Invoke-ADSyncDiagnostics lagts till för att diagnosticera synkronisering av Lösenordshash-relaterade problem. Information om cmdleten finns i artikeln [felsöka lösenordshashsynkronisering med Azure AD Connect-synkronisering](active-directory-aadconnectsync-troubleshoot-password-hash-synchronization.md).
* Azure AD Connect nu stöder synkronisering brevlåda offentlig mapp objekt från en lokal AD till Azure AD. Du kan aktivera funktionen med hjälp av Azure AD Connect-guiden under valfria funktioner. Om du vill veta mer om den här funktionen finns i artikeln [Office 365 Directory baserad Edge Blocking stöd för lokala mappar e-post aktiverad offentliga](https://blogs.technet.microsoft.com/exchange/2017/05/19/office-365-directory-based-edge-blocking-support-for-on-premises-mail-enabled-public-folders).
* Azure AD Connect kräver en AD DS-konto synkroniseras från lokala AD. Tidigare, om du har installerat Azure AD Connect med Express-läge kan du ange autentiseringsuppgifterna för ett Enterprise-administratör-konto och Azure AD Connect skapar AD DS-konto krävs. Men för en anpassad installation och att lägga till skogar i en befintlig distribution kan tvungen du att i stället ange AD DS-kontot. Nu kan möjlighet du också att ange autentiseringsuppgifterna för ett Enterprise-administratör-konto under en anpassad installation och låta Azure AD Connect skapa AD DS-kontot som krävs.
* Azure AD Connect har nu stöd för SQL AOA. Innan du installerar Azure AD Connect måste du aktivera SQL AOA. Under installationen identifierar Azure AD Connect om den angivna SQL-instansen är aktiverat för SQL AOA eller inte. Om SQL AOA är aktiverad, räknat Azure AD Connect ytterligare ut om SQL AOA har konfigurerats för att använda synkron replikering eller asynkron replikering. När du konfigurerar tillgänglighetsgruppens lyssnare, rekommenderas att du ställer in egenskapen RegisterAllProvidersIP till 0. Denna rekommendation beror på att Azure AD Connect för närvarande använder SQL Native Client för att ansluta till SQL och SQL Native Client stöder inte användning av egenskapen MultiSubNetFailover.
* Om du använder LocalDB som databas för din Azure AD Connect-servern och har nått storleksgränsen 10 GB, startar inte längre synkroniseringstjänsten. Tidigare var behöver du utföra ShrinkDatabase åtgärden på en LocalDB att frigöra tillräckligt med utrymme för DB att starta synkroniseringstjänsten. Då och du kan använda hanteraren för synkroniseringstjänsten för att ta bort körningshistorik för att frigöra mer DB-utrymme. Du kan nu använda Start-ADSyncPurgeRunHistory cmdlet att rensa köra historikdata från LocalDB frigöra DB-utrymme. Dessutom denna cmdlet har stöd för en offline-läge (genom att ange parametern - offline) som kan användas när synkroniseringstjänsten inte körs. Obs: Offline-läge kan endast användas om synkroniseringstjänsten körs inte och den databas som används är LocalDB.
* Om du vill minska mängden lagringsutrymme som krävs, Azure AD komprimerar Connect nu synkronisering felinformation innan du lagrar dem i LocalDB/SQL-databaser. När du uppgraderar från en äldre version av Azure AD Connect till den här versionen, utför Azure AD Connect ett enstaka komprimering på befintliga sync-felinformation.
* Tidigare, när du har uppdaterat OU-filtrering konfigurationen, måste du manuellt köra fullständig import för att säkerställa att befintliga objekt är korrekt inkluderad/exkluderad från katalogsynkronisering. Nu kan Azure AD Connect automatiskt utlöser fullständig import vid nästa synkronisering migreringscykel. Ytterligare, fullständig import tillämpas endast på de AD-anslutningsappar som påverkas av uppdateringen. Obs: den här förbättringen gäller för OU-filtrering uppdateringar som görs med hjälp av guiden Azure AD Connect. Den gäller inte för OU-filtrering uppdatering som gjorts med hjälp av hanteraren för synkroniseringstjänsten.
* Tidigare gruppbaserad filtrering har stöd för användare, grupper och kontakta endast objekt. Gruppbaserad filtrering stöder nu även datorobjekt.
* Tidigare, kan du ta bort anslutningsplatsen data utan att inaktivera Azure AD Connect sync scheduler. Nu kan blockerar hanteraren för synkroniseringstjänsten borttagningen av anslutningsplatsen data om upptäcks att scheduler är aktiverad. Dessutom returneras en varning för att informera kunder om potentiell dataförlust om koppling utrymmesdata tas bort.
* Tidigare, måste du inaktivera PowerShell avskrift för Azure AD Connect-guiden ska kunna köras korrekt. Det här problemet har delvis lösts. Du kan aktivera PowerShell avskrift om du använder Azure AD Connect-guiden för att hantera synkroniseringen. Om du använder Azure AD Connect-guiden för att hantera AD FS-konfigurationen måste du inaktivera PowerShell avskrift.



## <a name="114860"></a>1.1.486.0
Utgiven: April 2017

**Åtgärdade problem:**
* På ett problem har åtgärdats där Azure AD Connect kommer inte har installerats på översatt version av Windows Server.

## <a name="114840"></a>1.1.484.0
Utgiven: April 2017

**Kända problem:**

* Den här versionen av Azure AD Connect kommer inte har installerats om följande villkor är uppfyllda:
   1. Du utför antingen DirSync på plats uppgradering eller ny installation av Azure AD Connect.
   2. Du använder en översatt version av Windows Server där namnet på den inbyggda gruppen Administratörer på servern inte är ”administratörer”.
   3. Du använder SQL Server 2012 Express LocalDB installerad med Azure AD Connect i stället för att ange din egen fullständiga SQL.

**Åtgärdade problem:**

Azure AD Connect-synkronisering
* Ett problem har åtgärdats där sync scheduler hoppar över hela synkronisering steg om en eller flera anslutningar saknar körningsprofil för att synkronisera-steget. Exempelvis kan du manuellt lagt till en anslutning med hjälp av hanteraren för synkroniseringstjänsten utan att skapa en Deltaimport körningsprofil för den. Den här snabbkorrigeringen säkerställer att sync scheduler fortsätter att köra Deltaimport för andra anslutningsappar.
* Ett problem har åtgärdats där synkroniseringstjänsten direkt avbryta bearbetningen en körningsprofil påträffar när den är ett problem med ett av de kör stegen. Den här snabbkorrigeringen säkerställer att synkroniseringstjänsten hoppar över som kör steget och fortsätter att bearbeta resten. Till exempel ha en Deltaimport kör profil för din AD-koppling med flera kör steg (en för varje lokal AD-domän). Synkroniseringstjänsten körs Deltaimport med andra AD-domäner även om en av dem har problem med nätverksanslutningen.
* Ett problem som gör att Azure AD Connector uppdateringen som ska hoppas över när automatisk uppgradering har åtgärdats.
* Ett problem som gör att Azure AD Connect att felaktigt avgöra om servern är en domänkontrollant under installationen har åtgärdats, gör vilka i sin tur att DirSync uppgraderingen misslyckas.
* Ett problem som orsakar DirSync uppgradering att inte skapa någon körningsprofil för Azure AD Connector har åtgärdats.
* Ett problem har åtgärdats där Synchronization Service Manager-användargränssnittet slutar svara när du försöker konfigurera allmänna LDAP-Anslutningsappen.

AD FS-hantering
* Ett problem har åtgärdats där Azure AD Connect-guiden misslyckas om den primära AD FS-noden har flyttats till en annan server.

Enkel inloggning för skrivbord
* Ett problem har åtgärdats i Azure AD Connect-guiden där skärmen inloggning kan inte aktivera skrivbords-SSO-funktionen om du har valt Lösenordssynkronisering som ett alternativ under installationen av nya.

**Nya funktioner/förbättringar:**

Azure AD Connect-synkronisering
* Azure AD Connect Sync stöder nu användning av virtuellt tjänstkonto, Hanterat tjänstkonto och Grupphanterat tjänstkonto som dess tjänstkonto. Detta gäller för ny installation av Azure AD Connect endast. När du installerar Azure AD Connect:
    * Som standard, Azure AD Connect-guiden skapar ett virtuellt tjänstkonto och använder dem som dess tjänstkonto.
    * Om du installerar på en domänkontrollant, använder Azure AD Connect till beteende för tidigare där den skapar ett domänanvändarkonto och använder den som dess tjänstkonto i stället.
    * Du kan åsidosätta standardbeteendet genom att ange någon av följande:
      * Ett Grupphanterat tjänstkonto
      * Ett hanterat tjänstkonto
      * Ett domänanvändarkonto
      * Ett lokalt användarkonto
* Tidigare, om du uppgraderar till en ny version av Azure AD Connect som innehåller kopplingar uppdatera eller synkronisera regeländringar, Azure AD Connect ska utlösa en fullständig synkroniseringscykel. Azure AD Connect utlöser nu selektivt fullständig Import steg endast för kopplingar med uppdateringen, och fullständig synkronisering endast för kopplingar med synkronisera ändringar för regeln.
* Tidigare, tröskelvärde för borttagning exportera gäller endast för export som aktiveras via sync scheduler. Funktionen är nu utvidgas till att omfatta export som utlöses manuellt av kunden med hjälp av hanteraren för synkroniseringstjänsten.
* På din Azure AD-klient finns en tjänstkonfiguration som anger om synkronisering av lösenords-funktionen har aktiverats för din klient eller inte. Tidigare är det enkelt för konfigurationen av konfigureras felaktigt av Azure AD Connect när du har en aktiv och en fristående server. Nu kan Azure AD Connect försöker placera tjänstkonfigurationen konsekvent med din aktiva Azure AD Connect-servern.
* Azure AD Connect guiden nu identifierar och returnerar en varning om en lokal AD inte har AD-Papperskorgen aktiverat.
* Exportera tidigare till Azure AD-tidsgränsen ut och misslyckas om den kombinerade storleken av objekten i batchen överstiger tröskelvärde. Nu kan synkroniseringstjänsten gör ett nytt försök att skicka om objekten i separata, mindre batchar om problemet har påträffats.
* Synkronisering Service Key Management-programmet har tagits bort från Windows-startmenyn. Hantering av krypteringsnyckeln fortsätter att vara tillgänglig via kommandoradsgränssnittet med miiskmu.exe. Information om hantering av krypteringsnycklar finns i artikeln [avbryts krypteringsnyckeln Azure AD Connect Sync](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-azure-ad-connect-sync-encryption-key).
* Tidigare, ändrar du Azure AD Connect sync tjänstkontolösenord, synkroniseringstjänsten kan inte kan starta korrekt förrän du har avbrutit krypteringsnyckeln och initierats om Azure AD Connect sync tjänstkontolösenord. Den här processen är nu inte längre krävs.

Enkel inloggning för skrivbord

* Azure AD Connect-guiden kräver inte längre porten 9090 öppnas i nätverket när du konfigurerar direktautentisering och skrivbords-SSO. Endast port 443 krävs. 

## <a name="114430"></a>1.1.443.0
Utgiven: Mars 2017

**Åtgärdade problem:**

Azure AD Connect-synkronisering
* Ett problem som gör att Azure AD Connect-guiden att misslyckas om visningsnamnet för Azure AD Connector inte innehåller den första onmicrosoft.com-domän som är tilldelade till Azure AD-klient har åtgärdats.
* Ett problem som gör att Azure AD Connect-guiden att misslyckas när du gjorde anslutning till SQL database när lösenordet för synkroniseringstjänstkontot innehåller specialtecken, till exempel apostrof, kolon och utrymme har åtgärdats.
* Ett problem som orsakar felet ”dimage har en fästpunkt som skiljer sig från bilden” har åtgärdats så att det sker en Azure AD Connect-server i mellanlagringsläge när du har tillfälligt exkluderat en lokal AD objektet från att synkronisera och sedan ingår det igen för att synkronisera.
* Ett problem som orsakar felet ”det objekt som hittas av DN är en fiktiv” har åtgärdats så att det sker en Azure AD Connect-server i mellanlagringsläge när du har tillfälligt exkluderat en lokal AD objektet från att synkronisera och sedan ingår det igen för att synkronisera.

AD FS-hantering
* Ett problem där Azure AD Connect-guiden inte uppdatera AD FS-konfigurationen och ange rätt anspråk på det förlitande partsförtroendet när alternativa inloggnings-ID har konfigurerats har åtgärdats.
* Ett problem har åtgärdats där Azure AD Connect-guiden är inte korrekt hantera AD FS-servrar vars service-konton har konfigurerats med userPrincipalName format i stället för sAMAccountName-format.

Direktautentisering
* Ett problem som gör att Azure AD Connect-guiden att misslyckas om skicka via autentisering har valts men registreringen av dess anslutningen misslyckas har åtgärdats.
* Ett problem som orsakar Azure AD Connect-guiden för att kringgå verifiering kontrollerar på inloggningsmetod valt när skrivbords-SSO-funktionen är aktiverad har åtgärdats.

Återställning av lösenord
* Ett problem som kan orsaka Azure AAD Connect-servern för att försöka ansluta igen om anslutningen avbröts av en brandvägg eller proxyserver inte har åtgärdats.

**Nya funktioner/förbättringar:**

Azure AD Connect-synkronisering
* Get-ADSyncScheduler cmdlet returnerar nu en ny boolesk egenskap med namnet SyncCycleInProgress. Om det returnerade värdet är true, innebär det att det finns en cykel för schemalagd synkronisering pågår.
* Målmappen för lagring av Azure AD Connect-installationen och installationsloggar har flyttats från %localappdata%\AADConnect till %programdata%\AADConnect att förbättra tillgängligheten till loggfilerna.

AD FS-hantering
* Stöd har lagts till för att uppdatera SSL-certifikat för AD FS-servergruppen.
* Stöd har lagts till för att hantera AD FS 2016.
* Du kan nu ange befintliga gMSA (Grupphanterat tjänstkonto) under installationen av AD FS.
* Du kan nu konfigurera SHA-256 som signaturens hashalgoritm för Azure AD-förtroende för förlitande part.

Återställning av lösenord
* Introducerade förbättringar så att produkten ska fungera i miljöer med strängare brandväggsregler.
* Förbättrad anslutning tillförlitlighet till Azure Service Bus.

## <a name="113800"></a>1.1.380.0
Utgiven: December 2016

**Ett problem har åtgärdats:**

* På ett problem har åtgärdats där issuerid anspråksregel för Active Directory Federation Services (AD FS) saknas i den här versionen.

>[!NOTE]
>Den här versionen är inte tillgängligt för kunder via funktionen Azure AD Connect automatiskt uppgradera.

## <a name="113710"></a>1.1.371.0
Utgiven: December 2016

**Kända problem:**

* Anspråksregel issuerid för AD FS saknas i den här versionen. Anspråksregel issuerid krävs om du federerar flera domäner med Azure Active Directory (AD Azure). Om du använder Azure AD Connect för att hantera din lokala AD FS-distribution, uppgradera till den här bygger tar bort den befintliga regeln för issuerid-anspråket från AD FS-konfigurationen. Du kan undvika problemet genom att lägga till anspråksregel issuerid efter installationen eller uppgraderingen. Information om hur du lägger till issuerid anspråksregelmallar, finns den här artikeln på [stöd för flera domäner för federering med Azure AD](active-directory-aadconnect-multiple-domains.md).

**Ett problem har åtgärdats:**

* Om Port 9090 inte är öppen för utgående anslutningar, misslyckas Azure AD Connect-installationen eller uppgraderingen.

>[!NOTE]
>Den här versionen är inte tillgängligt för kunder via funktionen Azure AD Connect automatiskt uppgradera.

## <a name="113700"></a>1.1.370.0
Utgiven: December 2016

**Kända problem:**

* Anspråksregel issuerid för AD FS saknas i den här versionen. Anspråksregel issuerid krävs om du federerar flera domäner med Azure AD. Om du använder Azure AD Connect för att hantera din lokala AD FS-distribution, uppgradera till den här bygger tar bort den befintliga regeln för issuerid-anspråket från AD FS-konfigurationen. Du kan undvika problemet genom att lägga till anspråksregel issuerid efter installationen eller uppgraderingen. Information om hur du lägger till issuerid anspråksregelmallar, finns den här artikeln på [stöd för flera domäner för federering med Azure AD](active-directory-aadconnect-multiple-domains.md).
* Port 9090 måste vara öppna utgående att slutföra installationen.

**Nya funktioner:**

* Direktautentisering (förhandsversion).

>[!NOTE]
>Den här versionen är inte tillgängligt för kunder via funktionen Azure AD Connect automatiskt uppgradera.

## <a name="113430"></a>1.1.343.0
Utgiven: November 2016

**Kända problem:**

* Anspråksregel issuerid för AD FS saknas i den här versionen. Anspråksregel issuerid krävs om du federerar flera domäner med Azure AD. Om du använder Azure AD Connect för att hantera din lokala AD FS-distribution, uppgradera till den här bygger tar bort den befintliga regeln för issuerid-anspråket från AD FS-konfigurationen. Du kan undvika problemet genom att lägga till anspråksregel issuerid efter installationen eller uppgraderingen. Information om hur du lägger till issuerid anspråksregelmallar, finns den här artikeln på [stöd för flera domäner för federering med Azure AD](active-directory-aadconnect-multiple-domains.md).

**Åtgärdade problem:**

* Ibland misslyckas installerar Azure AD Connect eftersom det inte går att skapa ett lokalt tjänstkonto vars lösenord uppfyller nivå av komplexitet som anges av organisationens lösenordsprincip.
* Ett problem har åtgärdats där join regler inte omvärderas när ett objekt i anslutarplatsen samtidigt blir utanför omfattningen för en join regel och bli omfattade för en annan. Detta kan inträffa om du har två eller flera join regler vars kopplingsvillkor är ömsesidigt uteslutande.
* Ett problem har åtgärdats där regler för inkommande synkronisering (från Azure AD), som inte innehåller regler för koppling, bearbetas inte om de har lägre prioritet värden än de som innehåller regler för koppling.

**Förbättringar:**

* Stöd har lagts till för att installera Azure AD Connect i Windows Server 2016 Standard eller högre.
* Stöd har lagts till för att använda SQL Server 2016 som fjärrdatabasen för Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Utgiven: Augusti 2016

**Åtgärdade problem:**

* Ändringar för att synkronisera intervall äger inte rum förrän efter nästa synkroniseringscykel har slutförts.
* Azure AD Connect-guiden accepterar inte en Azure AD-konto vars användarnamn som börjar med ett understreck (\_).
* Azure AD Connect-guiden inte kan autentisera Azure AD-kontot om kontolösenordet innehåller för många specialtecken. Felmeddelandet ”Det går inte att verifiera autentiseringsuppgifterna. Ett oväntat fel har uppstått ”. returneras.
* Avinstallera mellanlagring server inaktiverar synkronisering av lösenord i Azure AD-klient och orsakar Lösenordssynkronisering att misslyckas med aktiva servern.
* Lösenordssynkronisering misslyckas i ovanliga fall när det finns inga lösenords-hash som lagras på användaren.
* När Azure AD Connect-servern har aktiverats för mellanlagringsläge, inaktiveras inte tillbakaskrivning av lösenord tillfälligt.
* Azure AD Connect-guiden Visa inte den faktiska Lösenordssynkronisering och konfigurationen för tillbakaskrivning av lösenord när servern är i mellanlagringsläge. Den visar alltid dem som inaktiverade.
* Konfigurationsändringar i Lösenordssynkronisering och tillbakaskrivning av lösenord är inte beständiga av Azure AD Connect-guiden när servern är i mellanlagringsläge.

**Förbättringar:**

* Uppdatera cmdleten Start-ADSyncSyncCycle för att ange om det är att kunna starta en ny synkroniseringscykel eller inte.
* Lägga till cmdleten Stop-ADSyncSyncCycle för att avsluta synkroniseringscykel och åtgärden, som är för närvarande pågår.
* Uppdatera cmdleten Stop-ADSyncScheduler för att avsluta synkroniseringscykel och åtgärden, som är för närvarande pågår.
* När du konfigurerar [katalogtillägg](active-directory-aadconnectsync-feature-directory-extensions.md) i Azure AD Connect-guiden, Azure AD-attribut av typen ”string Teletex” kan nu vara markerade.

## <a name="111890"></a>1.1.189.0
Utgiven: Juni 2016

**Åtgärdade problem och förbättringar:**

* Azure AD Connect kan nu installeras på en server som är FIPS-kompatibel.
  * Synkronisering av lösenord finns [lösenordshashsynkronisering och FIPS](active-directory-aadconnectsync-implement-password-hash-synchronization.md#password-hash-synchronization-and-fips).
* Ett problem har åtgärdats där ett NetBIOS-namn kunde inte kan matchas med det fullständiga Domännamnet i Active Directory-koppling.

## <a name="111800"></a>1.1.180.0
Utgiven: Maj 2016

**Nya funktioner:**

* Varnar och hjälper dig att kontrollera domäner om du inte innan du kör Azure AD Connect.
* Lagt till stöd för [Microsoft Cloud Tyskland](active-directory-aadconnect-instances.md#microsoft-cloud-germany).
* Lagt till stöd för senast [Microsoft Azure Government-molnet](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) infrastruktur med nya URL-kraven.

**Åtgärdade problem och förbättringar:**

* Lägga till filtrering till synkronisering Rule Editor för att göra det lättare att hitta Synkroniseringsregler.
* Bättre prestanda när du tar bort en anslutarplats.
* Ett problem har åtgärdats när samma objekt har både bort och lagts till i samma kör (kallas ta bort/Lägg till).
* En inaktiverad synkronisering regeln inte längre återaktiverar ingår objekt och uppdatera attribut på schemat för uppgradering eller katalogen.

## <a name="111300"></a>1.1.130.0
Utgiven: April 2016

**Nya funktioner:**

* Lagt till stöd för flera värden attribut till [katalogtillägg](active-directory-aadconnectsync-feature-directory-extensions.md).
* Lagt till stöd för flera configuration varianter av [automatisk uppgradering](active-directory-aadconnect-feature-automatic-upgrade.md) anses vara kvalificerade för uppgradering.
* Vissa cmdletar för har lagts till [anpassade scheduler](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Utgiven: Mars 2016

**Åtgärdade problem:**

* Gjort stöds att Express-installationen inte kan användas på Windows Server 2008 (pre-R2) eftersom synkronisera lösenord inte med det här operativsystemet.
* Uppgradera från DirSync med en konfiguration av anpassat filter fungerar inte som förväntat.
* När du uppgraderar till en nyare version och det finns inga ändringar i konfigurationen, bör det inte att schemalägga en fullständig import eller synkronisering.

## <a name="111100"></a>1.1.110.0
Utgiven: Februari 2016

**Åtgärdade problem:**

* Uppgradera från tidigare versioner fungerar inte om installationen inte är i mappen C:\Program Files standard.
* Om du installerar och rensa **starta synkroniseringsprocessen** i slutet av installationsguiden och kör guiden Installera en andra gång kommer det inte att scheduler.
* Scheduler fungerar inte som förväntat på servrar där US-en datum/tid-format inte används. Blockeras även `Get-ADSyncScheduler` att returnera rätt gånger.
* Om du har installerat en tidigare version av Azure AD Connect med AD FS som inloggningsalternativ och uppgraderingen kan köra du inte installationsguiden igen.

## <a name="111050"></a>1.1.105.0
Utgiven: Februari 2016

**Nya funktioner:**

* [Automatisk uppgradering](active-directory-aadconnect-feature-automatic-upgrade.md) funktionen för snabb inställningar kunder.
* Stöd för den globala administratören med hjälp av Azure Multi-Factor Authentication och Privileged Identity Management i installationsguiden.
  * Du måste tillåta att också tillåta trafik till proxyn https://secure.aadcdn.microsoftonline-p.com om du använder multi-Factor Authentication.
  * Du måste lägga till https://secure.aadcdn.microsoftonline-p.com till listan över betrodda platser för multi-Factor Authentication ska fungera korrekt.
* Tillåt ändrar användarens inloggningsmetod efter den första installationen.
* Tillåt [domän och Organisationsenhet filtrering](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) i installationsguiden. Detta kan också ansluta till skogar där inte alla domäner som är tillgängliga.
* [Scheduler](active-directory-aadconnectsync-feature-scheduler.md) är inbyggd i Synkroniseringsmotorn.

**Funktioner som befordras från förhandsversion till allmänt tillgänglig:**

* [Tillbakaskrivning av enhet](active-directory-aadconnect-feature-device-writeback.md).
* [Katalogtillägg](active-directory-aadconnectsync-feature-directory-extensions.md).

**Nya funktioner i förhandsversion:**

* Den nya standarden synkronisera cykel är 30 minuter. Används för att vara tre timmar för alla tidigare versioner. Lägger till stöd för att ändra den [scheduler](active-directory-aadconnectsync-feature-scheduler.md) beteende.

**Åtgärdade problem:**

* Sidan Kontrollera DNS-domäner inte alltid känner igen domänerna.
* Frågar efter autentiseringsuppgifter för domänen administratör när du konfigurerar AD FS.
* Lokalt AD-konton som inte identifieras av installationsguiden om finns i en domän med ett annat DNS-träd än rotdomänen.

## <a name="1091310"></a>1.0.9131.0
Utgiven: December 2015

**Åtgärdade problem:**

* Lösenordssynkronisering fungerar inte när du ändrar lösenord i Active Directory Domain Services (AD DS), men fungerar när du anger ett lösenord.
* När du har en proxyserver, autentisering till Azure AD kan misslyckas under installationen, eller om en uppgradering har avbrutits på konfigurationssidan.
* Det går inte att uppdatera från en tidigare version av Azure AD Connect med en fullständig SQL Server-instans om du inte är en SQL Server-systemadministratör (SA).
* Uppdaterar från en tidigare version av Azure AD Connect med en fjärransluten SQL Server visar felet ”Det gick inte att få åtkomst till ADSync SQL-databas”.

## <a name="1091250"></a>1.0.9125.0
Utgiven: November 2015

**Nya funktioner:**

* Konfigurera AD FS till Azure AD-förtroende.
* Du kan uppdatera Active Directory-schemat och återskapa Synkroniseringsregler.
* Inaktivera en synkroniseringsregel för.
* Kan definiera ”AuthoritativeNull” som en ny literal i en synkroniseringsregel.

**Nya funktioner i förhandsversion:**

* [Azure AD Connect Health för synkronisering](../connect-health/active-directory-aadconnect-health-sync.md).
* Stöd för [Azure AD Domain Services](../user-help/active-directory-passwords-update-your-own-password.md) Lösenordssynkronisering.

**Nytt scenario som stöds:**

* Har stöd för flera lokala Exchange-organisationer. Mer information finns i [hybriddistributioner med flera Active Directory-skogar](https://technet.microsoft.com/library/jj873754.aspx).

**Åtgärdade problem:**

* Synkroniseringsproblem för lösenord:
  * Ett objekt som flyttas från utanför omfattningen till omfattade inte lösenordet synkroniseras. Detta inkluderar både OU- och attributfiltrering.
  * Att välja en ny Organisationsenhet att inkludera synkroniserade kräver inte en fullständig Lösenordssynkronisering.
  * När en inaktiverad användare aktiveras synkroniserar inte lösenord.
  * Återförsök omförsöksintervall är oändliga och har tagits bort tidigare högst 5 000 objekt kommer att dras in.
* Det går inte att ansluta till Active Directory med Windows Server 2016 skogens funktionella nivå.
* Det går inte att ändra den grupp som används för gruppfiltrering efter den första installationen.
* Skapar en ny profil inte längre på Azure AD Connect-servern för varje användare som gör en ändring av lösenord med tillbakaskrivning av lösenord aktiverat.
* Det går inte att använda långt heltal värdena synkroniserade regler omfång.
* Kryssrutan ”tillbakaskrivning av enhet” är inaktiverad om det finns inte går att nå domänkontrollanter.

## <a name="1086670"></a>1.0.8667.0
Utgiven: Augusti 2015

**Nya funktioner:**

* Installationsguiden för Azure AD Connect är nu lokaliserade till alla Windows Server-språk.
* Stöd har lagts till för kontot låsa upp när du använder Azure AD-lösenordshantering.

**Åtgärdade problem:**

* Installationsguiden för Azure AD Connect kraschar om en annan användare fortsätter installationen snarare än den som först startas installationen.
* Om en tidigare avinstallation av Azure AD Connect inte kan avinstalleras Azure AD Connect-synkronisering, går det inte att installera om.
* Det går inte att installera Azure AD Connect med snabbinstallationen om användaren inte är i rotdomänen i skogen eller om du använder en icke-engelsk version av Active Directory.
* Om det fullständiga Domännamnet för Active Directory-användarkonto inte kan matchas visas vilseledande felmeddelandet ”Det gick inte att genomföra schemat”.
* Om det konto som används på den Active Directory-koppling har ändrats utanför guiden, kommer guiden att misslyckas på efterföljande körningar.
* Azure AD Connect inte ibland kan installeras på en domänkontrollant.
* Det går inte att aktivera och inaktivera ”mellanlagringsläge” om tilläggsattribut har lagts till.
* Tillbakaskrivning av lösenord misslyckas i vissa konfigurationer på grund av ett felaktigt lösenord på den Active Directory-koppling.
* DirSync kan inte uppgraderas om ett unikt namn (DN) används i attributfiltrering.
* Överdriven processoranvändning när du använder återställning av lösenord.

**Borttagna förhandsversionsfunktioner:**

* Förhandsversionsfunktionen [tillbakaskrivning av användare](active-directory-aadconnect-feature-preview.md#user-writeback) tillfälligt togs bort baserat på feedback från våra kunder för förhandsversionen. Det läggs igen senare när vi har åtgärdat den tillhandahållna feedbacken.

## <a name="1086410"></a>1.0.8641.0
Utgiven: Juni 2015

**Första versionen av Azure AD Connect.**

Ändrade namn från Azure AD Sync till Azure AD Connect.

**Nya funktioner:**

* [Standardinställningar](active-directory-aadconnect-get-started-express.md) installation
* Kan [konfigurera AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
* Kan [uppgradera från DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md)
* [Förhindra oavsiktliga borttagningar](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
* Introducerade [mellanlagringsläge](active-directory-aadconnectsync-operations.md#staging-mode)

**Nya funktioner i förhandsversion:**

* [Tillbakaskrivning av användare](active-directory-aadconnect-feature-preview.md#user-writeback)
* [Tillbakaskrivning av grupp](active-directory-aadconnect-feature-preview.md#group-writeback)
* [Tillbakaskrivning av enhet](active-directory-aadconnect-feature-device-writeback.md)
* [Katalogtillägg](active-directory-aadconnect-feature-preview.md)

## <a name="104940501"></a>1.0.494.0501
Utgiven: Maj 2015

**Nya krav:**

* Azure AD Sync nu kräver .NET Framework version 4.5.1 installeras.

**Åtgärdade problem:**

* Tillbakaskrivning av lösenord från Azure AD misslyckas med ett Azure Service Bus-anslutningsfel.

## <a name="104910413"></a>1.0.491.0413
Utgiven: April 2015

**Åtgärdade problem och förbättringar:**

* Active Directory-koppling bearbetar inte tar bort korrekt om Papperskorgen är aktiverad och det finns flera domäner i skogen.
* Prestanda för importåtgärder har förbättrats för Azure Active Directory Connector.
* När en grupp har överskridit gränsen för medlemskap (gränsen är som standard till 50 000 objekt), gruppen har tagits bort i Azure Active Directory. Med det nya beteendet gruppen tas inte bort, ett fel inträffar och nya Medlemskapsändringar exporteras inte.
* Det går inte att etablera ett nytt objekt om det finns redan en mellanlagrad borttagning med samma DN i anslutningsplatsen.
* Vissa objekt markeras för synkronisering vid en Deltasynkronisering trots att ingen ändring som mellanlagras på objektet.
* Tvinga fram en Lösenordssynkronisering tar också bort önskade DC-listan.
* CSExportAnalyzer har problem med stater som objekt.

**Nya funktioner:**

* En koppling kan nu ansluta till ”ANY”-objekttypen i MV.

## <a name="104850222"></a>1.0.485.0222
Utgiven: Februari 2015

**Förbättringar:**

* Förbättrad import prestanda.

**Åtgärdade problem:**

* Lösenordssynkronisering godkänner cloudFiltered attributet som används av attributfiltrering. Filtrerade objekten inte längre är inom omfånget för Lösenordssynkronisering.
* Lösenordssynkronisering fungerar inte i ovanliga situationer där topologin har många domänkontrollanter.
* ”Stoppad server” när du importerar från Azure AD Connector efter enhetshantering har aktiverats i Azure AD/Intune.
* Ansluta till externa säkerhetsobjekt (FSP: er) från flera domäner i samma skog orsakar ett tvetydigt join-fel.

## <a name="104751202"></a>1.0.475.1202
Utgiven: December 2014

**Nya funktioner:**

* Lösenordssynkronisering med attributet-baserad filtrering stöds nu. Mer information finns i [Lösenordssynkronisering med filtrering](active-directory-aadconnectsync-configure-filtering.md).
* Attributet msDS-ExternalDirectoryObjectID skrivs tillbaka till Active Directory. Den här funktionen lägger till stöd för Office 365-program. OAuth2 används för att komma åt Online och On-Premises postlådor på en Exchange-Hybridinstallation.

**Fast uppgraderingsproblem:**

* En nyare version av inloggningsassistenten är tillgänglig på servern.
* En anpassad installationssökväg användes för att installera Azure AD Sync.
* Ett ogiltigt anpassat join kriterium blockerar uppgraderingen.

**Andra korrigeringar:**

* Fasta mallarna för Office Pro Plus.
* Fast installationsproblem på grund av användarnamn som börjar med ett bindestreck.
* Fast att förlora sourceAnchor-inställningen när du kör installationsguiden av en andra gång.
* Fast ETW-spårning för Lösenordssynkronisering.

## <a name="104701023"></a>1.0.470.1023
Utgiven: Oktober 2014

**Nya funktioner:**

* Lösenordssynkronisering från flera lokala Active Directory till Azure AD.
* Lokaliserade installation Användargränssnittet till alla Windows Server-språk.

**Uppgradera från AADSync 1.0 GA**

Om du redan har installerats för Azure AD Sync, finns ytterligare ett steg du måste vidta om du har ändrat något av synkroniseringsreglerna som out-of-box. När du har uppgraderat till 1.0.470.1023 släpp synkroniseringen regler som du har ändrat dupliceras. För varje ändrad synkroniseringsregel gör du följande:

1.  Leta upp synkroniseringsregel du har ändrat och notera ändringarna.
* Ta bort synkroniseringsregel.
* Leta upp den nya sync-regeln som skapats av Azure AD Sync och tillämpa ändringarna.

**Behörigheter för Active Directory-konto**

Active Directory-konto måste beviljas ytterligare behörigheter för att kunna läsa lösenords-hash från Active Directory. Behörighet att bevilja kallas ”Replikera katalogändringar” och ”replikerar Directory ändras alla”. Båda behörigheter krävs för att kunna läsa lösenords-hash.

## <a name="104190911"></a>1.0.419.0911
Utgiven: September 2014

**Första versionen av Azure AD Sync.**

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
