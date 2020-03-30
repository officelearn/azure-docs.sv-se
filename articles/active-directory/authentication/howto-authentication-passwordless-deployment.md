---
title: Planera en lösenordslös autentiseringsdistribution med Azure AD
description: Lär dig hur du planerar och distribuerar en azure Active Directory-implementering av lösenordslös autentisering
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: baselden
author: iainfoulds
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 145439ebedd2ddf7c081339146010c66f37fe1af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136542"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Planera en lösenordslös autentiseringsdistribution i Azure Active Directory

> [!NOTE]
> Om du vill skapa en offlineversion av den här distributionsplanen använder du webbläsarens utskrift till PDF-funktioner.

De flesta cyberattacker börjar med ett komprometterat användarnamn och lösenord. Organisationer försöker motverka hotet genom att kräva att användarna använder någon av följande metoder:

- Långa lösenord
- Komplexa lösenord
- Frekventa lösenordsändringar
- Multi-Factor Authentication (MFA)

Microsofts [forskning visar](https://aka.ms/passwordguidance) att dessa ansträngningar irriterar användarna och driver upp supportkostnaderna. Mer information finns i [Din Pa $$word spelar ingen roll.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)

### <a name="benefits-of-passwordless-authentication"></a>Fördelar med lösenordslös autentisering

- **Ökad säkerhet**. Minska risken för nätfiske- och lösenordssprayattacker genom att ta bort lösenord som en attackyta.
-  **Bättre användarupplevelse**. Ge användarna ett bekvämt sätt att komma åt data var som helst. Ge enkel åtkomst till program och tjänster som Outlook, OneDrive eller Office när du är mobil.
-  **Robusta insikter**. Få insikter om användarnas lösenordslösa aktivitet med robust loggning och granskning.

Med lösenordslös ersätts lösenordet med något du har plus något du är eller något du vet. Windows Hello för företag kan till exempel använda en biometrisk gest som ett ansikte eller fingeravtryck eller en enhetsspecifik PIN-kod som inte överförs via ett nätverk.

## <a name="passwordless-authentication-methods"></a>Metoder för lösenordslös autentisering
Microsoft erbjuder tre lösenordslösa autentiseringsalternativ som täcker många scenarier. Dessa metoder kan användas tillsammans:

- [Windows Hello för företag](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) är bäst för användare på sina dedikerade Windows-datorer.
- Registrering av säkerhetsnycklar med [FIDO2-säkerhetsnycklar](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) är särskilt användbart för användare som loggar in på delade datorer som kiosker, i situationer där användningen av telefoner är begränsad och för mycket privilegierade identiteter.
- Telefon logga in med [Microsoft Authenticator app](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) är användbart för att ge ett lösenordslöst alternativ för användare med mobila enheter. Authenticator-appen förvandlar alla iOS- eller Android-telefoner till en stark, lösenordslös autentiseringsuppgifter genom att tillåta användare att logga in på valfri plattform eller webbläsare. Användare loggar in genom att få ett meddelande till sin telefon, matcha ett nummer som visas på skärmen till den på sin telefon, och sedan använda sina biometriska data eller PIN-kod för att bekräfta.

### <a name="passwordless-authentication-scenarios"></a>Scenarier för lösenordslös autentisering

Microsofts lösenordslösa autentiseringsmetoder möjliggör olika scenarier. Tänk på dina organisatoriska behov, förutsättningar och funktionerna i varje autentiseringsmetod för att välja din lösenordslösa autentiseringsstrategi. Vi rekommenderar att alla organisationer som använder Windows 10-enheter använder Windows Hello för företag. Lägg sedan till antingen telefon inloggning (med Microsoft Authenticator app) eller säkerhetsnycklar för ytterligare scenarier.

| Scenario | Telefonautentisering | Säkerhetsnycklar | Windows Hello för företag |
| --- | --- | --- | --- |
| **Logga in för datorn**: <br> Från tilldelad Windows 10-enhet | **Nej** | **Ja** <br> Med biometrisk, PIN-kod | **Ja**<br>med biometrisk igenkänning och eller PIN-kod |
| **Logga in för datorn**: <br> Från delad Windows 10-enhet | **Nej** | **Ja** <br> Med biometrisk, PIN-kod  | **Nej** |
| **Inloggning via webbapp:** <br>från en användardedikerad dator | **Ja** | **Ja** <br> Förutsatt att enkel inloggning till appar aktiveras av dator inloggning | **Ja**<br> Förutsatt att enkel inloggning till appar aktiveras av dator inloggning |
| **Inloggning via webbapp:** <br> från en mobil enhet eller enhet som inte är windows | **Ja** | **Nej** | **Nej** |
| **Logga in för datorn**: <br> Dator som inte kommer från Windows | **Nej** | **Nej** | **Nej** |

Information om hur du väljer den bästa metoden för din organisation finns i [Bestämma en lösenordslös metod](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless#deciding-a-passwordless-method).

## <a name="prerequisites"></a>Krav

Organisationer måste uppfylla följande förutsättningar innan en lösenordslös distribution påbörjas:

| Krav | Autentiseringsapp | FIDO2-säkerhetsnycklar |
| --- | --- | --- |
| [Kombinerad registrering för Azure Multi-factor autentisering och självbetjäning lösenordsåterställning (SSPR)](howto-registration-mfa-sspr-combined.md) är aktiverat (förhandsgranskningsfunktion) | √ | √ |
| [Användare kan utföra Azure Multi-factor autentisering](howto-mfa-getstarted.md) | √ | √ |
| [Användare har registrerat sig för Azure Multi-Factor autentisering och SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [Användare har registrerat sina mobila enheter i Azure Active Directory](../devices/overview.md) | √ |   |
| Windows 10 version 1809 eller senare med hjälp av en webbläsare som stöds som Microsoft Edge eller Mozilla Firefox <br> (version 67 eller högre). <br> *Microsoft rekommenderar version 1903 eller senare för inbyggt stöd*. |   | √ |
| Kompatibla FIDO2-säkerhetsnycklar. Kontrollera att du använder en [Microsoft-testad och verifierad](howto-authentication-passwordless-enable.md) FIDO2-säkerhetsenhet eller annan kompatibel FIDO2-säkerhetsenhet. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Förutsättningar för Windows Hello for Business

Förutsättningarna för Windows Hello är mycket beroende av om du distribuerar i en lokal konfiguration, hybrid eller molnkonfiguration. Mer information finns i [den fullständiga listan över förutsättningar för Windows Hello för företag](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification).

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Användare registrerar sin lösenordslösa metod som en del av azure multifaktorautentisering registreringsflödet. Multifaktorautentisering med ett användarnamn och lösenord tillsammans med en annan registrerad metod kan användas som reserv om de inte kan använda sin telefon eller säkerhetsnyckel i vissa scenarier.

### <a name="licensing"></a>Licensiering 
Det finns ingen extra kostnad för lösenordslös autentisering, även om vissa förutsättningar kan kräva en premiumprenumeration. Detaljerad funktions- och licensieringsinformation på [licensieringssidan](https://azure.microsoft.com/pricing/details/active-directory/)för Azure Active Directory . 

## <a name="develop-a-plan"></a>Ta fram en plan

Tänk på dina affärsbehov och användningsfall för varje autentiseringsmetod. Välj sedan den metod som bäst passar dina behov.

### <a name="use-cases"></a>Användningsfall

I följande tabell beskrivs de användningsfall som ska implementeras under det här projektet.

| Område | Beskrivning |
| --- | --- |
| **Åtkomst** | Lösenordslös inloggning är tillgänglig från en företags- eller personlig enhet inom eller utanför företagsnätverket. |
| **Granskning** | Användningsdata är tillgängliga för administratörer att granska i nära realtid. <br> Användningsdata hämtas till företagssystem minst var 29:e dag, eller så används SIEM-verktyget. |
| **Styrelseformer** | Livscykel för användartilldelningar till lämplig autentiseringsmetod och associerade grupper definieras och övervakas. |
| **Säkerhet** | Åtkomst till lämplig autentiseringsmetod styrs via användar- och grupptilldelningar. <br> Endast behöriga användare kan använda lösenordslös inloggning. |
| **Prestanda** | Åtkomsttilldelningsspridningstidslinjer dokumenteras och övervakas. <br> Inloggningstid mäts för enkel användning. |
| **Användarupplevelse** | Användarna är medvetna om mobilkompatibilitet. <br> Användare kan konfigurera den lösenordslösa inloggningen för Authenticator-appen. |
| **Support** | Användarna är medvetna om hur de hittar stöd för lösenordslösa inloggningsproblem. |

### <a name="engage-the-right-stakeholders"></a>Engagera rätt intressenter

När teknikprojekt misslyckas är det vanligtvis på grund av felaktiga förväntningar på påverkan, resultat och ansvar. För att undvika dessa fallgropar, [se till att du engagerar rätt intressenter](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) och att intressentrollerna i projektet är väl förstådda.

### <a name="plan-communications"></a>Planera kommunikation

Kommunikation är avgörande för att en ny tjänst ska lyckas. Proaktivt kommunicera hur användarnas upplevelse kommer att förändras, när det kommer att förändras, och hur man får stöd om de upplever problem.

Din kommunikation till slutanvändare bör innehålla följande information:

- [Aktivera den kombinerade säkerhetsregistreringsupplevelsen](howto-authentication-passwordless-phone.md)
- [Ladda ned Microsoft Authenticator-appen](../user-help/user-help-auth-app-download-install.md)
- [Registrera dig i Microsoft Authenticator-appen](howto-authentication-passwordless-phone.md)
- [Logga in med telefonen](../user-help/user-help-auth-app-sign-in.md)

Microsoft tillhandahåller [kommunikationsmallar](https://aka.ms/mfatemplates)för multifaktorautentisering, [SSPR-kommunikationsmallar](https://www.microsoft.com/download/details.aspx?id=56768)(Self-Service Password Reset) och dokumentation för [slutanvändare](../user-help/security-info-setup-signin.md) för att hjälpa dig att utarbeta din kommunikation. Du kan skicka [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) användare till att registrera sig direkt genom att välja **länkarna säkerhetsinformation** på den sidan.

### <a name="plan-to-pilot"></a>Planera att lotsa

När du distribuerar lösenordslös autentisering bör du först aktivera en eller flera pilotgrupper. Du kan [skapa grupper](../fundamentals/active-directory-groups-create-azure-portal.md) specifikt för detta ändamål. Lägg till de användare som ska delta i pilotprojektet i grupperna. Aktivera sedan nya lösenordslösa autentiseringsmetoder för de valda grupperna.

Grupper kan synkroniseras från en lokal katalog eller från Azure AD. När du är nöjd med resultatet av din pilot kan du aktivera den lösenordslösa autentiseringen för alla användare.

Se [Metodtips för en pilot](https://aka.ms/deploymentplans) på sidan distributionsplaner.

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>Planera lösenordslös autentisering med Microsoft Authenticator-appen

Microsoft Authenticator-appen är en kostnadsfri nedladdning från Google Play eller Apple App Store. [Läs mer om hur du laddar ned Microsoft Authenticator-appen](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). Be användare ladda ned Microsoft Authenticator-appen. och följ anvisningarna för att aktivera telefon logga in. 

Det förvandlar alla iOS eller Android-telefoner till en stark, lösenordslös referens. Användare loggar in på en plattform eller webbläsare genom att få ett meddelande till sin telefon, matcha ett nummer som visas på skärmen till den på sin telefon, och sedan använda biometri eller en PIN-kod för att bekräfta. [Se information om hur Microsoft Authenticator-appen fungerar](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless#user-using-microsoft-authenticator-for-passwordless-sign-in). 

![logga in med Appen Authenticator](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Tekniska överväganden för Microsoft Authenticator-appen

**AD FS-integrering** – När en användare aktiverar Microsoft Authenticator lösenordslös autentiseringsuppgifter, autentisering för den användaren som standard att skicka ett meddelande för godkännande. Användare i en hybridklient hindras från att dirigeras till ADFS för inloggning om de inte väljer "Använd ditt lösenord i stället". Den här processen kringgår också alla lokala principer för villkorlig åtkomst och direktautentiseringsflöden. Men om en *login_hint* anges vidarebefordras användaren till ADFS och kringgår alternativet att använda den lösenordslösa autentiseringsuppgifterna.

**Azure Multi-factor authentication server** - Slutanvändare som är aktiverade för multifaktorautentisering via en organisations lokala Azure MFA-server kan skapa och använda en enda lösenordslös telefoninsignningsautentisering. Om användaren försöker uppgradera flera installationer (5 eller fler) av Microsoft Authenticator med autentiseringsuppgifterna kan den här ändringen leda till ett fel.

**Enhetsregistrering** - Om du vill använda Autentiseringsappen för lösenordslös autentisering måste enheten vara registrerad i Azure AD-klienten och kan inte vara en delad enhet. En enhet kan bara registreras i en enda klient. Den här gränsen innebär att endast ett arbets- eller skolkonto stöds för telefon inloggning med hjälp av Authenticator-appen.

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>Planera lösenordslös autentisering med FIDO2-säkerhetsnycklar
Det finns tre typer av lösenordslösa inloggningsdistributioner som är tillgängliga med säkerhetsnycklar:

-    Azure Active Directory-webbappar i en webbläsare som stöds
-    Azure Active Directory Gick med i Windows 10-enheter
-    Hybrid Azure Active Directory Gick med i Windows 10-enheter (förhandsversion)
     -    Ger åtkomst till både molnbaserade och lokala resurser. Mer information om åtkomst till lokala resurser finns i [SSO till lokala resurser med FIDOP2-nycklar](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises)

Du måste aktivera **Kompatibla FIDO2-säkerhetsnycklar**. Microsoft tillkännagav [viktiga partnerskap med FIDO2 viktiga leverantörer](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493).

**För Azure AD-webbappar och Azure AD Windows-anslutna enheter:**

-    Windows 10 version 1809 eller högre med hjälp av en webbläsare som stöds som Microsoft Edge eller Mozilla Firefox (version 67 eller senare). 
-    Windows 10 version 1809 stöder FIDO2-inloggning och kan kräva att programvara från FIDO2-nyckeltillverkaren distribueras. Vi rekommenderar att du använder version 1903 eller senare. 

**För Hybrid Azure Active Directory Domain Joined-enheter:** 
-    Windows 10 Insider build 18945 eller senare
-    Fullständigt korrigerade domänservrar som kör Windows Server 2016 eller 2019.
-    Senaste versionen av Azure AD Connect

En fullständig lista över krav finns i Aktivera inloggning av [lösenordslös säkerhetsnyckel till Windows 10-enheter med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-windows#requirements).


### <a name="security-key-life-cycle"></a>Säkerhetsnyckellivscykel

Säkerhetsnycklar ger åtkomst till dina resurser och du bör planera hanteringen av dessa fysiska enheter.

1. **Nyckeldistribution**: Planera hur du etablerar nycklar till din organisation. Du kan ha en centraliserad etableringsprocess eller tillåta slutanvändare att köpa FIDO 2.0-kompatibla nycklar.
1. **Nyckelaktivering:** Slutanvändare måste självaktivera säkerhetsnyckeln. Slutanvändare registrerar sina säkerhetsnycklar på [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) och aktiverar den andra faktorn (PIN-kod eller biometrisk) vid första användningen.
1. **Inaktivera en nyckel:** Säkerhetsnyckelfunktionen är i förhandsgranskningsfasen, men det finns inget sätt för en administratör att ta bort en nyckel från ett användarkonto. Användaren måste ta bort den. Om en nyckel försvinner eller blir stulen:
   1. Ta bort användaren från valfri grupp som är aktiverad för lösenordslös autentisering.
   1. Kontrollera att de har tagit bort nyckeln som en autentiseringsmetod.
   1. Utfärda en ny nyckel. **Nyckelbyte:** Användare kan aktivera två säkerhetsnycklar samtidigt. När du ersätter en säkerhetsnyckel ska du se till att användaren också har tagit bort nyckeln som ersätts.

### <a name="enable-windows-10-support"></a>Aktivera Windows 10-stöd

För att aktivera Windows 10-inloggning med FIDO2-säkerhetsnycklar måste du aktivera autentiseringsuppgifterna i Windows 10. Välj något av följande:

- [Aktivera autentiseringsprovider med Intune](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - Intune-distribution är det rekommenderade alternativet.
- [Aktivera autentiseringsprovider med ett etableringspaket](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Om Intune-distribution inte är möjlig måste administratörer distribuera ett paket på varje dator för att aktivera autentiseringsuppgifter providerfunktionen. Paketinstallationen kan utföras av något av följande alternativ:
      - Grupprincip eller Konfigurationshanteraren
      - Lokal installation på en Windows 10-dator
- [Aktivera autentiseringsprovider med grupprincip](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - Stöds endast för hybrid Azure AD-anslutna enheter.

#### <a name="enable-on-premises-integration"></a>Aktivera lokal integrering

Om du vill aktivera åtkomst till lokala resurser följer du stegen för att [aktivera lösenordslös säkerhetsnyckel logga in på lokala resurser (förhandsversion)](howto-authentication-passwordless-security-key-on-premises.md).

> [!IMPORTANT]
> Dessa steg måste också slutföras för alla hybrid Azure AD-anslutna enheter för att använda FIDO2-säkerhetsnycklar för Windows 10-inloggning.

### <a name="register-security-keys"></a>Registrera säkerhetsnycklar

Användare måste registrera sin säkerhetsnyckel på var och en av sina Azure Active Directory-anslutna Windows 10-datorer.

Mer information finns i [Användarregistrering och hantering av FIDO2-säkerhetsnycklar](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys).


## <a name="plan-auditing-security-and-testing"></a>Planera granskning, säkerhet och testning
Planering för granskning som uppfyller dina ramverk för organisation och efterlevnad är en viktig del av distributionen.

### <a name="auditing-passwordless"></a>Granska lösenordslös

Azure AD har rapporter som ger tekniska och affärsinsikter. Låt dina företags- och tekniska programägare ta över ägandet av och använda dessa rapporter baserat på organisationens krav.

Avsnittet **Autentiseringsmetoder** i Azure Active Directory-portalen är där administratörer kan aktivera och hantera inställningar för lösenordslösa autentiseringsuppgifter.

Azure AD lägger till poster i granskningsloggarna när:

- En administratör gör ändringar i avsnittet Autentiseringsmetoder.
- En användare gör någon form av ändring av sina autentiseringsuppgifter i Azure Active Directory.

I följande tabell finns några exempel på typiska rapporteringsscenarier:

|   | Hantera risk | Öka produktiviteten | Styrning och efterlevnad |
| --- | --- | --- | --- |
| **Rapporttyper** | Autentiseringsmetoder- användare som registrerats för kombinerad säkerhetsregistrering | Autentiseringsmetoder – användare som är registrerade för appavisering | Inloggningar: granska vem som har åtkomst till klienten och hur |
| **Potentiella åtgärder** | Målanvändare har ännu inte registrerats | Driva antagande av Microsoft Authenticator-appen eller säkerhetsnycklar | Återkalla åtkomst eller tillämpa ytterligare säkerhetsprinciper för administratörer |

**Azure AD behåller de flesta granskningsdata i 30 dagar** och gör data tillgängliga via Azure Admin portal eller API för dig att ladda ner till dina analyssystem. Om du behöver längre kvarhållning, exportera och använda loggar i ett SIEM-verktyg som [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk eller Sumo Logic. [Läs mer om hur du visar dina åtkomst- och användningsrapporter](../reports-monitoring/overview-reports.md).

Användare kan registrera och hantera sina [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)autentiseringsuppgifter genom att navigera till . Den här länken leder användare till slutanvändarens hanteringsupplevelse för autentiseringsuppgifter som aktiverades via den kombinerade registreringsupplevelsen för SSPR/Multifaktorautentisering. Azure AD loggar registrering av FIDO2-säkerhetsenheter och ändringar av autentiseringsmetoder av en användare.

### <a name="plan-security"></a>Planera säkerhet
Som en del av den här distributionsplanen rekommenderar Microsoft att lösenordslös autentisering aktiveras för alla privilegierade administratörskonton.

När användare aktiverar eller inaktiverar kontot på en säkerhetsnyckel, eller återställer den andra faktorn för säkerhetsnyckeln på sina Windows 10-datorer, läggs en post till i säkerhetsloggen och finns under följande händelse-ID: *4670* och *5382*.

### <a name="plan-testing"></a>Planera testning

Kontrollera att resultaten är som förväntat i varje steg av distributionen när du testar scenarier och implementering.

#### <a name="testing-the-microsoft-authenticator-app"></a>Testa Appen Microsoft Authenticator

Följande är exempel på testfall för lösenordslös autentisering med Microsoft Authenticator-appen:

| Scenario | Förväntat resultat |
| --- | --- |
| Användaren kan registrera Microsoft Authenticator-appen | Användaren kan registrera appen från aka.ms/mysecurityinfo |
| Användaren kan aktivera telefonloggning | Telefon logga in konfigurerad för arbete konto |
| Användaren kan komma åt en app med telefonloggning | Användaren går via telefon inloggningsflöde och når programmet. |
| Testa att återställa registreringen av telefonsignering genom att inaktivera microsoft authenticator-lösenordslös inloggning på skärmen Autentiseringsmetoder i Azure Active Directory-portalen | Tidigare aktiverade användare kan inte använda lösenordslös inloggning från Microsoft Authenticator. |
| Ta bort telefon inloggning från Microsoft Authenticator app | Arbetskontot är inte längre tillgängligt på Microsoft Authenticator |

#### <a name="testing-security-keys"></a>Testa säkerhetsnycklar

Följande är exempel på testfall för lösenordslös autentisering med säkerhetsnycklar.

**Lösenordslös FIDO-inloggning till Azure Active Directory Gick med i Windows 10-enheter**

| Scenario | Förväntat resultat |
| --- | --- |
| Användaren kan registrera FIDO2-enhet (1809) | Användaren kan registrera FIDO2-enhet med hjälp av på Inställningar > konton > logga in alternativ > säkerhetsnyckel |
| Användaren kan återställa FIDO2-enheten (1809) | Användaren kan återställa FIDO2-enheten med hjälp av tillverkarens programvara |
| Användaren kan logga in med FIDO2-enhet (1809) | Användaren kan välja Säkerhetsnyckel i inloggningsfönstret och logga in. |
| Användaren kan registrera FIDO2-enhet (1903) | Användaren kan registrera FIDO2-enhet på Inställningar > konton > inloggningsalternativ > säkerhetsnyckel |
| Användaren kan återställa FIDO2-enheten (1903) | Användaren kan återställa FIDO2-enheten på Inställningar > konton > inloggningsalternativ > säkerhetsnyckel |
| Användaren kan logga in med FIDO2-enhet (1903) | Användaren kan välja Säkerhetsnyckel i inloggningsfönstret och logga in. |

**Lösenordslös FIDO-inloggning till Azure AD-webbappar**

| Scenario | Förväntat resultat |
| --- | --- |
| Användaren kan registrera FIDO2-enhet på aka.ms/mysecurityinfo med Hjälp av Microsoft Edge | Registreringen bör lyckas |
| Användaren kan registrera FIDO2-enhet på aka.ms/mysecurityinfo med Firefox | Registreringen bör lyckas |
| Användaren kan logga in på OneDrive online med FIDO2-enheten med Microsoft Edge | Inloggningen ska lyckas |
| Användaren kan logga in på OneDrive online med FIDO2-enheten med Firefox | Inloggningen ska lyckas |
| Testa att återställa FIDO2-enhetsregistrering genom att stänga av FIDO2-säkerhetsnycklar i fönstret Autentiseringsmetod i Azure Active Directory-portalen | Användare uppmanas att logga in med sin säkerhetsnyckel. Användare loggas in och ett fel visas: "Din företagspolicy kräver att du använder en annan metod för att logga in". Användare bör då kunna välja en annan metod och logga in. Stäng fönstret och logga in igen för att kontrollera att de inte ser samma felmeddelande. |

### <a name="plan-for-rollback"></a>Planera för återställning

Även om lösenordslös autentisering är en lätt funktion med minimal påverkan på slutanvändare, kan det vara nödvändigt att återställa.

För att återställa återställningen måste administratören logga in på Azure Active Directory-portalen, välja önskade starka autentiseringsmetoder och ändra alternativ till **Nej**. Den här processen inaktiverar funktionen utan lösenord för alla användare.

Användare som redan har registrerat FIDO2-säkerhetsenheter uppmanas att använda säkerhetsenheten vid nästa inloggning och sedan se följande fel:

![välja ett annat sätt att logga in](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>Distribuera och felsöka lösenordslös autentisering

Följ stegen som är anpassade till den valda metoden nedan.

### <a name="required-administrative-roles"></a>Obligatoriska administrativa roller

| Azure AD-roll | Beskrivning |
| --- | --- |
| Global administratör|Minst privilegierad roll som kan implementera kombinerad registreringsupplevelse. |
| Administratör för autentisering | Minst privilegierad roll som kan implementera och hantera autentiseringsmetoder. |
| Användare | Minst privilegierad roll för att konfigurera Authenticator-appen på enheten eller för att registrera säkerhetsnyckelenheten för webb- eller Windows 10-inloggning. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Distribuera telefon inloggning med Microsoft Authenticator-appen

Följ stegen i artikeln [Aktivera lösenordslös inloggning med Microsoft Authenticator-appen](howto-authentication-passwordless-phone.md) för att aktivera Microsoft Authenticator-appen som en lösenordslös autentiseringsmetod i organisationen.

### <a name="deploy-fido2-security-key-sign-in"></a>Distribuera FIDO2-säkerhet nyckel inloggning

Följ stegen i artikeln Aktivera [lösenordslös säkerhetsnyckel logga in för Azure AD för](howto-authentication-passwordless-security-key.md) att aktivera FIDO2-säkerhetsnycklar som lösenordslösa autentiseringsmetoder.

### <a name="troubleshoot-phone-sign-in"></a>Felsöka inloggning via telefon

| Scenario | Lösning |
| --- | --- |
| Användaren kan inte utföra kombinerad registrering. | Se till att [kombinerad registrering](concept-registration-mfa-sspr-combined.md) är aktiverad. |
| Användaren kan inte aktivera appen för telefonloggningsautentiseringsautentisering. | Se till att användaren är i omfång för distribution. |
| Användaren är INTE i utrymme för lösenordslös autentisering, men presenteras med lösenordslös inloggningsalternativ, som de inte kan slutföra. | Det här scenariot inträffar när användaren har aktiverat telefonloggning i programmet innan principen skapas. <br> *Så här aktiverar du inloggning:* Lägg till användaren i omfattningen av användare som är aktiverade för lösenordslös inloggning. <br> *Så här blockerar du inloggning:* låt användaren ta bort sina autentiseringsuppgifter från det programmet. |

### <a name="troubleshoot-security-key-sign-in"></a>Felsöka inloggning av säkerhetsnyckel

| Scenario | Lösning |
| --- | --- |
| Användaren kan inte utföra kombinerad registrering. | Se till att [kombinerad registrering](concept-registration-mfa-sspr-combined.md) är aktiverad. |
| Användaren kan inte lägga till en säkerhetsnyckel i sina [säkerhetsinställningar](https://aka.ms/mysecurityinfo). | Kontrollera att [säkerhetsnycklarna](howto-authentication-passwordless-security-key.md) är aktiverade. |
| Användaren kan inte lägga till säkerhetsnyckel i inloggningsalternativen för Windows 10. | [Se till att säkerhetsnycklar för Windows loggar in](howto-authentication-passwordless-enable.md) |
| **Felmeddelande:** Vi upptäckte att den här webbläsaren eller operativsystemet inte stöder FIDO2-säkerhetsnycklar. | Lösenordslösa FIDO2-säkerhetsenheter kan endast registreras i webbläsare som stöds (Microsoft Edge, Firefox version 67) på Windows 10 version 1809 eller senare. |
| **Felmeddelande:** Företagets princip kräver att du använder en annan metod för att logga in. | Osäkra säkerhetsnycklar är aktiverade i klienten. |
| Användaren kan inte hantera min säkerhetsnyckel på Windows 10 version 1809 | Version 1809 kräver att du använder säkerhetsnyckelhanteringsprogrammet som tillhandahålls av FIDO2-nyckelleverantören. Kontakta leverantören för support. |
| Jag tror att min FIDO2 säkerhetsnyckel kan vara defekt, hur kan jag testa den. | Navigera [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/)till , ange autentiseringsuppgifter för ett testkonto, **+** koppla in den misstänkta säkerhetsnyckeln, välj knappen längst upp till höger på skärmen, klicka på skapa och gå igenom skapandeprocessen. Om det här scenariot misslyckas kan enheten vara defekt. |

## <a name="next-steps"></a>Nästa steg

- [Aktivera lösenordslösa säkerhetsnycklar för inloggning för Azure AD](howto-authentication-passwordless-security-key.md)
- [Aktivera lösenordslös inloggning med Microsoft Authenticator-appen](howto-authentication-passwordless-phone.md)
- [Läs mer om användning av autentiseringsmetoder & insikter](howto-authentication-methods-usage-insights.md)

