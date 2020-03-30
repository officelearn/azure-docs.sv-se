---
title: Platsvillkor i Azure Active Directory villkorlig åtkomst
description: Lär dig hur du använder platsvillkoret för att styra åtkomsten till dina molnappar baserat på en användares nätverksplats.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263236"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Vad är platsvillkoret i Azure Active Directory Villkorlig åtkomst? 

Med [Azure Active Directory (Azure AD) Villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md)kan du styra hur behöriga användare kan komma åt dina molnappar. Med platsvillkoret för en princip för villkorlig åtkomst kan du koppla inställningar för åtkomstkontroller till användarnas nätverksplatser.

Den här artikeln innehåller den information du behöver för att konfigurera platsvillkoret.

## <a name="locations"></a>Platser

Azure AD möjliggör enkel inloggning på enheter, appar och tjänster var som helst på det offentliga internet. Med platsvillkoret kan du styra åtkomsten till dina molnappar baserat på en användares nätverksplats. Vanliga användningsfall för platsvillkoret är:

- Kräver multifaktorautentisering för användare som använder en tjänst när de inte är i företagsnätverket.
- Blockera åtkomst för användare som har åtkomst till en tjänst från vissa länder eller regioner.

En plats är en etikett för en nätverksplats som antingen representerar en namngiven plats eller betrodda IPs med flera faktorer.

## <a name="named-locations"></a>Namngivna platser

Med namngivna platser kan du skapa logiska grupperingar av IP-adressintervall eller länder och regioner.

Du kan komma åt dina namngivna platser i avsnittet **Hantera** på sidan Villkorlig åtkomst.

![Namngivna platser i villkorlig åtkomst](./media/location-condition/02.png)

En namngiven plats har följande komponenter:

![Skapa en ny namngiven plats](./media/location-condition/42.png)

- **Namn** - Visningsnamnet på en namngiven plats.
- **IP-intervall** - Ett eller flera IPv4-adressintervall i CIDR-format. Det går inte att ange ett IPv6-adressintervall.

   > [!NOTE]
   > IPv6-adressintervall kan för närvarande inte inkluderas på en namngiven plats. Det innebär att IPv6-intervall inte kan uteslutas från en princip för villkorlig åtkomst.

- **Markera som betrodd plats** - En flagga som du kan ange för en namngiven plats för att ange en betrodd plats. Betrodda platser är vanligtvis nätverksområden som styrs av IT-avdelningen. Förutom villkorlig åtkomst används betrodda namngivna platser också av Azure Identity Protection och Azure AD-säkerhetsrapporter för att minska [falska positiva identifieringar](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1).
- **Länder/regioner** - Med det här alternativet kan du välja ett eller flera länder eller regioner för att definiera en namngiven plats.
- **Inkludera okända områden** – Vissa IP-adresser mappas inte till ett visst land eller en viss region. Med det här alternativet kan du välja om dessa IP-adresser ska inkluderas på den namngivna platsen. Använd den här inställningen när principen med den namngivna platsen ska gälla för okända platser.

Antalet namngivna platser som du kan konfigurera begränsas av storleken på det relaterade objektet i Azure AD. Du kan konfigurera platser baserat på följande begränsningar:

- En namngiven plats med upp till 1200 IP-intervall.
- Högst 90 namngivna platser med ett IP-intervall tilldelat till var och en av dem.

Principen för villkorlig åtkomst gäller för IPv4- och IPv6-trafik. För närvarande namngivna platser tillåter inte att IPv6-områden konfigureras. Den här begränsningen orsakar följande situationer:

- Principen för villkorlig åtkomst kan inte riktas till specifika IPv6-intervall
- Principen för villkorlig åtkomst kan inte utesluta specifika IPV6-intervall

Om en princip är konfigurerad för att gälla för "Vilken plats som helst" gäller den för IPv4- och IPv6-trafik. Namngivna platser som konfigurerats för angivna länder och regioner stöder endast IPv4-adresser. IPv6-trafik ingår bara om alternativet att "inkludera okända områden" har valts.

## <a name="trusted-ips"></a>Tillförlitliga IP-adresser

Du kan också konfigurera IP-adressintervall som representerar organisationens lokala intranät i [inställningarna för tjänsten för multifaktorautentisering](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Med den här funktionen kan du konfigurera upp till 50 IP-adressintervall. IP-adressintervallen är i CIDR-format. Mer information finns i [Betrodda IPs](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Om du har konfigurerat betrodda IPs visas de som **MFA Trusted IPS** i listan över platser för platsvillkoret.

### <a name="skipping-multi-factor-authentication"></a>Hoppa över multifaktorautentisering

På sidan inställningar för multifaktorautentiseringstjänsten kan du identifiera användare av intranät från företag genom att välja **Hoppa över multifaktorautentisering för begäranden från federerade användare i intranätet**. Den här inställningen anger att anspråket i företagets nätverk, som utfärdas av AD FS, ska vara betrodd och användas för att identifiera användaren som i företagsnätverket. Mer information finns i [Aktivera funktionen Betrodda IPs med hjälp av villkorlig åtkomst](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

När du har markerat det här alternativet gäller **mfa-betrodda IPS** för alla principer med det här alternativet markerat.

För mobila och stationära program, som har långlivade sessionslivslängder, omvärderas villkorlig åtkomst regelbundet. Standardär en gång i timmen. När anspråk på det inre företagsnätverket endast utfärdas vid tidpunkten för den första autentiseringen kanske Azure AD inte har en lista över betrodda IP-intervall. I det här fallet är det svårare att avgöra om användaren fortfarande finns i företagets nätverk:

1. Kontrollera om användarens IP-adress finns i ett av de betrodda IP-intervallen.
2. Kontrollera om de tre första oktetterna för användarens IP-adress matchar de tre första oktetterna i IP-adressen för den första autentiseringen. IP-adressen jämförs med den första autentiseringen när anspråket i företagets nätverk ursprungligen utfärdades och användarplatsen validerades.

Om båda stegen misslyckas anses en användare inte längre vara på en betrodd IP.

## <a name="location-condition-configuration"></a>Konfiguration av platsvillkor

När du konfigurerar platsvillkoret har du möjlighet att skilja mellan:

- Valfri plats
- Alla betrodda platser
- Valda platser

![Konfiguration av platsvillkor](./media/location-condition/01.png)

### <a name="any-location"></a>Valfri plats

Om du väljer **Valfri plats** kan en princip tillämpas på alla IP-adresser, vilket innebär att vilken adress som helst på Internet. Den här inställningen är inte begränsad till IP-adresser som du har konfigurerat som namngiven plats. När du väljer **Valfri plats**kan du fortfarande utesluta specifika platser från en princip. Du kan till exempel tillämpa en princip på alla platser utom betrodda platser för att ange omfånget till alla platser, utom företagsnätverket.

### <a name="all-trusted-locations"></a>Alla betrodda platser

Det här alternativet gäller:

- Alla platser som har markerats som betrodda platser
- **MFA Trusted IPS** (om konfigurerad)

### <a name="selected-locations"></a>Valda platser

Med det här alternativet kan du välja en eller flera namngivna platser. För att en princip med den här inställningen ska gälla måste en användare ansluta från någon av de valda platserna. När du klickar på **Markera** den namngivna nätverksvalskontrollen som visar att listan över namngivna nätverk öppnas. Listan visar också om nätverksplatsen har markerats som betrodd. Den namngivna platsen som kallas **MFA Trusted IPs** används för att inkludera IP-inställningar som kan konfigureras på inställningssidan för multifaktorautentiseringstjänsten.

## <a name="what-you-should-know"></a>Det här bör du känna till

### <a name="when-is-a-location-evaluated"></a>När utvärderas en plats?

Principer för villkorlig åtkomst utvärderas när:

- En användare loggar inledningsvis in på en webbapp, mobil eller ett skrivbordsprogram.
- Ett mobil- eller skrivbordsprogram som använder modern autentisering använder en uppdateringstoken för att hämta en ny åtkomsttoken. Som standard är den här kontrollen en gång i timmen.

Den här kontrollen innebär att för mobila och stationära program med modern autentisering skulle en ändring av plats upptäckas inom en timme efter att nätverksplatsen ändrats. För mobila och stationära program som inte använder modern autentisering tillämpas principen på varje tokenbegäran. Frekvensen för begäran kan variera beroende på programmet. På samma sätt tillämpas principen för webbprogram vid första inloggningen och är bra för sessionens livstid i webbprogrammet. På grund av skillnader i sessionslivstid mellan program varierar också tiden mellan principutvärderingen. Varje gång programmet begär en ny inloggningstoken tillämpas principen.

Som standard utfärdar Azure AD en token per timme. Efter att ha flyttat från företagsnätverket tillämpas principen inom en timme för program med modern autentisering.

### <a name="user-ip-address"></a>Användarens IP-adress

IP-adressen som används i principutvärderingen är användarens offentliga IP-adress. För enheter i ett privat nätverk är den här IP-adressen inte klient-IP för användarens enhet på intranätet, det är den adress som används av nätverket för att ansluta till det offentliga internet.

> [!WARNING]
> Om enheten bara har en IPv6-adress stöds inte konfigurationen av platsvillkoret.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Massuppladdning och nedladdning av namngivna platser

När du skapar eller uppdaterar namngivna platser kan du för massuppdateringar ladda upp eller hämta en CSV-fil med IP-intervallen. En överföring ersätter IP-intervallen i listan med de från filen. Varje rad i filen innehåller ett IP-adressintervall i CIDR-format.

### <a name="cloud-proxies-and-vpns"></a>Moln proxyservrar och VPN

När du använder en molnbaserad proxy- eller VPN-lösning används IP-adressen som Azure AD använder när en princip utvärderas är IP-adressen för proxyn. X-Forwarded-For (XFF) -huvudet som innehåller användarens offentliga IP-adress används inte eftersom det inte finns någon validering av att den kommer från en betrodd källa, så skulle presentera en metod för att fejka en IP-adress.

När en molnproxy finns kan en princip som används för att kräva en domänansluten enhet användas, eller inuti corpnet-anspråk från AD FS.

### <a name="api-support-and-powershell"></a>API-stöd och PowerShell

API och PowerShell stöds ännu inte för namngivna platser eller för principer för villkorlig åtkomst.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst läser du [Kräv MFA för specifika appar med Azure Active Directory Villkorlig åtkomst](app-based-mfa.md).
- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö läser du [metodtipsen för villkorlig åtkomst i Azure Active Directory](best-practices.md).
