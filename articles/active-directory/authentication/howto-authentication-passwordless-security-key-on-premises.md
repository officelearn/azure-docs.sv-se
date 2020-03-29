---
title: Inloggning av lösenordslös säkerhetsnyckel till lokala resurser (förhandsversion) – Azure Active Directory
description: Lär dig hur du aktiverar inloggning med lösenordslös säkerhetsnyckel till lokala resurser med Azure Active Directory (förhandsversion)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b29f84931c169ffe1c2c81d5e32201cbc63fc88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942879"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory-preview"></a>Aktivera inloggning av lösenordslös säkerhetsnyckel till lokala resurser med Azure Active Directory (förhandsversion)

Det här dokumentet fokuserar på att aktivera lösenordslös autentisering till lokala resurser för miljöer med både **Azure AD-anslutna** och **hybrid Azure AD-anslutna** Windows 10-enheter. Den här funktionen ger sömlös enkel inloggning (SSO) till lokala resurser med Hjälp av Microsoft-kompatibla säkerhetsnycklar.

|     |
| --- |
| FIDO2-säkerhetsnycklar är en offentlig förhandsversion av Azure Active Directory. Mer information om förhandsgranskningar finns i [Tilläggsvillkor för användning för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>SSO till lokala resurser med FIDO2-nycklar

Azure Active Directory (AD) kan utfärda Kerberos Ticket Granting Tickets (TGTs) för en eller flera av dina Active Directory-domäner. Med den här funktionen kan användare logga in på Windows med moderna autentiseringsuppgifter som FIDO2-säkerhetsnycklar och komma åt traditionella Active Directory-baserade resurser. Kerberos-servicebiljetter och auktorisering fortsätter att styras av dina lokala Active Directory-domänkontrollanter.

Ett Azure AD Kerberos Server-objekt skapas i din lokala Active Directory och publiceras sedan på ett säkert sätt i Azure Active Directory. Objektet är inte associerat med några fysiska servrar. Det är helt enkelt en resurs som kan användas av Azure Active Directory för att generera Kerberos TGTs för din Active Directory-domän.

![Få en biljettbeviljande biljett (TGT) från Azure AD och AD DS](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. Användaren loggar in på sin Windows 10-enhet med en FIDO2-säkerhetsnyckel och autentiserar till Azure AD.
1. Azure AD kontrollerar katalogen för en Kerberos-servernyckel som matchar användarens lokala AD-domän.
   1. Azure AD genererar en Kerberos TGT för användarens lokala AD-domän. TGT innehåller endast användarens SID. Inga auktoriseringsdata ingår i TGT.
1. TGT returneras till klienten tillsammans med deras Azure AD Primary Refresh Token (PRT).
1. Klientdatorn kontaktar en lokal AD-domänkontrollant och byter den partiella TGT för en fullt utvecklad TGT.
1. Klientdatorn har nu en Azure AD PRT och en fullständig Active Directory TGT och kan komma åt både molnresurser och lokala resurser.

## <a name="requirements"></a>Krav

Organisationer måste slutföra stegen för att [aktivera lösenordslös säkerhetsnyckelskyltning på Windows 10-enheter (förhandsversion)](howto-authentication-passwordless-security-key.md) innan du slutför stegen i den här artikeln.

Organisationer måste också uppfylla följande programvarukrav.

- Enheter måste köra Windows 10 Insider Build 18945 eller nyare.
- Du måste ha version 1.4.32.0 eller senare av [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect).
  - Mer information om tillgängliga Azure AD-hybridautentiseringsalternativ finns i [Välj rätt autentiseringsmetod för din Azure Active Directory hybrididentitetslösning](../../security/fundamentals/choose-ad-authn.md) och [Välj vilken installationstyp som ska användas för Azure AD Connect](../hybrid/how-to-connect-install-select-installation.md).
- Windows Server-domänkontrollanterna måste ha följande korrigeringsfiler installerade:
    - För Windows Server 2016 -https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - För Windows Server 2019 -https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>Scenarier som stöds

Scenariot stöder enkel inloggning (SSO) i båda följande scenarier:

- För molnresurser som Office 365 och andra SAML-aktiverade program.
- För lokala resurser och Windows-integrerad autentisering till webbplatser. Resurserna kan innehålla webbplatser och SharePoint-webbplatser som kräver IIS-autentisering och /eller resurser som använder NTLM-autentisering.

### <a name="unsupported-scenarios"></a>Scenarier som inte stöds

Följande scenarier stöds inte:

- Windows Server Active Directory Domain Services (AD DS) domän anslöt sig (endast lokala enheter) distribution.
- RDP-, VDI- och Citrix-scenarier med hjälp av en säkerhetsnyckel.
- S/MIME med hjälp av en säkerhetsnyckel.
- "Kör som" med hjälp av en säkerhetsnyckel.
- Logga in på en server med hjälp av säkerhetsnyckeln.

## <a name="create-kerberos-server-object"></a>Skapa Kerberos-serverobjekt

Administratörer använder PowerShell-verktyg från sin Azure AD Connect-server för att skapa ett Azure AD Kerberos Server-objekt i sin lokala katalog. Kör följande steg i varje domän och skog i organisationen som innehåller Azure AD-användare:

1. Uppgradera till den senaste versionen av Azure AD Connect. Instruktionerna förutsätter att du redan har konfigurerat Azure AD Connect för att stödja din hybridmiljö.
1. Öppna en upphöjd PowerShell-fråga på Azure AD Connect Server och navigera till`C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. Kör följande PowerShell-kommandon för att skapa ett nytt Azure AD Kerberos-serverobjekt i både din lokala Active Directory-domän och Azure Active Directory-klient.

> [!NOTE]
> Ersätt `contoso.corp.com` i följande exempel med ditt lokala Active Directory-domännamn.

```powerShell
Import-Module ".\AzureAdKerberos.psd1"

# Specify the on-premises Active Directory domain. A new Azure AD
# Kerberos Server object will be created in this Active Directory domain.
$domain = "contoso.corp.com"

# Enter an Azure Active Directory global administrator username and password.
$cloudCred = Get-Credential

# Enter a domain administrator username and password.
$domainCred = Get-Credential

# Create the new Azure AD Kerberos Server object in Active Directory
# and then publish it to Azure Active Directory.
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>Visa och verifiera Azure AD Kerberos Server

Du kan visa och verifiera den nyligen skapade Azure AD Kerberos Server med följande kommando:

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Det här kommandot matar ut egenskaperna för Azure AD Kerberos Server. Du kan granska egenskaperna för att kontrollera att allt är i gott skick.

| Egenskap | Beskrivning |
| --- | --- |
| ID | Det unika ID:t för AD DS DC-objektet. Detta ID kallas ibland för "plats" eller "gren-ID". |
| DomainDnsName | DNS-domännamnet för Active Directory-domänen. |
| Datorkonto | Datorkontoobjektet för Azure AD Kerberos Server-objektet (DOMÄNKONTROLLANTEN). |
| UserAccount | Det inaktiverade användarkontoobjektet som innehåller krypteringsnyckeln för Azure AD Kerberos Server TGT. DN för detta konto är`CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` |
| KeyVersion (KeyVersion) | Nyckelversionen av Krypteringsnyckeln för Azure AD Kerberos Server TGT. Versionen tilldelas när nyckeln skapas. Versionen ökas sedan varje gång nyckeln roteras. Ökningarna baseras på replikeringsmetadata och sannolikt större än en. Den ursprungliga *KeyVersion* kan till exempel vara *192272*. Första gången nyckeln roteras kan versionen gå vidare till *212621*. Det viktiga att kontrollera är att *KeyVersion* för det lokala objektet och *CloudKeyVersion* för molnobjektet är desamma. |
| KeyUpdatedOn | Datum och tid då Azure AD Kerberos Server TGT-krypteringsnyckeln uppdaterades eller skapades. |
| KeyUpdatedFrån | Domänkontrollanten där Azure AD Kerberos Server TGT-krypteringsnyckeln senast uppdaterades. |
| CloudId (på) | ID:et från Azure AD-objektet. Måste matcha ID ovan. |
| CloudDomainDnsName | *DomainDnsName* från Azure AD-objektet. Måste matcha *DomainDnsName* ovan. |
| CloudKeyVersion (CloudKeyVersion) | *KeyVersion* från Azure AD-objektet. Måste matcha *KeyVersion* ovan. |
| CloudKeyUpdatedOn | *KeyUpdatedOn* från Azure AD-objektet. Måste matcha *KeyUpdatedOn* ovan. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Rotera Azure AD Kerberos Server-nyckeln

Azure AD Kerberos Server-krypteringskrbtgt-nycklarna bör roteras regelbundet. Vi rekommenderar att du följer samma schema som du använder för att rotera alla andra Krbtgt-nycklar för Active Directory-domänkontrollant.

> [!WARNING]
> Det finns andra verktyg som kan rotera krbtgt-nycklarna, men du måste använda de verktyg som nämns i det här dokumentet för att rotera krbtgt-nycklarna till din Azure AD Kerberos Server. Detta säkerställer att nycklarna uppdateras i både lokala AD och Azure AD.

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Ta bort Azure AD Kerberos-servern

Om du vill återställa scenariot och ta bort Azure AD Kerberos Server från både lokala Active Directory och Azure Active Directory kör du följande kommando:

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Scenarier med flera skogar och flera domäner

Azure AD Kerberos-serverobjektet representeras i Azure AD som ett *KerberosDomain-objekt.* Varje lokal Active Directory-domän representeras som ett enda *KerberosDomain-objekt* i Azure AD.

Din organisation har till exempel en Active Directory-skog med två domäner `contoso.com` och `fabrikam.com`. Om du väljer att tillåta Att Azure AD utfärdar Kerberos TGTs för hela skogen finns det två *KerberosDomain-objekt* i Azure AD. Ett *KerberosDomain-objekt* för `contoso.com` `fabrikam.com`och ett för . Om du har flera Active Directory-skogar finns det ett *KerberosDomain-objekt* för varje domän i varje skog.

Du måste köra stegen för att [skapa Kerberos-serverobjekt](#create-kerberos-server-object) i varje domän och skog i organisationen som innehåller Azure AD-användare.

## <a name="known-behavior"></a>Känt beteende

Logga in med FIDO blockeras om ditt lösenord har upphört att gälla. Förhoppningen är att användaren ska återställa sitt lösenord innan de kan logga in med FIDO.

## <a name="troubleshooting-and-feedback"></a>Felsökning och feedback

Om du vill dela feedback eller stöta på problem när du förhandsgranskar den här funktionen delar du via Appen Windows Feedback Hub med följande steg:

1. Starta **Feedback Hub** och se till att du är inloggad.
1. Skicka feedback under följande kategorisering:
   - Kategori: Säkerhet och sekretess
   - Underkategori: FIDO
1. Om du vill fånga in loggar använder du alternativet återskapa **mitt problem**

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="does-this-work-in-my-on-premises-environment"></a>Fungerar det här i min lokala miljö?

Den här funktionen fungerar inte för en ren lokal AD DS-miljö (Active Directory Domain Services).

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Min organisation kräver tvåfaktorsautentisering för att komma åt resurser. Vad kan jag göra för att stödja detta krav?

Säkerhetsnycklar finns i en mängd olika formfaktorer. Kontakta enhetstillverkaren av intresse för att diskutera hur deras enheter kan aktiveras med en PIN-kod eller biometrisk som en andra faktor.

### <a name="can-admins-set-up-security-keys"></a>Kan administratörer ställa in säkerhetsnycklar?

Vi arbetar med den här funktionen för allmän tillgänglighet (GA).

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Var kan jag hitta kompatibla säkerhetsnycklar?

[FIDO2-säkerhetsnycklar](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Vad gör jag om jag förlorar min säkerhetsnyckel?

Du kan ta bort nycklar från Azure-portalen genom att navigera till sidan **Säkerhetsinformation** och ta bort säkerhetsnyckeln.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Jag kan inte använda FIDO direkt efter att jag har skapat en azure AD-ansluten hybridmaskin

Om du har installerat en hybrid-Azure AD-ansluten dator måste du efter domänanslutningen och omstarten logga in med ett lösenord och vänta på att principen ska synkroniseras innan du kan använda FIDO för att logga in.

- Kontrollera din aktuella `dsregcmd /status` status genom att skriva in i ett kommandofönster och kontrollera att både *AzureAdJoined* och *DomainJoined* visar *JA*.
- Den här fördröjningen är en känd begränsning för domänanslutna enheter och är inte FIDO-specifik.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Jag kan inte få SSO till min NTLM-nätverksresurs efter att ha loggat in med FIDO och få en autentiseringsuppgifter prompt

Kontrollera att tillräckligt många domänkontrollanter har korrigerats för att svara i tid för att serva din resursbegäran. Om du vill kontrollera om du kan se en domänkontrollant som kör funktionen läser du utdata från `nltest /dsgetdc:contoso /keylist /kdc`.

## <a name="next-steps"></a>Nästa steg

[Läs mer om lösenordslös](concept-authentication-passwordless.md)
