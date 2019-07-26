---
title: Azure Active Directory Identity Protection Spelbok | Microsoft Docs
description: Lär dig hur Azure AD Identity Protection ger dig möjlighet att begränsa möjligheten för en angripare att utnyttja en komprometterad identitet eller enhet och att skydda en identitet eller en enhet som tidigare misstänkts eller var känd för att bli komprometterad.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 273a6aca2050676650b955ec078b47b2ffcfe319
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333924"
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Azure Active Directory Identity Protection Spelbok

Den här Spelbok hjälper dig att:

* Fyll i data i identitets skydds miljön genom att simulera risk händelser och sårbarheter
* Konfigurera riskfyllda principer för villkorlig åtkomst och testa påverkan av dessa principer

## <a name="simulating-risk-events"></a>Simulera risk händelser

Det här avsnittet innehåller steg för att simulera följande risk händelse typer:

* Inloggningar från anonyma IP-adresser (enkelt)
* Inloggningar från okända platser (måttliga)
* Omöjlig resa till ovanlig platser (svårt)

Andra risk händelser kan inte simuleras på ett säkert sätt.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inloggningar från anonyma IP-adresser

Mer information om den här risk händelsen finns i [inloggningar från anonyma IP-adresser](../reports-monitoring/concept-risk-events.md#sign-ins-from-anonymous-ip-addresses). 

Du måste använda följande procedur för att utföra följande steg:

- [Tor webbläsare](https://www.torproject.org/projects/torbrowser.html.en) för att simulera anonyma IP-adresser. Du kan behöva använda en virtuell dator om organisationen begränsar användningen av webbläsaren Tor.
- Ett test konto som ännu inte har registrerats för Multi-Factor Authentication.

**Utför följande steg för att simulera en inloggning från en anonym IP-adress**:

1. Använd [Tor webbläsare](https://www.torproject.org/projects/torbrowser.html.en)och navigera till [https://myapps.microsoft.com](https://myapps.microsoft.com).   
2. Ange autentiseringsuppgifterna för det konto som du vill ska visas i rapporten **inloggningar från anonyma IP-adresser** .

Inloggningen visas på instrument panelen för identitets skydd inom 10-15 minuter. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Inloggningar från okända platser

Mer information om den här risk händelsen finns i [inloggningar från okända platser](../reports-monitoring/concept-risk-events.md#sign-in-from-unfamiliar-locations). 

För att simulera okända platser måste du logga in från en plats och enheten ditt test konto har inte loggat in från tidigare.

I proceduren nedan används en nyligen skapad:

- VPN-anslutning för att simulera ny plats.
- Virtuell dator för att simulera en ny enhet.

För att utföra följande procedur måste du använda ett användar konto som har:

- Minst en 30-dagars inloggnings historik.
- Multi-Factor Authentication har Aktiver ATS.

**Utför följande steg för att simulera en inloggning från en okänd plats**:

1. När du loggar in med ditt test konto går det inte att köra MFA-utmaningen genom att inte skicka MFA-utmaningen.
2. Använd din nya VPN och navigera till [https://myapps.microsoft.com](https://myapps.microsoft.com) och ange autentiseringsuppgifterna för ditt test konto.

Inloggningen visas på instrument panelen för identitets skydd inom 10-15 minuter.

### <a name="impossible-travel-to-atypical-location"></a>Omöjlig resa till ovanlig plats

Mer information om den här risk händelsen finns i [omöjlig resa till ovanlig plats](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations). 

Det är svårt att simulera det omöjliga rese tillståndet eftersom algoritmen använder maskin inlärning för att använda falsk positiv positiv, till exempel omöjlig resa från vanliga enheter, eller inloggningar från VPN-nätverk som används av andra användare i katalogen. Dessutom kräver algoritmen inloggnings historiken 14 dagar och 10 inloggningar av användaren innan risk händelser genereras. På grund av komplexa Machine Learning-modeller och över regler, finns det en risk att följande steg inte leder till en risk händelse. Du kanske vill replikera de här stegen för flera Azure AD-konton för att publicera den här risk händelsen.

**Utför följande steg för att simulera en omöjlig resa till ovanlig-platsen**:

1. Använd din standard webbläsare och navigera till [https://myapps.microsoft.com](https://myapps.microsoft.com).  
2. Ange autentiseringsuppgifterna för det konto som du vill skapa en omöjlig rese risk händelse för.
3. Ändra användar agenten. Du kan ändra användar agenten i Internet Explorer från Utvecklarverktyg eller ändra användar agenten i Firefox eller Chrome med hjälp av ett växlaren-tillägg för användar agenten.
4. Ändra din IP-adress. Du kan ändra din IP-adress med hjälp av ett VPN, ett Tor-tillägg eller skapa en ny dator i Azure i ett annat data Center.
5. Logga in för att [https://myapps.microsoft.com](https://myapps.microsoft.com) använda samma autentiseringsuppgifter som tidigare och inom några minuter efter den tidigare inloggningen.

Inloggningen visas på instrument panelen för identitets skydd inom 2-4 timmar.

## <a name="simulating-vulnerabilities"></a>Simulera sårbarheter
Sårbarheter är svagheter i en Azure AD-miljö som kan utnyttjas av en felaktig aktör. För närvarande finns det tre typer av sårbarheter i Azure AD Identity Protection som utnyttjar andra funktioner i Azure AD. Dessa säkerhets risker visas automatiskt på instrument panelen för identitets skydd när dessa funktioner har kon figurer ATS.

* Azure AD [Multi-Factor Authentication](../authentication/multi-factor-authentication.md)
* Azure AD- [Cloud Discovery](https://docs.microsoft.com/cloud-app-security/).
* Azure AD- [Privileged Identity Management](../privileged-identity-management/pim-configure.md). 

## <a name="testing-security-policies"></a>Testa säkerhets principer

Det här avsnittet innehåller steg för att testa användar risken och säkerhets principen för inloggnings risk.

### <a name="user-risk-security-policy"></a>Säkerhets princip för användar risk

Mer information finns i [Så här konfigurerar du principen för användarrisk](howto-user-risk-policy.md).

![Användar risk](./media/playbook/02.png "Spelbok")

**Utför följande steg för att testa en säkerhets princip för användar risk**:

1. Logga in på [https://portal.azure.com](https://portal.azure.com) med autentiseringsuppgifter som global administratör för din klient organisation.
2. Navigera till **Identity Protection**. 
3. På sidan **Azure AD Identity Protection** klickar du på **användar risk princip**.
4. I avsnittet **tilldelningar** väljer du önskade användare (och grupper) och användar risk nivå.

    ![Användar risk](./media/playbook/03.png "Spelbok")

5. I avsnittet kontroller väljer du önskad åtkomst kontroll (t. ex. Kräv lösen ords ändring).
5. Som **tillämpa princip**väljer du **av**.
6. Öka användar risken för ett test konto genom att till exempel simulera en av risk händelserna några gånger.
7. Vänta några minuter och kontrol lera sedan att användar nivån för din användare är medels Tor. Om inte, simulerar du fler risk händelser för användaren.
8. Som **tillämpa princip**väljer du **på**.
9. Nu kan du testa användar riskbaserade villkorlig åtkomst genom att logga in med en användare med en upphöjd risk nivå.

### <a name="sign-in-risk-security-policy"></a>Säkerhets princip för inloggnings risk

Mer information finns i [Så här konfigurerar du principen för inloggningsrisk](howto-sign-in-risk-policy.md).

![Inloggnings risk](./media/playbook/01.png "Spelbok")

**Gör så här för att testa en princip för inloggnings risk:**

1. Logga in på [https://portal.azure.com](https://portal.azure.com) med autentiseringsuppgifter som global administratör för din klient organisation.
2. Navigera till **Azure AD Identity Protection**.
3. På huvud Azure AD Identity Protections sidan klickar du på **inloggnings risk princip**. 
4. I avsnittet **tilldelningar** väljer du önskade användare (och grupper) och inloggnings risk nivå.

    ![Inloggnings risk](./media/playbook/04.png "Spelbok")

5. I avsnittet **kontroller** väljer du önskad åtkomst kontroll (till exempel **kräver Multi-Factor Authentication**). 
6. Som **tillämpa princip**väljer du **på**.
7. Klicka på **Spara**.
8. Nu kan du testa inloggnings risk baserad villkorlig åtkomst genom att logga in med en riskfylld anslutning (till exempel med hjälp av Tor webbläsare). 

## <a name="see-also"></a>Se också

- [Identitetsskydd för Azure Active Directory](../active-directory-identityprotection.md)
