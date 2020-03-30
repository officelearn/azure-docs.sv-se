---
title: 'Azure AD Connect: Konton och behörigheter | Microsoft-dokument'
description: I det här avsnittet beskrivs de konton som används och skapas och behörigheter som krävs.
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
ms.date: 10/03/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6071e6553fb1275fea63a37b4897aef2685bd509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253772"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: Konton och behörigheter

## <a name="accounts-used-for-azure-ad-connect"></a>Konton som används för Azure AD Connect

![översikt över konton](media/reference-connect-accounts-permissions/account5.png)

Azure AD Connect använder 3 konton för att synkronisera information från lokalt eller Windows Server Active Directory till Azure Active Directory.  Dessa konton är:

- **AD DS Connector-konto:** används för att läsa/skriva information till Windows Server Active Directory

- **ADSync-tjänstkonto:** används för att köra synkroniseringstjänsten och komma åt SQL-databasen

- **Azure AD Connector-konto:** används för att skriva information till Azure AD

Utöver dessa tre konton som används för att köra Azure AD Connect behöver du också följande ytterligare konton för att installera Azure AD Connect.  Dessa är:

- **Lokalt administratörskonto**: Administratören som installerar Azure AD Connect och som har lokala administratörsbehörigheter på datorn.

- **AD DS Enterprise Administrator-konto:** Används som tillval för att skapa "AD DS Connector-kontot" ovan.

- **Azure AD Global Administrator-konto:** används för att skapa Azure AD Connector-kontot och konfigurera Azure AD.

- **SQL SA-konto (valfritt):** används för att skapa ADSync-databasen när du använder den fullständiga versionen av SQL Server.  Den här SQL-servern kan vara lokal eller fjärransluten till Azure AD Connect-installationen.  Det här kontot kan vara samma konto som företagsadministratören.  Etablering av databasen kan nu utföras utanför bandet av SQL-administratören och sedan installeras av Azure AD Connect-administratören med databasägarrättigheter.  Information om detta finns [i Installera Azure AD Connect med SQL-delegerade administratörsbehörigheter](how-to-connect-install-sql-delegation.md)


>[!IMPORTANT]
> Från och med version 1.4.###.# stöds det inte längre att använda en företagsadministratör eller ett domänadministratörskonto som AD DS Connector-konto.  Om du försöker ange ett konto som är en företagsadministratör eller domänadministratör när du anger **att det befintliga kontot ska användas**visas ett felmeddelande.

> [!NOTE]
> Det stöds för att hantera administrativa konton som används i Azure AD Connect från en ESAE Administrative Forest (även som kallas "Röd skog").
> Dedikerade administrativa skogar tillåter organisationer att vara värdar för administrativa konton, arbetsstationer och grupper i en miljö som har starkare säkerhetskontroller än produktionsmiljön.
> Mer information om dedikerade administrativa skogar finns i [ESAE Administrative Forest Design Approach](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#esae-administrative-forest-design-approach).

> [!NOTE]
> Rollen Global administratör krävs inte efter den första installationen och det enda obligatoriska kontot är rollkontot **katalogsynkroniseringskonton.** Det betyder inte nödvändigtvis att du bara vill ta bort kontot med rollen Global administratör. Det är bättre att ändra rollen till en mindre kraftfull roll, eftersom helt ta bort kontot kan införa problem om du någonsin behöver köra guiden igen. Genom att minska privilegiet för rollen kan du alltid åter höja privilegierna om du måste använda Azure AD Connect-guiden igen. 

## <a name="installing-azure-ad-connect"></a>Installera Azure AD Connect
Installationsguiden för Azure AD Connect innehåller två olika sökvägar:

* I Express-inställningar kräver guiden fler privilegier.  Detta är så att den enkelt kan konfigurera konfigurationen, utan att du behöver skapa användare eller konfigurera behörigheter.
* I Anpassade inställningar erbjuder guiden fler alternativ och alternativ. Det finns dock vissa situationer där du måste se till att du har rätt behörighet själv.



## <a name="express-settings-installation"></a>Installation av expressinställningar
I Express-inställningar frågar installationsguiden följande:

  - Autentiseringsuppgifter för AD DS Enterprise-administratör
  - Azure AD-globala administratörsautentiseringsuppgifter

### <a name="ad-ds-enterprise-admin-credentials"></a>AD DS Enterprise-administratörsautentiseringsuppgifter
AD DS Enterprise Admin-kontot används för att konfigurera din lokala Active Directory. Dessa autentiseringsuppgifter används endast under installationen och används inte när installationen har slutförts. Företagsadministratören, inte domänadministratören bör se till att behörigheterna i Active Directory kan anges i alla domäner.

Om du uppgraderar från DirSync används AD DS Enterprise Admins-autentiseringsuppgifterna för att återställa lösenordet för kontot som används av DirSync. Du behöver också Azure AD Global Administrator-autentiseringsuppgifter.

### <a name="azure-ad-global-admin-credentials"></a>Azure AD-globala administratörsautentiseringsuppgifter
Dessa autentiseringsuppgifter används endast under installationen och används inte när installationen har slutförts. Det används för att skapa Azure AD Connector-kontot som används för synkronisering av ändringar i Azure AD. Kontot möjliggör också synkronisering som en funktion i Azure AD.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>AD DS Connector-konto krävs behörigheter för snabbinställningar
AD DS Connector-kontot skapas för läsning och skrivning till Windows Server AD och har följande behörigheter när det skapas av expressinställningar:

| Behörighet | Används för |
| --- | --- |
| <li>Replikera katalogändringar</li><li>Replikera katalog ändrar alla |Synkronisering av lösenordsh hash |
| Läs/skriv alla egenskaper Användare |Importera och byta hybrid |
| Läs/skriv alla egenskaper iNetOrgPerson |Importera och byta hybrid |
| Läs/skriv alla egenskaper Grupp |Importera och byta hybrid |
| Läs/skriv alla egenskaper Kontakt |Importera och byta hybrid |
| Återställa lösenord |Förberedelse för att aktivera tillbakaskrivning av lösenord |

### <a name="express-installation-wizard-summary"></a>Sammanfattning av snabbinstallationsguiden

![Snabb installation](./media/reference-connect-accounts-permissions/express.png)

Följande är en sammanfattning av sidorna i snabbinstallationsguiden, de insamlade autentiseringsuppgifterna och vad de används till.

| Guidesida | Insamlade autentiseringsuppgifter | Behörigheter krävs | Används för |
| --- | --- | --- | --- |
| Ej tillämpligt |Användare som kör installationsguiden |Administratör för den lokala servern |<li>Skapar ADSync-tjänstkontot som används för att köra synkroniseringstjänsten. |
| Anslut till Azure AD |Azure AD-katalogautentiseringsuppgifter |Global administratörsroll i Azure AD |<li>Aktivera synkronisering i Azure AD-katalogen.</li>  <li>Skapa Azure AD Connector-konto som används för pågående synkroniseringsåtgärder i Azure AD.</li> |
| Anslut till AD DS |Lokala Active Directory-autentiseringsuppgifter |Medlem i gruppen Företagsadministratörer (EA) i Active Directory |<li>Skapar AD DS Connector-kontot i Active Directory och ger behörighet till det. Det här skapade kontot används för att läsa och skriva kataloginformation under synkroniseringen.</li> |


## <a name="custom-installation-settings"></a>Anpassade installationsinställningar

Med installationen av anpassade inställningar erbjuder guiden fler alternativ och alternativ. 

### <a name="custom-installation-wizard-summary"></a>Sammanfattning av guiden Anpassad installation

Följande är en sammanfattning av de anpassade installationsguidesidorna, de insamlade autentiseringsuppgifterna och vad de används till.

![Snabb installation](./media/reference-connect-accounts-permissions/customize.png)

| Guidesida | Insamlade autentiseringsuppgifter | Behörigheter krävs | Används för |
| --- | --- | --- | --- |
| Ej tillämpligt |Användare som kör installationsguiden |<li>Administratör för den lokala servern</li><li>Om du använder en fullständig SQL Server måste användaren vara Systemadministratör (SA) i SQL</li> |Som standard skapar det lokala konto som används som tjänstkonto för synkroniseringsmotorn. Kontot skapas endast när administratören inte anger ett visst konto. |
| Installera synkroniseringstjänster, alternativet Servicekonto |AD- eller lokala användarkontouppgifter |Användare, behörigheter beviljas av installationsguiden |Om administratören anger ett konto används det här kontot som tjänstkonto för synkroniseringstjänsten. |
| Anslut till Azure AD |Azure AD-katalogautentiseringsuppgifter |Global administratörsroll i Azure AD |<li>Aktivera synkronisering i Azure AD-katalogen.</li>  <li>Skapa Azure AD Connector-konto som används för pågående synkroniseringsåtgärder i Azure AD.</li> |
| Anslut dina kataloger |Lokala Active Directory-autentiseringsuppgifter för varje skog som är ansluten till Azure AD |Behörigheterna beror på vilka funktioner du aktiverar och finns i Skapa AD DS Connector-kontot |Det här kontot används för att läsa och skriva kataloginformation under synkroniseringen. |
| AD FS-servrar |För varje server i listan samlar guiden in autentiseringsuppgifter när inloggningsuppgifterna för den användare som kör guiden är otillräckliga för att ansluta |Domänadministratör |Installation och konfiguration av AD FS-serverrollen. |
| Proxyservrar för webbprogram |För varje server i listan samlar guiden in autentiseringsuppgifter när inloggningsuppgifterna för den användare som kör guiden är otillräckliga för att ansluta |Lokal administratör på målmaskinen |Installation och konfiguration av WAP-serverroll. |
| Autentiseringsuppgifter för proxyförtroende |Autentiseringsuppgifter för federationstjänstförtroende (de autentiseringsuppgifter som proxyn använder för att registrera för ett förtroendecertifikat från FS |Domänkonto som är lokal administratör för AD FS-servern |Första registrering av FS-WAP-förtroendecertifikat. |
| SIDAN AD FS-tjänstkonto, "Använd ett domänanvändarkontoalternativ" |Autentiseringsuppgifter för AD-användarkonton |Domänanvändare |Azure AD-användarkontot vars autentiseringsuppgifter tillhandahålls används som inloggningskonto för AD FS-tjänsten. |

### <a name="create-the-ad-ds-connector-account"></a>Skapa AD DS Connector-kontot

>[!IMPORTANT]
>En ny PowerShell-modul med namnet ADSyncConfig.psm1 introducerades med build **1.1.880.0** (släpptes i augusti 2018) som innehåller en samling cmdlets som hjälper dig att konfigurera rätt Active Directory-behörigheter för Azure AD DS Connector-kontot.
>
>Mer information finns i Azure AD Connect: Konfigurera behörighet för [AD DS-anslutningskonto](how-to-connect-configure-ad-ds-connector-account.md)

Kontot som du anger på sidan **Anslut katalogerna** måste finnas i Active Directory före installationen.  Azure AD Connect version 1.1.524.0 och senare har möjlighet att låta Azure AD Connect-guiden skapa **AD DS Connector-kontot** som används för att ansluta till Active Directory.  

Den måste också ha de behörigheter som krävs beviljade. Installationsguiden verifierar inte behörigheterna och eventuella problem hittas bara under synkroniseringen.

Vilka behörigheter du behöver beror på vilka valfria funktioner du aktiverar. Om du har flera domäner måste behörigheterna beviljas för alla domäner i skogen. Om du inte aktiverar någon av dessa funktioner är **standardbehörigheten för domänanvändare** tillräcklig.

| Funktion | Behörigheter |
| --- | --- |
| ms-DS-Konsekvensguid-funktionen |Skrivbehörigheter till attributet ms-DS-ConsistencyGuid som dokumenteras i [Designkoncept - Använda ms-DS-ConsistencyGuid som sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Synkronisering av lösenordsh hash |<li>Replikera katalogändringar</li>  <li>Replikera katalog ändrar alla |
| Distribution av Exchange-hybrid |Skriv behörigheter till attributen som dokumenteras i [Exchange hybridåterskrivning](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) för användare, grupper och kontakter. |
| Gemensam mapp för Exchange Mail |Läs behörigheter till attributen som dokumenteras i [Exchange Mails gemensamma mapp](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) för gemensamma mappar. | 
| Tillbakaskrivning av lösenord |Skriv behörigheter till de attribut som dokumenteras i [Komma igång med lösenordshantering](../authentication/howto-sspr-writeback.md) för användare. |
| Tillbakaskrivning av enheter |Behörigheter som beviljas med ett PowerShell-skript enligt beskrivningen i [enhetens tillbakaskrivning](how-to-connect-device-writeback.md). |
| Tillbakaskrivning av grupp |Gör att du kan skriva tillbaka **Office 365-grupper** till en skog med Exchange installerat.  Mer information finns i [Gruppåterskrivning](how-to-connect-preview.md#group-writeback).|

## <a name="upgrade"></a>Uppgradera
När du uppgraderar från en version av Azure AD Connect till en ny version behöver du följande behörigheter:

>[!IMPORTANT]
>Från och med build 1.1.484 introducerade Azure AD Connect ett regressionsfel som kräver sysadmin-behörigheter för att uppgradera SQL-databasen.  Denna bugg korrigeras i build 1.1.647.  Om du uppgraderar till den här versionen behöver du sysadmin-behörigheter.  Dbo-behörigheter är inte tillräckliga.  Om du försöker uppgradera Azure AD Connect utan att ha sysadmin-behörigheter misslyckas uppgraderingen och Azure AD Connect fungerar inte längre korrekt efteråt.  Microsoft är medvetet om detta och arbetar för att rätta till detta.


| Främsta | Behörigheter krävs | Används för |
| --- | --- | --- |
| Användare som kör installationsguiden |Administratör för den lokala servern |Uppdatera binärfiler. |
| Användare som kör installationsguiden |Medlem i ADSyncAdmins |Gör ändringar i synkroniseringsregler och annan konfiguration. |
| Användare som kör installationsguiden |Om du använder en fullständig SQL-server: DBO (eller liknande) i synkroniseringsmotordatabasen |Gör ändringar på databasnivå, till exempel uppdatera tabeller med nya kolumner. |

## <a name="more-about-the-created-accounts"></a>Mer om de skapade kontona
### <a name="ad-ds-connector-account"></a>AD DS-anslutningskonto
Om du använder standardinställningar skapas ett konto i Active Directory som används för synkronisering. Det skapade kontot finns i skogsrotdomänen i behållaren Användare och har sitt namn prefixed med **MSOL_**. Kontot skapas med ett långt komplext lösenord som inte upphör att gälla. Om du har en lösenordsprincip i domänen kontrollerar du att långa och komplexa lösenord tillåts för det här kontot.

![AD-konto](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

Om du använder anpassade inställningar är du ansvarig för att skapa kontot innan du startar installationen.  Se Skapa AD DS Connector-kontot.

### <a name="adsync-service-account"></a>ADSync-tjänstkonto
Synkroniseringstjänsten kan köras under olika konton. Det kan köras under ett **virtuellt tjänstkonto** (VSA), ett **grupphantlagt tjänstkonto** (gMSA/sMSA) eller ett vanligt användarkonto. Alternativen som stöds ändrades med 2017 aprilversionen av Connect när du gör en ny installation. Om du uppgraderar från en tidigare version av Azure AD Connect är dessa ytterligare alternativ inte tillgängliga.

| Typ av konto | Installationsalternativ | Beskrivning |
| --- | --- | --- |
| [Konto för virtuell tjänst](#virtual-service-account) | Express och custom, 2017 april och senare | Detta är det alternativ som används för alla expressinstallationer, förutom installationer på en domänkontrollant. För anpassade är det standardalternativet om inte ett annat alternativ används. |
| [Grupphanterat tjänstkonto](#group-managed-service-account) | Custom, 2017 april och senare | Om du använder en fjärr-SQL-server rekommenderar vi att du använder ett grupphanterade tjänstkonto. |
| [Användarkonto](#user-account) | Express och custom, 2017 april och senare | Ett användarkonto som är förinställt med AAD_ skapas endast under installationen när det installeras på Windows Server 2008 och när det installeras på en domänkontrollant. |
| [Användarkonto](#user-account) | Express och custom, 2017 mars och tidigare | Ett lokalt konto som föregås av AAD_ skapas under installationen. När du använder anpassad installation kan ett annat konto anges. |

Om du använder Anslut med en version från 2017 mars eller tidigare bör du inte återställa lösenordet på tjänstkontot eftersom Windows förstör krypteringsnycklarna av säkerhetsskäl. Du kan inte ändra kontot till något annat konto utan att installera om Azure AD Connect. Om du uppgraderar till en version från 2017 april eller senare stöds det att ändra lösenordet på tjänstkontot, men du kan inte ändra kontot som används.

> [!Important]
> Du kan bara ange tjänstkontot vid första installationen. Det stöds inte att ändra tjänstkontot när installationen har slutförts.

Det här är en tabell med standardalternativen, rekommenderade och stöds för synkroniseringstjänstkontot.

Förklaring:

- **Fetstil** anger standardalternativet och i de flesta fall det rekommenderade alternativet.
- *Kursiv anger* det rekommenderade alternativet när det inte är standardalternativet.
- 2008 - Standardalternativ när det installeras på Windows Server 2008
- Alternativ som inte är fetstilt – alternativ som stöds
- Lokalt konto - Lokalt användarkonto på servern
- Domänkonto - Domänanvändarkonto
- sMSA - [fristående konto för hanterade tjänster](https://technet.microsoft.com/library/dd548356.aspx)
- gMSA - [gruppkonto för hanterade tjänster](https://technet.microsoft.com/library/hh831782.aspx)

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Anpassat | Fjärr-SQL</br>Anpassat |
| --- | --- | --- | --- |
| **fristående/arbetsgruppsmaskin** | Stöds inte | **Vsa**</br>Lokalt konto (2008)</br>Lokalt konto |  Stöds inte |
| **domänansluten dator** | **Vsa**</br>Lokalt konto (2008) | **Vsa**</br>Lokalt konto (2008)</br>Lokalt konto</br>Domänkonto</br>sMSA, gMSA | **gMSA (på andra sätt)**</br>Domänkonto |
| **Domänkontrollant** | **Domänkonto** | *gMSA (på andra sätt)*</br>**Domänkonto**</br>Smsa| *gMSA (på andra sätt)*</br>**Domänkonto**|

#### <a name="virtual-service-account"></a>Konto för virtuella tjänster
Ett virtuellt tjänstkonto är en särskild typ av konto som inte har ett lösenord och hanteras av Windows.

![Vsa](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

VSA är avsedd att användas med scenarier där synkroniseringsmotorn och SQL finns på samma server. Om du använder fjärr-SQL rekommenderar vi att du använder ett grupphanterad tjänstkonto i stället.

Den här funktionen kräver Windows Server 2008 R2 eller senare. Om du installerar Azure AD Connect på Windows Server 2008 återgår installationen till att använda ett [användarkonto](#user-account) i stället.

#### <a name="group-managed-service-account"></a>Grupphanterad tjänstkonto
Om du använder en fjärr-SQL-server rekommenderar vi att du använder ett **grupphanterade tjänstkonto**. Mer information om hur du förbereder ditt Active Directory för grupphanterade tjänstkonto finns i [Översikt över grupphanterade tjänstkonton](https://technet.microsoft.com/library/hh831782.aspx).

Om du vill använda det här alternativet väljer du **Använd ett befintligt tjänstkonto**på sidan [Installera obligatoriska komponenter](how-to-connect-install-custom.md#install-required-components) och väljer **Hanterat tjänstkonto**.  
![Vsa](./media/reference-connect-accounts-permissions/serviceaccount.png)  
Det stöds också för att använda ett [fristående hanterat tjänstkonto](https://technet.microsoft.com/library/dd548356.aspx). Dessa kan dock bara användas på den lokala datorn och det finns ingen fördel med att använda dem via standardkontot för virtuella tjänster.

Den här funktionen kräver Windows Server 2012 eller senare. Om du behöver använda ett äldre operativsystem och använda fjärr-SQL måste du använda ett [användarkonto](#user-account).

#### <a name="user-account"></a>Användarkonto
Ett lokalt tjänstkonto skapas av installationsguiden (såvida du inte anger vilket konto som ska användas i anpassade inställningar). Kontot föregås **AAD_** och används för att den faktiska synkroniseringstjänsten ska köras som. Om du installerar Azure AD Connect på en domänkontrollant skapas kontot i domänen. Det **AAD_** tjänstkontot måste finnas i domänen om:
   - du använder en fjärrserver som kör SQL-server
   - du använder en proxy som kräver autentisering

![Synkronisera tjänstkonto](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

Kontot skapas med ett långt komplext lösenord som inte upphör att gälla.

Det här kontot används för att lagra lösenorden för de andra kontona på ett säkert sätt. Dessa andra kontolösenord lagras krypterade i databasen. De privata nycklarna för krypteringsnycklarna skyddas med kryptografiska tjänster secret-key kryptering med Hjälp av Windows Data Protection API (DPAPI).

Om du använder en fullständig SQL Server är tjänstkontot DBO för den skapade databasen för synkroniseringsmotorn. Tjänsten kommer inte att fungera som avsett med andra behörigheter. En SQL-inloggning skapas också.

Kontot beviljas också behörighet till filer, registernycklar och andra objekt som är relaterade till synkroniseringsmotorn.

### <a name="azure-ad-connector-account"></a>Azure AD Connector-konto
Ett konto i Azure AD skapas för synkroniseringstjänstens användning. Det här kontot kan identifieras med dess visningsnamn.

![AD-konto](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

Namnet på den server som kontot används på kan identifieras i den andra delen av användarnamnet. I bilden är servernamnet DC1. Om du har mellanlagringsservrar har varje server ett eget konto.

Kontot skapas med ett långt komplext lösenord som inte upphör att gälla. Det beviljas en särskild roll **Katalog synkronisering konton** som bara har behörighet att utföra katalog synkronisering uppgifter. Den här specialbyggda rollen kan inte beviljas utanför Azure AD Connect-guiden. Azure-portalen visar det här kontot med rollen **Användare**.

Det finns en gräns på 20 synkroniseringstjänstkonton i Azure AD. Om du vill hämta listan över befintliga Azure AD-tjänstkonton i din Azure AD kör du följande Azure AD PowerShell-cmdlet:`Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Om du vill ta bort oanvända Azure AD-tjänstkonton kör du följande Azure AD PowerShell-cmdlet:`Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

>[!NOTE]
>Innan du kan använda ovanstående PowerShell-kommandon måste du installera [Azure Active Directory PowerShell for Graph-modulen](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0#installing-the-azure-ad-module) och ansluta till din instans av Azure AD med [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0)

Mer information om hur du hanterar eller återställer lösenordet för Azure AD Connector-kontot finns i [Hantera Azure AD Connect-kontot](how-to-connect-azureadaccount.md)

## <a name="related-documentation"></a>Relaterad dokumentation
Om du inte har läst dokumentationen om [hur du integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)innehåller följande tabell länkar till relaterade ämnen.

|Hjälpavsnitt |Länk|  
| --- | --- |
|Ladda ned Azure AD Connect | [Ladda ned Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Installera med standardinställningar | [Snabbinstallation av Azure AD Connect](how-to-connect-install-express.md)|
|Installera med anpassade inställningar | [Anpassad installation av Azure AD Connect](./how-to-connect-install-custom.md)|
|Uppgradera från DirSync | [Uppgradera från Azure AD-synkroniseringsverktyget (DirSync)](how-to-dirsync-upgrade-get-started.md)|
|Efter installationen | [Verifiera installationen och tilldela licenser](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
