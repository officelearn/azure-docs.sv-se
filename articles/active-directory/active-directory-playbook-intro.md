---
title: Azure Active Directory PoC Playbook introduktion | Microsoft Docs
description: Utforska och snabbt implementera scenarier för identitets- och åtkomsthantering
services: active-directory
keywords: Azure active directory, playbook, konceptbevis, PoC
documentationcenter: ''
author: dstefanMSFT
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: 80b7ba9507b85621e7b0623362774edc0c8cc729
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
ms.locfileid: "26601215"
---
# <a name="azure-active-directory-proof-of-concept-playbook-introduction"></a>Azure Active Directory bevis på koncept Playbook: introduktion

Den här artikeln innehåller riktlinjer för att utforska olika Azure AD-funktioner i ett konceptbevis (PoC). Målgruppen för det här dokumentet är identitet arkitekter, IT-proffs och systemintegrerare.

## <a name="how-to-use-this-playbook"></a>Hur du använder den här Playbook

1. Använd den [tema](active-directory-playbook-ingredients.md#theme) avsnittet och välj områdena av intresse baserat på dina behov.  
2. Omfattningen PoC genom att välja de scenarier som överensstämmer med dina affärsmål. Ju kortare bättre. Vi rekommenderar att du gör det som kort och koncis som möjligt för att förmedla värdet till de berörda parter och minimerar komplexitet för att använda den.  
3. Använd den [implementering](active-directory-playbook-implementation.md) avsnitt för att förstå scenarierna och vad de betyder för din miljö. I varje scenario beskrivs hur du ställer in (vi kallar [byggblock](active-directory-playbook-building-blocks.md)), och hur du navigerar scenarier. 
4. Varje byggblock beskrivs kraven behövs, samt en ungefärlig tid att slutföra. Detta kan hjälpa dig under planeringsprocessen. 
5. Baserat på 1 till 3 ovan, definierar den [miljö](active-directory-playbook-ingredients.md#environment) där du vill köra. Vi rekommenderar för att sträva efter en produktionsmiljö att få en bra känsla av för dina användare. 
6. När med motstridiga krav, använder du matrisen användbara förhållandet 
   * Tema till Central visar värde  
   * Jämn förbereda, konfigurera och köra scenarier 
   * Minimal tid att köra målscenarier 
   * Så nära produktion som möjligt i din begränsningar 

>[!NOTE]
> I den här artikeln visas vissa specifika tredjepartsprogram och produkter som exempel i informationssyfte. Azure AD stöder tusentals program i vår [programgalleriet](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) som du kan använda baserat på dina behov och miljö. 



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]