---
title: Felsöka äldre Azure Active Directory-anslutna hybridenheter
description: Felsöka hybrid-Azure Active Directory-anslutna enheter på lägre nivå.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e168deea1ba442d48f483264c1e97ce618040f18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74379106"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Felsöka hybrid-Azure Active Directory-anslutna enheter på lägre nivå 

Den här artikeln gäller endast följande enheter: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 

För Windows 10 eller Windows Server 2016 finns i [Felsöka hybrid-Azure Active Directory-anslutna enheter till Windows 10 och Windows Server 2016](troubleshoot-hybrid-join-windows-current.md).

Den här artikeln förutsätter att du har [konfigurerat hybrid Azure Active Directory-anslutna enheter](hybrid-azuread-join-plan.md) för att stödja följande scenarier:

- Enhetsbaserad villkorlig åtkomst

Den här artikeln innehåller felsökningsvägledning om hur du löser potentiella problem.  

**Vad du bör veta:** 

- Hybrid Azure AD-koppling för Windows-enheter på lägre nivå fungerar något annorlunda än i Windows 10. Många kunder inser inte att de behöver AD FS (för federerade domäner) eller Seamless SSO konfigurerad (för hanterade domäner).
- För kunder med federerade domäner, om SERVICE Connection Point (SCP) har konfigurerats så att den pekar på det hanterade domännamnet (till exempel contoso.onmicrosoft.com, i stället för contoso.com), kommer Hybrid Azure AD Join för Windows-enheter på nednivå att inte fungerar.
- Det maximala antalet enheter per användare gäller för närvarande även för azure AD-anslutna enheter på nednivånivå. 
- Samma fysiska enhet visas flera gånger i Azure AD när flera domänanvändare loggar in på lågnivåhybriden Azure AD-anslutna enheter.  Om till exempel *jdoe* och *jharnett* loggar in på en enhet skapas en separat registrering (DeviceID) för var och en av dem på fliken **ANVÄNDARinformation.** 
- Du kan också hämta flera poster för en enhet på fliken Användarinformation på grund av en ominstallation av operativsystemet eller en manuell omregistrering.
- Den första registreringen / kopplingen av enheter är konfigurerad för att utföra ett försök till antingen inloggning eller lås / låsa upp. Det kan vara 5 minuters fördröjning som utlöses av en aktivitetsschemaläggare-aktivitet. 
- Kontrollera att [KB4284842](https://support.microsoft.com/help/4284842) är installerat, när det gäller Windows 7 SP1 eller Windows Server 2008 R2 SP1. Den här uppdateringen förhindrar framtida autentiseringsfel på grund av kundens åtkomstförlust till skyddade nycklar efter att lösenord har ändrats.

## <a name="step-1-retrieve-the-registration-status"></a>Steg 1: Hämta registreringsstatus 

**Så här verifierar du registreringsstatusen:**  

1. Logga in med användarkontot som har utfört en hybrid Azure AD-koppling.
1. Öppna kommandotolken 
1. Skriv `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

Det här kommandot visar en dialogruta som ger dig information om kopplingsstatusen.

![Arbetsplatsanslutning för Windows](./media/troubleshoot-hybrid-join-windows-legacy/01.png)

## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Steg 2: Utvärdera hybridstatusen för Azure AD-anslutning 

Om enheten inte var hybrid Azure AD gick, kan du försöka göra hybrid Azure AD-koppling genom att klicka på "Gå med"-knappen. Om försöket att göra hybrid Azure AD-koppling misslyckas visas information om felet.

**De vanligaste problemen är:**

- Ett felkonfigurerat AD FS- eller Azure AD- eller nätverksproblem

    ![Arbetsplatsanslutning för Windows](./media/troubleshoot-hybrid-join-windows-legacy/02.png)
    
   - Autoworkplace.exe kan inte tyst autentisera med Azure AD eller AD FS. Detta kan bero på att ad FS (för federerade domäner saknas) eller saknas eller konfigureras felaktigt azure AD Seamless Single Sign-On (för hanterade domäner) eller nätverksproblem. 
   - Det kan vara så att MFA (Multi Factor Authentication) är aktiverat/konfigurerat för användaren och WIAORMULTIAUTHN inte har konfigurerats på AD FS-servern. 
   - En annan möjlighet är att HRD-sidan (Home Realm Discovery) väntar på användarinteraktion, vilket förhindrar **att autoworkplace.exe** tyst begär en token.
   - Det kan vara så att AD FS- och Azure AD-url:er saknas i IE:s intranätzon på klienten.
   - Problem med nätverksanslutningen kan hindra **autoworkplace.exe** från att nå AD FS- eller Azure AD-url:er. 
   - **Autoworkplace.exe** kräver att klienten har direkt siktlinje från klienten till organisationens lokala AD-domänkontrollant, vilket innebär att hybrid Azure AD-koppling endast lyckas när klienten är ansluten till organisationens intranät.
   - Din organisation använder Azure AD Seamless `https://autologon.microsoftazuread-sso.com` `https://aadg.windows.net.nsatc.net` Single Sign-On, eller finns inte på enhetens IE intranätinställningar, och **Tillåt uppdateringar av statusfältet via skript** är inte aktiverat för zonen Intranät.
- Du är inte inloggad som domänanvändare

   ![Arbetsplatsanslutning för Windows](./media/troubleshoot-hybrid-join-windows-legacy/03.png)

   Det finns några olika orsaker till varför detta kan inträffa:

   - Den inloggade användaren är inte en domänanvändare (till exempel en lokal användare). Hybrid Azure AD-koppling på enheter på nednivå stöds endast för domänanvändare.
   - Klienten kan inte ansluta till en domänkontrollant.    
- En kvot har uppnåtts

    ![Arbetsplatsanslutning för Windows](./media/troubleshoot-hybrid-join-windows-legacy/04.png)

- Tjänsten svarar inte 

    ![Arbetsplatsanslutning för Windows](./media/troubleshoot-hybrid-join-windows-legacy/05.png)

Du hittar också statusinformationen i händelseloggen under: **Program- och tjänstlogg\Microsoft-Arbetsplatsanslutning**
  
**De vanligaste orsakerna till en misslyckad Azure AD-hybridkoppling är:** 

- Datorn är inte ansluten till organisationens interna nätverk eller till ett VPN med en anslutning till din lokala AD-domänkontrollant.
- Du är inloggad på datorn med ett lokalt datorkonto. 
- Problem med tjänstens konfiguration: 
   - AD FS-servern har inte konfigurerats för att stödja **WIAORMULTIAUTHN**. 
   - Datorns skog har inget Service Connection Point-objekt som pekar på ditt verifierade domännamn i Azure AD 
   - Eller om din domän hanteras, då Seamless SSO inte konfigurerades eller fungerar.
   - En användare har nått gränsen för enheter. 

## <a name="next-steps"></a>Nästa steg

Frågor om enhetshantering finns i vanliga frågor om [enhetshantering](faq.md)  
