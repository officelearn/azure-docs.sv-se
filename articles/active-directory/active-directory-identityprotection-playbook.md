---
title: Azure Active Directory-identitetsskydd playbook | Microsoft Docs
description: Lär dig hur Azure AD Identity Protection gör att du kan begränsa möjligheten för en angripare som utnyttjar en komprometterad identitet eller en enhet och att skydda en identitet eller en enhet som har tidigare eller misstänks vara hotad.
services: active-directory
keywords: Azure active directory identitetsskydd, cloud app discovery, hantera program, säkerhet, risk, risknivå, säkerhetsproblem och säkerhetsprincip
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 60836abf-f0e9-459d-b344-8e06b8341d25
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 29d8afb54ffbbed2178c8af611c0c0bb77fbe915
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Azure Active Directory-identitetsskydd playbook

Den här playbook hjälper dig att:

* Fyll i data i Identity Protection-miljö genom simulering riskhändelser och säkerhetsproblem
* Konfigurera principer för risk-baserad villkorlig åtkomst och testa effekten av dessa principer


## <a name="simulating-risk-events"></a>Simulera riskhändelser

Det här avsnittet innehåller steg för att simulera händelsetyper för följande risker:

* Inloggningar från anonyma IP-adresser (enkel)
* Inloggningar från okända platser (måttlig)
* Omöjligt att resa till ovanliga platser (svårt)

Andra riskhändelser kan simuleras på ett säkert sätt.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inloggningar från anonyma IP-adresser

Mer information om händelsen risken finns [inloggningar från anonyma IP-adresser](active-directory-reporting-risk-events.md#sign-ins-from-anonymous-ip-addresses). 

Slutför följande procedur måste du använda:

- Den [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) att simulera anonyma IP-adresser. Du kan behöva använda en virtuell dator om ditt företag begränsar med Tor-webbläsare.
- Ett testkonto som ännu inte har registrerats för multifaktorautentisering.

**Utför följande steg för att simulera en inloggning från en anonym IP**:

1. Med hjälp av den [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en), gå till [ https://myapps.microsoft.com ](https://myapps.microsoft.com).   
2. Ange autentiseringsuppgifter för det konto som du vill ska visas i den **inloggningar från anonyma IP-adresser** rapporten.

Logga in visas på instrumentpanelen identitetsskydd inom 10 – 15 minuter. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Inloggningar från okända platser

Mer information om händelsen risken finns [inloggningar från okända platser](active-directory-reporting-risk-events.md#sign-in-from-unfamiliar-locations). 

Du måste logga in från en plats och en test-kontot inte har loggat in från innan enheten för att simulera okända platser.

Proceduren nedan använder en nyligen skapade:

- VPN-anslutningen, för att simulera ny plats.

- Virtuell dator att simulera en ny enhet.

Slutför följande procedur måste du använda ett konto som har:

- Minst en 30-dagars inloggning historik.
- Multifaktorautentisering aktiveras.


**Utför följande steg för att simulera en inloggning från en okänd plats**:

1. När du loggar in med ditt test, misslyckas MFA-kontrollen genom att inte skicka MFA-kontrollen.
2. Använd din nya VPN-anslutningar, navigera till [ https://myapps.microsoft.com ](https://myapps.microsoft.com) och ange autentiseringsuppgifterna för kontot för testet.
   

Logga in visas på instrumentpanelen identitetsskydd inom 10 – 15 minuter.

### <a name="impossible-travel-to-atypical-location"></a>Omöjligt att resa till ovanliga plats

Mer information om händelsen risken finns [Impossible resa till ovanliga plats](active-directory-reporting-risk-events.md#impossible-travel-to-atypical-locations). 

Det är svårt att simulera villkoret omöjligt att resa eftersom algoritmen använder maskininlärning att filtrera ut FALSKT positiva identifieringar som omöjligt att resa från bekant enheter eller inloggningar från VPN-anslutningar som används av andra användare i katalogen. Dessutom kräver algoritmen en historik 14 dagar och 10 inloggningar för användaren innan den börjar skapa riskhändelser. På grund av komplexa modeller för maskininlärning och senare regler finns en risk att följande inte leder till en händelse för risk. Du kanske vill replikera de här stegen för flera Azure AD-konton att publicera den här händelsen för risk.


**Utför följande steg för att simulera en omöjligt att resa till ovanliga plats**:

1. Använd din standard-webbläsare, navigera till [ https://myapps.microsoft.com ](https://myapps.microsoft.com).  
2. Ange autentiseringsuppgifter för det konto som du vill generera en händelse med omöjliga resor risken för.
3. Ändra din användaragent. Du kan ändra användaragent i Internet Explorer från utvecklingsverktyg eller ändra din användaragent i Firefox eller Chrome med en användaragent switcher tillägg.
4. Ändra IP-adress. Du kan ändra din IP-adress med hjälp av en VPN-anslutning, Tor-tillägg eller startas en ny dator i Azure i olika datacenter.
5. Logga in på [ https://myapps.microsoft.com ](https://myapps.microsoft.com) använder du samma inloggningsuppgifter som förut och inom några minuter efter den föregående inloggningen.

Logga in visas i instrumentpanelen identitetsskydd inom 2-4 timmar.

## <a name="simulating-vulnerabilities"></a>Simulera säkerhetsrisker
Säkerhetsrisker är svagheter i en Azure AD-miljö som kan utnyttjas av en felaktig aktören. För närvarande exponeras 3 typer av säkerhetsproblem i Azure AD Identity Protection som utnyttjar andra funktioner i Azure AD. Dessa problem visas på instrumentpanelen identitetsskydd automatiskt när dessa funktioner har ställts in.

* Azure AD [Multifaktorautentisering](authentication/multi-factor-authentication.md)
* Azure AD [Cloud App Discovery](manage-apps/cloud-app-discovery.md).
* Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md). 


## <a name="testing-security-policies"></a>Testa säkerhetsprinciper

Det här avsnittet innehåller steg för att testa användare risk och säkerhetsprincip inloggning risk.


### <a name="user-risk-security-policy"></a>Användaren risk säkerhetsprincip

Mer information finns i [användaren risk säkerhetsprincip](active-directory-identityprotection.md#user-risk-security-policy).

![Användaren risk](./media/active-directory-identityprotection-playbook/02.png "Playbook")


**Utför följande steg om du vill testa en säkerhetsprincip för användaren risk**:

1. Logga in på [ https://portal.azure.com ](https://portal.azure.com) med globala administratörsbehörigheter för din klient.
2. Gå till **identitetsskydd**. 
3. På den **Azure AD Identity Protection** klickar du på **risk användarprincip**.
4. I den **tilldelningar** , Välj önskade användare (och grupper) och risknivå för användaren.

    ![Användaren risk](./media/active-directory-identityprotection-playbook/03.png "Playbook")

5. Välj önskad åtkomstkontroll i avsnittet kontroller (t.ex. lösenordet måste ändras).
5. Som **tillämpa principen**väljer **av**.
6. Höj användaren risken för ett testkonto av, till exempel simulera en av riskhändelser några gånger.
7. Vänta en stund och kontrollera att användarnivå för dina användare är Medium. Om inte, simulera mer riskhändelser för användaren.
8. Som **tillämpa principen**väljer **på**.
9. Nu kan du testa användare risk-baserad villkorlig åtkomst genom att logga in med hjälp av en användare med en ökad risk för nivå.
    
    

### <a name="sign-in-risk-security-policy"></a>Logga in risk säkerhetsprincip

Mer information finns i [användaren risk säkerhetsprincip](active-directory-identityprotection.md#user-risk-security-policy).

![Logga in risk](./media/active-directory-identityprotection-playbook/01.png "Playbook")


**Utför följande steg om du vill testa ett tecken i riskprincipen:**

1. Logga in på [ https://portal.azure.com ](https://portal.azure.com) med globala administratörsbehörigheter för din klient.

2. Gå till **Azure AD Identity Protection**.

3. På primära **Azure AD Identity Protection** klickar du på **inloggning riskprincipen**. 

4. I den **tilldelningar** markerar du önskade användare (och grupper) och logga in risknivå.

    ![Logga in risk](./media/active-directory-identityprotection-playbook/04.png "Playbook")


5. I den **kontroller** väljer du önskad åtkomstkontroll (till exempel **kräver Multi-Factor authentication**). 

6. Som **tillämpa principen**väljer **på**.

7. Klicka på **Spara**.

8. Du kan nu testa inloggningen Risk-baserad villkorlig åtkomst genom att logga in med en riskfyllda session (till exempel med hjälp av Tor webbläsaren). 

 




## <a name="see-also"></a>Se också

- [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

