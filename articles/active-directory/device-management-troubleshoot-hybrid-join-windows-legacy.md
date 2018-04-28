---
title: Felsökning av hybrid Azure Active Directory-anslutna enheter äldre | Microsoft Docs
description: Felsökning av hybrid Azure Active Directory-anslutna äldre enheter.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 43c1907bf3f9bb8eea92dc02889df24a5a0cc9e3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Felsökning av hybrid Azure Active Directory-anslutna äldre enheter 

Den här artikeln gäller endast för följande enheter: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Windows 10 eller Windows Server 2016 finns [felsökning hybrid Azure Active Directory-anslutna enheter för Windows 10 och Windows Server 2016](device-management-troubleshoot-hybrid-join-windows-current.md).

Den här artikeln förutsätter att du har [konfigurerade hybrid Azure Active Directory-anslutna enheter](device-management-hybrid-azuread-joined-devices-setup.md) till stöd för följande scenarier:

- Enhetsbaserad villkorlig åtkomst

- [Enterprise centrala inställningar](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello för företag](active-directory-azureadjoin-passport-deployment.md) 





Den här artikeln finns felsökningsanvisningar om hur du löser problem.  

**Vad du bör känna till:** 

- Det maximala antalet enheter per användare är enhetscentrerad. Till exempel om *jdoe* och *jharnett* logga in på en enhet, en separat registrering (DeviceID) skapas för var och en av dem i den **användaren** fliken information.  

- Registreringen / koppling för enheter som har konfigurerats för att utföra ett försök vid inloggning eller Lås / Lås upp. Det dröja 5 minuter innan utlöses av en uppgift i Schemaläggaren. 

- Installera om operativsystemet eller manuell re-registreringar kan skapa en ny registrering på Azure AD, vilket resulterar i flera poster information på fliken användare i Azure-portalen. 

## <a name="step-1-retrieve-the-registration-status"></a>Steg 1: Hämta registreringsstatus 

**Kontrollera registreringsstatus:**  

1. Logga in med det användarkonto som har utfört en hybrid Azure AD-anslutning.

2. Öppna Kommandotolken som administratör 

3. Typ `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

Detta kommando visar en dialogruta där du får mer information om status för anslutning till.

![Anslut till arbetsplatsen för Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Steg 2: Utvärdera hybrid Azure AD join-status 

Om inte Azure AD-anslutning hybrid lyckades ger dialogrutan dig information om problem som har uppstått.

**De flesta vanliga problem är:**

- En felkonfigurerad AD FS eller Azure AD

    ![Anslut till arbetsplatsen för Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/02.png)

- Du är inte inloggad som domänanvändare

    ![Anslut till arbetsplatsen för Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/03.png)
    
    Det finns ett par olika skäl till varför detta kan inträffa:
    
    - Den inloggade användaren är inte en domänanvändare (till exempel en lokal användare). Hybrid Azure AD-anslutning på äldre enheter stöds endast för domänanvändare.
    
    - Autoworkplace.exe kan inte autentisera tyst med Azure AD eller AD FS. Detta kan bero på ett inkommande bundna problem med nätverksanslutningen till Azure AD URL-adresser (kontrollera krav). Det kan också vara att Multi-Factor authentication (MFA) är aktiverat/konfigurerats för användaren och WIAORMUTLIAUTHN inte är konfigurerad på federationsservern (kontrollera konfigurationssteg). En annan möjlighet är startsfär (HRD) för identifiering sidan väntar för användarinteraktion, vilket förhindrar **autoworkplace.exe** tyst får en token.
    
    - Din organisation använder Azure AD sömlös enkel inloggning, `https://autologon.microsoftazuread-sso.com` finns inte på enhetens IE intranätsinställningar och **tillåta uppdateringar av statusfältet via skript** har inte aktiverats för zonen Intranät.

- En kvot har uppnåtts

    ![Anslut till arbetsplatsen för Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- Tjänsten svarar inte 

    ![Anslut till arbetsplatsen för Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

Du kan också hitta statusinformation i Loggboken under: **program och tjänster Log\Microsoft Workplace Join**
  
**De vanligaste orsakerna till en misslyckad hybrid Azure AD-koppling är:** 

- Datorn är inte ansluten till organisationens interna nätverket och inte heller att en VPN-anslutning med en anslutning till din lokala AD-domänkontrollant.

- Du är inloggad på datorn med ett lokalt datorkonto. 

- Konfigurationsproblem för tjänsten: 

  - Federationsservern har konfigurerats för att stödja **WIAORMULTIAUTHN**. 

  - Datorns skogen inte har något tjänstanslutningspunkten-objekt som pekar på ett verifierat domännamn i Azure AD 

  - En användare har nått gränsen på enheter. 

## <a name="next-steps"></a>Nästa steg

Frågor, finns det [enhetshantering vanliga frågor och svar](device-management-faq.md)  
