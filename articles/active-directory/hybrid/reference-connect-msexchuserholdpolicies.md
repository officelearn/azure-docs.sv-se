---
title: 'Azure AD Connect: msExchUserHoldPolicies och cloudMsExchUserHoldPolicies | Microsoft Docs'
description: Det här avsnittet beskriver attributets beteende för attributen msExchUserHoldPolicies och cloudMsExchUserHoldPolicies
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4c637a01825616334cda8faa594efd08f29de8d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74213079"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect-msExchUserHoldPolicies och cloudMsExchUserHoldPolicies
I följande referens dokument beskrivs de attribut som används av Exchange och det korrekta sättet att redigera standard reglerna för synkronisering.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Vad är msExchUserHoldPolicies och cloudMsExchUserHoldPolicies?
Det finns två typer av [undantag](https://docs.microsoft.com/Exchange/policy-and-compliance/holds/holds?view=exchserver-2019) som är tillgängliga för en Exchange-Server: kvarhållning av undantag och på plats. När tvister har Aktiver ATS spärras alla objekt i post lådan.  En på-plats-plats används endast för att bevara de objekt som uppfyller villkoren i en Sök fråga som du har definierat med hjälp av eDiscovery-verktyget på plats.

Attributen MsExchUserHoldPolcies och cloudMsExchUserHoldPolicies tillåter lokala AD och Azure AD för att avgöra vilka användare som är under ett undantag beroende på om de använder lokalt Exchange eller Exchange Online.

## <a name="msexchuserholdpolicies-synchronization-flow"></a>flöde för msExchUserHoldPolicies-synkronisering
Som standard synkroniseras MsExchUserHoldPolcies av Azure AD Connect direkt till msExchUserHoldPolicies-attributet i metaversum och sedan till msExchUserHoldPolices-attributet i Azure AD

I följande tabeller beskrivs flödet:

Inkommande från lokala Active Directory:

|Active Directory attribut|Attributnamn|Flödes typ|Metaversum-attribut|Synkroniseringsregel|
|-----|-----|-----|-----|-----|
|Lokalt Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolices|I från AD-User Exchange|

Utgående till Azure AD:

|Metaversum-attribut|Attributnamn|Flödes typ|Azure AD-attribut|Synkroniseringsregel|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolicies|Ut till AAD – UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>flöde för cloudMsExchUserHoldPolicies-synkronisering
Som standard synkroniseras cloudMsExchUserHoldPolicies av Azure AD Connect direkt till cloudMsExchUserHoldPolicies-attributet i metaversum. Sedan, om msExchUserHoldPolices inte är null i metaversum, skickas attributet i utflödet till Active Directory.

I följande tabeller beskrivs flödet:

Inkommande från Azure AD:

|Active Directory attribut|Attributnamn|Flödes typ|Metaversum-attribut|Synkroniseringsregel|
|-----|-----|-----|-----|-----|
|Lokalt Active Directory|cloudMsExchUserHoldPolicies|Direct|cloudMsExchUserHoldPolicies|I från AAD – användar utbyte|

Utgående till lokal Active Directory:

|Metaversum-attribut|Attributnamn|Flödes typ|Azure AD-attribut|Synkroniseringsregel|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|IF (NOT NULL)|msExchUserHoldPolicies|Ut till AD – UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>Information om attributets beteende
MsExchangeUserHoldPolicies är ett attribut för en myndighet.  Ett enda Authority-attribut kan anges för ett objekt (i det här fallet användar objekt) i den lokala katalogen eller i moln katalogen.  Starten av auktoritets regler anger att om attributet synkroniseras från den lokala datorn kommer Azure AD inte att kunna uppdatera det här attributet.

Om du vill tillåta att användare anger en undantags princip för ett användar objekt i molnet används attributet cloudMSExchangeUserHoldPolicies. Det här attributet används eftersom Azure AD inte kan ställa in msExchangeUserHoldPolicies direkt baserat på de regler som beskrivs ovan.  Det här attributet synkroniseras sedan tillbaka till den lokala katalogen om, msExchangeUserHoldPolicies inte är null och ersätter det aktuella värdet för msExchangeUserHoldPolicies.

Under vissa omständigheter, till exempel, om båda har ändrats lokalt och i Azure på samma gång kan detta orsaka vissa problem.  

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
