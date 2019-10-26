---
title: Slutför en distribution med lösenordsskyddad autentisering med Azure AD
description: Slutför en Azure Active Directory distribution av lösenordsskyddad autentisering
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6996b6163c1d5a2a4747093743a937dfd9eb7d4f
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933125"
---
# <a name="complete-a-passwordless-authentication-deployment"></a>Slutför en distribution av lösenordsskyddad autentisering

En majoritet av cyberattacker börjar med ett användar namn och ett lösen ord som stjäls från någon i en organisation. 

Organisationer försöker räkna med hotet genom att kräva att användarna använder:

- Långa lösen ord
- Komplexa lösen ord
- Frekventa lösen ords ändringar
- Multi-Factor Authentication (MFA)

Microsofts [forskning visar](https://aka.ms/passwordguidance) att dessa ansträngningar Annoy användare och driver support kostnader. Mer information finns i [din pa $ $Word spelar ingen roll](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

Distribution av lösenordsskyddad autentisering ger följande fördelar:

- Ökad säkerhet. Minska risken för nätfiske och attacker genom att ta bort lösen ord som en attack yta.
- Bättre användar upplevelse. Ge användarna ett bekvämt sätt att komma åt data från var som helst, och ge enkel åtkomst till Outlook, OneDrive, Office och mycket annat medan mobilt.
- Robusta insikter. Få insikter om hur användarna får en lösen ords åtgärd med robust loggning och granskning.

Lösen ordet ersätts med något som du har plus något som du är eller något som du känner till. Till exempel i Windows Hello för företag en bio metrisk gest, till exempel ett ansikte eller finger avtryck, eller en enhetsspecifika PIN-kod som inte överförs över ett nätverk.

Microsoft erbjuder tre lösen ords skydds alternativ som omfattar många olika scenarier. Dessa metoder kan användas för att kopiera. 

- [Windows Hello för företag](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) är bäst för användare på sina dedikerade Windows-datorer.
- Säkerhets nycklar logga in med [FIDO2-säkerhetsnycklar](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) är särskilt användbara för användare som loggar in på delade datorer, t. ex. kiosker, situationer där användningen av telefoner är begränsad och för hög privilegierade identiteter.
- Logga in med Microsoft Authenticator- [appen](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) är användbar för att ange ett lösen ords lös alternativ för användare med mobila enheter. Det sätter en iOS-eller Android-telefon till en stark, lösen ords lös autentiseringsuppgift genom att tillåta att användare loggar in på valfri plattform eller webbläsare. Användarna loggar in genom att få ett meddelande till telefonen, matcha ett tal som visas på skärmen till det som finns på telefonen och sedan använda sina bio metriska data eller PIN-kod för att bekräfta.

## <a name="compare-passwordless-authentication-methods"></a>Jämföra autentiseringsmetoder för lösen ord

Microsofts autentiseringsmetoder för lösen ords kryptering möjliggör olika scenarier. Överväg organisationens behov, krav och funktionerna i varje autentiseringsmetod för att välja din strategi för lösen ords lös autentisering. Vi rekommenderar att alla organisationer som använder Windows 10-enheter använder Windows Hello för företag. Lägg sedan till antingen telefon inloggningen (med Microsoft Authenticator-appen) eller säkerhets nycklar för ytterligare scenarier.

### <a name="passwordless-authentication-scenarios"></a>Scenarier med lösenordsskyddad autentisering

| Scenario | Telefon-autentisering | Säkerhets nycklar | Windows Hello för företag |
| --- | --- | --- | --- |
| **Dator inloggning**: <br> Från tilldelad Windows 10-enhet | **Nej** | **Ja** <br> Med bio metrisk PIN-kod | **Ja**<br>med bio metrisk igenkänning och eller PIN-kod |
| **Dator inloggning**: <br> Från delad Windows 10-enhet | **Nej** | **Ja** <br> Med bio metrisk PIN-kod  | **Nej** |
| **Web App-inloggning**: <br>från en användardefinierad dator | **Ja** | **Ja** <br> Den tillhandahållna enkel inloggning till appar har Aktiver ATS genom inloggning på datorn | **Ja**<br> Den tillhandahållna enkel inloggning till appar har Aktiver ATS genom inloggning på datorn |
| **Web App-inloggning**: <br> från en mobil eller icke-Windows-enhet | **Ja** | **Nej** | **Nej** |
| **Dator inloggning**: <br> Icke-Windows-dator | **Nej** | **Nej** | **Nej** |

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Tekniska överväganden för Microsoft Authenticator-appen

**AD FS-integration** – när en användare aktiverar Microsoft Authenticator lösen ords lös autentiseringsuppgifter, kommer autentiseringen för den användaren att skicka ett meddelande för godkännande. Användare i en hybrid klient hindras från att dirigeras till ADFS för inloggning, om de inte väljer Använd lösen ordet i stället. Den här processen kringgår också alla lokala principer för villkorlig åtkomst och genom strömnings flöden. Men om en login_hint anges vidarebefordras användaren till ADFS och alternativet att använda lösen ords lös autentiseringsuppgifter kringgås.

**Azure MFA Server** – slutanvändare som har Aktiver ATS för MFA via en organisations lokala Azure MFA-Server kan fortfarande skapa och använda ett enda lösen ord för lösen ords lös inloggning. Om användaren försöker uppgradera flera installationer (5 +) av Microsoft Authenticator med autentiseringsuppgifterna kan denna ändring resultera i ett fel.

**Enhets registrering** – om du vill använda Authenticator-appen för lösen ords lös autentisering måste enheten vara registrerad i Azure AD-klienten och kan inte vara en delad enhet. En enhet kan bara registreras i en enda klient. Den här gränsen innebär att endast ett arbets-eller skol konto stöds för telefon inloggning med hjälp av Authenticator-appen.

## <a name="prerequisites"></a>Krav

Organisationer måste uppfylla följande krav innan en lösenordsskyddad distribution påbörjas.

| Krav | Autentiseringsapp | FIDO2 säkerhets nycklar |
| --- | --- | --- |
| [Kombinerad registrering för Azure MFA och självbetjäning för återställning av lösen ord (SSPR)](howto-registration-mfa-sspr-combined.md) är aktiverat (förhands gransknings funktion) | √ | √ |
| [Användare kan utföra Azure MFA](howto-mfa-getstarted.md) | √ | √ |
| [Användare har registrerat sig för Azure MFA och SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [Användare har registrerat sina mobila enheter på Azure Active Directory](../devices/overview.md) | √ |   |
| Windows 10 version 1809 eller senare med en webbläsare som stöds, t. ex. Microsoft Edge eller Mozilla Firefox <br> (version 67 eller senare). <br> *Microsoft rekommenderar version 1903 eller senare för intern support*. |   | √ |
| Kompatibla säkerhets nycklar för FIDO2. Se till att du använder en [Microsoft-testad och verifierad](howto-authentication-passwordless-enable.md) FIDO2-säkerhetsenhet eller annan kompatibel FIDO2-säkerhetsenhet. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Krav för Windows Hello för företag

Kraven för Windows Hello är mycket beroende av om du distribuerar i en lokal, hybrid eller molnbaserad konfiguration. Mer information finns i [fullständig lista över krav för Windows Hello för företag](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification).

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Användarna registrerar sin metod för lösen ords begränsning som en del av registrerings flödet för Azure MFA. Multi-Factor Authentication med ett användar namn och lösen ord tillsammans med en annan registrerad metod kan användas som en reserv om de inte kan använda sin telefon-eller säkerhets nyckel i vissa situationer.

### <a name="security-key-lifecycle"></a>Livs cykel för säkerhets nyckel

Säkerhets nycklar ger åtkomst till dina resurser och du bör planera hanteringen av dessa fysiska enheter.

1. Nyckel distribution: planera hur du ska etablera nycklar till din organisation. Du kan ha en centraliserad etablerings process eller tillåta slutanvändare att köpa FIDO 2,0-kompatibla nycklar.
1. Nyckel aktivering: slutanvändare måste själv aktivera säkerhets nyckeln. Slutanvändare registrerar sina säkerhets nycklar på [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) och aktiverar den andra faktorn (PIN-kod eller bio metrisk) vid första användningen.
1. Inaktivera en nyckel: När funktionen för säkerhets nycklar är i förhands gransknings fasen finns det inget sätt för en administratör att ta bort en nyckel från ett användar konto. Användaren måste ta bort den. Om en nyckel tappas bort eller blir stulen:
   1. Ta bort användaren från en grupp som är aktive rad för autentisering utan lösen ord.
   1. Kontrol lera att de har tagit bort nyckeln som en autentiseringsmetod.
   1. Utfärda en ny nyckel.
1. Nyckel ersättning: användare kan aktivera två säkerhets nycklar samtidigt. När du ersätter en säkerhets nyckel bör du se till att användaren även har tagit bort nyckeln som byts ut.

### <a name="enable-windows-10-support"></a>Aktivera stöd för Windows 10

Aktivering av Windows 10-inloggning med FIDO2-säkerhetsnycklar kräver aktivering av funktionen för Credential-Provider i Windows 10. Aktivera det på något av två sätt:

- [Aktivera autentiseringsprovider via riktad Intune-distribution](howto-authentication-passwordless-security-key.md#enable-targeted-intune-deployment)
   - Intune-distribution är det rekommenderade alternativet för Azure Active Directory anslutna datorer.
- [Aktivera autentiseringsprovider via ett etablerings paket](howto-authentication-passwordless-security-key.md#enable-credential-provider-via-provisioning-package)
   - Om Intune-distributionen inte är möjlig måste administratörerna distribuera ett paket på varje dator för att aktivera funktionen för Credential-Provider. Paket installationen kan utföras med ett av följande alternativ:
      - Grupprincip eller System Center Configuration Manager (SCCM)
      - Lokal installation på en Windows 10-dator

### <a name="register-security-keys"></a>Registrera säkerhets nycklar

Användarna måste registrera sina säkerhets nycklar på var och en av sina Azure Active Directory anslutna Windows 10-datorer.

Mer information finns i [användar registrering och hantering av säkerhets nycklar för FIDO2](howto-authentication-passwordless-security-key.md).

### <a name="licensing-for-passwordless-authentication"></a>Licensiering för lösenordsskyddad autentisering

Det finns ingen extra kostnad för lösen ords lösa autentisering, även om vissa krav kan kräva en Premium-prenumeration. Se detaljerad information och licensierings information på [sidan Azure Active Directory licensiering](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="develop-a-plan"></a>Utveckla en plan

Överväg dina affärs behov och användnings fall för varje autentiseringsmetod. Välj sedan den metod som passar dina behov bäst.

### <a name="use-cases"></a>Användningsfall

I följande tabell beskrivs de användnings fall som implementeras under det här projektet.

| Område | Beskrivning |
| --- | --- |
| **Åtkomst** | Lösen ords återställnings inloggning är tillgängligt från en företags enhet eller en personlig enhet inom eller utanför företags nätverket. |
| **Granskning** | Användnings data är tillgängliga för administratörer att granska i nära real tid. <br> Användnings data hämtas till företags system minst var 29: e dag, eller så används SIEM-verktyget. |
| **Ledning** | Livs cykeln för användar tilldelningar till lämplig autentiseringsmetod och associerade grupper definieras och övervakas. |
| **Säkerhet** | Åtkomst till rätt autentiseringsmetod styrs via användar-och grupp tilldelningar. <br> Endast behöriga användare kan använda lösen ords skydd. |
| **Prestanda** | Tids linjer för distribution av åtkomst tilldelningar dokumenteras och övervakas. <br> Inloggnings tider mäts för enkel användning. |
| **Användar upplevelse** | Användare är medvetna om mobil kompatibilitet. <br> Användare kan konfigurera autentiseraren för autentiserade appar i lösen ord. |
| **Support** | Användare är medvetna om hur man hittar stöd för lösen ords lös inloggnings problem. |

### <a name="engage-the-right-stakeholders"></a>Engagera rätt intressenter

När teknik projekt kraschar är det vanligt vis på grund av en felaktig matchning av konsekvenserna, resultat och ansvars områden. För att undvika dessa fall GRO par bör [du se till att du är engagerande rätt intressenter](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) och att från intressenter-rollerna i projektet är väl förstå.

### <a name="organization-communications"></a>Organisations kommunikation

Kommunikationen är nödvändig för att en ny tjänst ska lyckas. Kommunicera proaktivt hur användarnas upplevelse kommer att ändras, när de kommer att ändras och hur de får support om de drabbas av problem.

Din kommunikation för slutanvändarna måste inkludera:

- [Aktivera den kombinerade säkerhets registrerings upplevelsen](howto-authentication-passwordless-phone.md)
- [Laddar ned Microsoft Authenticator-appen](../user-help/user-help-auth-app-download-install.md)
- [Registrera i Microsoft Authenticator-appen](howto-authentication-passwordless-phone.md)
- [Logga in med din telefon](../user-help/user-help-auth-app-sign-in.md)

Microsoft tillhandahåller MFA- [certifikatmallar](https://aka.ms/mfatemplates), SSPR (Self-Service Password reservering [) och](https://www.microsoft.com/download/details.aspx?id=56768) [dokumentation för slutanvändare](../user-help/security-info-setup-signin.md) för att hjälpa till att formulera kommunikationen. Du kan skicka användare till [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) och registrera dem direkt genom att välja länkarna för säkerhets information på sidan.

### <a name="testing-passwordless"></a>Testa lösen ord

I varje steg i distributionen ser du till att du testar att resultatet är som förväntat.

#### <a name="testing-the-microsoft-authenticator-app"></a>Testa Microsoft Authenticator-appen

Följande är exempel test fall för lösen ords utan autentisering med Microsoft Authenticator-appen

| Scenario | Förväntat resultat |
| --- | --- |
| Användaren kan registrera Microsoft Authenticator app | Användaren kan registrera appen från aka.ms/mysecurityinfo |
| Användaren kan aktivera telefonin loggning | Telefonin loggning har kon figurer ATS för arbets konto |
| Användaren har åtkomst till en app med telefon inloggning | Användaren går igenom telefonens inloggnings flöde och når det angivna programmet. |
| Testa återställning av telefonin loggning genom att inaktivera Microsoft Authenticator lösen ords återställning på skärmen autentiseringsmetoder i Azure Active Directorys portalen | Tidigare aktiverade användare kan inte använda lösen ords lös inloggning från Microsoft Authenticator. |
| Ta bort telefonin loggning från Microsoft Authenticator-appen | Arbets kontot är inte längre tillgängligt på Microsoft Authenticator |

#### <a name="testing-security-keys"></a>Testa säkerhets nycklar

Följande är exempel test fall för lösen ords utan autentisering med säkerhets nycklar.

**Lösen ords lös FIDO logga in till Azure Active Directory anslutna Windows 10-enheter**

| Scenario | Förväntat resultat |
| --- | --- |
| Användaren kan registrera FIDO2-enheten (1809) | Användaren kan registrera FIDO2-enheten med inställningarna > Konton > inloggnings alternativ > säkerhets nyckel |
| Användaren kan återställa FIDO2-enheten (1809) | Användaren kan återställa FIDO2-enheten med hjälp av program vara för tillverkare |
| Användaren kan logga in med FIDO2-enheten (1809) | Användaren kan välja säkerhets nyckel från inloggnings fönstret och logga in. |
| Användaren kan registrera FIDO2-enheten (1903) | Användaren kan registrera FIDO2-enheten på Inställningar > Konton > inloggning alternativ > säkerhets nyckel |
| Användaren kan återställa FIDO2-enheten (1903) | Användaren kan återställa FIDO2-enheten på Inställningar > Konton > inloggning alternativ > säkerhets nyckel |
| Användaren kan logga in med FIDO2-enheten (1903) | Användaren kan välja säkerhets nyckel från inloggnings fönstret och logga in. |

**Lösen ords lös FIDO logga in på Azure AD-webbappar**

| Scenario | Förväntat resultat |
| --- | --- |
| Användaren kan registrera FIDO2-enheten på aka.ms/mysecurityinfo med Microsoft Edge | Registreringen ska lyckas |
| Användaren kan registrera FIDO2-enheten på aka.ms/mysecurityinfo med hjälp av Firefox | Registreringen ska lyckas |
| Användaren kan logga in på OneDrive online med FIDO2-enhet med Microsoft Edge | Inloggningen ska lyckas |
| Användaren kan logga in på OneDrive online med FIDO2-enhet med hjälp av Firefox | Inloggningen ska lyckas |
| Testa att återställa FIDO2 enhets registrering genom att inaktivera FIDO2-säkerhetsnycklar på bladet autentiseringsmetoder i Azure Active Directory-portalen | Användarna uppmanas att logga in med sina säkerhets nycklar, den kommer att kunna logga in och ett fel visas: "företags principen kräver att du använder en annan metod för att logga in". Användarna bör sedan kunna välja en annan metod och logga in. Stäng fönstret och logga in igen för att kontrol lera att de inte ser samma fel meddelande. |

### <a name="auditing-passwordless"></a>Granska lösen ords kontroll

Azure AD har rapporter som ger teknisk och affärs insikter. Be dina företags-och teknik program ägare att ha ägande rätt till och använda rapporterna utifrån organisationens krav.

Avsnittet autentiseringsmetoder i Azure Active Directory Portal är där administratörer kan aktivera och hantera inställningar för lösen ords kryptering.

Azure AD lägger till poster i gransknings loggarna när:

- En administratör gör ändringar i avsnittet autentiseringsmetoder.
- En användare gör alla typer av ändringar i sina autentiseringsuppgifter i Azure Active Directory.

Tabellen nedan innehåller några exempel på typiska rapporterings scenarier.

|   | Hantera risk | Öka produktiviteten | Styrning och efterlevnad |
| --- | --- | --- | --- |
| **Rapport typer** | Autentiseringsmetoder – användare som registrerats för kombinerad säkerhets registrering | Autentiseringsmetoder – användare registrerade för appens avisering | Inloggningar: granska vem som ansluter till klienten och hur |
| **Möjliga åtgärder** | Mål användare har ännu inte registrerats | Enhets införande av Microsoft Authenticator-app eller säkerhets nycklar | Återkalla åtkomst eller Använd ytterligare säkerhets principer för administratörer |

**Azure AD behåller de flesta gransknings data i 30 dagar** och gör data tillgängliga via Azure Admin Portal eller API som du kan ladda ned till dina analys system. Om din organisation kräver längre kvarhållning måste loggarna exporteras och förbrukas i ett SIEM-verktyg, till exempel [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk eller Sumo Logic. [Lär dig mer om att visa åtkomst-och användnings rapporter](../reports-monitoring/overview-reports.md).

Användare kan registrera och hantera sina autentiseringsuppgifter genom att gå till [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Den här länken dirigerar användarna till den hantering av autentiseringsuppgifter för slutanvändare som har Aktiver ATS via den kombinerade SSPR/MFA-registrerings upplevelsen. All registrering av FIDO2-säkerhetsenheter eller ändringar av autentiseringsmetoder av en användare loggas i Azure Active Directory gransknings loggar.

När användarna aktiverar eller inaktiverar kontot för en säkerhets nyckel eller återställer den andra faktorn för säkerhets nyckeln på sina Windows 10-datorer, läggs en post till i säkerhets loggen och anges under följande händelse-ID: 4670 5382.

### <a name="plan-for-rollback"></a>Planera för återställning

Även om lösenordsbaserad autentisering är en förenklad funktion med minimal påverkan på slutanvändare kan det vara nödvändigt att återställa igen.

Om du återställer måste administratören logga in på Azure Active Directory Portal, välja önskade metoder för stark autentisering och ändra alternativet Aktivera till Nej. Med den här processen inaktive ras lösen ords lös funktionalitet för alla användare.

Användare som redan har registrerat FIDO2 säkerhets enheter uppmanas att använda säkerhetsenheten vid nästa inloggning och kommer sedan att se följande fel:

![Välj ett annat sätt att logga in på](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

### <a name="plan-to-pilot"></a>Planera för pilot

När du distribuerar lösenordsbaserad autentisering bör du först aktivera en eller flera pilot grupper. Du kan [skapa grupper](../fundamentals/active-directory-groups-create-azure-portal.md) särskilt för detta ändamål. Lägg till de användare som ska ingå i piloten till grupperna. Aktivera sedan nya metoder för lösen ords kryptering för de valda grupperna.

Grupper kan synkroniseras från en lokal katalog eller från Azure AD. När du är nöjd med resultatet från piloten kan du växla till lösen ords avinstallationen för alla användare.

Se [metod tips för en pilot](https://aka.ms/deploymentplans) på sidan distributions planer.

## <a name="deploy-passwordless-authentication"></a>Distribuera lösenordsskyddad autentisering

Följ stegen som är anpassade till den valda metoden nedan.

### <a name="required-administrative-roles"></a>Administrativa roller som krävs

| Azure AD-roll | Beskrivning |
| --- | --- |
| Administratör för autentisering | Minst privilegie rad roll som kan implementera och hantera autentiseringsmetoder |
| Användare | Minst privilegie rad roll för att konfigurera Authenticator-appen på enheten eller för att registrera säkerhets nyckel enhet för webb-eller Windows 10-inloggning. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Distribuera telefonin loggning med Microsoft Authenticator-appen

Följ anvisningarna i artikeln [Aktivera lösen ords lös inloggning med Microsoft Authenticator-appen](howto-authentication-passwordless-phone.md) för att aktivera Microsoft Authenticator-appen som en metod för lösen ords lös autentisering i din organisation.

### <a name="deploy-fido2-security-key-sign-in"></a>Distribuera FIDO2-inloggning för säkerhets nyckel

Följ anvisningarna i artikeln [Aktivera lösen ords lös säkerhets nyckel logga in för Azure AD](howto-authentication-passwordless-security-key.md) för att aktivera FIDO2-säkerhetsnycklar som lösen ords skydds metoder i din organisation.
 
### <a name="troubleshoot-phone-sign-in"></a>Felsöka telefonin loggning

| Scenario | Lösning |
| --- | --- |
| Användaren kan inte utföra kombinerad registrering | Se till att [kombinerad registrering](concept-registration-mfa-sspr-combined.md) är aktive rad. |
| Användaren kan inte aktivera telefon signering i Authenticator-appen | Se till att användaren är inom omfånget för distribution |
| Användaren är inte inom omfånget för lösen ords full autentisering, men visas med alternativet lösen ords full inloggning, som inte kan slutföras. | Det här fallet inträffar när användaren har aktiverat telefonin loggning i n programmet innan principen skapas. <br> Aktivera inloggning: Lägg till användaren i omfånget för användare som har Aktiver ATS för lösen ords lös inloggning. <br> Om du vill blockera inloggning: Låt användaren ta bort sina autentiseringsuppgifter från programmet. |

### <a name="troubleshoot-security-key-sign-in"></a>Felsöka säkerhets nyckel inloggning

| Scenario | Lösning |
| --- | --- |
| Användaren kan inte utföra kombinerad registrering | Se till att [kombinerad registrering](concept-registration-mfa-sspr-combined.md) är aktive rad. |
| Användaren kan inte lägga till en säkerhets nyckel i sina [säkerhets inställningar](https://aka.ms/mysecurityinfo) | Se till att [säkerhets nycklar](howto-authentication-passwordless-security-key.md) är aktiverade. |
| Användaren kan inte lägga till säkerhets nyckel i Windows 10-inloggnings alternativ | [Kontrol lera att säkerhets nycklar för Windows-inloggning](howto-authentication-passwordless-enable.md) |
| **Fel meddelande**: vi har upptäckt att den här webbläsaren eller operativ systemet inte stöder FIDO2 säkerhets nycklar. | FIDO2-säkerhetsenheter kan bara registreras i webbläsare som stöds (Microsoft Edge, Firefox version 67) på Windows 10 version 1809 eller senare. |
| **Fel meddelande**: företags principen kräver att du använder en annan metod för att logga in. | Se till att säkerhets nycklar är aktiverade i klient organisationen. |
| Användaren kan inte hantera min säkerhets nyckel i Windows 10 version 1809 | Version 1809 kräver att du använder program varan för hantering av säkerhets nycklar som tillhandahålls av FIDO2 Key-leverantören. Kontakta leverantören för support. |
| Jag tror att min FIDO2-säkerhetsnyckel kan vara defekt, hur kan jag testa den | Navigera till [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/), ange autentiseringsuppgifter för ett test konto, Anslut den misstänkta säkerhets nyckeln, klicka på knappen "+" längst upp till höger på skärmen, klicka på Skapa och gå igenom skapande processen. Om det här scenariot Miss lyckas kan det bero på att enheten är skadad. |

## <a name="next-steps"></a>Nästa steg

- [Aktivera lösen ords skydds nycklar för inloggning för Azure AD](howto-authentication-passwordless-security-key.md)
- [Aktivera inloggning med lösen ord med Microsoft Authenticator-appen](howto-authentication-passwordless-phone.md)
- [Lär dig mer om autentiseringsmetoder användning & insikter](howto-authentication-methods-usage-insights.md)
