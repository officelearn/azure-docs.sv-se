---
title: 'Azure AD Connect: Funktioner i förhandsversionen | Microsoft-dokument'
description: I det här avsnittet beskrivs mer detaljerade funktioner som finns i förhandsversionen i Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7def733a80aea1be77825bb9069217f5f43e003
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261286"
---
# <a name="more-details-about-features-in-preview"></a>Mer information om funktioner i förhandsversion
I det här avsnittet beskrivs hur du använder funktioner som för närvarande förhandsgranskas.

## <a name="group-writeback"></a>Tillbakaskrivning av grupp
Alternativet för gruppåterskrivning i valfria funktioner gör att du kan skriva tillbaka **Office 365-grupper** till en skog med Exchange installerat. Det här är en grupp som alltid behärskas i molnet. Om du har Exchange lokalt kan du skriva tillbaka dessa grupper till lokala så att användare med en lokal Exchange-postlåda kan skicka och ta emot e-postmeddelanden från dessa grupper.

Mer information om Office 365-grupper och hur du använder dem finns [här](https://aka.ms/O365g).

En Office 365-grupp representeras som en distributionsgrupp i lokala AD DS. Din lokala Exchange-server måste finnas på exchange 2013 kumulativ uppdatering 8 (släpptes i mars 2015) eller Exchange 2016 för att känna igen den nya grupptypen.

**Anteckningar under förhandsgranskningen**

* Adressboksattributet är för närvarande inte ifyllt i förhandsgranskningen. Utan det här attributet visas inte gruppen i GAL. Det enklaste sättet att fylla i det här attributet är att använda Exchange PowerShell-cmdlet `update-recipient`.
* Endast skogar med Exchange-schemat är giltiga mål för grupper. Om inget Exchange upptäcktes går det inte att aktivera gruppåterskrivning.
* Endast distributioner av Exchange-enheter med en skog stöds för närvarande. Om du har mer än en Exchange-organisation lokalt behöver du en lokal GALSync-lösning för att dessa grupper ska visas i dina andra skogar.
* Funktionen Gruppåterskrivning hanterar inte säkerhetsgrupper eller distributionsgrupper.

> [!NOTE]
> En prenumeration på Azure AD Premium krävs för gruppåterskrivning.
> 
>

## <a name="user-writeback"></a>Tillbakaskrivning av användare
> [!IMPORTANT]
> Förhandsgranskningsfunktionen för användare togs bort i uppdateringen i augusti 2015 till Azure AD Connect. Om du har aktiverat den bör du inaktivera den här funktionen.
>
>

## <a name="next-steps"></a>Nästa steg
Fortsätt din [anpassade installation av Azure AD Connect](how-to-connect-install-custom.md).

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
