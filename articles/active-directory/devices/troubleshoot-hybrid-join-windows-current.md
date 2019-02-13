---
title: Felsöka hybrid Azure Active Directory-anslutna enheter för Windows 10 och Windows Server 2016 | Microsoft Docs
description: Felsöka hybrid Azure Active Directory-anslutna enheter för Windows 10 och Windows Server 2016.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ee5620bfcd421dcb108ce330be3a21cb8ba4a43
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56187176"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-windows-10-and-windows-server-2016-devices"></a>Felsöka hybrid Azure Active Directory-anslutna enheter för Windows 10 och Windows Server 2016 

Den här artikeln gäller för följande klienter:

-   Windows 10
-   Windows Server 2016

Andra Windows-klienter finns i [felsökning Azure Active Directory-anslutna äldre enheter](troubleshoot-hybrid-join-windows-legacy.md).

Den här artikeln förutsätter att du har [konfigurerade Azure Active Directory-anslutna hybridenheter](hybrid-azuread-join-plan.md) till stöd för följande scenarier:

- Enhetsbaserad villkorad åtkomst

- [Företagsnätverksväxling av inställningar](../active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello för företag](../active-directory-azureadjoin-passport-deployment.md)


Det här dokumentet innehåller felsökningsinformation för att lösa eventuella problem. 


För Windows 10 och Windows Server 2016, hybrid Azure Active Directory-koppling har stöd för Windows 10 November 2015 uppdatera och senare. Vi rekommenderar att du använder Anniversary update.

## <a name="step-1-retrieve-the-join-status"></a>Steg 1: Hämta join-status 

**Hämta join-status:**

1. Öppna Kommandotolken som administratör

2. Typ **dsregcmd/status**



    +----------------------------------------------------------------------+
    | Enhetens tillstånd |  +----------------------------------------------------------------------+
    
        AzureAdJoined: YES
     EnterpriseJoined: Inga DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7 Thumbprint: B753A6679CE720451921302CA873794D94C6204A KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9 KeyProvider: Microsoft-plattformen kryptografiprovider TpmProtected: Ja KeySignTest:: MÅSTE köra upphöjd att testa.
                  Idp: login.windows.net TenantId: 72b988bf-86f1-41af-91ab-2d7cd011db47 TenantName: Contoso AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ== JoinSrvVersion: 1.0 JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/ JoinSrvId: urn: ms-drs:enterpriseregistration.windows.net KeySrvVersion: 1.0 KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/ KeySrvId: urn: ms-drs:enterpriseregistration.windows.net DomainJoined: Ja domännamn: CONTOSO
    
    +----------------------------------------------------------------------+
    | Användartillstånd |  +----------------------------------------------------------------------+
    
                 NgcSet: YES
               NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
        WorkplaceJoined: NO
          WamDefaultSet: YES
    WamDefaultAuthority: organisationer WamDefaultId: https://login.microsoft.com       WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd) AzureAdPrt: JA



## <a name="step-2-evaluate-the-join-status"></a>Steg 2: Utvärdera join-status 

Granska följande fält och se till att de har de förväntade värdena:

### <a name="azureadjoined--yes"></a>AzureAdJoined: JA  

Det här fältet visar om enheten är ansluten med Azure AD. Om värdet är **nr**, ansluta till Azure AD har inte slutförts ännu. 

**Möjliga orsaker:**

- Det gick inte att autentisering på datorn för en koppling.

- Det finns en HTTP-proxy i organisationen som inte kan identifieras av datorn

- Datorn kan inte nå Azure AD för att autentisera eller Azure DRS för registrering

- Datorn kanske inte i organisationens interna nätverket eller VPN-anslutning med direkt åtkomst till en lokal AD-domänkontrollant.

- Om datorn har TPM, kan det vara i ett felaktigt tillstånd.

- Det kan finnas en felaktig konfiguration av tjänsterna anges i dokumentet tidigare att du måste verifiera igen. Vanliga exempel:

    - Federationsservern har inte aktiverat WS-Trust-slutpunkter

    - Federationsservern tillåter inte inkommande autentisering från datorer i nätverket med hjälp av integrerad Windows-autentisering.

    - Det finns ingen tjänstanslutningspunkt-objekt som pekar på ett verifierat domännamn i Azure AD i AD-skogen där datorn tillhör

---

### <a name="domainjoined--yes"></a>DomainJoined: JA  

Det här fältet visar om enheten är ansluten till en lokal Active Directory eller inte. Om värdet är **nr**, enheten kan inte utföra en hybrid Azure AD-anslutning.  

---

### <a name="workplacejoined--no"></a>WorkplaceJoined: NO  

Det här fältet anger om enheten är registrerad med Azure AD som en personlig enhet (märkta *Arbetsplatsanslutna*). Det här värdet ska vara **nr** för en domänansluten dator som också är hybrid Azure AD-ansluten. Om värdet är **Ja**, ett arbets- eller skolkonto konto har lagts till innan slutförandet av hybrid Azure AD-anslutning. I det här fallet ignoreras kontot när du använder Anniversary Update-versionen av Windows 10 (1607).

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet: Ja och AzureADPrt: JA
  
De här fälten anger om användaren har autentiserats till Azure AD när du loggar in på enheten. Om värdena är **nr**, det kan vara på grund av:

- Felaktig lagringsnyckeln (STK) i TPM som hör till enheten vid registreringen (kontrollera KeySignTest när du kör upphöjd).

- Alternativa inloggnings-ID

- Det gick inte att hitta HTTP-Proxy

## <a name="next-steps"></a>Nästa steg

Frågor finns i den [enhetshantering vanliga frågor och svar](faq.md) 
