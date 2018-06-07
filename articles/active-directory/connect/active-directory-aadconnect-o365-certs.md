---
title: Certifikat förnyas för Office 365 och Azure AD-användare | Microsoft Docs
description: Den här artikeln förklarar hur du löser problem med e-postmeddelanden som meddelar dem om att förnya ett certifikat till Office 365-användare.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 543b7dc1-ccc9-407f-85a1-a9944c0ba1be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: e8f6b30bb7cbe82159e86fa48721afce3f9477d8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591505"
---
# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Förnya federationscertifikat för Office 365 och Azure Active Directory
## <a name="overview"></a>Översikt
För lyckad federation mellan Azure Active Directory (Azure AD) och Active Directory Federation Services (AD FS) matchar de certifikat som används av AD FS för att signera säkerhetstoken till Azure AD det som har konfigurerats i Azure AD. Några matchningsfel kan leda till brutet förtroende. Azure AD ser till att den här informationen hålls synkroniserade när du distribuerar AD FS och Webbprogramproxy (för åtkomst till extranät).

Den här artikeln innehåller ytterligare information för att hantera certifikat för tokensignering och hålla dem synkroniserade med Azure AD i följande fall:

* Du distribuerar inte Web Application Proxy och federationsmetadata är därför inte tillgänglig i extranätet.
* Du använder inte standardkonfigurationen för AD FS för certifikat för tokensignering.
* Du använder en tredjeparts identitetsprovider.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Standardkonfigurationen för AD FS för certifikat för tokensignering
Certifikat för tokensignering och token-dekryptering certifikat är vanligtvis självsignerade certifikat och är bra för ett år. Som standard AD FS innehåller en automatisk förnyelse process som kallas **AutoCertificateRollover**. Om du använder AD FS 2.0 eller senare, Office 365 och Azure AD automatiskt uppdatera ditt certifikat innan den upphör.

### <a name="renewal-notification-from-the-office-365-portal-or-an-email"></a>Förnyelse meddelande från Office 365-portalen eller ett e-postmeddelande
> [!NOTE]
> Om du har fått ett e-postmeddelande eller en portal meddelande som ber dig att förnya certifikatet för Office finns [hantera ändringar i certifikat för tokensignering](#managecerts) att kontrollera om du behöver göra något. Microsoft är medveten om ett eventuellt problem som kan leda till meddelanden för förnyelse av certifikat som skickas, även om ingen åtgärd krävs.
>
>

Azure AD försöker övervaka federationsmetadata och uppdatera certifikaten för tokensignering, som anges av dessa metadata. 30 dagar före förfallodatum för certifikat för tokensignering kontrollerar Azure AD om nya certifikat är tillgängliga genom att avsöka federationsmetadata.

* Om den kan har avsöka federationsmetadata och hämta de nya certifikaten, utfärdas utan e-postmeddelande eller en varning i Office 365-portalen för användaren.
* Om det går inte att hämta nya certifikat för tokensignering, antingen eftersom federationsmetadata kan inte nås eller automatisk förnyelse inte är aktiverat Azure AD utfärdar ett e-postmeddelande och en varning i Office 365-portalen.

![Office 365-portalen meddelande](./media/active-directory-aadconnect-o365-certs/notification.png)

> [!IMPORTANT]
> Om du använder AD FS för att garantera kontinuitet för företag, kontrollerar du att servrarna har följande uppdateringar så att autentiseringsfel för kända problem, inte sker. Detta minskar kända AD FS-proxy serverproblem för förnyelse och framtida förnyelse punkter:
>
> Server 2012 R2 - [Windows Server kan 2014-uppdateringen](http://support.microsoft.com/kb/2955164)
>
> Server 2008 R2 och 2012 - [autentisering via proxy misslyckas i Windows Server 2012 eller Windows 2008 R2 SP1](http://support.microsoft.com/kb/3094446)
>
>

## Kontrollera om certifikat som behöver uppdateras <a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>Steg 1: Kontrollera AutoCertificateRollover tillstånd
Öppna PowerShell på AD FS-servern. Kontrollera att värdet för AutoCertificateRollover har angetts till True.

    Get-Adfsproperties

![AutoCertificateRollover](./media/active-directory-aadconnect-o365-certs/autocertrollover.png)

>[!NOTE] 
>Om du använder AD FS 2.0 kan du först köra Add-Pssnapin Microsoft.Adfs.Powershell.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Steg 2: Kontrollera att AD FS och Azure AD är synkroniserade
Öppna Azure AD PowerShell-Kommandotolken på AD FS-servern och Anslut till Azure AD.

> [!NOTE]
> Du kan ladda ned Azure AD PowerShell [här](https://technet.microsoft.com/library/jj151815.aspx).
>
>

    Connect-MsolService

Kontrollera de certifikat som konfigurerats i AD FS och Azure AD litar på egenskaperna för den angivna domänen.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/active-directory-aadconnect-o365-certs/certsync.png)

Om tumavtrycken i båda utdata matchar är ditt certifikat synkroniserade med Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Steg 3: Kontrollera om ditt certifikat upphör snart att gälla
I resultatet av Get-MsolFederationProperty eller Get-AdfsCertificate, kontrollera datum under ”inte efter”. Om datumet är mindre än 30 dagar direkt, bör du vidta åtgärder.

| AutoCertificateRollover | Certifikat som är synkroniserade med Azure AD | Federationsmetadata är offentligt tillgänglig | Giltighetsperiod | Åtgärd |
|:---:|:---:|:---:|:---:|:---:|
| Ja |Ja |Ja |- |Ingen åtgärd krävs. Se [förnya certifikat för tokensignering certifikat automatiskt](#autorenew). |
| Ja |Nej |- |Mindre än 15 dagar |Förnya omedelbart. Se [förnya certifikat för tokensignering certifikat manuellt](#manualrenew). |
| Nej |- |- |Mindre än 30 dagar |Förnya omedelbart. Se [förnya certifikat för tokensignering certifikat manuellt](#manualrenew). |

\[-] Spelar ingen roll

## Förnya certifikatet för tokensignering automatiskt (rekommenderas) <a name="autorenew"></a>
Du behöver inte utföra några manuella steg om båda av följande stämmer:

* Du har distribuerat Web Application Proxy som kan ge åtkomst till federationsmetadata från extranätet.
* Du använder standardkonfigurationen för AD FS (AutoCertificateRollover är aktiverat).

Kontrollera följande för att bekräfta att certifikatet kan uppdateras automatiskt.

**1. AD FS-egenskapen AutoCertificateRollover måste anges till True.** Detta anger att AD FS kommer automatiskt att generera nya certifikat för tokensignering och tokendekryptering certifikat, innan gammalt de som går ut.

**2. Metadata för AD FS-federation är offentligt tillgänglig.** Kontrollera att din federationsmetadata är offentligt tillgänglig genom att navigera till följande URL från en dator på internet (utanför företagsnätverket):

https:// (your_FS_name) /federationmetadata/2007-06/federationmetadata.xml

där `(your_FS_name) `ersätts med värden federationstjänstnamnet används i din organisation, till exempel fs.contoso.com.  Om du ska kunna kontrollera båda av de här inställningarna, du behöver inte göra något annat.  

Exempel: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml
## Förnya certifikatet för tokensignering manuellt <a name="manualrenew"></a>
Du kan välja att förnya certifikaten för tokensignering manuellt. Till exempel kan följande scenarier fungera bättre för manuell förnyelse:

* Token signera certifikat är inte självsignerade certifikat. Den vanligaste orsaken till detta är att din organisation hanterar AD FS-certifikat som registrerats från en organisations certifikatutfärdare.
* Nätverkssäkerhet tillåter inte federationsmetadata ska vara offentligt tillgängliga.

I dessa scenarier varje gång du uppdaterar certifikat för tokensignering måste du uppdatera din Office 365-domän med hjälp av PowerShell-kommando, Update-MsolFederatedDomain.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Steg 1: Kontrollera att AD FS har nya certifikat för tokensignering
**Icke-standardkonfigurationen**

Om du använder en icke-förvalt konfiguration av AD FS (där **AutoCertificateRollover** är inställd på **FALSKT**), använder du troligtvis anpassade certifikat (inte självsignerade). Mer information om hur du förnyar AD FS-certifikat för tokensignering finns [vägledning för kunder som inte använder AD FS självsignerade certifikat](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Federationsmetadata är inte tillgänglig**

Å andra sidan, om **AutoCertificateRollover** är inställd på **SANT**, men din federationsmetadata är inte offentligt tillgänglig, kontrollera först att nya certifikat för tokensignering har genererats av AD FS. Bekräfta att du har nya certifikaten för tokensignering genom att utföra följande steg:

1. Kontrollera att du har loggat in till den primära AD FS-servern.
2. Kontrollera de aktuella signeringscertifikat i AD FS genom att öppna ett PowerShell-kommandofönster och köra följande kommando:

    PS C:\>Get-ADFSCertificate – CertificateType certifikat för tokensignering

   > [!NOTE]
   > Om du använder AD FS 2.0, bör du köra Add-Pssnapin Microsoft.Adfs.Powershell först.
   >
   >
3. Granska kommandoutdata på alla certifikat i listan. Om AD FS har genererat ett nytt certifikat, bör du se två certifikat i utdata: för vilka den **IsPrimary** värdet är **True** och **NotAfter** datumet ligger inom fem dagar och ett som **IsPrimary** är **FALSKT** och **NotAfter** handlar om ett år i framtiden.
4. Om du bara ser ett certifikat och **NotAfter** datumet ligger inom fem dagar, måste du generera ett nytt certifikat.
5. Om du vill skapa ett nytt certifikat kör du följande kommando i Kommandotolken PowerShell: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.
6. Kontrollera uppdateringen genom att köra följande kommando igen: PS C:\>Get-ADFSCertificate – CertificateType certifikat för tokensignering

Två certifikat ska visas nu, ett som har en **NotAfter** datumet ungefär ett år i framtiden, och som den **IsPrimary** värdet är **FALSKT**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Steg 2: Uppdatera den nya token som signerar certifikat för Office 365-förtroende
Uppdatera Office 365 med den nya token som signerar certifikat som ska användas för förtroendet på följande sätt.

1. Öppna Microsoft Azure Active Directory-modulen för Windows PowerShell.
2. Kör $cred = Get-Credential. När denna cmdlet efterfrågar autentiseringsuppgifter, skriver du cloud service administratörskontots autentiseringsuppgifter.
3. Kör Anslut MsolService – autentiseringsuppgifter $cred. Denna cmdlet ansluter till Molntjänsten. Skapa en kontext som ansluter till Molntjänsten krävs innan du kör ytterligare cmdlets som installerats av verktyget.
4. Om du kör kommandona på en dator som inte är den primära federationsservern i AD FS, kör Set-MSOLAdfscontext-datorn &lt;primära AD FS-servern&gt;, där &lt;primära AD FS-servern&gt; är det interna FQDN namnet på den primära AD FS-servern. Denna cmdlet skapar en kontext som ansluter till AD FS.
5. Kör Update MSOLFederatedDomain – DomainName &lt;domän&gt;. Denna cmdlet uppdaterar inställningarna från AD FS i Molntjänsten och konfigurerar en förtroenderelation mellan två.

> [!NOTE]
> Om du behöver stöd för flera toppnivådomäner, till exempel contoso.com och fabrikam.com, måste du använda den **SupportMultipleDomain** växel med alla cmdlets. Mer information finns i [stöd för flera domäner på översta nivån](active-directory-aadconnect-multiple-domains.md).
>


## Reparera Azure AD-förtroende med hjälp av Azure AD Connect <a name="connectrenew"></a>
Om du har konfigurerat dina AD FS-servergrupp och Azure AD-förtroende med hjälp av Azure AD Connect kan du använda Azure AD Connect för att identifiera om du behöver vidta några åtgärder för certifikat för tokensignering. Du kan använda Azure AD Connect för att göra det om du behöver förnya certifikat.

Mer information finns i [reparera förtroendet](active-directory-aadconnect-federation-management.md).

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>AD FS och Azure AD-certifikat update steg
Token signera certifikat är standard-X509 certifikat som används för att signera alla variabler som federationsservern utfärdar på ett säkert sätt. Certifikat för tokendekryptering är standard-X509 certifikat som används för att dekryptera alla inkommande token. 

Som standard konfigureras AD FS för att generera certifikat för tokensignering och tokendekryptering certifikat automatiskt, både när inledande konfiguration och när certifikaten närmar sig sin upphör att gälla.

Azure AD försöker hämta ett nytt certifikat från dina metadata för federation service 30 dagar före utgången av det aktuella certifikatet. Om ett nytt certifikat inte är tillgängliga vid den tiden fortsätter Azure AD att övervaka dagliga regelbundet metadata. Så snart det nya certifikatet är tillgängligt i metadata, uppdateras federation inställningarna för domänen med ny information för certifikat. Du kan använda `Get-MsolDomainFederationSettings` att verifiera om du ser det nya certifikatet i NextSigningCertificate / SigningCertificate.

Mer information om certifikat för tokensignering certifikat i AD FS finns [hämta och konfigurera certifikat för tokensignering och Token dekryptering certifikat för AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs)
