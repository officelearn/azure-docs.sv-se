---
title: Klassrum Labs-koncept - Azure Lab Services | Microsoft Docs
description: Lär dig de grundläggande principerna för Lab Services och hur det kan göra det enkelt att skapa och hantera labb.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: spelluru
ms.openlocfilehash: 255cbff94e954dd05acfe77b97929c313b1dd3cf
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2019
ms.locfileid: "56808582"
---
# <a name="classroom-labs-concepts"></a>Klassrum Labs-koncept
I följande lista innehåller huvudbegrepp Lab Services och definitioner:

## <a name="user-profiles"></a>Användarprofiler
Den här artikeln beskriver olika användarprofiler i Azure Lab Services. 

### <a name="lab-account-owner"></a>Labbkontoägare
Vanligtvis fungerar en IT-administratör för organisationens molnresurser som ansvarar för Azure-prenumerationen som labbkontoägare och utför följande uppgifter:   

- Konfigurerar ett labbkonto för organisationen.
- Hanterar och konfigurerar principer för alla labb.
- Ger behörigheter till personer i organisationen för att skapa ett labb under labbkontot.

### <a name="professor"></a>Professor
Normalt skapar användare till exempel en lärare eller en online-undervisare klassrumslabb under ett labbkonto. En utbildare utför följande uppgifter: 

- Skapar ett klassrumslabb.
- Skapar virtuella maskiner i labbet. 
- Installerar rätt programvara på virtuella datorer.
- Anger vem som kan få åtkomst till labbet.
- Tillhandahåller registreringslänken till labbet för studenter.

### <a name="student"></a>Student
En student utför följande uppgifter:

- Använder registreringslänken som denne får från labbskaparen för att registrera sig för labbet. 
- Ansluter till en virtuell dator i labbet och använder den för att göra klassarbete, uppgifter och projekt. 

## <a name="next-steps"></a>Nästa steg
Kom igång med att konfigurera ett labbkonto som krävs för att skapa ett klassrumslabb med Azure Lab Services:

- [Konfigurera ett labbkonto](tutorial-setup-lab-account.md)
