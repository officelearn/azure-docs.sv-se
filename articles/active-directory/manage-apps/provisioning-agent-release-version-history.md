---
title: 'Azure AD Connect etablerings agent: Versions historik | Microsoft Docs'
description: Den här artikeln innehåller en lista över alla versioner av Azure AD Connect etablerings agenten och beskriver nya funktioner och problem som har åtgärd ATS
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: de6ef5dedb0779536cfbc1e6a3440e748ff78524
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862114"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Azure AD Connect etablerings agent: Versionshistorik
Den här artikeln innehåller versioner och funktioner i Azure AD Connect etablerings agent som har släppts. Azure AD-teamet uppdaterar regelbundet etablerings agenten med nya funktioner och funktioner. Etablerings agenter uppdateras automatiskt när en ny version släpps. 

Vi rekommenderar att du aktiverar automatisk uppdatering för dina agenter så att du har de senaste funktionerna och fel korrigeringarna. Microsoft tillhandahåller direkt support för den senaste agent versionen och en version.

## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Versions status

9 september 2019: Publicerat för automatisk uppdatering

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

* Möjlighet att konfigurera ytterligare spårning och loggning för fel sökning av etablerings agent problem
* Möjlighet att bara hämta de AD-attribut som kon figurer ATS i mappningen för att förbättra synkroniseringens prestanda

### <a name="fixed-issues"></a>Åtgärdade problem

* En bugg har åtgärd ATS som gjorde att agenten övergick i ett tillstånd som inte svarar om det uppstod problem med AD-anslutningsfel
* En bugg har åtgärd ATS som orsakade problem när binära data lästes från Active Directory
* Åtgärdade ett fel där-i agenten kunde inte förnya förtroende med moln hybrid identitets tjänsten

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Versions status

23 jan 2019: Frigjord för nedladdning

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

* Förbättringar Provisioning agent & kopplings arkitektur för bättre prestanda, stabilitet och pålitlighet 
* Förenklad etablering agent konfiguration med hjälp av UI-driven installations guide 
* Stöd har lagts till för automatiska agent uppdateringar

