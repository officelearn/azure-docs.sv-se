---
title: 'Azure AD Connect: funktioner i förhands granskning | Microsoft Docs'
description: I det här avsnittet beskrivs mer detaljer som är i för hands version i Azure AD Connect.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376079"
---
# <a name="more-details-about-features-in-preview"></a>Mer information om funktioner i förhands granskning
I det här avsnittet beskrivs hur du använder funktioner som för hands version.

## <a name="group-writeback"></a>Tillbakaskrivning av grupp
Alternativet för tillbakaskrivning av grupp i valfria funktioner gör att du kan ångra **Office 365-grupper** till en skog med Exchange installerat. Det här är en grupp som alltid är hanterad i molnet. Om du har Exchange lokalt kan du skriva tillbaka de här grupperna till lokalt så att användare med en lokal Exchange-postlåda kan skicka och ta emot e-post från dessa grupper.

Mer information om Office 365-grupper och hur du kan använda dem finns [här](https://aka.ms/O365g).

En Office 365-grupp representeras som en distributions grupp i en lokal AD DS. Din lokala Exchange Server måste finnas i Exchange 2013 Cumulative Update 8 (lanseras i mars 2015) eller Exchange 2016 för att identifiera den nya grupp typen.

**Anteckningar under för hands versionen**

* Adress bokens attribut är för närvarande inte ifyllt i förhands granskningen. Utan det här attributet visas inte gruppen i GAL. Det enklaste sättet att fylla i det här attributet är att använda Exchange PowerShell-cmdleten `update-recipient`.
* Endast skogar med Exchange-schemat är giltiga mål för grupper. Om inget utbyte upptäcktes går det inte att aktivera gruppen tillbakaskrivning.
* Det finns för närvarande stöd för distribution av en Exchange-organisation med endast en skog. Om du har mer än en Exchange-organisation lokalt behöver du en lokal GALSync-lösning för att dessa grupper ska visas i dina andra skogar.
* Funktionen för tillbakaskrivning av grupp hanterar inte säkerhets grupper eller distributions grupper.

> [!NOTE]
> En prenumeration på Azure AD Premium krävs för tillbakaskrivning av grupp.
> 
>

## <a name="user-writeback"></a>Tillbakaskrivning av användare
> [!IMPORTANT]
> Funktionen för förhands granskning av användare togs bort från uppdateringen augusti 2015 till Azure AD Connect. Om du har aktiverat den, bör du inaktivera den här funktionen.
>
>

## <a name="next-steps"></a>Nästa steg
Fortsätt med din [anpassade installation av Azure AD Connect](how-to-connect-install-custom.md).

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
