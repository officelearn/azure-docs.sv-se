---
title: "Felsökning av hybrid Azure Active Directory-anslutna enheter äldre | Microsoft Docs"
description: "Felsökning av hybrid Azure Active Directory-anslutna äldre enheter."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: ecf77a614922ef58cdfb2b2c8174f66e01ea9b46
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Felsökning av hybrid Azure Active Directory-anslutna äldre enheter 

Det här avsnittet gäller endast för följande enheter: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Windows 10 eller Windows Server 2016 finns [felsökning hybrid Azure Active Directory-anslutna enheter för Windows 10 och Windows Server 2016](device-management-troubleshoot-hybrid-join-windows-current.md).

Det här avsnittet förutsätter att du har [konfigurerade hybrid Azure Active Directory-anslutna enheter](device-management-hybrid-azuread-joined-devices-setup.md) till stöd för följande scenarier:

- Enhetsbaserad villkorlig åtkomst

- [Enterprise centrala inställningar](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello för företag](active-directory-azureadjoin-passport-deployment.md) 





Det här avsnittet ger dig felsökningsanvisningar om hur du löser problem.  

**Vad du bör känna till:** 

- Det maximala antalet enheter per användare är enhetscentrerad. Till exempel om *jdoe* och *jharnett* logga in på en enhet, en separat registrering (DeviceID) skapas för var och en av dem i den **användaren** fliken information.  

- Registreringen / koppling för enheter som har konfigurerats för att utföra ett försök vid inloggning eller Lås / Lås upp. Det dröja 5 minuter innan utlöses av en uppgift i Schemaläggaren. 

- En ominstallation av operativsystemet eller en manuell unregister och registrera kan skapa en ny registrering på Azure AD och resulterar i flera poster information på fliken användare i Azure-portalen. 

## <a name="step-1-retrieve-the-registration-status"></a>Steg 1: Hämta registreringsstatus 

**Kontrollera registreringsstatus:**  

1. Öppna Kommandotolken som administratör 

2. Typ `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /i"`

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
    
    1. Om användaren loggat in är inte en domänanvändare (till exempel en lokal användare). Hybrid Azure AD-anslutning på äldre enheter stöds endast för domänanvändare.
    
    2. Om Autoworkplace.exe av någon anledning inte kan autentisera tyst med Azure AD eller AD FS. Några möjliga orsaker kan vara routningsserver bundna problem med nätverksanslutningen till Azure AD-URL: er (kontrollera krav) eller om MFA är aktiverat/konfigurerats för användaren, men WIAORMUTLIAUTHN har inte konfigurerats på federationsservern (kontrollera konfigurationssteg). En annan möjlighet är startsfär (HRD) för identifiering sidan väntar för användarinteraktion, vilket förhindrar att Autoworkplace.exe tyst hämta en token.
    
    3. Om organisationen använder Azure AD finns sömlös enkel inloggning, följande webbadresser inte på enhetens IE intranätsinställningar:
    
       - https://autologon.microsoftazuread-sso.com
       - https://aadg.windows.net.nsatc.net
    
       och inställningen ”Tillåt uppdateringar i statusfältet via skript” måste vara aktiverat för zonen Intranät.

- En kvot har uppnåtts

    ![Anslut till arbetsplatsen för Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/04.png)

- Tjänsten svarar inte 

    ![Anslut till arbetsplatsen för Windows](./media/active-directory-device-registration-troubleshoot-windows-legacy/05.png)

Du kan också hitta statusinformation i Loggboken under **program och tjänster Log\Microsoft-Arbetsplatsanslutning**.
  
**De vanligaste orsakerna till en misslyckad hybrid Azure AD-koppling är:** 

- Datorn finns inte på det interna nätverket eller en VPN-anslutning utan anslutning till en lokal AD-domänkontrollant.

- Du är inloggad på datorn med ett lokalt datorkonto. 

- Konfigurationsproblem för tjänsten: 

  - Federationsservern har konfigurerats för att stödja **WIAORMULTIAUTHN**. 

  - Det finns inget tjänstanslutningspunkten-objekt som pekar på ett verifierat domännamn i Azure AD i AD-skog där datorn tillhör.

  - En användare har nått gränsen på enheter. 

## <a name="next-steps"></a>Nästa steg

Frågor, finns det [enhetshantering vanliga frågor och svar](device-management-faq.md)  
