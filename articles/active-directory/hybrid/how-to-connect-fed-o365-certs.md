---
title: Förnyelse av certifikat för Office 365- och Azure AD-användare | Microsoft-dokument
description: I den här artikeln beskrivs för Office 365-användare hur du löser problem med e-postmeddelanden som meddelar dem om att förnya ett certifikat.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 543b7dc1-ccc9-407f-85a1-a9944c0ba1be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/20/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d98a1aabef2de505e66b2127226b9e89cd791e20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60244849"
---
# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Förnya federationscertifikat för Office 365 och Azure Active Directory
## <a name="overview"></a>Översikt
För framgångsrik federation mellan Azure Active Directory (Azure AD) och Active Directory Federation Services (AD FS) bör de certifikat som används av AD FS för att signera säkerhetstoken till Azure AD matcha vad som är konfigurerat i Azure AD. Eventuella avvikelser kan leda till brutet förtroende. Azure AD säkerställer att den här informationen synkroniseras när du distribuerar AD FS- och webbprogramproxy (för extranätsåtkomst).

Den här artikeln innehåller ytterligare information för att hantera dina tokensigneringscertifikat och hålla dem synkroniserade med Azure AD, i följande fall:

* Du distribuerar inte proxy för webbprogram och därför är federationsmetadata inte tillgängliga i extranätet.
* Du använder inte standardkonfigurationen för AD FS för tokensigneringscertifikat.
* Du använder en identitetsleverantör från tredje part.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Standardkonfiguration för AD FS för tokensigneringscertifikat
Tokensignerings- och tokendekrypteringscertifikaten är vanligtvis självsignerade certifikat och är bra för ett år. Som standard innehåller AD FS en automatisk förnyelseprocess som kallas **AutoCertificateRollover**. Om du använder AD FS 2.0 eller senare uppdaterar Office 365 och Azure AD automatiskt ditt certifikat innan det upphör att gälla.

### <a name="renewal-notification-from-the-microsoft-365-admin-center-or-an-email"></a>Förnyelsemeddelande från Administrationscentret för Microsoft 365 eller ett e-postmeddelande
> [!NOTE]
> Om du har fått ett e-postmeddelande eller ett portalmeddelande där du uppmanas att förnya certifikatet för Office läser du [Hantera ändringar av tokensigneringscertifikat](#managecerts) för att kontrollera om du behöver vidta några åtgärder. Microsoft är medvetet om ett eventuellt problem som kan leda till att meddelanden om förnyelse av certifikat skickas, även om ingen åtgärd krävs.
>
>

Azure AD försöker övervaka federationsmetadata och uppdatera tokensigneringscertifikat som anges av dessa metadata. 30 dagar före utgången av tokensigneringscertifikaten kontrollerar Azure AD om nya certifikat är tillgängliga genom att avsöka federationsmetadata.

* Om federationsmetadata framgångsrikt kan avsökas och de nya certifikaten hämtas utfärdas ingen e-postmeddelande eller varning i Microsoft 365-administrationscentret till användaren.
* Om det inte går att hämta de nya tokensigneringscertifikaten, antingen för att federationsmetadata inte kan nås eller automatisk certifikatförvaltning inte är aktiverad, utfärdar Azure AD ett e-postmeddelande och en varning i Administrationscentret för Microsoft 365.

![Meddelande om Office 365-portal](./media/how-to-connect-fed-o365-certs/notification.png)

> [!IMPORTANT]
> Om du använder AD FS, för att säkerställa kontinuitet i verksamheten, kontrollera att dina servrar har följande uppdateringar så att autentiseringsfel för kända problem inte uppstår. Detta minskar kända AD FS-proxyserverproblem för den här förnyelsen och framtida förnyelseperioder:
>
> Server 2012 R2 – [Windows Server samlad i maj 2014](https://support.microsoft.com/kb/2955164)
>
> Server 2008 R2 och 2012 – [Autentisering via proxy misslyckas i Windows Server 2012 eller Windows 2008 R2 SP1](https://support.microsoft.com/kb/3094446)
>
>

## <a name="check-if-the-certificates-need-to-be-updated"></a>Kontrollera om certifikaten behöver uppdateras<a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>Steg 1: Kontrollera autocertificateRollover-tillståndet
Öppna PowerShell på AD FS-servern. Kontrollera att värdet AutoCertificateRollover är inställt på True.

    Get-Adfsproperties

![AutoCertificateRollover](./media/how-to-connect-fed-o365-certs/autocertrollover.png)

>[!NOTE] 
>Om du använder AD FS 2.0 kör du först Add-Pssnapin Microsoft.Adfs.Powershell.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Steg 2: Bekräfta att AD FS och Azure AD är synkroniserade
Öppna MSOnline PowerShell-prompten på AD FS-servern och anslut till Azure AD.

> [!NOTE]
> MSOL-Cmdlets ingår i MODULEN MSOnline PowerShell.
> Du kan hämta MSOnline PowerShell-modulen direkt från PowerShell-galleriet.
> 
>

    Install-Module MSOnline

Anslut till Azure AD med MSOnline PowerShell-modulen.

    Import-Module MSOnline
    Connect-MsolService

Kontrollera certifikaten som konfigurerats i AD FS- och Azure AD-förtroendeegenskaper för den angivna domänen.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/how-to-connect-fed-o365-certs/certsync.png)

Om tumavtrycken i båda utgångarna matchar är dina certifikat synkroniserade med Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Steg 3: Kontrollera om certifikatet håller på att upphöra att gälla
I utdata från antingen Get-MsolFederationProperty eller Get-AdfsCertificate kontrollerar du datumet under "Inte efter". Om datumet är mindre än 30 dagar bort bör du vidta åtgärder.

| AutoCertificateRollover | Certifikat synkroniserade med Azure AD | Federationsmetadata är allmänt tillgängliga | Giltighet | Åtgärd |
|:---:|:---:|:---:|:---:|:---:|
| Ja |Ja |Ja |- |Det behövs ingen åtgärd. Se [Förnya tokensigneringscertifikat automatiskt](#autorenew). |
| Ja |Inga |- |Mindre än 15 dagar |Förnya omedelbart. Se [Förnya tokensigneringscertifikat manuellt](#manualrenew). |
| Inga |- |- |Mindre än 30 dagar |Förnya omedelbart. Se [Förnya tokensigneringscertifikat manuellt](#manualrenew). |

\[-] Spelar ingen roll

## <a name="renew-the-token-signing-certificate-automatically-recommended"></a>Förnya tokensigneringscertifikatet automatiskt (rekommenderas)<a name="autorenew"></a>
Du behöver inte utföra några manuella steg om båda följande är sanna:

* Du har distribuerat webbprogramproxy, vilket kan aktivera åtkomst till federationsmetadata från extranätet.
* Du använder STANDARDKONFIGURATIONEN FÖR AD FS (AutoCertificateRollover är aktiverat).

Kontrollera följande för att bekräfta att certifikatet kan uppdateras automatiskt.

**1. AD FS-egenskapen AutoCertificateRollover måste vara true.** Detta indikerar att AD FS automatiskt genererar nya tokensignerings- och tokendekrypteringscertifikat innan de gamla upphör att gälla.

**2. AD FS federation metadata är allmänt tillgänglig.** Kontrollera att federationsmetadata är allmänt tillgängliga genom att navigera till följande webbadress från en dator på det offentliga internet (utanför företagsnätverket):

https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

där `(your_FS_name)` ersätts med federationstjänstvärdens namn som organisationen använder, till exempel fs.contoso.com.  Om du kan verifiera båda dessa inställningar behöver du inte göra något annat.  

Exempel: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml
## <a name="renew-the-token-signing-certificate-manually"></a>Förnya tokensigneringscertifikatet manuellt<a name="manualrenew"></a>
Du kan välja att förnya tokensigneringscertifikaten manuellt. Följande scenarier kan till exempel fungera bättre för manuell förnyelse:

* Tokensigneringscertifikat är inte självsignerade certifikat. Den vanligaste orsaken till detta är att din organisation hanterar AD FS-certifikat som registrerats från en organisationscertifikatutfärdare.
* Nätverkssäkerhet tillåter inte att federationsmetadata är allmänt tillgängliga.

I dessa scenarier måste du varje gång du uppdaterar tokensigneringscertifikaten också uppdatera din Office 365-domän med hjälp av PowerShell-kommandot Update-MsolFederatedDomain.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Steg 1: Se till att AD FS har nya tokensigneringscertifikat
**Konfiguration som inte är standard**

Om du använder en icke-standardkonfiguration av AD FS (där **AutoCertificateRollover** är inställt på **Falskt)** använder du förmodligen anpassade certifikat (inte självsignerade). Mer information om hur du förnyar AD FS-tokensigneringscertifikat finns i [Vägledning för kunder som inte använder AD FS självsignerade certifikat](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Federationsmetadata är inte allmänt tillgängliga**

Å andra sidan, om **AutoCertificateRollover** är inställt på **Sant**, men dina federationsmetadata inte är allmänt tillgängliga, se först till att nya tokensigneringscertifikat har genererats av AD FS. Bekräfta att du har nya tokensigneringscertifikat genom att vidta följande åtgärder:

1. Kontrollera att du är inloggad på den primära AD FS-servern.
2. Kontrollera de aktuella signeringscertifikaten i AD FS genom att öppna ett PowerShell-kommandofönster och köra följande kommando:

    PS C:\>Get-ADFSCertificate –CertificateType tokensignering

   > [!NOTE]
   > Om du använder AD FS 2.0 bör du köra Add-Pssnapin Microsoft.Adfs.Powershell först.
   >
   >
3. Titta på kommandoutdata på alla certifikat som anges. Om AD FS har genererat ett nytt certifikat bör du se två certifikat i utdata: ett för vilket **IsPrimary-värdet** är **sant** och **notafter-datumet** är inom 5 dagar, och ett för vilket **IsPrimary** är **falskt** och **NotAfter** är ungefär ett år i framtiden.
4. Om du bara ser ett certifikat och **datumet Efter** är inom 5 dagar måste du generera ett nytt certifikat.
5. Om du vill generera ett nytt certifikat kör du `PS C:\>Update-ADFSCertificate –CertificateType token-signing`följande kommando vid en PowerShell-kommandotolk: .
6. Verifiera uppdateringen genom att köra följande kommando\>igen: PS C: Get-ADFSCertificate –CertificateType tokensignering

Två certifikat bör listas nu, varav ett har ett **NotAfter-datum** på ungefär ett år i framtiden och för vilket **IsPrimary-värdet** är **falskt**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Steg 2: Uppdatera de nya tokensigneringscertifikaten för Office 365-förtroendet
Uppdatera Office 365 med de nya tokensigneringscertifikat som ska användas för förtroendet enligt följande.

1. Öppna Microsoft Azure Active Directory Module för Windows PowerShell.
2. Kör $cred=Hämta-autentiseringsuppgifter. När den här cmdleten frågar dig efter autentiseringsuppgifter skriver du dina autentiseringsuppgifter för molntjänstadministratörskontot.
3. Kör Connect-MsolService – $cred. Den här cmdleten ansluter dig till molntjänsten. Det krävs att du skapar en kontext som ansluter dig till molntjänsten innan du kör någon av de ytterligare cmdlets som installeras av verktyget.
4. Om du kör dessa kommandon på en dator som inte är AD FS primära federationsserver, kör Set-MSOLAdfscontext &lt;-Computer AD FS primär&gt;server , där &lt;AD FS primära server&gt; är det interna FQDN-namnet på den primära AD FS-servern. Den här cmdleten skapar en kontext som kopplar dig till AD FS.
5. Kör domänen &lt;&gt;Update-MSOLFederatedDomain –DomainName . Den här cmdleten uppdaterar inställningarna från AD FS till molntjänsten och konfigurerar förtroenderelationen mellan de två.

> [!NOTE]
> Om du behöver ha stöd för flera toppdomäner, till exempel contoso.com och fabrikam.com, måste du använda **supportmultipledomain-växeln** med alla cmdlets. Mer information finns i [Stöd för flera toppdomäner](how-to-connect-install-multiple-domains.md).
>


## <a name="repair-azure-ad-trust-by-using-azure-ad-connect"></a>Reparera Azure AD-förtroende med hjälp av Azure AD Connect<a name="connectrenew"></a>
Om du har konfigurerat AD FS-servergruppen och Azure AD-förtroendet med hjälp av Azure AD Connect kan du använda Azure AD Connect för att identifiera om du behöver vidta några åtgärder för tokensigneringscertifikat. Om du behöver förnya certifikaten kan du använda Azure AD Connect för att göra det.

Mer information finns i [Reparera förtroendet](how-to-connect-fed-management.md).

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>Ad FS- och Azure AD-certifikatuppdateringssteg
Tokensigneringscertifikat är standardcertifikat för X509 som används för att på ett säkert sätt signera alla token som federationsservern utfärdar. Tokendekrypteringscertifikat är standardcertifikat för X509 som används för att dekryptera inkommande token. 

Som standard är AD FS konfigurerat för att generera tokensignerings- och tokendekrypteringscertifikat automatiskt, både vid den första konfigurationstiden och när certifikaten närmar sig utgångsdatumet.

Azure AD försöker hämta ett nytt certifikat från federationstjänstens metadata 30 dagar före utgången av det aktuella certifikatet. Om ett nytt certifikat inte är tillgängligt vid den tidpunkten fortsätter Azure AD att övervaka metadata med jämna dagliga intervall. Så snart det nya certifikatet är tillgängligt i metadata uppdateras federationsinställningarna för domänen med den nya certifikatinformationen. Du kan `Get-MsolDomainFederationSettings` använda för att kontrollera om du ser det nya certifikatet i NextSigningCertificate /SigningCertificate.

Mer information om tokensigneringscertifikat i AD FS finns i [Hämta och konfigurera tokensignerings- och tokendekrypteringscertifikat för AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs)
