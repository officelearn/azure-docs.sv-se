---
title: 'Azure AD Connect: Funktioner i förhandsversion | Microsoft Docs'
description: Det här avsnittet beskrivs i mer detalj som finns i förhandsversion i Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: ab64cc2fc206772fe0f842af9d2f4c3596d76c07
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46314197"
---
# <a name="more-details-about-features-in-preview"></a>Mer information om funktioner i förhandsversion
Det här avsnittet beskriver hur du använder funktioner för närvarande i förhandsversion.

## <a name="group-writeback"></a>Tillbakaskrivning av grupp
Alternativet för tillbakaskrivning av grupp i valfria funktioner kan du tillbakaskrivning av **Office 365-grupper** i en skog med Exchange installerad. Det här är en grupp som alltid lärt dig i molnet. Om du har Exchange lokalt, kan sedan du skriva tillbaka grupperna till den lokala så att användare med en lokal Exchange-postlåda kan skicka och ta emot e-postmeddelanden från dessa grupper.

Mer information om Office 365-grupper och hur de används finns [här](https://aka.ms/O365g).

En Office 365-grupp representeras som en distributionsgrupp i lokala AD DS. Den lokala Exchange-servern måste vara i Exchange 2013 cumulative update 8 (släpptes i mars 2015) eller Exchange 2016 för att identifiera den här nya grupptypen.

**Anteckningar i förhandsversionen**

* Attributet adressboken fylls inte för närvarande i förhandsversionen. Utan det här attributet visas inte i gruppen i den globala Adresslistan. Det enklaste sättet att fylla i det här attributet är att använda Exchange PowerShell-cmdleten `update-recipient`.
* Endast skogar med Exchange-schemat är ogiltigt mål för grupper. Tillbakaskrivning av grupp är inte möjligt att aktivera om ingen Exchange har identifierats.
* Endast sammanhållna Exchange-organisation distributioner stöds för närvarande. Om du har mer än ett Exchange organisation lokalt, måste en lokal GALSync lösning för dessa grupper visas i dina andra skogar.
* Funktionen för tillbakaskrivning av grupp hanteras inte säkerhetsgrupper eller distributionsgrupper.

> [!NOTE]
> En prenumeration på Azure AD Premium krävs för tillbakaskrivning av grupp.
> 
>

## <a name="user-writeback"></a>Tillbakaskrivning av användare
> [!IMPORTANT]
> Förhandsversionsfunktionen för tillbakaskrivning av användare har tagits bort i augusti 2015-uppdateringen till Azure AD Connect. Om du har aktiverat det, bör du inaktivera den här funktionen.
>
>

## <a name="next-steps"></a>Nästa steg
Fortsätta din [anpassad installation av Azure AD Connect](how-to-connect-install-custom.md).

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
