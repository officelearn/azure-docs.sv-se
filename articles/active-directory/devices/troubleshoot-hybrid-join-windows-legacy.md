---
title: Felsöka hybrid Azure Active Directory-anslutna äldre enheter | Microsoft Docs
description: Felsöka hybrid Azure Active Directory-anslutna äldre enheter.
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
ms.date: 04/23/2018
ms.author: markvi
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea658f18af7ae44d73f78cb5fe4f7ae1316ea901
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57439134"
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

- Enhetsbaserad villkorad åtkomst


Den här artikeln ger felsökningsanvisningar för att lösa eventuella problem.  

**Vad du bör känna till:** 

- Hybrid Azure AD-anslutning för äldre Windows-enheter fungerar lite annorlunda än den sker i Windows 10. Många kunder utan att veta att de behöver för AD FS (för federerade domäner) eller sömlös SSO konfigurerats (för hanterade domäner).

- För kunder med federerade domäner om den tjänstanslutningspunkt (SCP) har konfigurerats så att den pekar till det hanterade domännamnet (t.ex, contoso.onmicrosoft.com, i stället för contoso.com), kommer sedan Hybrid Azure AD Join för äldre Windows-enheter inte fungera.

- Det maximala antalet enheter per användare för närvarande gäller även för äldre hybrid Azure AD-anslutna enheter. 

- På samma fysiska enhet visas flera gånger i Azure AD när flera användare logga in äldre hybrid Azure AD-anslutna enheter.  Till exempel om *jdoe* och *jharnett* logga in på en enhet, en separat registrering (DeviceID) skapas för var och en av dem i den **användaren** informationsfliken. 

- Du kan även få flera poster för en enhet på informationsfliken användare på grund av en ominstallation av operativsystemet eller en manuell omregistrering.

- Den första registreringen / koppling för enheter som har konfigurerats för att utföra ett försök med antingen logga in eller låsa / låsa upp. Det kan vara 5 minuters fördröjning som utlöses av en uppgift i Schemaläggaren. 

- Se till att [KB4284842](https://support.microsoft.com/help/4284842) installeras när det gäller Windows 7 SP1 eller Windows Server 2008 R2 SP1. Den här uppdateringen förhindrar framtida autentiseringsfel på grund av förlust av kundens åtkomst till skyddade nycklar när du har ändrat lösenordet.

## <a name="step-1-retrieve-the-registration-status"></a>Steg 1: Hämta registreringsstatus 

**Så här kontrollerar registreringsstatus:**  

1. Logga in med det användarkonto som har utfört en hybrid Azure AD-anslutning.

2. Öppna Kommandotolken 

3. Typ `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

Det här kommandot visar en dialogruta som ger dig information om join-status.

![Anslut till arbetsplatsen för Windows](./media/troubleshoot-hybrid-join-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Steg 2: Utvärdera hybrid Azure AD join-status 

Om enheten inte hybrid Azure AD-anslutna, kan du försöka göra hybrid Azure AD-anslutning genom att klicka på knappen ”Gå med”. Om det inte går att försök att utföra hybrid Azure AD-anslutning, visas information om felet.


**De vanligaste problemen är:**

- En felkonfigurerad AD FS eller Azure AD eller problem med nätverket

    ![Anslut till arbetsplatsen för Windows](./media/troubleshoot-hybrid-join-windows-legacy/02.png)
    
    - Autoworkplace.exe kan inte autentisera tyst med Azure AD eller AD FS. Detta kan bero på saknade eller felkonfigurerad AD FS (för federerade domäner) eller saknas eller är felkonfigurerad Azure AD sömlös enkel inloggning (för hanterade domäner) eller nätverksproblem. 
    
     - Det kan vara att Multi-Factor authentication (MFA) är aktiverat/konfigurerats för användaren och WIAORMUTLIAUTHN inte är konfigurerad på AD FS-servern. 
     
     - En annan möjlighet är startsfär (HRD) för identifiering sidan väntar för användarinteraktion, vilket förhindrar att **autoworkplace.exe** tyst begär en token.
     
     - Det kan vara att AD FS och Azure AD-URL: er saknas i Internet Explorers intranätzonen på klienten.
     
     - Problem med nätverksanslutningen kan förhindra **autoworkplace.exe** från att nå AD FS eller URL: er för Azure AD. 
     
     - **Autoworkplace.exe** kräver att klienten har direkt åtkomst från klienten till organisationens lokala AD-domänkontrollant, vilket innebär att hybrid Azure AD-anslutning lyckas bara när klienten är ansluten till intranätet.
     
     - Din organisation använder Azure AD sömlös enkel inloggning, `https://autologon.microsoftazuread-sso.com` eller `https://aadg.windows.net.nsatc.net` finns inte på enhetens intranätsinställningar för Internet Explorer, och **tillåta uppdateringar till statusfältet via skript** har inte aktiverats för zonen Intranät.

- Du är inte inloggad som domänanvändare

    ![Anslut till arbetsplatsen för Windows](./media/troubleshoot-hybrid-join-windows-legacy/03.png)
    
    Det finns flera olika skäl till varför detta kan inträffa:
    
    - Den inloggade användaren är inte en domänanvändare (till exempel en lokal användare). Hybrid Azure AD join i äldre enheter stöds endast för domänanvändare.
    
    - Klienten är inte kan ansluta till en domänkontrollant.    

- En kvot har uppnåtts

    ![Anslut till arbetsplatsen för Windows](./media/troubleshoot-hybrid-join-windows-legacy/04.png)

- Tjänsten svarar inte 

    ![Anslut till arbetsplatsen för Windows](./media/troubleshoot-hybrid-join-windows-legacy/05.png)

Du kan också hitta statusinformation i Loggboken under: **Program och tjänster Log\Microsoft Workplace Join**
  
**De vanligaste orsakerna till en misslyckad hybrid Azure AD-anslutning är:** 

- Datorn är inte ansluten till din organisations interna nätverk eller till en VPN-anslutning med en anslutning till din lokala AD-domänkontrollant.

- Du är inloggad på datorn med ett lokalt datorkonto. 

- Tjänsten konfigurationsproblem: 

  - AD FS-servern inte har konfigurerats för att stödja **WIAORMULTIAUTHN**. 

  - Datorns skog har ingen tjänstanslutningspunkt-objekt som pekar på ett verifierat domännamn i Azure AD 
  
  - Eller om din domän är hanterad och sömlös enkel inloggning har inte konfigurerats eller fungerar.

  - En användare har nått gränsen för enheter. 

## <a name="next-steps"></a>Nästa steg

Frågor finns i den [enhetshantering vanliga frågor och svar](faq.md)  
