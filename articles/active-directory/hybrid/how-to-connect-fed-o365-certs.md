---
title: Certifikat förnyelse för Office 365 och Azure AD-användare | Microsoft Docs
description: I den här artikeln beskrivs hur 365 du löser problem med e-postmeddelanden som meddelar dem om att förnya ett certifikat med e-post.
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
ms.topic: how-to
ms.date: 10/20/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04f523a2615892268d56c167a682987453dc997c
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2020
ms.locfileid: "85359746"
---
# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Förnya Federations certifikat för Office 365 och Azure Active Directory
## <a name="overview"></a>Översikt
För lyckad federation mellan Azure Active Directory (Azure AD) och Active Directory Federation Services (AD FS) (AD FS) bör de certifikat som används av AD FS för att signera säkerhetstoken till Azure AD matcha vad som har kon figurer ATS i Azure AD. Eventuella matchnings fel kan leda till trasigt förtroende. Azure AD säkerställer att den här informationen hålls synkroniserad när du distribuerar AD FS och Webbprogramproxy (för extra nät åtkomst).

Den här artikeln innehåller ytterligare information om hur du hanterar certifikat för tokensignering och behåller dem synkroniserade med Azure AD i följande fall:

* Du distribuerar inte Webbprogramproxy, och därför är federationsmetadata inte tillgängligt i extra nätet.
* Du använder inte standard konfigurationen för AD FS för token signerings certifikat.
* Du använder en identitets leverantör från tredje part.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Standard konfiguration av AD FS för certifikat för tokensignering
Token signering och token dekryptering av certifikat är vanligt vis självsignerade certifikat och är lämpliga i ett år. AD FS innehåller som standard en process för automatisk förnyelse som heter **AutoCertificateRollover**. Om du använder AD FS 2,0 eller senare uppdaterar Office 365 och Azure AD automatiskt ditt certifikat innan det upphör att gälla.

### <a name="renewal-notification-from-the-microsoft-365-admin-center-or-an-email"></a>Förnya meddelande från Microsoft 365 administrations Center eller ett e-postmeddelande
> [!NOTE]
> Om du har fått ett e-postmeddelande eller ett Portal meddelande som uppmanar dig att förnya certifikatet för Office, se [hantera ändringar av certifikat för tokensignering](#managecerts) för att kontrol lera om du behöver vidta några åtgärder. Microsoft känner till ett möjligt problem som kan leda till att meddelanden om certifikat förnyelse skickas, även när ingen åtgärd krävs.
>
>

Azure AD försöker övervaka federationsmetadata och uppdaterar de token signerings certifikat som anges av dessa metadata. 30 dagar före förfallo datum för certifikat för tokensignering kontrollerar Azure AD om nya certifikat är tillgängliga genom att avsöka federationsmetadata.

* Om det går att avsöka federationsmetadata och hämta de nya certifikaten, utfärdas inga e-postmeddelanden eller varningar i Microsoft 365 administrations centret till användaren.
* Om det inte går att hämta de nya certifikaten för tokensignering, antingen på grund av att det inte går att komma åt federationsmetadata eller automatisk certifikat förnyelse inte är aktive rad, utfärdar Azure AD ett e-postmeddelande och en varning i Microsoft 365 administrations centret.

![Office 365-portal meddelande](./media/how-to-connect-fed-o365-certs/notification.png)

> [!IMPORTANT]
> Om du använder AD FS för att säkerställa affärs kontinuiteten kontrollerar du att servrarna har följande uppdateringar så att autentiseringsfel för kända problem inte inträffar. Detta minimerar kända AD FS problem med proxyservern för den här förnyelsen och framtida förnyelse perioder:
>
> Server 2012 R2 – [Windows Server maj 2014 Rollup](https://support.microsoft.com/kb/2955164)
>
> Server 2008 R2 och 2012- [autentisering via proxy Miss lyckas i Windows Server 2012 eller windows 2008 R2 SP1](https://support.microsoft.com/kb/3094446)
>
>

## <a name="check-if-the-certificates-need-to-be-updated"></a>Kontrol lera om certifikaten behöver uppdateras<a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>Steg 1: kontrol lera status för AutoCertificateRollover
Öppna PowerShell på AD FS-servern. Kontrol lera att värdet för AutoCertificateRollover är inställt på sant.

    Get-Adfsproperties

![AutoCertificateRollover](./media/how-to-connect-fed-o365-certs/autocertrollover.png)

>[!NOTE] 
>Om du använder AD FS 2,0 måste du först köra Add-PSSnapin Microsoft. ADFS. PowerShell.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Steg 2: bekräfta att AD FS och Azure AD är synkroniserade
Öppna MSOnline PowerShell-prompten på AD FS-servern och Anslut till Azure AD.

> [!NOTE]
> MSOL-cmdlets är en del av MSOnline PowerShell-modulen.
> Du kan ladda ned MSOnline PowerShell-modulen direkt från PowerShell-galleriet.
> 
>

    Install-Module MSOnline

Anslut till Azure AD med hjälp av MSOnline PowerShell-modulen.

    Import-Module MSOnline
    Connect-MsolService

Kontrol lera de certifikat som kon figurer ATS i AD FS och egenskaper för Azure AD-förtroende för den angivna domänen.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/how-to-connect-fed-o365-certs/certsync.png)

Om tumavtrycken i båda utdata matchar dina certifikat synkroniseras med Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Steg 3: kontrol lera om certifikatet håller på att gå ut
I utdata från antingen get-MsolFederationProperty eller get-AdfsCertificate, söker du efter datumet under "inte efter". Om datumet är mindre än 30 dagar långt bör du vidta åtgärder.

| AutoCertificateRollover | Certifikat som synkroniseras med Azure AD | Federationsmetadata är offentligt tillgängliga | Bestämma | Åtgärd |
|:---:|:---:|:---:|:---:|:---:|
| Ja |Ja |Ja |- |Det behövs ingen åtgärd. Se [förnya token signerings certifikat automatiskt](#autorenew). |
| Yes |Inga |- |Mindre än 15 dagar |Förnya omedelbart. Se [förnya token signerings certifikat manuellt](#manualrenew). |
| No |- |- |Mindre än 30 dagar |Förnya omedelbart. Se [förnya token signerings certifikat manuellt](#manualrenew). |

\[-] Spelar ingen roll

## <a name="renew-the-token-signing-certificate-automatically-recommended"></a>Förnya token signerings certifikatet automatiskt (rekommenderas)<a name="autorenew"></a>
Du behöver inte utföra några manuella steg om båda följande stämmer:

* Du har distribuerat Webbprogramproxy, som kan ge åtkomst till federationsmetadata från extra nätet.
* Du använder AD FS standard konfiguration (AutoCertificateRollover är aktive rad).

Kontrol lera följande för att bekräfta att certifikatet kan uppdateras automatiskt.

**1. egenskapen AutoCertificateRollover för AD FS måste anges till true.** Detta anger att AD FS automatiskt genererar nya token signerings-och token-dekrypterings certifikat innan de gamla upphör att gälla.

**2. metadata för AD FS federationen är allmänt tillgängliga.** Kontrol lera att federationsmetadata är offentligt tillgängliga genom att gå till följande URL från en dator på det offentliga Internet (utanför företags nätverket):

https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

var `(your_FS_name)` ersätts med det värd namn för Federations tjänsten som organisationen använder, till exempel FS.contoso.com.  Om du kan kontrol lera att båda inställningarna har slutförts behöver du inte göra något annat.  

Exempel: `https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml`
## <a name="renew-the-token-signing-certificate-manually"></a>Förnya token signerings certifikatet manuellt<a name="manualrenew"></a>
Du kan välja att förnya token signerings certifikat manuellt. Följande scenarier kan till exempel fungera bättre för manuell förnyelse:

* Token signerings certifikat är inte självsignerade certifikat. Den vanligaste orsaken till detta är att din organisation hanterar AD FS certifikat som har registrerats från en organisations certifikat utfärdare.
* Nätverks säkerhet tillåter inte att federationsmetadata är offentligt tillgängliga.

I dessa scenarier måste du uppdatera din Office 365-domän varje gång du uppdaterar certifikaten för Token-signering.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Steg 1: kontrol lera att AD FS har nya token signerings certifikat
**Konfiguration som inte är standard**

Om du använder en icke-standardkonfiguration av AD FS (där **AutoCertificateRollover** har angetts till **false**) använder du förmodligen anpassade certifikat (inte självsignerade). Mer information om hur du förnyar AD FS token signerings certifikat finns i [rikt linjer för kunder som inte använder AD FS självsignerade certifikat](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Federationsmetadata är inte offentligt tillgängliga**

Å andra sidan, om **AutoCertificateRollover** är inställt på **True**, men federationens metadata inte är offentligt tillgängliga, kontrollerar du först att nya certifikat för tokensignering har genererats av AD FS. Bekräfta att du har nya certifikat för tokensignering genom att vidta följande steg:

1. Kontrol lera att du är inloggad på den primära AD FS servern.
2. Kontrol lera de aktuella signerings certifikaten i AD FS genom att öppna ett PowerShell-kommando fönster och köra följande kommando:

    PS C: \> Get-ADFSCertificate – CertificateType token-signing

   > [!NOTE]
   > Om du använder AD FS 2,0 ska du först köra Add-PSSnapin Microsoft. ADFS. PowerShell.
   >
   >
3. Titta på kommandoutdata i certifikaten i listan. Om AD FS har genererat ett nytt certifikat, bör du se två certifikat i utdata: ett för vilket **IsPrimary** -värdet är **Sant** och **NotAfter** -datumet infaller inom 5 dagar, och ett för vilka **IsPrimary** är **falskt** och **NotAfter** är ungefär ett år i framtiden.
4. Om du bara ser ett certifikat och **NotAfter** -datumet ligger inom 5 dagar måste du generera ett nytt certifikat.
5. Om du vill generera ett nytt certifikat kör du följande kommando i PowerShell-kommando tolken: `PS C:\>Update-ADFSCertificate –CertificateType token-signing` .
6. Verifiera uppdateringen genom att köra följande kommando igen: PS C: \> Get-ADFSCertificate – CertificateType token-signing

Två certifikat bör visas nu, varav ett har ett **NotAftert** datum på ungefär ett år i framtiden och för vilka **IsPrimary** -värdet är **falskt**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Steg 2: uppdatera de nya token signerings certifikaten för Office 365-förtroendet
Uppdatera Office 365 med de nya token signerings certifikat som ska användas för förtroendet enligt följande.

1. Öppna Microsoft Azure Active Directory-modul för Windows PowerShell.
2. Kör $cred = Get-Credential. När du uppmanas att ange autentiseringsuppgifter för den här cmdleten skriver du autentiseringsuppgifterna för ditt moln tjänst administratörs konto.
3. Kör Connect-MsolService – Credential $cred. Denna cmdlet ansluter dig till moln tjänsten. Att skapa en kontext som ansluter dig till moln tjänsten krävs innan du kör någon av de ytterligare cmdlets som installeras av verktyget.
4. Om du kör de här kommandona på en dator som inte är AD FS primär Federations server kör du Set-MSOLAdfscontext-Computer &lt; AD FS Primary Server &gt; , där &lt; AD FS primär server &gt; är det interna fullständiga domän namnet för den primära AD FS servern. Denna cmdlet skapar en kontext som ansluter dig till AD FS.
5. Kör Update-MSOLFederatedDomain – &lt; domän domän &gt; . Den här cmdleten uppdaterar inställningarna från AD FS till moln tjänsten och konfigurerar förtroende relationen mellan de två.

> [!NOTE]
> Om du behöver stöd för flera domäner på översta nivån, till exempel contoso.com och fabrikam.com, måste du använda växeln **supportmultipledomain** med alla cmdletar. Mer information finns i [stöd för flera domäner på den översta nivån](how-to-connect-install-multiple-domains.md).
>


## <a name="repair-azure-ad-trust-by-using-azure-ad-connect"></a>Reparera Azure AD-förtroende med hjälp av Azure AD Connect<a name="connectrenew"></a>
Om du har konfigurerat AD FS-gruppen och Azure AD-förtroende med Azure AD Connect kan du använda Azure AD Connect för att identifiera om du behöver vidta några åtgärder för dina certifikat för tokensignering. Om du behöver förnya certifikaten kan du använda Azure AD Connect för att göra det.

Mer information finns i [Reparera förtroendet](how-to-connect-fed-management.md).

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>Uppdaterings steg för AD FS och Azure AD-certifikat
Token signerings certifikat är standard-X509-certifikat som används för att på ett säkert sätt Signera alla tokens som Federations servern utfärdar. Certifikat för token-dekryptering är standard-X509-certifikat som används för att dekryptera inkommande tokens. 

Som standard är AD FS konfigurerad för att generera certifikat för Token-signering och token-dekryptering automatiskt, både vid den inledande konfigurations tiden och när certifikaten närmar sig sitt förfallo datum.

Azure AD försöker hämta ett nytt certifikat från Federations tjänstens metadata 30 dagar innan det aktuella certifikatet upphör att gälla. Om ett nytt certifikat inte är tillgängligt vid den tiden fortsätter Azure AD att övervaka metadata enligt regelbundna dags intervall. Så snart det nya certifikatet är tillgängligt i metadata, uppdateras Federations inställningarna för domänen med den nya certifikat informationen. Du kan använda `Get-MsolDomainFederationSettings` för att kontrol lera om du ser det nya certifikatet i NextSigningCertificate/SigningCertificate.

Mer information om token för signering av certifikat i AD FS finns i [Hämta och konfigurera Token signering och token dekryptering av certifikat för AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs)
