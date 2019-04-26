---
title: Azure Active Directory Identity Protection spelbok | Microsoft Docs
description: Lär dig hur Azure AD Identity Protection kan du begränsa möjligheten för en angripare att utnyttja en komprometterad identitet eller en enhet och skyddar en identitet eller en enhet som har tidigare eller misstänks äventyras.
services: active-directory
keywords: Azure active directory identity protection kan molnidentifiering, hantering av program, säkerhet, risk, risknivå, säkerhetsproblem, säkerhetsprincip
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 60836abf-f0e9-459d-b344-8e06b8341d25
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 697bb8a60861acb120e92d8fd1dda3892a957b57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60294386"
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Azure Active Directory Identity Protection spelbok

Spelboken hjälper dig att:

* Fyll i data i Identity Protection-miljö genom simulering riskhändelser och sårbarheter
* Konfigurera principer för riskbaserad villkorlig åtkomst och testa effekten av dessa principer


## <a name="simulating-risk-events"></a>Simulera riskhändelser

Det här avsnittet innehåller steg för att simulera följande riskhändelsetyper:

* Inloggningar från anonyma IP-adresser (enkel)
* Inloggningar från okända platser (måttlig)
* Omöjlig resa till ovanliga platser (svårt)

Vara det går inte att simuleras andra riskhändelser på ett säkert sätt.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inloggningar från anonyma IP-adresser

Läs mer om den här riskhändelsen [inloggningar från anonyma IP-adresser](../reports-monitoring/concept-risk-events.md#sign-ins-from-anonymous-ip-addresses). 

Slutför följande procedur måste du använda:

- Den [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) att simulera anonyma IP-adresser. Du kan behöva använda en virtuell dator om din organisation begränsar webbläsaren Tor.
- Ett testkonto som ännu inte har registrerats för multifaktorautentisering.

**Utför följande steg för att simulera en inloggning från en anonym IP-adress,**:

1. Med hjälp av den [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en), gå till [ https://myapps.microsoft.com ](https://myapps.microsoft.com).   
2. Ange autentiseringsuppgifterna för det konto som du vill ska visas i den **inloggningar från anonyma IP-adresser** rapporten.

Logga in visas på instrumentpanelen för Identity Protection inom 10 – 15 minuter. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Inloggningar från okända platser

Läs mer om den här riskhändelsen [inloggningar från okända platser](../reports-monitoring/concept-risk-events.md#sign-in-from-unfamiliar-locations). 

Du måste logga in från en plats och ditt testkonto inte har loggat in från innan enheten för att simulera okända platser.

Proceduren nedan använder en nyligen skapade:

- VPN-anslutning att simulera ny plats.

- Den virtuella datorn att simulera en ny enhet.

Slutför följande procedur måste du använda ett konto som har:

- Minst en 30-dagars inloggningshistorik.
- Multifaktorautentisering aktiverat.


**Utför följande steg för att simulera en inloggning från en okänd plats**:

1. När du loggar in med ditt test, misslyckas MFA-kontrollen genom att inte skicka MFA-kontrollen.
2. Din nya VPN, navigera till [ https://myapps.microsoft.com ](https://myapps.microsoft.com) och ange autentiseringsuppgifterna för ditt konto för testning.
   

Logga in visas på instrumentpanelen för Identity Protection inom 10 – 15 minuter.

### <a name="impossible-travel-to-atypical-location"></a>Omöjlig resa till ovanlig plats

Läs mer om den här riskhändelsen [omöjlig resa till ovanlig plats](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations). 

Det är svårt att simulera det omöjliga att resa villkoret eftersom algoritmen använder maskininlärning att filtrera ut falskpositiva resultat, t.ex omöjlig resa från välbekanta enheter eller inloggningar från VPN: er som används av andra användare i katalogen. Algoritmen kräver dessutom en historik över 14 dagar och 10 inloggningar för användaren innan den börjar generera riskhändelser. På grund av komplexa machine learning-modeller och senare regler, finns det en risk att följande steg inte leder till en riskhändelse. Du kanske vill replikera de här stegen för flera Azure AD-konton att publicera den här riskhändelsen.


**Utför följande steg för att simulera en omöjlig resa till ovanlig plats**:

1. Använd din standard webbläsare, navigera till [ https://myapps.microsoft.com ](https://myapps.microsoft.com).  
2. Ange autentiseringsuppgifterna för det konto som du vill generera en omöjlig resa riskhändelse för.
3. Ändra ditt användar-agent. Du kan ändra användaragenten i Internet Explorer från utvecklarverktyg eller ändra din användaragenten i Firefox eller Chrome med en användaragent mellan aktiviteter tillägg.
4. Ändra din IP-adress. Du kan ändra din IP-adress genom att använda en VPN-anslutning, en Tor-tillägg, eller skapa en ny dator i Azure i ett annat datacenter.
5. Logga in på [ https://myapps.microsoft.com ](https://myapps.microsoft.com) med samma autentiseringsuppgifter som förut och inom ett par minuter efter den föregående inloggningen.

Logga in visas i Identity Protection-instrumentpanelen inom 2 – 4 timmar.

## <a name="simulating-vulnerabilities"></a>Simulera sårbarheter
Sårbarheter är svagheter i en Azure AD-miljö som kan utnyttjas av en obehörig. För närvarande exponeras 3 typer av sårbarheter som i Azure AD Identity Protection som drar nytta av andra funktioner i Azure AD. Dessa problem visas på Identity Protection-instrumentpanelen automatiskt när de här funktionerna har ställts in.

* Azure AD [Multifaktorautentisering](../authentication/multi-factor-authentication.md)
* Azure AD [Cloud Discovery](https://docs.microsoft.com/cloud-app-security/).
* Azure AD [Privileged Identity Management](../privileged-identity-management/pim-configure.md). 


## <a name="testing-security-policies"></a>Testa säkerhetsprinciper

Det här avsnittet innehåller steg för att testa användarrisk och inloggningsrisk säkerhetsprincip.


### <a name="user-risk-security-policy"></a>Riskprincip

Mer information finns i [Så här konfigurerar du principen för användarrisk](howto-user-risk-policy.md).

![Användarrisk](./media/playbook/02.png "Spelbok")


**Utför följande steg om du vill testa en riskprincip**:

1. Logga in på [ https://portal.azure.com ](https://portal.azure.com) med autentiseringsuppgifterna för global administratör för din klient.
2. Gå till **identitetsskydd**. 
3. På den **Azure AD Identity Protection** klickar du på **användarprincip**.
4. I den **tilldelningar** , Välj önskade användare (och grupper) och risknivån.

    ![Användarrisk](./media/playbook/03.png "Spelbok")

5. Markera önskad åtkomstkontroll under kontroller (t.ex. kräva ändring av lösenord).
5. Som **framtvinga princip**väljer **av**.
6. Utöka användare risken för ett testkonto av, till exempel simulera en av riskhändelser några gånger.
7. Vänta några minuter och sedan kontrollera att användarnivå för dina användare är Medium. Annars kan du simulera mer riskhändelser för användaren.
8. Som **framtvinga princip**väljer **på**.
9. Nu kan du testa riskbaserad villkorlig åtkomst genom att logga in med hjälp av en användare med en upphöjd risknivå.
    
    

### <a name="sign-in-risk-security-policy"></a>Säkerhetsprincip för inloggningsrisk

Mer information finns i [Så här konfigurerar du principen för inloggningsrisk](howto-sign-in-risk-policy.md).

![Inloggningsrisk](./media/playbook/01.png "Spelbok")


**Om du vill testa ett tecken i riskprincipen, utför du följande steg:**

1. Logga in på [ https://portal.azure.com ](https://portal.azure.com) med autentiseringsuppgifterna för global administratör för din klient.

2. Gå till **Azure AD Identity Protection**.

3. På huvudsidan **Azure AD Identity Protection** klickar du på **inloggning riskprincipen**. 

4. I den **tilldelningar** markerar du önskade användare (och grupper) och logga in risknivå.

    ![Inloggningsrisk](./media/playbook/04.png "Spelbok")


5. I den **kontroller** väljer du önskad åtkomstkontroll (till exempel **kräva multifaktorautentisering**). 

6. Som **framtvinga princip**väljer **på**.

7. Klicka på **Spara**.

8. Nu kan du testa logga in riskbaserad villkorlig åtkomst genom att logga in med en riskfylld session (till exempel med hjälp av Tor-webbläsare). 

 




## <a name="see-also"></a>Se också

- [Identitetsskydd för Azure Active Directory](../active-directory-identityprotection.md)

