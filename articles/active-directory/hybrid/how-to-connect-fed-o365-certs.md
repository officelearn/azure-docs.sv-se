---
title: Certifikat förnyelse för Office 365 och Azure AD-användare | Microsoft Docs
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
ms.openlocfilehash: 0205552c073bf03c40da4d108256fa09e71fbbe3
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46314757"
---
# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Förnya federationscertifikat för Office 365 och Azure Active Directory
## <a name="overview"></a>Översikt
För lyckad federation mellan Azure Active Directory (Azure AD) och Active Directory Federation Services (AD FS), bör de certifikat som används av AD FS för att signera säkerhetstoken till Azure AD matchar konfigurationen i Azure AD. Eventuella matchningsfel kan leda till brutet förtroende. Azure AD till att den här informationen hålls synkroniserade när du distribuerar AD FS och Web Application Proxy (för åtkomst till extranät).

Den här artikeln innehåller ytterligare information för att hantera certifikat för tokensignering och synkronisera dem med Azure AD i följande fall:

* Du distribuerar inte Web Application Proxy och därför federationsmetadata är inte tillgänglig i extranätet.
* Du använder inte standardkonfigurationen för AD FS för token som signerar certifikat.
* Du använder en tredjeparts identitetsprovider.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Standardkonfigurationen för AD FS för certifikat för tokensignering
Token-signering och token dekryptera certifikat är vanligtvis självsignerade certifikat och är bra för ett år. Som standard AD FS innehåller en automatisk förnyelse process som kallas **AutoCertificateRollover**. Om du använder AD FS 2.0 eller senare, Office 365 och Azure AD automatiskt uppdaterar dina certifikat innan den upphör.

### <a name="renewal-notification-from-the-office-365-portal-or-an-email"></a>Förnyelse-meddelande från Office 365-portalen eller ett e-postmeddelande
> [!NOTE]
> Om du har fått ett e-postmeddelande eller en portal-meddelande visas som ber dig att förnya certifikatet för Office, se [hantera ändringar i certifikat för tokensignering](#managecerts) att kontrollera om du behöver vidta några åtgärder. Microsoft är medveten om ett eventuellt problem som kan leda till meddelanden för förnyelse av certifikat som skickas, även om ingen åtgärd krävs.
>
>

Azure AD försöker övervaka federationsmetadata och uppdatera certifikaten för tokensignering, som anges av dessa metadata. 30 dagar före förfallodatum för certifikat för tokensignering Azure AD som kontrollerar om nya certifikat är tillgängliga genom att avsöka federationsmetadata.

* Om den kan har avsöka federationsmetadata och hämta de nya certifikaten, utfärdas utan e-postmeddelande eller en varning i Office 365-portalen för användaren.
* Om det går inte att hämta nya certifikat för tokensignering, skickar antingen eftersom federationsmetadata kan inte nås eller är inte aktiverat för automatisk certifikatförnyelse, Azure AD ett e-postmeddelande och en varning i Office 365-portalen.

![Office 365 portal-meddelande](./media/how-to-connect-fed-o365-certs/notification.png)

> [!IMPORTANT]
> Om du använder AD FS för att garantera kontinuitet för företag, kontrollerar du att dina servrar har följande uppdateringar så att inte sker autentiseringsförsök för kända problem. Detta minskar kända AD FS-proxy serverproblem för den här förnyelse och för framtida förnyelseperioder:
>
> Server 2012 R2 - [Windows Server maj 2014 Samlad](http://support.microsoft.com/kb/2955164)
>
> Server 2008 R2 och 2012 - [autentisering via proxy misslyckas i Windows Server 2012 eller Windows 2008 R2 SP1](http://support.microsoft.com/kb/3094446)
>
>

## Kontrollera om certifikat som behöver uppdateras <a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>Steg 1: Kontrollera AutoCertificateRollover-tillstånd
Öppna PowerShell på AD FS-servern. Kontrollera att värdet för AutoCertificateRollover har angetts till True.

    Get-Adfsproperties

![AutoCertificateRollover](./media/how-to-connect-fed-o365-certs/autocertrollover.png)

>[!NOTE] 
>Om du använder AD FS 2.0 kan du först köra Add-Pssnapin Microsoft.Adfs.Powershell.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Steg 2: Kontrollera att AD FS och Azure AD är synkroniserade
Öppna MSOnline PowerShell-kommandotolk på din AD FS-servern och Anslut till Azure AD.

> [!NOTE]
> MSOL-cmdlet: ar är en del av MSOnline PowerShell-modulen.
> Du kan hämta PowerShell-modulen MSOnline direkt från PowerShell-galleriet.
> 
>

    Install-Module MSOnline

Anslut till Azure AD med hjälp av PowerShell-modulen MSOnline.

    Import-Module MSOnline
    Connect-MsolService

Kontrollera att certifikaten som konfigurerats i AD FS och Azure AD litar egenskaper för den angivna domänen.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/how-to-connect-fed-o365-certs/certsync.png)

Om tumavtrycken i båda utdata matchar är ditt certifikat synkroniserade med Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Steg 3: Kontrollera om ditt certifikat som snart upphör att gälla
I utdata från Get-MsolFederationProperty eller Get-AdfsCertificate, kontrollera datum under ”inte efter”. Om datumet är mindre än 30 dagar, bör du vidta åtgärder.

| AutoCertificateRollover | Certifikat synkroniserade med Azure AD | Federationsmetadata är allmänt tillgänglig | Giltighet | Åtgärd |
|:---:|:---:|:---:|:---:|:---:|
| Ja |Ja |Ja |- |Ingen åtgärd behövs. Se [förnya token signera certifikat automatiskt](#autorenew). |
| Ja |Nej |- |Mindre än 15 dagar |Förnya omedelbart. Se [förnya token signera certifikat manuellt](#manualrenew). |
| Nej |- |- |Mindre än 30 dagar |Förnya omedelbart. Se [förnya token signera certifikat manuellt](#manualrenew). |

\[Spelar ingen roll-]

## Förnya certifikatet för tokensignering automatiskt (rekommenderas) <a name="autorenew"></a>
Du behöver inte utföra några manuella åtgärder om följande stämmer:

* Du har distribuerat Web Application Proxy som kan aktivera åtkomst till federationsmetadata från extranätet.
* Du använder standardkonfigurationen AD FS (AutoCertificateRollover är aktiverat).

Kontrollera följande för att bekräfta att certifikatet kan uppdateras automatiskt.

**1. AD FS-egenskapen AutoCertificateRollover måste anges till True.** Detta anger att AD FS genererar automatiskt nya tokensignering och tokendekryptering certifikat, innan gammalt de som upphör att gälla.

**2. AD FS-federationsmetadata är allmänt tillgänglig.** Kontrollera att din federationsmetadata är offentligt tillgänglig genom att navigera till följande URL från en dator i det offentliga internet (från företagsnätverket):

https:// (your_FS_name) /federationmetadata/2007-06/federationmetadata.xml

där `(your_FS_name) `ersätts med värden federationstjänstnamnet din organisation använder, till exempel fs.contoso.com.  Om du har verifierat båda dessa inställningar, du behöver inte göra något annat.  

Exempel: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml
## Förnya certifikatet för tokensignering manuellt <a name="manualrenew"></a>
Du kan välja att förnya certifikaten för tokensignering manuellt. Till exempel kanske följande scenarier fungerar bättre för manuell förnyelse:

* Token signerar certifikat är inte självsignerade certifikat. Den vanligaste orsaken till detta är att din organisation hanterar AD FS-certifikat som registrerats från en organisations certifikatutfärdare.
* Nätverkssäkerhet tillåter inte att vara offentligt tillgängliga federationsmetadata.

I dessa scenarier kan varje gång du uppdaterar den token som signerar certifikat, måste du även uppdatera din Office 365-domän med hjälp av PowerShell-kommando, Update-MsolFederatedDomain.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Steg 1: Kontrollera att AD FS har nya certifikat för tokensignering
**Icke-standardkonfigurationen**

Om du använder en icke-standard-konfiguration av AD FS (där **AutoCertificateRollover** är inställd på **FALSKT**), använder du troligtvis anpassade certifikat (inte självsignerade). Mer information om hur du förnyar AD FS-certifikat för tokensignering finns i [vägledning för kunder som inte använder AD FS självsignerade certifikat](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Federationsmetadata är inte allmänt tillgängliga**

Å andra sidan, om **AutoCertificateRollover** är inställd på **SANT**, men din federationsmetadata inte är allmänt tillgänglig, kontrollera först att nya certifikat för tokensignering har genererats av AD FS. Bekräfta att du har nya token som signerar certifikat genom att utföra följande steg:

1. Kontrollera att du har loggat in till den primära AD FS-servern.
2. Kontrollera de aktuella signeringscertifikat i AD FS genom att öppna ett PowerShell-kommandofönster och köra följande kommando:

    PS C:\>Get-ADFSCertificate – CertificateType token-signering

   > [!NOTE]
   > Om du använder AD FS 2.0 kan köra du Add-Pssnapin Microsoft.Adfs.Powershell först.
   >
   >
3. Titta på utdata från kommandot på alla certifikat i listan. Om AD FS har skapat ett nytt certifikat, bör du se två certifikat i utdata: en som den **IsPrimary** värdet är **SANT** och **NotAfter** infaller inom 5 dagar , och en som **IsPrimary** är **FALSKT** och **NotAfter** är ungefär ett år i framtiden.
4. Om du bara ser ett certifikat och **NotAfter** infaller inom 5 dagar, måste du generera ett nytt certifikat.
5. Generera ett nytt certifikat genom att köra följande kommando i en PowerShell-kommandotolk: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.
6. Kontrollera uppdateringen genom att köra följande kommando igen: PS C:\>Get-ADFSCertificate – CertificateType token-signering

Två certifikat bör visas nu, varav har en **NotAfter** datum på ungefär ett år i framtiden, och för vilka den **IsPrimary** värdet är **FALSKT**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Steg 2: Uppdatera den nya token som signerar certifikat för Office 365-förtroende
Uppdatera Office 365 med den nya token som signerar certifikat som ska användas för förtroende, på följande sätt.

1. Öppna Microsoft Azure Active Directory-modulen för Windows PowerShell.
2. Kör $cred = Get-Credential. När denna cmdlet uppmanar dig om autentiseringsuppgifter, skriver du cloud service administratörskontots autentiseringsuppgifter.
3. Kör Connect-MsolService – $cred av autentiseringsuppgifter. Denna cmdlet har du tillgång till Molntjänsten. Skapa en kontext som ansluter till Molntjänsten krävs innan du kör någon av de ytterligare cmdletar som installerats av verktyget.
4. Om du kör dessa kommandon på en dator som inte är den primära federationsservern i AD FS, kör Set-MSOLAdfscontext-datorn &lt;AD FS-primärserver&gt;, där &lt;AD FS-primärserver&gt; är det interna FQDN namnet på den primära AD FS-servern. Denna cmdlet skapar en kontext som ansluter till AD FS.
5. Kör Update-MSOLFederatedDomain-DomainName &lt;domän&gt;. Den här cmdleten uppdaterar inställningarna från AD FS i Molntjänsten och konfigurerar förtroenderelationen mellan två.

> [!NOTE]
> Om du behöver stöd för flera toppdomänerna, till exempel contoso.com och fabrikam.com, måste du använda den **SupportMultipleDomain** växel med cmdlets. Mer information finns i [stöd för flera domäner på toppnivå nivå](how-to-connect-install-multiple-domains.md).
>


## Reparera förtroendet i Azure AD med hjälp av Azure AD Connect <a name="connectrenew"></a>
Om du har konfigurerat dina AD FS-servergrupp och Azure AD-förtroende med hjälp av Azure AD Connect kan du använda Azure AD Connect för att identifiera om du behöver vidta några åtgärder för certifikat för tokensignering. Du kan använda Azure AD Connect för att göra det om du vill förnya certifikaten.

Mer information finns i [reparera förtroendet](how-to-connect-fed-management.md).

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>AD FS och Azure AD-certifikat update steg
Token signerar certifikat är standard-X509 certifikat som används för att signera alla variabler som federationsservern utfärdar på ett säkert sätt. Certifikat för tokendekryptering är standard-X509 certifikat som används för att dekryptera alla inkommande token. 

Som standard har AD FS konfigurerats för att generera tokensignering och tokendekryptering certifikat automatiskt, både vid den första konfigurationen och när certifikat som närmar deras utgångsdatum.

Azure AD försöker hämta ett nytt certifikat från din tjänst federationsmetadata 30 dagar innan det aktuella certifikatet upphör att gälla. Om ett nytt certifikat inte är tillgänglig vid den tidpunkten, Azure AD kommer att fortsätta att övervaka metadata på dagliga regelbundet. När det nya certifikatet finns i metadata, uppdateras federationsinställningarna för domänen med nya certifikatinformationen. Du kan använda `Get-MsolDomainFederationSettings` att verifiera om du ser det nya certifikatet i NextSigningCertificate / SigningCertificate.

Mer information om tokensignering certifikat i AD FS finns i [hämta och konfigurera Token-signering och Token certifikat för dekryptering för AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs)
