---
title: 'Azure AD Connect: Funktioner i preview | Microsoft Docs'
description: "Det här avsnittet beskrivs i mer detalj som finns i förhandsgranskningen i Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: cbf8f729d0ebfb271bb0d8702ac043442b42c262
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="more-details-about-features-in-preview"></a>Mer information om funktionerna i förhandsversionen
Det här avsnittet beskriver hur du använder funktioner för närvarande under förhandsgranskning.

## <a name="group-writeback"></a>Tillbakaskrivning av grupp
Alternativet för tillbakaskrivning av grupp i valfria funktioner kan du tillbakaskrivning **Office 365-grupper** till en skog med Exchange installerad. Det här är en grupp som alltid lärt dig i molnet. Om du har Exchange lokalt kan sedan du skriva tillbaka grupperna till lokalt så att användare med en lokal Exchange-postlåda kan skicka och ta emot e-post från dessa grupper.

Mer information om Office 365-grupper och hur de används finns [här](http://aka.ms/O365g).

En grupp för Office 365 representeras som en distributionsgrupp i lokala AD DS. Lokal Exchange-server måste vara i Exchange 2013 cumulative update 8 (ut i mars 2015) eller Exchange 2016 att identifiera den här nya grupptypen.

**Anteckningar under förhandsgranskningen**

* -Bok postattributet för närvarande inte har fyllts i i förhandsgranskningen. Utan det här attributet visas inte gruppen i den globala Adresslistan. Det enklaste sättet att fylla i det här attributet är att använda Exchange PowerShell-cmdleten `update-recipient`.
* Endast skogar med Exchange-schemat är ogiltigt mål för grupper. Tillbakaskrivning av grupp är inte möjligt att aktivera om ingen Exchange upptäcktes.
* Endast sammanhållna Exchange-organisation distributioner stöds för närvarande. Om du har mer än ett Exchange organisation lokalt, måste en lokal GALSync lösning för dessa grupper visas i din andra skogar.
* Funktionen för tillbakaskrivning av grupp hanteras inte säkerhetsgrupper eller distributionsgrupper.

> [!NOTE]
> Det krävs en prenumeration på Azure AD Premium för tillbakaskrivning av grupp.
> 
>

## <a name="user-writeback"></a>Tillbakaskrivning av användare
> [!IMPORTANT]
> Förhandsgranskningsfunktionen för tillbakaskrivning av användare har tagits bort i augusti 2015-uppdateringen till Azure AD Connect. Om du har aktiverat det, bör du inaktivera den här funktionen.
>
>

## <a name="next-steps"></a>Nästa steg
Fortsätta din [anpassad installation av Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
