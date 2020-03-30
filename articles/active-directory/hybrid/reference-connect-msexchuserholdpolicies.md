---
title: 'Azure AD Connect: msExchUserHoldPolicies och cloudMsExchUserHoldPolicies | Microsoft-dokument'
description: I det här avsnittet beskrivs attributbeteendet för attributen msExchUserHoldPolicies och cloudMsExchUserHoldPolicies
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74213079"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect - msExchUserHoldPolicies och cloudMsExchUserHoldPolicies
I följande referensdokument beskrivs dessa attribut som används av Exchange och det korrekta sättet att redigera standardsynkroniseringsreglerna.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Vad är msExchUserHoldPolicies och cloudMsExchUserHoldPolicies?
Det finns två typer av [spärrar](https://docs.microsoft.com/Exchange/policy-and-compliance/holds/holds?view=exchserver-2019) tillgängliga för en Exchange Server: Bevarande av juridiska skäl och In-Place Hold. När bevarande av juridiska skäl är aktiverat placeras alla postlådeobjekt i alla objekt.  Ett in-Place Hold används för att bevara endast de objekt som uppfyller villkoren för en sökfråga som du har definierat med hjälp av verktyget EDiscovery på plats.

Attributen MsExchUserHoldPolcies och cloudMsExchUserHoldPolicies gör det möjligt för lokala AD och Azure AD att avgöra vilka användare som är under spärr beroende på om de använder lokala Exchange eller Exchange on-line.

## <a name="msexchuserholdpolicies-synchronization-flow"></a>msExchUserHoldPolicies synkroniseringsflöde
Som standard synkroniseras MsExchUserHoldPolcies av Azure AD Connect direkt till attributet msExchUserHoldPolicies i metaversumet och sedan till attributet msExchUserHoldPolices i Azure AD

I följande tabeller beskrivs flödet:

Inkommande från lokal Active Directory:

|Active Directory-attribut|Attributnamn|Flödestyp|Metaversumattribut|Synkroniseringsregel|
|-----|-----|-----|-----|-----|
|Lokalt Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolices|In från AD - User Exchange|

Utgående till Azure AD:

|Metaversumattribut|Attributnamn|Flödestyp|Azure AD-attribut|Synkroniseringsregel|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|Direct|msExchUserHoldPolicies|Ut till AAD - UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>cloudMsExchUserHoldPolicies synkroniseringsflöde
Som standard cloudMsExchUserHoldPolicies synkroniseras av Azure AD Connect direkt till molnetMsExchUserHoldPolicies attribut i metaversumet. Om msExchUserHoldPolices sedan inte är null i metaversumet flödade attributet ut till Active Directory.

I följande tabeller beskrivs flödet:

Inkommande från Azure AD:

|Active Directory-attribut|Attributnamn|Flödestyp|Metaversumattribut|Synkroniseringsregel|
|-----|-----|-----|-----|-----|
|Lokalt Active Directory|cloudMsExchUserHoldPolicies|Direct|cloudMsExchUserHoldPolicies|In från AAD - User Exchange|

Utgående till lokal Active Directory:

|Metaversumattribut|Attributnamn|Flödestyp|Azure AD-attribut|Synkroniseringsregel|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|IF(INTE NULL)|msExchUserHoldPolicies|Ut till AD – UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>Information om attributbeteendet
MsExchangeUserHoldPolicies är ett enda myndighetsattribut.  Ett enda auktoritetsattribut kan ställas in på ett objekt (i det här fallet användarobjekt) i den lokala katalogen eller i molnkatalogen.  Reglerna för start av auktoritet dikterar att om attributet synkroniseras från lokala, kommer Azure AD inte att tillåtas att uppdatera det här attributet.

För att tillåta användare att ange en spärrprincip för ett användarobjekt i molnet används attributet cloudMSExchangeUserHoldPolicies. Det här attributet används eftersom Azure AD inte kan ange msExchangeUserHoldPolicies direkt baserat på de regler som beskrivs ovan.  Det här attributet synkroniseras sedan tillbaka till den lokala katalogen om msExchangeUserHoldPolicies inte är null och ersätter det aktuella värdet för msExchangeUserHoldPolicies.

Under vissa omständigheter, till exempel, om båda ändrades lokalt och i Azure samtidigt, kan detta orsaka vissa problem.  

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
