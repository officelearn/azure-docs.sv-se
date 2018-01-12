---
title: "Plats-villkor i Azure Active Directory för villkorlig åtkomst | Microsoft Docs"
description: "Lär dig använda villkoret plats för att styra åtkomsten till dina molnappar baserat på användarens nätverksplats."
services: active-directory
keywords: "villkorlig åtkomst till appar, villkorlig åtkomst med Azure AD, säker åtkomst till företagets resurser, principer för villkorlig åtkomst"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/11/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 47b4d70c991bd618ea4ea6e5d2fd1dea86911798
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2018
---
# <a name="location-conditions-in-azure-active-directory-conditional-access"></a>Plats-villkor i Azure Active Directory för villkorlig åtkomst 

Med [villkorlig åtkomst i Azure Active Directory (AD Azure)](active-directory-conditional-access-azure-portal.md), du kan styra hur behöriga användare kan komma åt dina molnappar. Plats-villkoret för en princip för villkorlig åtkomst kan du koppla kontroller inställningar för åtkomst till nätverksplatserna för dina användare.

Den här artikeln ger dig den information du behöver att konfigurera villkor för platsen. 

## <a name="locations"></a>Platser

Azure AD aktiverar enkel inloggning på enheter, appar och tjänster från var som helst på internet. Du kan styra åtkomst till dina molnappar baserat på nätverksplats för en användare med villkoret plats. Vanliga användningsområden för villkoret plats är:

- Kräver multifaktorautentisering för användare som ansluter till en tjänst när är utanför företagets nätverk  

- Blockera åtkomst för användare som ansluter till en tjänst från specifika länder eller regioner. 

En plats är en etikett för en nätverksplats som antingen representerar en namngiven plats eller multifaktorautentisering betrott IP-adresser.


## <a name="named-locations"></a>Namngivna platser 

Du kan skapa logiska grupperingar av IP-adressintervall, länder och regioner med namngivna platser. 

 En namn-plats innehåller följande komponenter:

![Platser](./media/active-directory-conditional-access-locations/42.png)

- **Namnet** -namnet för en namngiven plats.

- **IP-adressintervall** -en eller flera IP-adressintervall i CIDR-format.

- **Markera som betrodd plats** -en flagga som du kan ange för en namngiven plats att indikera en betrodd plats. Betrodda platser normalt nätverksområden som kontrolleras av IT-avdelningen. Förutom villkorlig åtkomst betrodda sökvägarna används också av säkerhetsrapporter identitetsskydd för Azure och Azure AD för att minska [falska positiva identifieringar](active-directory-reporting-risk-events.md#impossible-travel-to-atypical-locations-1).

- **Länder / regioner** -det här alternativet kan du välja en eller flera land eller region för att definiera en namngiven plats. 

- **Inkludera okänd områden** -vissa IP-adresser mappas inte till ett visst land. Det här alternativet kan du välja om dessa IP-adresser ska tas med i Namngiven plats. De kan kontrolleras när principen med den namngivna placeringen ska tillämpas på okända platser.

Antalet namngivna platser som du kan konfigurera är begränsad av storleken på det relaterade objektet i Azure AD. Du kan konfigurera:

- En namngiven plats med upp till 1200 IP-adressintervall.

- Upp till 90 sökvägarna med en IP-adressintervall som är tilldelade till var och en av dem.




## <a name="trusted-ips"></a>Tillförlitliga IP-adresser

Du kan också konfigurera IP-adressintervall som motsvarar din organisations lokalt intranät i den [multifaktorautentisering tjänstinställningar](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). Den här funktionen kan du konfigurera upp till 50 IP-adressintervall. Det är de IP-adressintervall i CIDR-format. Mer information finns i [tillförlitliga IP-adresser](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).  

Om du har tillförlitliga IP-adresser konfigureras de visas som **MFA tillförlitliga IP-adresser** i listan över platser för plats-villkor.   

### <a name="skipping-multi-factor-authentication"></a>Hoppa över multifaktorautentisering

På sidan multifaktorautentisering service-inställningar du kan identifiera företagsintranät användare genom att välja **hoppa över multifaktorautentisering för förfrågningar från externa användare i intranätet**. Den här inställningen anger att innanför företagets nätverk anspråk som utfärdas av AD FS bör betrodd och används för att identifiera användaren som i företagsnätverket. Mer information finns i [aktivera funktionen tillförlitliga IP-adresser med hjälp av villkorlig åtkomst](../multi-factor-authentication/multi-factor-authentication-whats-next.md#enable-the-trusted-ips-feature-by-using-conditional-access).

När du har kontrollerat att det här alternativet, inklusive namngiven plats **MFA tillförlitliga IP-adresser** gäller för alla principer med den här valda.

För bärbara och stationära program som har kvar längre session livslängd, är villkorlig åtkomst regelbundet ny utvärdering. Standardvärdet är en gång i timmen. När inuti företagsnätverket anspråk är och endast utfärdas vid tidpunkten för den första autentiseringen, Azure AD kanske inte har en lista över betrodda IP-adressintervall. I det här fallet är det svårare att avgöra om användaren fortfarande företagets nätverk:

1. Kontrollera att användarens IP-adress är i något av de betrodda IP-adressintervall.

2. Kontrollera om de tre första oktetterna i användarens IP-adress matchar de första 3 oktetterna i IP-adressen för den första autentiseringen. IP-adressen jämförs med den första autentiseringen eftersom det är när inuti företagsnätverket anspråk ursprungligen utfärdade och platsen som har verifierats.

Om båda dessa steg misslyckas, kan en användare anses inte längre på en betrodd IP-adress.



## <a name="location-condition-configuration"></a>Konfiguration av villkor

När du konfigurerar villkoret plats har möjlighet att skilja mellan:

- Vilken plats som helst 
- Alla betrodda platser
- Valda platser

![Platser](./media/active-directory-conditional-access-locations/01.png)

### <a name="any-location"></a>Vilken plats som helst

Som standard väljer **var som helst** orsakar en princip som ska tillämpas på alla IP-adresser, vilket innebär att alla adresser på Internet. Den här inställningen är inte begränsat till IP-adresser som du har konfigurerat som namngiven plats inte. När du väljer **var som helst**, du kan fortfarande undanta specifika platser från en princip. Du kan till exempel använda en princip för alla platser excepts betrodda platser för att ange omfång för alla platser, förutom företagets nätverk.

### <a name="all-trusted-locations"></a>Alla betrodda platser

Det här alternativet gäller för:

- Alla platser som har markerats som betrodd plats
- **MFA tillförlitliga IP-adresser** (om konfigurerad)


### <a name="selected-locations"></a>Valda platser

Med det här alternativet kan du välja en eller flera namngivna platser. För en princip med den här inställningen ska gälla måste användaren ansluta från någon av de valda platserna. Var du klickar på **Välj** namngivet nätverk markeringen kontrollen som innehåller en lista över namngivna nätverk öppnas. I listan visas också om en nätverksplats nedan har markerats som betrodda. Namngiven plats kallas **MFA tillförlitliga IP-adresser** används för att inkludera de IP-inställningar som kan konfigureras i sidan multifaktorautentisering inställningen.

## <a name="what-you-should-know"></a>Vad du bör känna till

### <a name="when-is-a-location-evaluated"></a>När en plats i utvärderas?

Principer för villkorlig åtkomst utvärderas när: 

- En användare loggar först in 

- Azure AD utfärdar en token för principen för villkorlig åtkomst har ställts in på molnappen. 

Azure AD utfärdar en token timme som standard. När du har flyttat av företagets nätverk, inom en timme aktiveras principen för program som använder modern autentisering.


### <a name="user-ip-address"></a>Användarens IP-adress

IP-adressen som används i principutvärdering är offentliga IP-adressen för användaren. Det är inte klientens IP-Adressen för användarens enhet på intranätet, det är den adress som används av nätverket för att ansluta till internet för enheter i ett privat nätverk. 

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Massinläsning överföring och hämtning av namngivna platser

När du skapar eller uppdaterar kan med namnet platser för massuppdateringar, du överföra eller hämta en CSV-fil med IP-adressintervall. Överföra ersätter IP-adressintervall i listan med de från filen. Varje rad i filen innehåller ett IP-adressintervall i CIDR-format. 


### <a name="cloud-proxies-and-vpns"></a>Molnet proxyservrar och VPN-anslutningar 

När du använder en molnbaserade proxy- eller VPN-lösning, använder den IP-adressen Azure AD vid utvärdering av en princip är IP-adressen för proxyservern. Rubriken X-Forwarded-For (XFF) som innehåller de användare som offentliga IP-adressen inte används eftersom ingen validering som den kommer från en betrodd källa, skulle så innebära en metod för faking en IP-adress. 

När en proxy i molnet är, en princip som används för att kräva en domän enhet kan användas, eller inre corpnet anspråk från AD FS.



### <a name="api-support-and-powershell"></a>API-stöd och PowerShell 

API och PowerShell ännu stöds inte för namngivna platser eller för principer för villkorlig åtkomst.





## <a name="next-steps"></a>Nästa steg

- Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst finns [Kom igång med villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö finns i [bästa praxis för villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-best-practices.md). 
