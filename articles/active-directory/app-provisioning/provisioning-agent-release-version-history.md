---
title: 'Etablerande agent för Azure AD Connect: Versionsversionshistorik | Microsoft-dokument'
description: I den här artikeln visas alla versioner av Azure AD Connect-etableringsagenten och nya funktioner och fasta problem beskrivs
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
ms.date: 02/26/2020
ms.subservice: app-provisioning
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 559bca4f5020cebe06be7f24f7af5ec2e94bec0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183252"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Etablerande agent för Azure AD Connect: Versionsversionshistorik
I den här artikeln visas de versioner och funktioner i Azure Active Directory Connect-etableringsagenten som har släppts. Azure AD-teamet uppdaterar regelbundet etableringsagenten med nya funktioner och funktioner. Etableringsagenten uppdateras automatiskt när en ny version släpps. 

Microsoft ger direkt stöd för den senaste agentversionen och en version tidigare.

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Versionsstatus

4 december 2019: Släppt för nedladdning

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

* Inkluderar stöd för [Azure AD Connect-molnetablering](../cloud-provisioning/what-is-cloud-provisioning.md) för synkronisering av användar-, kontakt- och gruppdata från lokala Active Directory till Azure AD


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Versionsstatus

September 9, 2019: Släppt för automatisk uppdatering

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

* Möjlighet att konfigurera ytterligare spårning och loggning för felsökning av etableringsagentproblem
* Möjlighet att hämta endast de Azure AD-attribut som är konfigurerade i mappningen för att förbättra prestanda för synkronisering

### <a name="fixed-issues"></a>Åtgärdade problem

* Fixade ett fel där agenten gick in i ett tillstånd som inte svarade om det fanns problem med Azure AD-anslutningsfel
* Åtgärdade ett fel som orsakade problem när binära data lästes från Azure Active Directory
* Fixade ett fel där agenten inte kunde förnya förtroendet med molnhybrididentitetstjänsten

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Versionsstatus

23 januari 2019: Släppt för nedladdning

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

* Omarbetade etableringsagenten och anslutningsarkitekturen för bättre prestanda, stabilitet och tillförlitlighet 
* Förenklad konfiguration för etableringsagent med hjälp av användargränssnittsstyrd installationsguide 
* Lagt till stöd för automatiska agentuppdateringar

