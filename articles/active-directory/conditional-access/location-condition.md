---
title: Plats villkor i Azure Active Directory villkorlig åtkomst
description: Lär dig hur du använder plats villkoret för att kontrol lera åtkomsten till dina molnappar baserat på användarens nätverks plats.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 915675af1e646f2cb77e36c0018ed372ff9496fc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263236"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Vad är plats villkoret i Azure Active Directory villkorlig åtkomst? 

Med [Azure Active Directory (Azure AD) villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md)kan du styra hur behöriga användare får åtkomst till dina molnappar. Plats villkoret för en princip för villkorlig åtkomst gör att du kan använda inställningar för åtkomst kontroll till användarnas nätverks platser.

Den här artikeln innehåller den information du behöver för att konfigurera plats villkoret.

## <a name="locations"></a>Platser

Azure AD möjliggör enkel inloggning till enheter, appar och tjänster från var som helst på det offentliga Internet. Med plats villkoret kan du styra åtkomsten till dina molnappar baserat på användarens nätverks plats. Vanliga användnings fall för plats villkoret är:

- Kräver Multi-Factor Authentication för användare som har åtkomst till en tjänst när de är utanför företags nätverket.
- Blockera åtkomst för användare som har åtkomst till en tjänst från vissa länder eller regioner.

En plats är en etikett för en nätverks plats som antingen representerar en namngiven plats eller en Multi-Factor Authentication-betrodd IP-adress.

## <a name="named-locations"></a>Namngivna platser

Med namngivna platser kan du skapa logiska grupperingar av IP-adressintervall eller länder och regioner.

Du kan komma åt dina namngivna platser i avsnittet **Hantera** på sidan för villkorlig åtkomst.

![Namngivna platser i villkorlig åtkomst](./media/location-condition/02.png)

En namngiven plats har följande komponenter:

![Skapa en ny namngiven plats](./media/location-condition/42.png)

- **Namn** – visnings namnet för en namngiven plats.
- **IP-intervall** – ett eller flera IPv4-ADRESSINTERVALL i CIDR-format. Det finns inte stöd för att ange ett IPv6-adressintervall.

   > [!NOTE]
   > IPv6-adressintervall kan för närvarande inte tas med på en namngiven plats. Detta innebär att IPv6-intervall inte kan uteslutas från en princip för villkorlig åtkomst.

- **Markera som betrodd plats** – en flagga som du kan ange för en namngiven plats för att ange en betrodd plats. Normalt är betrodda platser nätverks områden som styrs av IT-avdelningen. Förutom villkorlig åtkomst används även betrodda namngivna platser av Azure Identity Protection och Azure AD-säkerhetsrapporter för att minska antalet [falska positiva identifieringar](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1).
- **Länder/regioner** – med det här alternativet kan du välja ett eller flera länder eller regioner för att definiera en namngiven plats.
- **Inkludera okända områden** -vissa IP-adresser mappas inte till ett visst land eller en viss region. Med det här alternativet kan du välja om de här IP-adresserna ska tas med på den namngivna platsen. Använd den här inställningen när principen som använder den namngivna platsen ska gälla för okända platser.

Antalet namngivna platser som du kan konfigurera begränsas av storleken på det relaterade objektet i Azure AD. Du kan konfigurera platser baserat på följande begränsningar:

- En namngiven plats med upp till 1200 IP-intervall.
- Högst 90 namngivna platser med ett IP-adressintervall tilldelat var och en av dem.

Principen för villkorlig åtkomst gäller för IPv4-och IPv6-trafik. Namngivna platser tillåter inte att IPv6-intervall konfigureras. Den här begränsningen orsakar följande situationer:

- Principen för villkorlig åtkomst kan inte riktas mot specifika IPv6-intervall
- Principen för villkorlig åtkomst kan inte exkludera vissa IPV6-intervall

Om en princip är konfigurerad att gälla för "valfri plats" gäller den för IPv4-och IPv6-trafik. Namngivna platser som kon figurer ATS för angivna länder och regioner stöder endast IPv4-adresser. IPv6-trafik inkluderas bara om alternativet för att inkludera okända områden har marker ATS.

## <a name="trusted-ips"></a>Tillförlitliga IP-adresser

Du kan också konfigurera IP-adressintervall som representerar organisationens lokala intranät i [inställningarna för Multi-Factor Authentication-tjänsten](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Med den här funktionen kan du konfigurera upp till 50 IP-adressintervall. IP-adressintervallet är i CIDR-format. Mer information finns i [betrodda IP-adresser](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Om du har konfigurerat betrodda IP-adresser visas de som **MFA-betrodda IP-adresser** i listan över platser för plats villkoret.

### <a name="skipping-multi-factor-authentication"></a>Hoppar över Multi-Factor Authentication

På sidan Inställningar för Multi-Factor Authentication Service kan du identifiera företags intranäts användare genom att välja **hoppa över Multi-Factor Authentication för förfrågningar från federerade användare i mitt intranät**. Den här inställningen anger att det i företags nätverks anspråk, som utfärdas av AD FS, ska vara betrott och användas för att identifiera användaren som i företags nätverket. Mer information finns i [aktivera funktionen för betrodda IP-adresser med hjälp av villkorlig åtkomst](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

När du har markerat det här alternativet, inklusive den namngivna platsen **MFA-betrodda IP-adresser** kommer att gälla för alla principer med det här alternativet valt.

För mobil-och skriv bords program, som har länge livs längd för sessionens livs längd, utvärderas villkorlig åtkomst regelbundet om. Standardvärdet är en gång i timmen. Om det interna nätverks anspråk bara utfärdas vid tidpunkten för den inledande autentiseringen kanske inte Azure AD har en lista över betrodda IP-intervall. I det här fallet är det svårare att avgöra om användaren fortfarande befinner sig i företags nätverket:

1. Kontrol lera att användarens IP-adress finns i något av de betrodda IP-intervallen.
2. Kontrol lera om de tre första oktetterna i användarens IP-adress matchar de tre första oktetterna i IP-adressen för den inledande autentiseringen. IP-adressen jämförs med den första autentiseringen när den interna nätverks anspråket utfärdades ursprungligen och användar platsen verifierades.

Om båda stegen inte fungerar, anses en användare inte längre på en betrodd IP-adress.

## <a name="location-condition-configuration"></a>Konfiguration av plats villkor

När du konfigurerar plats villkoret har du möjlighet att skilja mellan:

- Valfri plats
- Alla betrodda platser
- Valda platser

![Konfiguration av plats villkor](./media/location-condition/01.png)

### <a name="any-location"></a>Valfri plats

Som standard gör en princip att tillämpas på alla IP-adresser, vilket innebär vilken adress **som helst på** Internet. Den här inställningen är inte begränsad till IP-adresser som du har konfigurerat som namngiven plats. När du väljer **en plats**kan du fortfarande utesluta vissa platser från en princip. Du kan till exempel använda en princip för alla platser utom betrodda platser för att ange omfattningen till alla platser, förutom företags nätverket.

### <a name="all-trusted-locations"></a>Alla betrodda platser

Det här alternativet gäller för:

- Alla platser som har marker ATS som betrodda platser
- **MFA-betrodda IP-adresser** (om de kon figurer ATS)

### <a name="selected-locations"></a>Valda platser

Med det här alternativet kan du välja en eller flera namngivna platser. För att en princip med den här inställningen ska tillämpas måste användaren ansluta från någon av de valda platserna. När du klickar på **Välj** den namngivna nätverks val kontrollen som visar listan över namngivna nätverk som öppnas. I listan visas även om nätverks platsen har marker ATS som betrodd. Den namngivna platsen för **MFA-betrodda IP-adresser** används för att inkludera de IP-inställningar som kan konfigureras på inställnings sidan för Multi-Factor Authentication-tjänsten.

## <a name="what-you-should-know"></a>Det här bör du känna till

### <a name="when-is-a-location-evaluated"></a>När utvärderas platsen?

Principer för villkorlig åtkomst utvärderas när:

- En användare loggar in från början till en webbapp, ett mobilt eller Skriv bords program.
- Ett mobil-eller Skriv bords program som använder modern autentisering använder en uppdateringstoken för att hämta en ny åtkomsttoken. Som standard är den här kontrollen en gång i timmen.

Den här kontrollen avser mobila och Station ära program som använder modern autentisering, men en ändrings plats upptäcktes inom en timme efter att nätverks platsen ändrades. För mobila och Station ära program som inte använder modern autentisering tillämpas principen på varje Tokenbegäran. Frekvensen för begäran kan variera beroende på programmet. På samma sätt gäller för webb program principen vid inledande inloggning och är lämpligt för sessionens livs längd i webb programmet. På grund av skillnader i sessionernas livs längder mellan olika program, kommer tiden mellan princip utvärdering också att variera. Varje gången programmet begär en ny inloggnings-token tillämpas principen.

Som standard utfärdar Azure AD en token per timme. När du har flyttat företags nätverket inom en timme tillämpas principen för program som använder modern autentisering.

### <a name="user-ip-address"></a>Användarens IP-adress

IP-adressen som används i princip utvärderingen är användarens offentliga IP-adress. För enheter i ett privat nätverk är den här IP-adressen inte klientens IP-adress för användarens enhet i intranätet, den är den adress som används av nätverket för att ansluta till det offentliga Internet.

> [!WARNING]
> Om enheten bara har en IPv6-adress stöds inte konfigurering av plats villkoret.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Mass överföring och hämtning av namngivna platser

När du skapar eller uppdaterar namngivna platser för Mass uppdateringar kan du ladda upp eller ladda ned en CSV-fil med IP-intervall. En uppladdning ersätter IP-intervallen i listan med dem från filen. Varje rad i filen innehåller ett IP-adressintervall i CIDR-format.

### <a name="cloud-proxies-and-vpns"></a>Cloud-proxyservrar och VPN-nätverk

När du använder en molnbaserad proxy eller VPN-lösning för molnet används IP-adressen för Azure AD vid utvärdering av en princip är proxyserverns IP-adress. Det X-vidarebefordrade-for-(XFF)-huvud som innehåller användarens offentliga IP-adress används inte eftersom det inte finns någon validering av att den kommer från en betrodd källa, så att det finns en metod för att Faking en IP-adress.

När en molnbaserad proxy är på plats kan en princip som används för att kräva en domänansluten enhet användas, eller inifrån Corpnet-anspråk från AD FS.

### <a name="api-support-and-powershell"></a>API-stöd och PowerShell

API och PowerShell stöds ännu inte för namngivna platser eller för principer för villkorlig åtkomst.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst, se [KRÄV MFA för vissa appar med Azure Active Directory villkorlig åtkomst](app-based-mfa.md).
- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö, se [metod tips för villkorlig åtkomst i Azure Active Directory](best-practices.md).
