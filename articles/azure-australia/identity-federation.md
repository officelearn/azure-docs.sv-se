---
title: Identitets Federation i Azure Australien
description: Vägledning om hur du konfigurerar identitets Federation inom de australiska regionerna för att uppfylla de särskilda kraven i den australiska regeringens politik, förordningar och lagstiftning.
author: galey801
ms.service: azure-australia
ms.topic: quickstart
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: e161e36ee7b403381b65f52a6f416be09025d0a2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570861"
---
# <a name="identity-federation-in-azure-australia"></a>Identitets Federation i Azure Australien

Identitets hantering och Federation med offentliga moln erbjudanden är en av de viktigaste första stegen för att använda molnet. Microsofts Azure Active Directory tjänst lagrar användar information för att ge åtkomst till moln tjänster och är ett krav för att använda andra Azure-tjänster.

Den här artikeln beskriver viktiga design punkter för att implementera Azure Active Directory, synchronising-användare från en Active Directory Domain Services domän och implementering av säker autentisering. Rekommendationerna i de australiska cyberhot-Security Centerens informations säkerhets manual (ISM) och Azure-certifierings rapporter är särskilt placerade.

Klassificeringen av information som lagras i Azure Active Directory bör meddela beslut om hur den är utformad. Följande utdrag tillhandahålls från [certifierings rapporten för ACSC – Microsoft Azure](https://aka.ms/au-irap):

>**ACSC certifierings rapport – Microsoft Azure** Azure Active Directory (Azure AD) måste konfigureras med Active Directory Federation Services när samväldet samväldet entiteter klassificerar användningen av och data innehållet i sina Active Directory vid skyddad. Även om Active Directory data som inte klassificeras som UDLM-klassificering (deklassificerad spridning) inte kräver Federation, kan samväldet entiteter fortfarande implementera Federation för att minimera risker som är kopplade till tjänsten från utsidan i Australien.

Som sådan information är synkroniserad och den mekanism som användarna autentiseras med, är de två viktiga problemen som beskrivs här.

## <a name="key-design-considerations"></a>Viktiga design överväganden

### <a name="user-synchronisation"></a>Användarens Synchronisation

När du distribuerar Azure AD Connect finns det flera beslut som måste fattas om de data som ska vara synkroniserad. Azure AD Connect baseras på Microsoft Identity Manager och ger en robust funktions uppsättning för [omvandling](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-best-practices-changing-default-configuration) av data mellan kataloger.

Microsofts konsult tjänster kan användas för att utföra en ADRAP utvärdering av din befintliga Windows Server-Active Directory. ADRAP hjälper till att fastställa eventuella problem som kan behöva korrigeras innan synchronising med Azure Active Directory. Microsoft Premier Support-avtal omfattar vanligt vis denna tjänst.

[IDFix-verktyget](https://docs.microsoft.com/office365/enterprise/install-and-run-idfix) söker igenom din lokala Active Directory-domän efter problem innan synchronising med Azure AD. IDFix är ett viktigt första steg innan du implementerar Azure AD Connect. Även om en IDFix-genomsökning kan identifiera ett stort antal problem kan många av de här problemen antingen lösas snabbt med skript eller med hjälp av data transformationer i Azure AD Connect.

Azure AD kräver att användare har en externt flyttbar toppnivå domän för att aktivera autentisering. Om din domän har ett UPN-suffix som inte är externt flyttbart, måste du ange [alternativt inloggnings-ID](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-userprincipalname) i AD Connect till användarens e-postattribut. Användarna loggar sedan in på Azure-tjänster med sin e-postadress i stället för deras domän inloggning.

UPN-suffixet för användar konton kan också ändras med verktyg som PowerShell, men Det kan ha oförutsedda följder för andra anslutna system och anses inte längre vara bästa praxis.

När du bestämmer vilka attribut som ska synkroniseras för att Azure Active Directory, är det säkrast att anta att alla attribut krävs. Det är ovanligt att en katalog innehåller faktiska skyddade data, men det rekommenderas att du utför en granskning. Om skyddade data hittas i katalogen kan du bedöma effekten av att utesluta eller transformera attributet. Som en hjälp guide finns det en lista över attribut som Microsoft Cloud Services [kräver](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-attributes-synchronized).

### <a name="authentication"></a>Authentication

Det är viktigt att förstå vilka alternativ som är tillgängliga och hur de kan användas för att skydda slutanvändare.
Microsoft erbjuder [tre interna lösningar](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin) för att autentisera användare mot Azure Active Directory:

* Hash-synkronisering av lösen ord – de hash-baserade lösen orden från Active Directory Domain Services synkroniserad av Azure AD Connect till Azure Active Directory.
* [Direkt autentisering](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) – lösen ord finns kvar i Active Directory Domain Services. Användare autentiseras mot Active Directory Domain Services via en agent. Inga lösen ord lagras i Azure AD.
* [FEDERERAD SSO](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-whatis) -Azure Active Directory är federerad med Active Directory Federation Services (AD FS) under inloggningen dirigerar Azure användare till Active Directory Federation Services (AD FS) att autentisera sig. Inga lösen ord lagras i Azure AD.

Lösen ordets hash-Synchronisation kan användas i scenarier där officiella: känsliga och underordnade data lagras i katalogen. Scenarier där skyddade data lagras kräver ett av de två återstående alternativen.

Alla tre av dessa alternativ har stöd för [tillbakaskrivning av lösen ord](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback), vilket [ACSC-konsument guiden](https://aka.ms/au-irap) rekommenderar att den inaktive ras. Ändå organisationer bör utvärdera risken för att inaktivera tillbakaskrivning av lösen ord mot produktivitets vinster och minska support ansträngningen för att använda självbetjäning för återställning av lösen ord.

#### <a name="pass-through-authentication-pta"></a>Direktautentisering (PTA)

Direktautentisering släpptes när IRAP-utvärderingen hade slutförts och därför bör utvärderas individuellt för att avgöra hur lösningen passar din organisations riskhanterings profil. Direktautentisering prioriteras framför federationen av Microsoft på grund av den förbättrade säkerhets position.

![Direktautentisering](media/pta1.png)

Direktautentisering visar flera design faktorer som ska övervägas:

* Genom strömnings agenten måste kunna upprätta utgående anslutningar till Microsoft Cloud tjänster.
* Installera mer än en agent för att säkerställa att tjänsten är hög tillgänglig. Vi rekommenderar att du distribuerar minst tre agenter och upp till högst 12 agenter.
* Bästa praxis är att undvika att installera agenten direkt på en Active Directory-domän kontrollanter. Som standard när du distribuerar Azure AD Connect med direktautentisering, kommer den att installera agenten på AD Connect-servern.
* Direktautentisering är ett lägre underhålls alternativ än Active Directory Federation Services (AD FS) eftersom det inte kräver särskilda Server infrastrukturer, certifikat hantering eller inkommande brand Väggs regler.

#### <a name="active-directory-federation-services-adfs"></a>Active Directory Federation Services (AD FS) (ADFS)

Active Directory Federation Services (AD FS) togs med i IRAP-utvärderingen och godkänns för användning i skyddade miljöer.

![Federation](media/federated-identity.png)

Active Directory Federation Services (AD FS) visar flera design faktorer som ska övervägas:

* Federations tjänster kräver nätverks ingångar för HTTPS-trafik från Internet eller med minst Microsofts tjänst slut punkter.
* Federation Services använder PKI och certifikat, vilket kräver kontinuerlig hantering och förnyelse.
* Federations tjänster bör distribueras på dedikerade servrar och kräver den relevanta nätverks infrastrukturen för att på ett säkert sätt kunna nås externt.

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

Avsnittet ISM i Multi-Factor Authentication rekommenderar att du implementerar det i följande scenarier baserat på din risk profil:

* Autentisera standard användare
* Autentisera privilegierade konton
* Autentisera användare fjärråtkomst
* Användare som utför privilegierade åtgärder

Azure Active Directory ger Multi-Factor Authentication som kan aktive ras för antingen alla eller en delmängd av användare (till exempel endast privilegierade konton). Microsoft tillhandahåller också en lösning som kallas villkorlig åtkomst, vilket ger mer detaljerad kontroll över hur Multi-Factor Authentication används (till exempel bara när användare loggar in från fjärr-IP-adressintervall).

Azure Multi-Factor Authentication stöder följande ISM-godkända formulär för verifiering:

* Telefonsamtal
* SMS-meddelande
* Microsoft Authenticator program
* Maskinvaru-token som stöds

Privileged Identity Management en komponent i Azure Active Directory kan användas för att genomdriva användningen av Multi-Factor Authentication när användare höjer sina behörigheter för att uppfylla den fjärde rekommendationen.

## <a name="next-steps"></a>Nästa steg

Läs artikeln om [rollbaserade åtkomst kontroller och Privileged Identity Management](role-privileged.md).
