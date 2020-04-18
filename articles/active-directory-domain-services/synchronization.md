---
title: Så här fungerar synkronisering i Azure AD Domain Services | Microsoft-dokument
description: Lär dig hur synkroniseringsprocessen fungerar för objekt och autentiseringsuppgifter från en Azure AD-klientorganisation eller lokal Active Directory Domain Services-miljö till en hanterad Azure Active Directory Domain Services-domän.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 38ed48df4d681543cc30daccf46b98635d973b89
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639914"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-ad-domain-services-managed-domain"></a>Så här synkroniseras objekt och autentiseringsuppgifter i en hanterad Azure AD Domain Services-domän

Objekt och autentiseringsuppgifter i en AD DS-hanterad domän (Azure Active Directory Domain Services) kan antingen skapas lokalt i domänen eller synkroniseras från en Azure Active Directory -klientorganisation (Azure AD). När du först distribuerar Azure AD DS konfigureras en automatisk enkelriktad synkronisering och startas för att replikera objekten från Azure AD. Den här enkelriktade synkroniseringen fortsätter att köras i bakgrunden för att hålla Azure AD DS-hanterade domänen uppdaterad med eventuella ändringar från Azure AD. Ingen synkronisering sker från Azure AD DS tillbaka till Azure AD.

I en hybridmiljö kan objekt och autentiseringsuppgifter från en lokal AD DS-domän synkroniseras med Azure AD med Azure AD Connect. När dessa objekt har synkroniserats till Azure AD gör den automatiska bakgrundssynkroniseringen sedan dessa objekt och autentiseringsuppgifter tillgängliga för program med azure AD DS-hanterad domän.

Följande diagram illustrerar hur synkronisering fungerar mellan Azure AD DS, Azure AD och en lokal AD DS-miljö som tillval:

![Synkroniseringsöversikt för en hanterad Azure AD Domain Services-domän](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>Synkronisering från Azure AD till Azure AD DS

Användarkonton, gruppmedlemskap och autentiseringsuppgifter synkroniseras på ett sätt från Azure AD till Azure AD DS. Den här synkroniseringsprocessen är automatisk. Du behöver inte konfigurera, övervaka eller hantera den här synkroniseringsprocessen. Den första synkroniseringen kan ta några timmar till ett par dagar, beroende på antalet objekt i Azure AD-katalogen. När den första synkroniseringen är klar synkroniseras ändringar som görs i Azure AD, till exempel lösenord eller attributändringar, automatiskt till Azure AD DS.

När en användare skapas i Azure AD synkroniseras de inte med Azure AD DS förrän de ändrar sitt lösenord i Azure AD. Den här lösenordsändringsprocessen gör att lösenordsh hashar för Kerberos- och NTLM-autentisering genereras och lagras i Azure AD. Lösenordshã¤nden behövs för att autentisera en användare i Azure AD DS.

Synkroniseringsprocessen är enkelriktad/ enkelriktad av design. Det finns ingen omvänd synkronisering av ändringar från Azure AD DS tillbaka till Azure AD. En Azure AD DS-hanterad domän är i stort sett skrivskyddad förutom anpassade us som du kan skapa. Du kan inte göra ändringar i användarattribut, användarlösenord eller gruppmedlemskap i en Azure AD DS-hanterad domän.

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>Synkronisering och mappning av attribut till Azure AD DS

I följande tabell visas några vanliga attribut och hur de synkroniseras med Azure AD DS.

| Attribut i Azure AD DS | Källa | Anteckningar |
|:--- |:--- |:--- |
| UPN | Användarens *UPN-attribut* i Azure AD-klient | UPN-attributet från Azure AD-klienten synkroniseras som det är till Azure AD DS. Det mest tillförlitliga sättet att logga in på en Azure AD DS-hanterad domän är att använda UPN. |
| SAMAccountName | Användarens *mailNickname-attribut* i Azure AD-klientorganisation eller automatiskt skapad | *Attributet SAMAccountName* kommer från attributet *mailNickname* i Azure AD-klienten. Om flera användarkonton har samma *mailNickname-attribut* skapas *SAMAccountName* automatiskt. Om användarens *mailNickname-* eller *UPN-prefix* är längre än 20 tecken skapas *SAMAccountName* automatiskt för att uppfylla 20-teckensgränsen för *SAMAccountName-attribut.* |
| Lösenord | Användarens lösenord från Azure AD-klienten | Äldre lösenordshar som krävs för NTLM- eller Kerberos-autentisering synkroniseras från Azure AD-klienten. Om Azure AD-klienten är konfigurerad för hybridsynkronisering med Azure AD Connect kommer dessa lösenordshöftningar från den lokala AD DS-miljön. |
| Primär användare/grupp SID | Automatiskt urraderad | Det primära SID för användar-/gruppkonton skapas automatiskt i Azure AD DS. Det här attributet matchar inte objektets primära användare/grupp SID i en lokal AD DS-miljö. Den här obalansen beror på att azure AD DS-hanterad domän har ett annat SID-namnområde än den lokala AD DS-domänen. |
| SID-historik för användare och grupper | Lokalt primäranvändare och grupp SID | *Attributet SidHistory* för användare och grupper i Azure AD DS är inställt på att matcha motsvarande primära användare eller grupp SID i en lokal AD DS-miljö. Den här funktionen hjälper till att göra lyft-och-skift av lokala program till Azure AD DS enklare eftersom du inte behöver åter-ACL-resurser. |

> [!TIP]
> **Logga in på den hanterade domänen med UPN-formatet** *Attributet SAMAccountName,* `AADDSCONTOSO\driley`till exempel , kan genereras automatiskt för vissa användarkonton i en Azure AD DS-hanterad domän. Användarnas automatiskt genererade *SAMAccountName* kan skilja sig från deras UPN-prefix, så är inte alltid ett tillförlitligt sätt att logga in.
>
> Om till exempel flera användare har samma *mailNickname-attribut* eller om användare har alltför långa UPN-prefix, kan *SAMAccountName* för dessa användare genereras automatiskt. Använd UPN-formatet, `driley@aaddscontoso.com`till exempel , för att logga in på ett tillförlitligt sätt i en Azure AD DS-hanterad domän.

### <a name="attribute-mapping-for-user-accounts"></a>Attributmappning för användarkonton

Följande tabell visar hur specifika attribut för användarobjekt i Azure AD synkroniseras med motsvarande attribut i Azure AD DS.

| Användarattribut i Azure AD | Användarattribut i Azure AD DS |
|:--- |:--- |
| accountEnabled |userAccountControl (anger eller rensar ACCOUNT_DISABLED bit) |
| city |l |
| land |Co |
| avdelning |avdelning |
| displayName |displayName |
| faksimilTelephoneNumber |faksimilTelephoneNumber |
| förnamn |förnamn |
| jobbTitle |title |
| e-post |e-post |
| mailNickname (mailNickname) |msDS-AzureADMailNickname |
| mailNickname (mailNickname) |SAMAccountName (kan ibland vara autogenererat) |
| mobil |mobil |
| Objectid |msDS-AzureADObjectId |
| påPremiseSecurityIdentifier |Sidhistory |
| lösenordPolicies |userAccountControl (anger eller rensar DONT_EXPIRE_PASSWORD-biten) |
| fysisktDeliveryOfficeName |fysisktDeliveryOfficeName |
| Postnummer |Postnummer |
| preferredLanguage |preferredLanguage |
| state |st |
| streetAddress (streetAddress) |streetAddress (streetAddress) |
| surname |sn |
| telefonAntal |telefonAntal |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Attributmappning för grupper

Följande tabell visar hur specifika attribut för gruppobjekt i Azure AD synkroniseras med motsvarande attribut i Azure AD DS.

| Gruppattribut i Azure AD | Gruppattribut i Azure AD DS |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (kan ibland vara autogenererat) |
| e-post |e-post |
| mailNickname (mailNickname) |msDS-AzureADMailNickname |
| Objectid |msDS-AzureADObjectId |
| påPremiseSecurityIdentifier |Sidhistory |
| säkerhetEnbar |grupptyp |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>Synkronisering från lokala AD DS till Azure AD och Azure AD DS

Azure AD Connect används för att synkronisera användarkonton, gruppmedlemskap och autentiseringsuppgifter från en lokal AD DS-miljö till Azure AD. Attribut för användarkonton som UPN och den lokala säkerhetsidentifieraren (SID) synkroniseras. Om du vill logga in med Azure AD DS synkroniseras även äldre lösenordsh hashar som krävs för NTLM- och Kerberos-autentisering till Azure AD.

> [!IMPORTANT]
> Azure AD Connect bör endast installeras och konfigureras för synkronisering med lokala AD DS-miljöer. Det stöds inte för att installera Azure AD Connect i en Azure AD DS-hanterad domän för att synkronisera objekt tillbaka till Azure AD.

Om du konfigurerar tillbakaskrivning synkroniseras ändringar från Azure AD tillbaka till den lokala AD DS-miljön. Om en användare till exempel ändrar sitt lösenord med hjälp av Azure AD-självbetjäningslösenordshantering uppdateras lösenordet tillbaka i den lokala AD DS-miljön.

> [!NOTE]
> Använd alltid den senaste versionen av Azure AD Connect för att se till att du har korrigeringar för alla kända fel.

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Synkronisering från en lokal miljö med flera skogar

Många organisationer har en ganska komplex lokal AD DS-miljö som innehåller flera skogar. Azure AD Connect stöder synkronisering av användare, grupper och autentiseringsuppgifter från miljöer med flera skogar till Azure AD.

Azure AD har ett mycket enklare och platt namnområde. Om du vill göra det möjligt för användare att på ett tillförlitligt sätt komma åt program som skyddas av Azure AD löser du UPN-konflikter mellan användarkonton i olika skogar. Azure AD DS-hanterade domäner använder en platt organisationsenhetsstruktur, liknande Azure AD. Alla användarkonton och grupper lagras i behållaren *AADDC-användare,* trots att de synkroniseras från olika lokala domäner eller skogar, även om du har konfigurerat en hierarkisk organisationsenhetsstruktur lokalt. Den Hanterade Azure AD DS-domänen förenklar alla hierarkiska organisationsenhetsstrukturer.

Som tidigare beskrivits finns det ingen synkronisering från Azure AD DS tillbaka till Azure AD. Du kan [skapa en anpassad organisationsenhet (OU)](create-ou.md) i Azure AD DS och sedan användare, grupper eller tjänstkonton inom dessa anpassade organisationsenheter. Inget av de objekt som skapas i anpassade företagsenheter synkroniseras tillbaka till Azure AD. Dessa objekt är endast tillgängliga i Azure AD DS-hanterade domänen och visas inte med Azure AD PowerShell-cmdlets, Microsoft Graph API eller med hjälp av Azure AD-hanteringsgränssnittet.

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>Vad synkroniseras inte med Azure AD DS

Följande objekt eller attribut synkroniseras inte från en lokal AD DS-miljö till Azure AD eller Azure AD DS:

* **Undantagna attribut:** Du kan välja att utesluta vissa attribut från synkronisering till Azure AD från en lokal AD DS-miljö med Azure AD Connect. Dessa uteslutna attribut är då inte tillgängliga i Azure AD DS.
* **Grupprinciper:** Grupprinciper som konfigurerats i en lokal AD DS-miljö synkroniseras inte med Azure AD DS.
* **Sysvol mapp:** Innehållet i *Sysvol-mappen* i en lokal AD DS-miljö synkroniseras inte med Azure AD DS.
* **Datorobjekt:** Datorobjekt för datorer som är anslutna till en lokal AD DS-miljö synkroniseras inte med Azure AD DS. Dessa datorer har ingen förtroenderelation med Azure AD DS-hanterad domän och tillhör endast den lokala AD DS-miljön. I Azure AD DS visas endast datorobjekt för datorer som uttryckligen har domänanslutna till den hanterade domänen.
* **SidHistory-attribut för användare och grupper:** Den primära användar- och primärgrupps-SID:erna från en lokal AD DS-miljö synkroniseras med Azure AD DS. Befintliga *SidHistory-attribut* för användare och grupper synkroniseras dock inte från den lokala AD DS-miljön till Azure AD DS.
* **Organisationsenheter (OU) strukturer:** Organisationsenheter som definierats i en lokal AD DS-miljö synkroniseras inte med Azure AD DS. Det finns två inbyggda aggregat i Azure AD DS - en för användare och en för datorer. Den Hanterade Azure AD DS-domänen har en platt organisationsenhetsstruktur. Du kan välja att [skapa en anpassad organisationsenhet i den hanterade domänen](create-ou.md).

## <a name="password-hash-synchronization-and-security-considerations"></a>Synkronisering av lösenordsh hash- och säkerhetsaspekter

När du aktiverar Azure AD DS krävs äldre lösenordshar för NTLM + Kerberos-autentisering. Azure AD lagrar inte lösenord för klartext, så dessa hashar kan inte genereras automatiskt för befintliga användarkonton. När NTLM och Kerberos-kompatibla lösenordsh hashar har genererats och lagras lagras de alltid på ett krypterat sätt i Azure AD.

Krypteringsnycklarna är unika för varje Azure AD-klientorganisation. Dessa hashar är krypterade så att endast Azure AD DS har åtkomst till dekrypteringsnycklarna. Ingen annan tjänst eller komponent i Azure AD har åtkomst till dekrypteringsnycklarna.

Äldre lösenords hashar synkroniseras sedan från Azure AD till domänkontrollanterna för en Azure AD DS-hanterad domän. Diskarna för dessa hanterade domänkontrollanter i Azure AD DS krypteras i vila. Dessa lösenordshökar lagras och skyddas på dessa domänkontrollanter som liknar hur lösenord lagras och skyddas i en lokal AD DS-miljö.

För Azure AD-miljöer med endast molnet [måste användare återställa/ändra sitt lösenord](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) för att de nödvändiga lösenordshönaren ska kunna genereras och lagras i Azure AD. För alla molnanvändarkonto som skapas i Azure AD efter att azure AD-domäntjänster har aktiverats genereras och lagras lösenordshã¤nderarna i kompatibla NTLM- och Kerberos-format. Alla molnanvändarkonton måste ändra sitt lösenord innan de synkroniseras till Azure AD DS.

För hybridanvändarkonton som synkroniserats från lokal AD DS-miljö med Azure AD Connect måste du [konfigurera Azure AD Connect för att synkronisera lösenordshökar i NTLM- och Kerberos-kompatibla format](tutorial-configure-password-hash-sync.md).

## <a name="next-steps"></a>Nästa steg

Mer information om detaljerna i lösenordssynkronisering finns i Så här fungerar synkronisering av [lösenord hash med Azure AD Connect](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context).

Skapa [en hanterad domän](tutorial-create-instance.md)för att komma igång med Azure AD DS .
