---
title: 'Azure AD Connect: Konton och behörigheter | Microsoft Docs'
description: Det här avsnittet beskrivs de konton som används och skapas och behörigheter som krävs.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d145407331ed652f21510483b51a4617bf28e2fa
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56879126"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: Konton och behörigheter

## <a name="accounts-used-for-azure-ad-connect"></a>Konton som används för Azure AD Connect

![](media/reference-connect-accounts-permissions/account5.png)

Azure AD Connect använder 3-konton för att kunna synkronisera information från en lokal eller Windows Server Active Directory till Azure Active Directory.  Dessa konton är:

- **AD DS-anslutningskontot**: används för att läsa/skriva information till Windows Server Active Directory

- **ADSync tjänstkonto**: används för att köra synkroniseringstjänsten och få åtkomst till SQL-databasen

- **Azure AD-anslutningskontot**: används för att skriva information till Azure AD

Förutom dessa tre konton som används för att köra Azure AD Connect, måste du också följande ytterligare konton att installera Azure AD Connect.  Dessa är:

- **Lokalt administratörskonto**: Den administratör som installerar Azure AD Connect och vem som har behörighet som lokal administratör på datorn.

- **Administratörskontot för AD DS Enterprise**: Du kan också används för att skapa kontot ”AD DS-koppling” ovan.

- **Azure AD globala administratörskonto**: används för att skapa konto för Azure AD-koppling och konfigurera Azure AD.

- **SQL-SA-kontot (valfritt)**: används för att skapa ADSync-databas när du använder den fullständiga versionen av SQL Server.  Den här SQL-servern kan vara lokal eller fjärransluten till Azure AD Connect-installationen.  Det här kontot kan vara samma konto som företagsadministratör.  Distribuera databasen nu utförs out of band av SQL-administratören och installeras sedan av Azure AD Connect-administratören med databasägarrättigheter.  Information om den här finns [installera Azure AD Connect med SQL-delegerade administratörsbehörigheter](how-to-connect-install-sql-delegation.md)

## <a name="installing-azure-ad-connect"></a>Installerar Azure AD Connect
Installationsguiden för Azure AD Connect innehåller två olika sökvägar:

* I inställningarna för Express-guiden kräver fler privilegier.  Det här är så att den kan konfigurera din konfiguration enkelt, utan att behöva skapa användare eller konfigurera behörigheter.
* I anpassade inställningar erbjuder i guiden fler alternativ och alternativ. Det finns dock vissa situationer där du behöver se till att du har tillräckliga behörigheter för dig själv.



## <a name="express-settings-installation"></a>Snabbinstallation av inställningar
I standardinställningar frågar guiden för följande:

  - Autentiseringsuppgifter för Företagsadministratörer för AD DS
  - Autentiseringsuppgifter för Azure AD Global administratör

### <a name="ad-ds-enterprise-admin-credentials"></a>AD DS företagsadministratörautentiseringsuppgifter
Företagsadministratör för AD DS-kontot används för att konfigurera din lokala Active Directory. Dessa autentiseringsuppgifter används bara under installationen och används inte när installationen har slutförts. Enterprise-administratör inte Domänadministratörer bör kontrollera att behörigheter i Active Directory kan ställas in i alla domäner.

Om du uppgraderar från DirSync används Företagsadministratörer för AD DS-autentiseringsuppgifter för att återställa lösenordet för det konto som används av DirSync. Du måste också Global administratör för Azure AD-autentiseringsuppgifter.

### <a name="azure-ad-global-admin-credentials"></a>Autentiseringsuppgifter för Azure AD Global administratör
Dessa autentiseringsuppgifter används bara under installationen och används inte när installationen har slutförts. Den används för att skapa Azure AD Connector-kontot som används för synkronisering ändringar till Azure AD. Kontot kan också synkronisera som en funktion i Azure AD.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>Behörigheter som krävs för AD DS-anslutningskontot för standardinställningar
AD DS-anslutningskontot för läsning och skrivning till Windows Server AD och har följande behörigheter när du skapade med standardinställningar:

| Behörighet | Används för |
| --- | --- |
| <li>Replikera katalogändringar</li><li>Replikera katalogen ändras alla |Lösenordets hash-synkronisering |
| Läs/Skriv alla egenskaper för användare |Import- och Exchange-hybrid |
| Läs/Skriv alla egenskaper iNetOrgPerson |Import- och Exchange-hybrid |
| Gruppen för Läs/Skriv alla egenskaper |Import- och Exchange-hybrid |
| Läs/Skriv alla egenskaper kontakta |Import- och Exchange-hybrid |
| Återställa lösenord |Förberedelser för att aktivera tillbakaskrivning av lösenord |

### <a name="express-installation-wizard-summary"></a>Snabbinstallation guiden sammanfattning

![Snabbinstallation](./media/reference-connect-accounts-permissions/express.png)

Följande är en sammanfattning av expressinstallationsfiler sidorna i guiden, de autentiseringsuppgifter som samlats in och hur de används.

| Guidesidan | Autentiseringsuppgifter samlas in | Behörigheter som krävs | Används för |
| --- | --- | --- | --- |
| Gäller inte |Användaren som kör installationsguiden |Administratör för den lokala servern |<li>Skapar ADSync kontot som används för att köra synkroniseringstjänsten. |
| Anslut till Azure AD |Autentiseringsuppgifter för Azure AD-katalog |Rollen som global administratör i Azure AD |<li>Aktiverar synkronisering i Azure AD-katalog.</li>  <li>Skapa ett Azure AD Connector-konto som används för pågående synkroniseringsåtgärder i Azure AD.</li> |
| Anslut till AD DS |Den lokala Active Directory-autentiseringsuppgifter |Medlem i gruppen Enterprise administratörer (EA) i Active Directory |<li>Skapar AD DS-anslutningskontot i Active Directory och ger behörighet till den. Om du skapade kontot används för att läsa och skriva kataloginformation under synkroniseringen.</li> |


## <a name="custom-installation-settings"></a>Inställningar för anpassad installation

Med anpassade inställningar-installationen erbjuder guiden fler alternativ och alternativ. 

### <a name="custom-installation-wizard-summary"></a>Anpassad installation sammanfattning

Följande är en sammanfattning av anpassad installation sidorna i guiden, de autentiseringsuppgifter som samlats in och hur de används.

![Snabbinstallation](./media/reference-connect-accounts-permissions/customize.png)

| Guidesidan | Autentiseringsuppgifter samlas in | Behörigheter som krävs | Används för |
| --- | --- | --- | --- |
| Gäller inte |Användaren som kör installationsguiden |<li>Administratör för den lokala servern</li><li>Om du använder en fullständig SQL Server, måste användaren vara systemadministratörskontot (SA) i SQL</li> |Som standard skapar du det lokala kontot som används som synkroniseringstjänstkontot för motorn. Kontot skapas endast när administratören inte anger ett visst konto. |
| Installera synkroniseringstjänsterna, tjänsten kontoalternativ |AD eller lokala autentiseringsuppgifter för konto |Användare, behörigheter beviljas av installationsguiden |Om administratören anger ett konto, används det här kontot som tjänstkontot för synkroniseringstjänsten. |
| Anslut till Azure AD |Autentiseringsuppgifter för Azure AD-katalog |Rollen som global administratör i Azure AD |<li>Aktiverar synkronisering i Azure AD-katalog.</li>  <li>Skapa ett Azure AD Connector-konto som används för pågående synkroniseringsåtgärder i Azure AD.</li> |
| Anslut dina kataloger |Den lokala Active Directory-autentiseringsuppgifter för varje skog som är ansluten till Azure AD |Behörigheterna som beror på vilka funktioner du aktiverar och finns i skapa AD DS-anslutningskontot |Det här kontot används för att läsa och skriva kataloginformation under synkroniseringen. |
| AD FS-servrar |För varje server i listan, guiden samlar in autentiseringsuppgifter när inloggningsuppgifterna för användaren som kör guiden är tillräckligt för att ansluta |Domänadministratören |Installation och konfiguration av AD FS-serverrollen. |
| Web application proxy-servrar |För varje server i listan, guiden samlar in autentiseringsuppgifter när inloggningsuppgifterna för användaren som kör guiden är tillräckligt för att ansluta |Lokal administratör på måldatorn. |Installation och konfiguration av WAP-serverrollen. |
| Autentiseringsuppgifter för proxyförtroende |Autentiseringsuppgifter för proxyförtroende Federation service (autentiseringsuppgifter proxyn använder för att registrera för ett betrott certifikat från FS |Domänkonto som är lokal administratör på AD FS-servern |Den första registreringen av FS WAP betrott certifikat. |
| Sidan för AD FS-tjänstkontot, ”använda en domän användare kontoalternativet” |Autentiseringsuppgifter för AD-användare |Domain user |AD-användarkonto vars autentiseringsuppgifter tillhandahålls används som inloggningskontot för AD FS-tjänsten. |

### <a name="create-the-ad-ds-connector-account"></a>Skapa AD DS-anslutningskontot

>[!IMPORTANT]
>En ny PowerShell-modul namngivna ADSyncConfig.psm1 introducerades med build **1.1.880.0** (släpptes i augusti 2018) som innehåller en samling av cmdletar för att konfigurera rätt Active Directory-behörigheter för Azure AD DS Anslutningskontot.
>
>Mer information finns i [Azure AD Connect: Konfigurera AD DS-Connector-kontot behörighet](how-to-connect-configure-ad-ds-connector-account.md)

Det konto som du anger på den **Anslut dina kataloger** sidan måste finnas i Active Directory före installationen.  Azure AD Connect version 1.1.524.0 och senare har alternativet för att låta Azure AD Connect-guiden skapa den **AD DS-anslutningskontot** används för att ansluta till Active Directory.  

Det måste också ha behörighet som beviljas. Installationsguiden kontrollerar inte behörigheterna och eventuella problem finns endast under synkroniseringen.

Vilka behörigheter som du behöver beror på de valfria funktionerna aktivera du. Om du har flera domäner, måste behörigheterna som beviljas i alla domäner i skogen. Om du inte aktivera någon av dessa funktioner, standard **domänanvändare** har rätt behörighet.

| Funktion | Behörigheter |
| --- | --- |
| MS-DS-ConsistencyGuid funktion |Skrivbehörighet till attributet ms-DS-ConsistencyGuid som beskrivs i [designbegrepp – med ms-DS-ConsistencyGuid som sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Lösenordets hash-synkronisering |<li>Replikera katalogändringar</li>  <li>Replikera katalogen ändras alla |
| Exchange-hybridinstallation |Skrivbehörighet till de attribut som beskrivs i [tillbakaskrivning av Exchange-hybrid](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) för användare, grupper och kontakter. |
| Offentlig mapp för Exchange-e-post |Läsbehörighet till de attribut som beskrivs i [offentlig mapp för Exchange-e-post](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) för gemensamma mappar. | 
| Tillbakaskrivning av lösenord |Skrivbehörighet till de attribut som beskrivs i [komma igång med lösenordshantering](../authentication/howto-sspr-writeback.md) för användare. |
| Tillbakaskrivning av enheter |Behörigheter som beviljas med ett PowerShell-skript enligt beskrivningen i [tillbakaskrivning av enhet](how-to-connect-device-writeback.md). |
| Tillbakaskrivning av grupp |Gör det möjligt att tillbakaskrivning av **Office 365-grupper** i en skog med Exchange installerad.  Mer information finns i [tillbakaskrivning av grupp](how-to-connect-preview.md#group-writeback).|

## <a name="upgrade"></a>Uppgradera
När du uppgraderar från en version av Azure AD Connect till en ny version, behöver du följande behörigheter:

>[!IMPORTANT]
>Från och med version 1.1.484, introducerade Azure AD Connect en regression bugg som kräver sysadmin-behörighet för att uppgradera SQL-databasen.  Det här problemet korrigeras i bygger 1.1.647.  Om du uppgraderar till den här bygger behöver du sysadmin-behörighet.  Dbo-behörigheter är inte tillräckliga.  Om du försöker uppgradera Azure AD Connect utan sysadmin-behörighet, så misslyckas uppgraderingen och Azure AD Connect kommer inte längre att fungera korrekt efteråt.  Microsoft är medvetna om detta och arbetar med för att åtgärda detta.


| Huvudkonto | Behörigheter som krävs | Används för |
| --- | --- | --- |
| Användaren som kör installationsguiden |Administratör för den lokala servern |Uppdatera binärfiler. |
| Användaren som kör installationsguiden |Medlem i ADSyncAdmins |Göra ändringar i Synkroniseringsregler och annan konfiguration. |
| Användaren som kör installationsguiden |Om du använder en fullständig SQLServer: DBO (eller liknande) av motorn sync-databasen |Kontrollera databasen ändras, till exempel uppdaterar tabeller med nya kolumner. |

## <a name="more-about-the-created-accounts"></a>Mer om kontona
### <a name="ad-ds-connector-account"></a>AD DS-anslutningskontot
Om du använder standardinställningarna skapas ett konto i Active Directory som används för synkronisering. Skapade kontot finns i rotdomänen i skogen i behållaren användare och har namnet med prefixet **MSOL_**. Kontot har skapats med ett långt komplexa lösenord som inte upphör att gälla. Om du har en lösenordsprincip i din domän, se till att långa och komplexa lösenord ska tillåtas för det här kontot.

![AD-konto](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

Om du använder anpassade inställningar, är du ansvarig för att skapa kontot innan du påbörjar installationen.  Se Skapa AD DS-anslutningskontot.

### <a name="adsync-service-account"></a>ADSync-tjänstkontot
Synkroniseringstjänsten kan köras under olika konton. Det kan köras en **virtuellt tjänstkonto** (VSA), en **Grupphanterat tjänstkonto** (gMSA/sMSA), eller ett vanligt användarkonto. Alternativ som stöds har ändrats med 2017 April version av Connect när du gör en ny installation. Dessa ytterligare alternativ är inte tillgängliga om du uppgraderar från en tidigare version av Azure AD Connect.

| Typ av konto | Installationsalternativ | Beskrivning |
| --- | --- | --- |
| [Virtuellt tjänstkonto](#virtual-service-account) | Snabb och anpassade, 2017 April och senare | Detta är det alternativ som används för alla express-installationer, förutom installationer på en domänkontrollant. För anpassade är det standardalternativet, såvida inte används av ett annat alternativ. |
| [Grupphanterat tjänstkonto](#group-managed-service-account) | Anpassade, 2017 April och senare | Om du använder en fjärransluten SQLServer, sedan rekommenderar vi för att använda en grupphanterade tjänstkontot. |
| [Användarkonto](#user-account) | Snabb och anpassade, 2017 April och senare | Ett användarkonto som föregås av prefixet AAD_ skapas endast under installationen när du installerade på Windows Server 2008 och installeras på en domänkontrollant. |
| [Användarkonto](#user-account) | Snabb och anpassade, 2017 mars och tidigare | Ett lokalt konto prefixet AAD_ skapas under installationen. När du använder anpassad installation, kan du ange ett annat konto. |

Om du använder Anslut med en version från 2017 mars eller tidigare, sedan bör du inte återställa lösenordet till tjänstkontot eftersom Windows förstör krypteringsnycklarna av säkerhetsskäl. Du kan inte ändra kontot till ett annat konto utan att installera om Azure AD Connect. Om du uppgraderar till en version från 2017 April eller senare, är det stöds om du vill ändra lösenordet till tjänstkontot, men du kan inte ändra det konto som används.

> [!Important]
> Du kan bara ange kontot på första installationen. Det går inte för att ändra tjänstkontot när installationen har slutförts.

Det här är en tabell med standardvärdet, alternativ för rekommenderade och stöds för synkroniseringstjänstkontot.

Förklaring:

- **Fet** anger standardalternativet och i de flesta fall det rekommenderade alternativet.
- *Kursiv* anger det rekommenderade alternativet om den inte är standardalternativet.
- 2008 - standardalternativet när installerad på Windows Server 2008
- Icke-fet - alternativ som stöds
- Local account - Local user account on the server
- Domain account - Domain user account
- sMSA - [fristående Hanterat tjänstkonto](https://technet.microsoft.com/library/dd548356.aspx)
- gMSA - [gruppkontot Managed Service](https://technet.microsoft.com/library/hh831782.aspx)

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Anpassat | Fjärr-SQL</br>Anpassat |
| --- | --- | --- | --- |
| **fristående/arbetsgruppsdator** | Stöds inte | **VSA**</br>Lokalt konto (2008)</br>Lokalt konto |  Stöds inte |
| **domänanslutna datorer** | **VSA**</br>Lokalt konto (2008) | **VSA**</br>Lokalt konto (2008)</br>Lokalt konto</br>Domänkonto</br>sMSA,gMSA | **gMSA**</br>Domänkonto |
| **Domänkontrollant** | **Domänkonto** | *gMSA*</br>**Domänkonto**</br>sMSA| *gMSA*</br>**Domänkonto**|

#### <a name="virtual-service-account"></a>Virtuellt tjänstkonto
Ett virtuellt tjänstkonto är en särskild typ av konto som inte har ett lösenord och hanteras av Windows.

![VSA](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

Attributet är avsedd att användas med scenarier där Synkroniseringsmotorn och SQL är på samma server. Om du använder fjärr-SQL, sedan rekommenderar vi för att använda ett Grupphanterat tjänstkonto i stället.

Den här funktionen kräver Windows Server 2008 R2 eller senare. Om du installerar Azure AD Connect på Windows Server 2008 och installationen återgår till att använda en [användarkonto](#user-account) i stället.

#### <a name="group-managed-service-account"></a>Grupphanterade tjänstkontot
Om du använder en fjärransluten SQLServer så vi rekommenderar att du använder en **grupphanterat tjänstkonto**. Läs mer om hur du förbereder din Active Directory för gruppen hanterade tjänstkontot [gruppen konton översikt över Grupphanterade](https://technet.microsoft.com/library/hh831782.aspx).

Använder det här alternativet på den [installera nödvändiga komponenter](how-to-connect-install-custom.md#install-required-components) väljer **använder ett befintligt tjänstkonto**, och välj **tjänstkonto**.  
![VSA](./media/reference-connect-accounts-permissions/serviceaccount.png)  
Det går också för att använda en [fristående Hanterat tjänstkonto](https://technet.microsoft.com/library/dd548356.aspx). Men de kan bara användas på den lokala datorn och det finns några fördelar med att använda dem via det virtuella tjänstkontot för standard.

Den här funktionen kräver Windows Server 2012 eller senare. Om du behöver använda ett äldre operativsystem och Använd fjärr-SQL så måste du använda en [användarkonto](#user-account).

#### <a name="user-account"></a>Användarkonto
Ett lokalt tjänstkonto skapas av guiden Installera (såvida du inte anger kontot som ska användas i anpassade inställningar). Kontot är prefixet **AAD_** och används för den faktiska synkroniseringstjänsten för att köra som. Om du installerar Azure AD Connect på en domänkontrollant måste skapas kontot i domänen. Den **AAD_** -tjänstkontot måste finnas i domänen om:
   - du använder en fjärrserver som kör SQLServer
   - du använder en proxyserver som kräver autentisering

![Synkroniseringstjänstkontot](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

Kontot har skapats med ett långt komplexa lösenord som inte upphör att gälla.

Det här kontot används för att lagra lösenord för de andra kontona i ett säkert sätt. Andra konton lösenord lagras krypterade i databasen. Privata nycklar för krypteringsnycklarna är skyddade med kryptografiska hemlig nyckel-kryptering med Windows Data Protection API (DPAPI).

Om du använder en fullständig SQL Server är kontot databasen som har skapats för Synkroniseringsmotorn DBO. Tjänsten kommer inte att fungera som avsett med andra behörigheter. En SQL-inloggning skapas också.

Kontot är också ge behörigheter till filer, registernycklar och andra objekt som rör Synkroniseringsmotorn.

### <a name="azure-ad-connector-account"></a>Azure AD-anslutningskontot
Ett konto i Azure AD har skapats för användning av synkroniseringstjänsten. Det här kontot kan identifieras av dess visningsnamn.

![AD-konto](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

Namnet på konton som används på servern kan identifieras i den andra delen av användarnamnet. I bilden är namnet på DC1. Om du har testservrar, har ett eget konto på varje server.

Kontot har skapats med ett långt komplexa lösenord som inte upphör att gälla. Det har beviljats en särskild roll **konton för katalogsynkronisering** som har endast behörighet att utföra uppgifter för directory-synkronisering. Den här särskilda inbyggd roll kan inte beviljas utanför Azure AD Connect-guiden. Azure-portalen visar det här kontot med rollen **användaren**.

Det finns en gräns på 20 tjänstkonton för synkronisering i Azure AD. Om du vill hämta listan över befintliga Azure AD-tjänstkonton i Azure AD, kör du följande Azure AD PowerShell-cmdlet: `Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Ta bort oanvända Azure AD tjänstkonton, kör du följande Azure AD PowerShell-cmdlet: `Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

## <a name="related-documentation"></a>Relaterad dokumentation
Om du inte Läs i dokumentationen om [integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md), i följande tabell innehåller länkar till närliggande ämnen.

|Avsnitt |Länk|  
| --- | --- |
|Ladda ned Azure AD Connect | [Ladda ned Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Installera med standardinställningar | [Snabbinstallation av Azure AD Connect](how-to-connect-install-express.md)|
|Installera med anpassade inställningar | [Anpassad installation av Azure AD Connect](./how-to-connect-install-custom.md)|
|Uppgradera från DirSync | [Uppgradera från Azure AD-synkroniseringsverktyg (DirSync)](how-to-dirsync-upgrade-get-started.md)|
|Efter installationen | [Verifiera installationen och tilldela licenser](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
