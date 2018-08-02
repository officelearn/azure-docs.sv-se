---
title: Felsöka hybrid Azure Active Directory-anslutna äldre enheter | Microsoft Docs
description: Felsöka hybrid Azure Active Directory-anslutna äldre enheter.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 1121bc9e34f0e544ba5d7acb9179944ee2382efd
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414961"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Felsöka hybrid Azure Active Directory-anslutna äldre enheter 

Den här artikeln gäller endast för följande enheter: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Windows 10 eller Windows Server 2016 finns i [felsökning Azure Active Directory-anslutna Windows 10 och Windows Server 2016](troubleshoot-hybrid-join-windows-current.md).

Den här artikeln förutsätter att du har [konfigurerade Azure Active Directory-anslutna hybridenheter](hybrid-azuread-join-plan.md) till stöd för följande scenarier:

- Enhetsbaserad villkorlig åtkomst

- [Företagsnätverksväxling av inställningar](../active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello för företag](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) 





Den här artikeln ger felsökningsanvisningar för att lösa eventuella problem.  

**Vad du bör känna till:** 

- Det maximala antalet enheter per användare är enhetscentrerad. Till exempel om *jdoe* och *jharnett* logga in på en enhet, en separat registrering (DeviceID) skapas för var och en av dem i den **användaren** informationsfliken.  

- Den första registreringen / koppling för enheter som har konfigurerats för att utföra ett försök med antingen logga in eller låsa / låsa upp. Det kan vara 5 minuters fördröjning som utlöses av en uppgift i Schemaläggaren. 

- Du kan hämta flera poster för en enhet på informationsfliken användare på grund av en ominstallation av operativsystemet eller en manuell omregistrering. 

- Se till att [KB4284842](https://support.microsoft.com/en-us/help/4284842) installeras när det gäller Windows 7 SP1 eller Windows Server 2008 R2 SP1. Den här uppdateringen förhindrar framtida autentiseringsfel på grund av förlust av kundens åtkomst till skyddade nycklar när du har ändrat lösenordet.

## <a name="step-1-retrieve-the-registration-status"></a>Steg 1: Hämta registreringsstatus 

**Så här kontrollerar registreringsstatus:**  

1. Logga in med det användarkonto som har utfört en hybrid Azure AD-anslutning.

2. Öppna Kommandotolken som administratör 

3. Typ `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

Det här kommandot visar en dialogruta som ger dig med mer information om join-status.

![Anslut till arbetsplatsen för Windows](./media/troubleshoot-hybrid-join-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Steg 2: Utvärdera hybrid Azure AD join-status 

Om hybrid Azure AD-anslutning inte slutfördes korrekt visas ger dialogrutan dig information om problemet som har inträffat.

**De vanligaste problemen är:**

- En felkonfigurerad AD FS eller Azure AD

    ![Anslut till arbetsplatsen för Windows](./media/troubleshoot-hybrid-join-windows-legacy/02.png)

- Du är inte inloggad som domänanvändare

    ![Anslut till arbetsplatsen för Windows](./media/troubleshoot-hybrid-join-windows-legacy/03.png)
    
    Det finns flera olika skäl till varför detta kan inträffa:
    
    - Den inloggade användaren är inte en domänanvändare (till exempel en lokal användare). Hybrid Azure AD join i äldre enheter stöds endast för domänanvändare.
    
    - Autoworkplace.exe kan inte autentisera tyst med Azure AD eller AD FS. Detta kan bero på ett ut bundna problem med nätverksanslutningen till URL: er för Azure AD. Det kan också vara att Multi-Factor authentication (MFA) är aktiverat/konfigurerats för användaren och WIAORMUTLIAUTHN inte är konfigurerad på federationsservern. En annan möjlighet är startsfär (HRD) för identifiering sidan väntar för användarinteraktion, vilket förhindrar att **autoworkplace.exe** tyst begär en token.
    
    - Din organisation använder Azure AD sömlös enkel inloggning, `https://autologon.microsoftazuread-sso.com` eller `https://aadg.windows.net.nsatc.net` finns inte på enhetens intranätsinställningar för Internet Explorer, och **tillåta uppdateringar till statusfältet via skript** har inte aktiverats för zonen Intranät.

- En kvot har uppnåtts

    ![Anslut till arbetsplatsen för Windows](./media/troubleshoot-hybrid-join-windows-legacy/04.png)

- Tjänsten svarar inte 

    ![Anslut till arbetsplatsen för Windows](./media/troubleshoot-hybrid-join-windows-legacy/05.png)

Du kan också hitta statusinformation i Loggboken under: **program och tjänster Log\Microsoft-Arbetsplatsanslutning**
  
**De vanligaste orsakerna till en misslyckad hybrid Azure AD-anslutning är:** 

- Datorn är inte ansluten till din organisations interna nätverk eller till en VPN-anslutning med en anslutning till din lokala AD-domänkontrollant.

- Du är inloggad på datorn med ett lokalt datorkonto. 

- Tjänsten konfigurationsproblem: 

  - Federationsservern har konfigurerats för att stödja **WIAORMULTIAUTHN**. 

  - Datorns skog har ingen tjänstanslutningspunkt-objekt som pekar på ett verifierat domännamn i Azure AD 

  - En användare har nått gränsen för enheter. 

## <a name="next-steps"></a>Nästa steg

Frågor finns i den [enhetshantering vanliga frågor och svar](faq.md)  
