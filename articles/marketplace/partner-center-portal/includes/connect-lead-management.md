---
title: ta med fil
description: ta med fil
services: commercial marketplace
documentationcenter: partner-center-commercial-marketplace
author: qianw211
manager: evansma
editor: ''
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/16/2019
ms.author: v-qiwe
ms.custom: include file
ms.openlocfilehash: 059a2691c8e7905295a65daf262338ece65247d1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812652"
---
När du publicerar ditt erbjudande till Marketplace via partner Center måste du ansluta ditt erbjudande till ditt CRM-system (Customer Relations hip Management) så att du kan ta emot kund kontakt uppgifter direkt efter att en kund uttrycker intresse eller distribuerar din produkt.

1. **Välj ett mål för lead där du vill att vi ska skicka kund leads**. Följande CRM-system stöds:

    * [Dynamics 365](../commercial-marketplace-lead-management-instructions-dynamics.md) för kund engagemang
    * [Marketo](../commercial-marketplace-lead-management-instructions-marketo.md)
    * [Salesforce](../commercial-marketplace-lead-management-instructions-salesforce.md)

    Om ditt CRM-system inte uttryckligen stöds i listan ovan, har du följande alternativ som gör att du kan lagra kundens lead-data, och sedan kan du exportera eller importera dessa data till CRM-systemet.

    * [Azure-tabell](../commercial-marketplace-lead-management-instructions-azure-table.md)
    * [HTTPS-slutpunkt](../commercial-marketplace-lead-management-instructions-https.md)

2. Läs dokumentationen som är länkad ovan till ditt valda mål för lead för att se hur du ställer in Lead-målet för att ta emot leads från Marketplace-erbjudandet. 
3. Anslut ditt erbjudande till målet för målet och publicera erbjudandet till Marketplace i Partner Center. Se dokumentationen som är länkad ovan för hur du gör detta.
4. Bekräfta att anslutningen till målet för målet har kon figurer ATS korrekt. När du har konfigurerat ditt lead-mål och har publicerat publicera på ditt erbjudande i Partner Center, kommer vi att validera anslutningen och skicka ett test lead. När du visar erbjudandet innan du går live kan du också testa din lead-anslutning genom att försöka skaffa erbjudandet själv i för hands versionen. 
5. Se till att anslutningen till mål platsen är uppdaterad så att du inte förlorar några leads, så se till att du uppdaterar anslutningarna när något har ändrats i slutet.
