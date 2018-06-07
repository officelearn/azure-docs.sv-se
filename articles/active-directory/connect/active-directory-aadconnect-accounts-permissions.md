---
title: 'Azure AD Connect: Konton och behörigheter | Microsoft Docs'
description: Det här avsnittet beskrivs de konton som används och skapas och behörigheter som krävs.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: bde8e68eeb63e76a0dde40a09eededde8a545a83
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595095"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: Konton och behörigheter
Installationsguiden för Azure AD Connect innehåller två olika sökvägar:

* I inställningarna för Express-guiden kräver fler privilegier.  Detta är så att den kan enkelt, ställa in konfigurationen utan att behöva skapa användare eller konfigurera behörigheter.
* I anpassade inställningar erbjuder i guiden flera alternativ och alternativ. Det finns dock vissa situationer där du behöver se till att du har rätt behörigheter själv.

## <a name="related-documentation"></a>Relaterad dokumentation
Om du inte läsa dokumentationen på [integrera dina lokala identiteter med Azure Active Directory](../active-directory-aadconnect.md), i följande tabell innehåller länkar till närliggande ämnen.

|Avsnitt |Länk|  
| --- | --- |
|Ladda ned Azure AD Connect | [Ladda ned Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)|
|Installera med standardinställningar | [Snabbinstallation av Azure AD Connect](./active-directory-aadconnect-get-started-express.md)|
|Installera med anpassade inställningar | [Anpassad installation av Azure AD Connect](./active-directory-aadconnect-get-started-custom.md)|
|Uppgradera från DirSync | [Uppgradera från Azure AD-synkroniseringsverktyg (DirSync)](./active-directory-aadconnect-dirsync-upgrade-get-started.md)|
|Efter installationen | [Bekräfta installationen och tilldela licenser ](active-directory-aadconnect-whats-next.md)|

## <a name="express-settings-installation"></a>Snabbinstallation inställningar
I standardinställningar begär installationsguiden autentiseringsuppgifter som företagsadministratör för AD DS.  Detta är så att din lokala Active Directory kan konfigureras med behörigheter som krävs för Azure AD Connect. Om du uppgraderar från DirSync används autentiseringsuppgifter för Företagsadministratörer för AD DS för att återställa lösenordet för kontot som används av DirSync. Du måste också autentiseringsuppgifter som Global administratör för Azure AD.

| Sidan i guiden | Autentiseringsuppgifter samlas in | Behörigheter som krävs | Används för |
| --- | --- | --- | --- |
| Gäller inte |Användaren som kör installationsguiden |Administratör på den lokala servern |<li>Skapar det lokala kontot som används som den [synkronisera motorn tjänstkonto](#azure-ad-connect-sync-service-account). |
| Anslut till Azure AD |Autentiseringsuppgifter för Azure AD-katalog |Rollen som global administratör i Azure AD |<li>Aktiverar synkronisering i Azure AD-katalog.</li>  <li>Skapandet av den [Azure AD-kontot](#azure-ad-service-account) som används för pågående synkroniseringsåtgärder i Azure AD.</li> |
| Anslut till AD DS |Lokala Active Directory-autentiseringsuppgifter |Medlem i gruppen Enterprise administratörer (EA) i Active Directory |<li>Skapar en [konto](#active-directory-account) i Active Directory och ger behörighet till den. Skapa kontot används för att läsa och skriva kataloginformation under synkroniseringen.</li> |

### <a name="enterprise-admin-credentials"></a>Autentiseringsuppgifter som företagsadministratör
Dessa autentiseringsuppgifter används bara under installationen och används inte när installationen har slutförts. Enterprise-administratören inte Domain Admin bör kontrollera behörigheter i Active Directory kan anges i alla domäner.

### <a name="global-admin-credentials"></a>Autentiseringsuppgifter som global administratör
Dessa autentiseringsuppgifter används bara under installationen och används inte när installationen har slutförts. Används för att skapa den [Azure AD-kontot](#azure-ad-service-account) används för att synkronisera ändringarna till Azure AD. Kontot kan också synkronisera som en funktion i Azure AD.

### <a name="permissions-for-the-created-ad-ds-account-for-express-settings"></a>Behörigheter för den skapade AD DS-konto för standardinställningar
Den [konto](#active-directory-account) skapas för läsning och skrivning till AD DS har följande behörigheter när de skapas med standardinställningar:

| Behörighet | Används för |
| --- | --- |
| <li>Replikera katalogändringar</li><li>Replikera katalogen ändras alla |Lösenordets hash-synkronisering |
| Läs/Skriv alla egenskaper för användaren |Import- och Exchange-hybrid |
| Läs/Skriv alla egenskaper iNetOrgPerson |Import- och Exchange-hybrid |
| Gruppen för läsning och skrivning alla egenskaper |Import- och Exchange-hybrid |
| Läsning och skrivning kontaktar du alla egenskaper |Import- och Exchange-hybrid |
| Återställa lösenord |Förberedelser för att aktivera tillbakaskrivning av lösenord |

## <a name="custom-settings-installation"></a>Installation av anpassade inställningar
Azure AD Connect version 1.1.524.0 och senare har alternativet för att låta Azure AD Connect-guiden skapa kontot används för att ansluta till Active Directory.  Tidigare versioner kräver att kontot har skapats innan installationen. De behörigheter som du måste ge det här kontot finns i [skapa AD DS-konto](#create-the-ad-ds-account). 

| Sidan i guiden | Autentiseringsuppgifter samlas in | Behörigheter som krävs | Används för |
| --- | --- | --- | --- |
| Gäller inte |Användaren som kör installationsguiden |<li>Administratör på den lokala servern</li><li>Om du använder en fullständig SQL Server, måste användaren vara systemadministratörskontot (SA) i SQL</li> |Som standard skapar du det lokala kontot som används som den [synkronisera motorn tjänstkonto](#azure-ad-connect-sync-service-account). Kontot skapas endast när administratören inte anger ett visst konto. |
| Installera synkroniseringstjänsterna kontoalternativet för tjänsten |AD eller autentiseringsuppgifter för lokal användare |Användare, behörigheter beviljas av installationsguiden |Om administratören anger ett konto, används det här kontot som tjänstkontot för sync-tjänsten. |
| Anslut till Azure AD |Autentiseringsuppgifter för Azure AD-katalog |Rollen som global administratör i Azure AD |<li>Aktiverar synkronisering i Azure AD-katalog.</li>  <li>Skapandet av den [Azure AD-kontot](#azure-ad-service-account) som används för pågående synkroniseringsåtgärder i Azure AD.</li> |
| Anslut dina kataloger |Lokala Active Directory-autentiseringsuppgifter för varje skog som är ansluten till Azure AD |Behörigheterna som beror på vilka funktioner du aktiverar och finns i [skapa AD DS-konto](#create-the-ad-ds-account) |Det här kontot används för att läsa och skriva kataloginformation under synkroniseringen. |
| AD FS-servrar |För varje server i listan, guiden samlar in autentiseringsuppgifter när inloggningsuppgifterna för användaren som kör guiden är tillräckligt för att ansluta |Domänadministratören |Installation och konfiguration av AD FS-serverrollen. |
| Webbprogramproxyservrarna |För varje server i listan, guiden samlar in autentiseringsuppgifter när inloggningsuppgifterna för användaren som kör guiden är tillräckligt för att ansluta |Lokal administratör på måldatorn. |Installation och konfiguration av WAP-serverrollen. |
| Autentiseringsuppgifter för proxyförtroende |Federationstjänsten förtroende autentiseringsuppgifter (autentiseringsuppgifter proxyn använder för att registrera ett betrodda certifikat från en FS |Domänkonto som är lokal administratör på AD FS-servern |Första registreringen av FS WAP betrott certifikat. |
| Sidan för AD FS-tjänstkontot, ”använda kontoalternativet en domän användare” |Autentiseringsuppgifter för användarkontot AD |Domänanvändare |AD-användarkontot vars autentiseringsuppgifter tillhandahålls används som inloggningskontot för AD FS-tjänsten. |

### <a name="create-the-ad-ds-account"></a>Skapa AD DS-konto
Kontot du anger på den **Anslut dina kataloger** sidan måste finnas i Active Directory före installationen.  Det måste också ha beviljats behörighet. Installationsguiden verifierar inte behörigheterna och eventuella problem endast hittades under synkroniseringen.

Vilka behörigheter som du behöver beror på de valfria funktionerna aktiveras. Om du har flera domäner, måste behörigheterna som beviljas i alla domäner i skogen. Om du inte aktivera någon av dessa funktioner, standard **domänanvändare** har rätt behörighet.

| Funktion | Behörigheter |
| --- | --- |
| msDS-ConsistencyGuid funktion |Skrivbehörigheter till attributet msDS-ConsistencyGuid dokumenterade i [designbegrepp - med msDS-ConsistencyGuid som sourceAnchor](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor). | 
| Lösenordets hash-synkronisering |<li>Replikera katalogändringar</li>  <li>Replikera katalogen ändras alla |
| Exchange-hybridinstallation |Skrivbehörigheter till attribut i [Exchange hybrid tillbakaskrivning](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) för användare, grupper och kontakter. |
| Offentlig mapp för Exchange-e-post |Läsbehörighet till de attribut som beskrivs i [offentlig mapp för Exchange-e-post](active-directory-aadconnectsync-attributes-synchronized.md#exchange-mail-public-folder) för offentliga mappar. | 
| Tillbakaskrivning av lösenord |Skrivbehörigheter till attribut i [komma igång med lösenordshantering](../authentication/howto-sspr-writeback.md) för användare. |
| Tillbakaskrivning av enheter |Behörigheterna med ett PowerShell-skript som beskrivs i [tillbakaskrivning av enheter](active-directory-aadconnect-feature-device-writeback.md). |
| Tillbakaskrivning av grupp |Läsa, skapa, uppdatera och ta bort grupp objekt för synkroniserats **Office 365-grupper**.  Mer information finns i [tillbakaskrivning av grupp](active-directory-aadconnect-feature-preview.md#group-writeback).|

## <a name="upgrade"></a>Uppgradera
När du uppgraderar från en version av Azure AD Connect på en ny version, måste följande behörigheter:

>[!IMPORTANT]
>Från och med version 1.1.484, introducerade Azure AD Connect en regression bugg som kräver sysadmin-behörighet för att uppgradera den SQL-databasen.  Det här felet är åtgärdat i build 1.1.647.  Om du uppgraderar till den här versionen måste sysadmin-behörighet.  Dbo-behörigheter är inte tillräcklig.  Om du försöker uppgradera Azure AD Connect utan att ha sysadmin-behörighet uppgraderingen misslyckas och Azure AD Connect kommer inte längre att fungera korrekt efteråt.  Microsoft är medvetna om detta och arbetar för att åtgärda detta.


| Huvudkonto | Behörigheter som krävs | Används för |
| --- | --- | --- |
| Användaren som kör installationsguiden |Administratör på den lokala servern |Uppdatera binärfiler. |
| Användaren som kör installationsguiden |Medlem i ADSyncAdmins |Göra ändringar i regler för synkronisering och annan konfiguration. |
| Användaren som kör installationsguiden |Om du använder en fullständig SQLServer: DBO (eller liknande) av motorn sync-databasen |Gör databasen nivån ändringar, till exempel uppdaterar tabeller med nya kolumner. |

## <a name="more-about-the-created-accounts"></a>Mer information om skapade konton
### <a name="active-directory-account"></a>Active Directory-konto
Om du använder standardinställningarna skapas ett konto i Active Directory som används för synkronisering. Konto som skapades finns i skogsrotdomänen i behållaren användare och har namnet med prefixet **MSOL_**. Kontot har skapats med ett långt komplexa lösenord som inte upphör. Om du har en lösenordsprincip i din domän, se till att långa och komplexa lösenord skulle vara tillgängliga för det här kontot.

![AD-kontot](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

Om du använder anpassade inställningar, är du ansvarar för att skapa kontot innan du startar installationen.

### <a name="azure-ad-connect-sync-service-account"></a>Azure AD Connect sync-tjänstkonto
Sync-tjänsten kan köras under olika konton. Det kan köras en **virtuella tjänstkonto** (VSA), en **Grupphanterat tjänstkonto** (gMSA/sMSA), eller ett vanligt användarkonto. Alternativ som stöds har ändrats med 2017 April versionen av Connect när du gör en ny installation. Dessa ytterligare alternativ är inte tillgängliga om du uppgraderar från en tidigare version av Azure AD Connect.

| Typ av konto | Installationsalternativ | Beskrivning |
| --- | --- | --- |
| [Virtuella tjänstkonto](#virtual-service-account) | Snabb och anpassade, 2017 April och senare | Detta är det alternativet används för alla express-installationer, förutom installationer på en domänkontrollant. För anpassade är det standardalternativet såvida inte används av ett annat alternativ. |
| [Grupphanterat tjänstkonto](#group-managed-service-account) | Anpassade, 2017 April och senare | Om du använder en fjärransluten SQLServer, sedan rekommenderar vi för att använda en grupp hanteras tjänstkonto. |
| [Användarkonto](#user-account) | Snabb och anpassade, 2017 April och senare | Ett användarkonto med AAD_ prefixet skapas endast under installationen installeras på Windows Server 2008 och installeras på en domänkontrollant. |
| [Användarkonto](#user-account) | Snabb och anpassade, 2017 mars och tidigare | Ett lokalt konto med AAD_ prefixet skapas under installationen. När du använder anpassad installation, går att ange ett annat konto. |

Om du använder Anslut med en version från 2017 mars eller tidigare, sedan bör du inte återställa lösenordet för kontot eftersom Windows förstör krypteringsnycklarna av säkerhetsskäl. Du kan inte ändra kontot till ett annat konto utan att installera om Azure AD Connect. Om du uppgraderar till en version från 2017 April eller senare, är det stöds om du vill ändra lösenordet för tjänstkontot, men du kan inte ändra det konto som används.

> [!Important]
> Du kan bara ange kontot på den första installationen. Det går inte för att ändra tjänstkontot efter installationen har slutförts.

Det här är en tabell med standardvärdet, alternativ för rekommenderade och stöds för synkroniseringstjänstkontot.

Förklaringen:

- **Fetstil** anger standardalternativet och i de flesta fall det rekommenderade alternativet.
- *Kursiv* anger det rekommenderade alternativet om det inte är standardalternativet.
- 2008 - standardalternativet när installerad på Windows Server 2008
- Icke-bold - alternativ som stöds
- Lokalt konto - kontot lokal användare på servern
- Domänkonto - domänanvändarkonto
- sMSA - [fristående Hanterat tjänstkonto](https://technet.microsoft.com/library/dd548356.aspx)
- gMSA - [gruppen Hanterat tjänstkonto](https://technet.microsoft.com/library/hh831782.aspx)

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Anpassat | Fjärr-SQL</br>Anpassat |
| --- | --- | --- | --- |
| **fristående eller arbetsgrupp datorn** | Stöds inte | **VSA**</br>Lokalt konto (2008)</br>Lokalt konto |  Stöds inte |
| **domänanslutna datorer** | **VSA**</br>Lokalt konto (2008) | **VSA**</br>Lokalt konto (2008)</br>Lokalt konto</br>Domänkonto</br>sMSA gMSA | **gMSA**</br>Domänkonto |
| **Domänkontrollant** | **Domänkonto** | *gMSA*</br>**Domänkonto**</br>sMSA| *gMSA*</br>**Domänkonto**|

#### <a name="virtual-service-account"></a>Virtuella tjänstkonto
En virtuell tjänstkontot är en särskild typ av konto som inte har ett lösenord och hanteras av Windows.

![VSA](./media/active-directory-aadconnect-accounts-permissions/aadsyncvsa.png)

Attributet är avsedd att användas med scenarier där Synkroniseringsmotorn och SQL är på samma server. Om du använder fjärr-SQL, så vi rekommenderar att du använder en [Grupphanterat tjänstkonto](#managed-service-account) i stället.

Den här funktionen kräver Windows Server 2008 R2 eller senare. Om du installerar Azure AD Connect på Windows Server 2008 och sedan installationen faller tillbaka till med hjälp av en [användarkontot](#user-account) i stället.

#### <a name="group-managed-service-account"></a>Grupphanterade tjänstkontot
Om du använder en fjärransluten SQLServer, så vi rekommenderar att du använder en **grupp Hanterat tjänstkonto**. Mer information om hur du förbereder din Active Directory för gruppen Hanterat tjänstkonto finns [grupp hanterade tjänstkonton – översikt](https://technet.microsoft.com/library/hh831782.aspx).

Att använda det här alternativet på den [installera nödvändiga komponenter](active-directory-aadconnect-get-started-custom.md#install-required-components) väljer **använder ett befintligt tjänstkonto**, och välj **Hanterat tjänstkonto**.  
![VSA](./media/active-directory-aadconnect-accounts-permissions/serviceaccount.png)  
Det går också för att använda en [fristående Hanterat tjänstkonto](https://technet.microsoft.com/library/dd548356.aspx). Men de kan bara användas på den lokala datorn och det finns ingen fördel med att använda dem över virtuella service-kontot av standardtyp.

Den här funktionen kräver Windows Server 2012 eller senare. Om du behöver använda ett äldre operativsystem och använda fjärr-SQL, så du måste använda en [användarkontot](#user-account).

#### <a name="user-account"></a>Användarkonto
Ett lokalt tjänstkonto som skapas av guiden Installera (såvida du inte anger kontot som ska användas i anpassade inställningar). Kontot som prefix **AAD_** och används för den faktiska synkroniseringstjänsten för att köra som. Om du installerar Azure AD Connect på en domänkontrollant måste skapas kontot i domänen. Den **AAD_** -tjänstkontot måste finnas i domänen om:
   - du använder en fjärrserver som kör SQLServer
   - du använder en proxyserver som kräver autentisering

![Synkroniseringstjänstkontot](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

Kontot har skapats med ett långt komplexa lösenord som inte upphör.

Det här kontot används för att lagra lösenord för andra konton på ett säkert sätt. Andra konton lösenord lagras krypterade i databasen. Krypteringsnycklarna privata nycklar är skyddade med kryptografiska tjänster hemlig nyckel kryptering med Windows Data Protection API (DPAPI).

Om du använder en fullständig SQL Server är kontot DBO av databasen som skapats för Synkroniseringsmotorn. Tjänsten kommer inte att fungera som avsett med andra behörigheter. En SQL-inloggning skapas också.

Kontot också tilldelas behörigheter till filer, registernycklar och andra objekt som är relaterade till Synkroniseringsmotorn.

### <a name="azure-ad-service-account"></a>Azure AD-tjänstkonto
Ett konto i Azure AD har skapats för användning av sync-tjänsten. Det här kontot kan identifieras av dess namn.

![AD-kontot](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount.png)

Namnet på kontot som används på servern kan identifieras i den andra delen av användarnamnet. Servernamnet är FABRIKAMCON i bilden. Om du har mellanlagring servrar har varje server ett eget konto.

Kontot har skapats med ett långt komplexa lösenord som inte upphör. Det har beviljats en särskild roll **synkronisering Katalogkonton** som har endast behörighet att utföra uppgifter för directory-synkronisering. Den här särskilda inbyggda rollen kan inte ges utanför Azure AD Connect-guiden. Azure-portalen visar det här kontot med rollen **användaren**.

Det finns en gräns på 20 tjänstkonton för synkronisering i Azure AD. Om du vill hämta en lista över befintliga konton i Azure AD-tjänsten i din Azure AD, kör du följande Azure AD PowerShell-cmdlet: `Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Ta bort oanvända Azure AD-tjänstkonton, kör du följande Azure AD PowerShell-cmdlet: `Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](../active-directory-aadconnect.md).
