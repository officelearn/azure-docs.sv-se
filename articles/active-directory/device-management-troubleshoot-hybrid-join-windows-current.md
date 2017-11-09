---
title: "Felsökning av hybrid Azure Active Directory-anslutna enheter för Windows 10 och Windows Server 2016 | Microsoft Docs"
description: "Felsökning av hybrid Azure Active Directory-anslutna enheter för Windows 10 och Windows Server 2016."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: ffce288555e718b37882f30f877162d0c0467f1f
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-windows-10-and-windows-server-2016-devices"></a>Felsökning av hybrid Azure Active Directory-anslutna enheter för Windows 10 och Windows Server 2016 

Det här avsnittet gäller följande klienter:

-   Windows 10
-   Windows Server 2016

Andra windowsklienter finns i [felsökning hybrid Azure Active Directory-anslutna enheter för äldre](device-management-troubleshoot-hybrid-join-windows-legacy.md).

Det här avsnittet förutsätter att du har [konfigurerade hybrid Azure Active Directory-anslutna enheter](device-management-hybrid-azuread-joined-devices-setup.md) till stöd för följande scenarier:

- Enhetsbaserad villkorlig åtkomst

- [Enterprise centrala inställningar](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello för företag](active-directory-azureadjoin-passport-deployment.md)


Det här dokumentet innehåller felsökningsinformation om hur du löser problem. 


För Windows 10 och Windows Server 2016 hybrid Azure Active Directory-koppling har stöd för Windows 10 November 2015 uppdatera och högre. Vi rekommenderar att du använder årsdagar uppdateringen.

## <a name="step-1-retrieve-the-join-status"></a>Steg 1: Hämta join-status 

**Hämta status för koppling:**

1. Öppna Kommandotolken som administratör

2. Typen **dsregcmd/status**



    +----------------------------------------------------------------------+
   | Enhetens tillstånd |+----------------------------------------------------------------------+
    
        AzureAdJoined: YES
     EnterpriseJoined: Inga DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7 tumavtryck: B753A6679CE720451921302CA873794D94C6204A KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9 KeyProvider: Microsoft-plattformen krypteringsprovider TpmProtected: Ja KeySignTest:: måste köra utökade att testa.
                  IDP: login.windows.net TenantId: 72b988bf-86f1-41af-91ab-2d7cd011db47 TenantName: Contoso AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize AccessTokenUrl: https://login.microsoftonline.com/ msitsupp.microsoft.com/oauth2/token MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx dmComplianceUrl: https:// Portal.Manage-Beta.microsoft.com/?portalAction=Compliance SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ == JoinSrvVersion: 1.0 JoinSrvUrl: https:// enterpriseregistration.windows.net/EnrollmentServer/device/ JoinSrvId: urn: ms-drs:enterpriseregistration.windows.net KeySrvVersion: 1.0 KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/ KeySrvId: urn: ms-drs: enterpriseregistration.Windows.NET DomainJoined: Ja DomainName: CONTOSO
    
    +----------------------------------------------------------------------+
   | Användartillstånd |+----------------------------------------------------------------------+
    
                 NgcSet: YES
               NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
        WorkplaceJoined: NO
          WamDefaultSet: YES
    WamDefaultAuthority: organisationer WamDefaultId: https://login.microsoft.com WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd) AzureAdPrt: Ja



## <a name="step-2-evaluate-the-join-status"></a>Steg 2: Utvärdera join-status 

Granska följande fält och se till att de har de förväntade värdena:

### <a name="azureadjoined--yes"></a>AzureAdJoined: Ja  

Det här fältet anger om enheten är ansluten med Azure AD. Om värdet är **nr**, ansluta till Azure AD har inte slutförts ännu. 

**Möjliga orsaker:**

- Autentisering av en anslutning till datorn misslyckades.

- Det finns en HTTP-proxy i organisationen som inte identifieras av datorn

- Datorn kan inte nå Azure AD för att autentisera eller Azure DRS för registrering

- Datorn kanske inte i organisationens interna nätverket eller VPN-anslutning med fri till en lokal AD-domänkontrollant.

- Om datorn har en TPM, kan det vara i ett felaktigt tillstånd.

- Det kan finnas en felaktig konfiguration i tjänsterna antecknade i dokumentet tidigare att du måste verifiera igen. Vanliga exempel är:

    - Federationsservern har inte aktiverats för WS-Trust-slutpunkter

    - Federationsservern tillåter inte inkommande autentisering från datorer i nätverket med hjälp av integrerad Windows-autentisering.

    - Det finns inget tjänstanslutningspunkten-objekt som pekar på ett verifierat domännamn i Azure AD i AD-skog där datorn tillhör

---

### <a name="domainjoined--yes"></a>DomainJoined: Ja  

Det här fältet anger om enheten är ansluten till en lokal Active Directory eller inte. Om värdet är **nr**, enheten kan inte utföra en hybrid Azure AD-koppling.  

---

### <a name="workplacejoined--no"></a>WorkplaceJoined: Nej  

Det här fältet anger om enheten är registrerad med Azure AD som en personlig enhet (markerad som *Arbetsplatsanslutna*). Det här värdet ska vara **nr** för en domänansluten dator som är också ansluten hybrid Azure AD. Om värdet är **Ja**, ett arbets- eller skolkonto konto har lagts till före slutförande av hybrid Azure AD-koppling. I det här fallet ignoreras kontot när du använder den årsdagar uppdaterade versionen av Windows 10 (1607).

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet: Ja och AzureADPrt: Ja
  
De här fälten visar om användaren har autentiserats till Azure AD när du loggar in till enheten. Om värdena är **nr**, kan det bero på grund av:

- Felaktig lagring nyckeln (STK) i TPM som är kopplade till enheten vid registreringen (kontrollera KeySignTest när du kör upphöjd).

- Alternativt inloggnings-ID

- Det gick inte att hitta HTTP-Proxy

## <a name="next-steps"></a>Nästa steg

Frågor, finns det [enhetshantering vanliga frågor och svar](device-management-faq.md) 