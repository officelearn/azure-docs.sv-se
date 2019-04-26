---
title: Vad är platsvillkoret för villkorlig åtkomst i Azure Active Directory? | Microsoft Docs
description: Lär dig hur du använder platsvillkoret för att styra åtkomsten till dina molnappar baserat på användarens nätverksplats.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 04/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6771cf093f62ef7823e57ced8223e4cc6c0dc57e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60354670"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Vad är platsvillkoret för villkorlig åtkomst i Azure Active Directory? 

Med [villkorlig åtkomst i Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md), du kan styra hur behöriga användare kan komma åt dina appar i molnet. Platsvillkoret för en princip för villkorlig åtkomst kan du koppla kontroller åtkomstinställningar till nätverksplatser för dina användare.

Den här artikeln ger dig den information du behöver att konfigurera platsvillkoret.

## <a name="locations"></a>Platser

Azure AD aktiverar enkel inloggning till enheter, appar och tjänster från var som helst på internet. Du kan styra åtkomsten till dina molnappar baserat på nätverksplats för en användare med platsvillkor. Vanliga användningsområden för platsvillkoret är:

- Att kräva multifaktorautentisering för användare som ansluter till en tjänst när är utanför företagets nätverk.
- Blockera åtkomst för användare som ansluter till en tjänst från vissa länder eller regioner.

En plats är en etikett för en nätverksplats som antingen representerar en namngiven plats eller multifaktorautentisering tillförlitliga IP-adresser.

## <a name="named-locations"></a>Namngivna platser

Du kan skapa logiska grupperingar av IP-adressintervall eller andra länder och regioner med namngivna platser.

Du kan komma åt din sökvägarna i den **hantera** på sidan för villkorlig åtkomst.

![Namngivna platser för villkorlig åtkomst](./media/location-condition/02.png)

En namngiven plats har följande komponenter:

![Skapa en ny med namnet plats](./media/location-condition/42.png)

- **Namn på** – visningsnamnet för en namngiven plats.
- **IP-intervall** - minst en IPv4-adressintervall i CIDR-format. Finns inte stöd för att ange en IPv6-adressintervall.

   > [!NOTE]
   > IPv6-adress rangess kan för närvarande inte ingå i en namngiven locationThis measn IPv6-intervall inte kan uteslutas från principen för villkorlig åtkomst.

- **Markera som betrodd plats** – en flagga som du kan ange för en namngiven plats att ange en betrodd plats. Betrodda platser är oftast nätverksområden som kontrolleras av IT-avdelningen. Förutom villkorlig åtkomst, betrodda namngivna platser används också av Azure Identity Protection och Azure AD-säkerhetsrapporter för att minska [falska positiva identifieringar](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1).
- **Länder/regioner** – det här alternativet kan du välja en eller flera land eller region för att definiera en namngiven plats.
- **Inkludera okända områden** -vissa IP-adresser mappas inte till ett visst land. Det här alternativet kan du välja om dessa IP-adresser ska ingå i den namngivna platsen. Använd den här inställningen när principen med hjälp av den namngivna platsen ska gälla för okända platser.

Antalet namngivna platser som du kan konfigurera begränsas av storleken på det relaterade objektet i Azure AD. Du kan konfigurera platser baserat på följande begränsningar:

- En namnet plats med upp till 1200 IP-intervall.
- Högst 90 namngivna platser med en IP-adressintervall som är tilldelade till var och en av dem.

Princip för villkorlig åtkomst gäller för IPv4 och IPv6-trafik. För närvarande namngivna platser tillåter inte IPv6-intervall som ska konfigureras. Den här begränsningen gör följande situationer:

- Princip för villkorlig åtkomst tillämpas inte på specifika IPv6-intervall
- Princip för villkorlig åtkomst kan inte undanta specifika IPV6-intervall

Om en princip är konfigurerad för att tillämpa ”var som helst”, gäller det för IPv4 och IPv6-trafik. Namngivna platser som har konfigurerats för angivna länder och regioner har endast stöd för IPv4-adresser. IPv6-trafik är endast om du väljer att ”inkludera okända områden”.

## <a name="trusted-ips"></a>Tillförlitliga IP-adresser

Du kan också konfigurera IP-adressintervall som motsvarar din organisations lokalt intranät i den [multifaktorautentisering tjänstinställningar](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Den här funktionen kan du konfigurera upp till 50 IP-adressintervall. Det är de IP-adressintervall i CIDR-format. Mer information finns i [tillförlitliga IP-adresser](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Om du har tillförlitliga IP-adresser konfigureras, de visas som **MFA tillförlitliga IP-adresser** i listan över platser för platsvillkoret.

### <a name="skipping-multi-factor-authentication"></a>Hoppar över multifaktorautentisering

På inställningssidan för multi-Factor authentication-tjänsten kan du identifiera företagsintranät användare genom att välja **hoppa över multifaktorautentisering för förfrågningar från federerade användare från mitt intranät**. Den här inställningen anger att innanför företagets nätverk anspråk som utfärdas av AD FS, bör betrodd och används för att identifiera användaren är i företagsnätverket. Mer information finns i [aktivera funktionen tillförlitliga IP-adresser med hjälp av villkorlig åtkomst](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

När du har kontrollerat det här alternativet, inklusive namngiven plats **MFA tillförlitliga IP-adresser** gäller för alla principer med det här alternativet.

För mobila och skrivbordsprogram, som har länge sessionen livslängd, omvärderas villkorlig åtkomst med jämna mellanrum. Standardvärdet är en gång i timmen. När inuti företagsnätverkanspråk utfärdas endast vid tidpunkten för den första autentiseringen, Azure AD kan inte ha en lista över betrodda IP-adressintervall. I det här fallet är det svårare att avgöra om användaren är fortfarande i företagets nätverk:

1. Kontrollera om användarens IP-adress är i något av de betrodda IP-adressintervall.
2. Kontrollera om de tre första oktetterna i användarens IP-adress matchar de tre första oktetterna i IP-adressen för den första autentiseringen. IP-adressen jämförs med första autentisering när innanför företagets nätverk anspråk ursprungligen utfärdade och platsen som har verifierats.

Om båda stegen misslyckas, anses en användare som inte längre finnas på en tillförlitlig IP-adress.

## <a name="location-condition-configuration"></a>Konfiguration av villkor

När du konfigurerar platsvillkoret har möjlighet att skilja mellan:

- Vilken plats som helst
- Alla betrodda platser
- Valda platser

![Konfiguration av villkor](./media/location-condition/01.png)

### <a name="any-location"></a>Vilken plats som helst

Som standard att välja **valfri plats** orsakar en princip som ska tillämpas på alla IP-adresser, vilket innebär att alla adresser på Internet. Den här inställningen är inte begränsad till IP-adresser som du har konfigurerat som namn. När du väljer **valfri plats**, du kan fortfarande utesluta särskilda platser från en princip. Du kan till exempel använda en princip för att alla platser utom betrodda platser för att ange omfånget till alla platser utom företagets nätverk.

### <a name="all-trusted-locations"></a>Alla betrodda platser

Det här alternativet gäller för:

- Alla platser som har markerats som betrodd plats
- **MFA tillförlitliga IP-adresser** (om konfigurerad)

### <a name="selected-locations"></a>Valda platser

Med det här alternativet kan du välja en eller flera namngivna platser. En användare måste ansluta från någon av de valda platserna för en princip med den här inställningen ska gälla. När du klickar på **Välj** den namngivna nätverk val av kontroll som visar en lista över namngivna nätverk öppnas. I listan visas också om en nätverksplats nedan har markerats som betrodda. Namngiven plats kallas **MFA tillförlitliga IP-adresser** används för att ta med IP-inställningar som kan konfigureras på sidan Inställningar för multi-Factor authentication-tjänsten.

## <a name="what-you-should-know"></a>Det här bör du känna till

### <a name="when-is-a-location-evaluated"></a>När utvärderas en plats?

Principer för villkorlig åtkomst utvärderas när:

- En användare loggar först in till ett webbprogram för app-, Mobil- eller desktop.
- Ett program för mobil eller dator som använder modern autentisering använder en uppdateringstoken för att få en ny åtkomsttoken. Den här kontrollen är en gång i timmen som standard.

Den här kontrollen innebär för mobila och skrivbordsprogram som använder modern autentisering, en ändring på plats så identifieras inom en timme efter att ändra nätverksplatsen. För mobila och skrivbordsprogram som inte använder modern autentisering, tillämpas principen på varje begäran-token. Frekvensen för begäran kan variera beroende på programmet. På samma sätt tillämpas vid första inloggningen principen för webbprogram och är bra för livslängden för sessionen på webbprogrammet. På grund av skillnader i sessionen livslängd för program är varierar tiden mellan principutvärdering. Varje gång programmet begär en ny token logga in tillämpas principen.

Azure AD utfärdar en token timme som standard. När du har flyttat av företagets nätverk, inom en timme tillämpas principen för program som använder modern autentisering.

### <a name="user-ip-address"></a>Användarens IP-adress

IP-adressen som används i principutvärdering är den offentliga IP-adressen för användaren. För enheter i ett privat nätverk, IP-adressen är inte klientens IP-Adressen för användarens enhet på intranätet, det är den adress som används av nätverket för att ansluta till det offentliga internet.

> [!WARNING]
> Om enheten har endast en IPv6-adress, stöds konfigurera platsvillkoret inte.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Massinläsning överföring och hämtning av namngivna platser

När du skapar eller uppdaterar kan namngivna platser för massuppdateringar, du överföra eller hämta en CSV-fil med IP-intervall. Ladda upp ersätter IP-adressintervall i listan med de från filen. Varje rad i filen innehåller en IP-adressintervall i CIDR-format.

### <a name="cloud-proxies-and-vpns"></a>Molnet proxyservrar och VPN-anslutningar

När du använder en molnvärd proxy eller VPN-lösning använder IP-adressen Azure AD när utvärdering av en princip är IP-adressen för proxyservern. X-Forwarded-For (XFF)-huvud som innehåller den offentliga IP-adressen används inte eftersom det finns ingen validering som den kommer från en betrodd källa, så skulle innebära en metod för faking en IP-adress.

När en cloud-proxy är på plats eller en princip som används för att kräva en domänansluten enhet kan användas för insidan corpnet anspråk från AD FS.

### <a name="api-support-and-powershell"></a>API-stöd och PowerShell

API och PowerShell stöds inte ännu för namngivna platser eller för principer för villkorlig åtkomst.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta hur du konfigurerar principer för villkorlig åtkomst finns i [kräver MFA för specifika appar med villkorlig åtkomst i Azure Active Directory](app-based-mfa.md).
- Om du är redo att konfigurera principer för villkorsstyrd åtkomst för din miljö kan du läsa sidan om [metodtips för villkorsstyrd åtkomst i Azure Active Directory](best-practices.md).
