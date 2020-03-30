---
title: ta med fil
description: ta med fil
documentationcenter: partner-center-commercial-marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 08/16/2019
ms.author: dsindona
ms.custom: include file
ms.openlocfilehash: ee6ee600160349548a0e886131f1a29b90de8746
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275891"
---
När du publicerar ditt erbjudande till marknadsplatsen via Partner Center måste du ansluta ditt erbjudande till ditt CRM-system (Customer Relationship Management) så att du kan få kundkontaktinformation omedelbart efter det att en kund har uttryckt intresse eller distribuerar din produkt.

1. **Välj en leaddestination där du vill att vi ska skicka kundleads.** Följande CRM-system stöds:

    * [Dynamics 365](../commercial-marketplace-lead-management-instructions-dynamics.md) för kundengagemang
    * [Marketo](../commercial-marketplace-lead-management-instructions-marketo.md)
    * [Salesforce](../commercial-marketplace-lead-management-instructions-salesforce.md)

    Om CRM-systemet inte uttryckligen stöds i listan ovan har du följande alternativ som gör att du kan lagra kundleaddata och sedan kan du exportera eller importera dessa data till CRM-systemet.

    * [Azure-tabell](../commercial-marketplace-lead-management-instructions-azure-table.md)
    * [HTTPS-slutpunkt](../commercial-marketplace-lead-management-instructions-https.md)

2. Läs dokumentationen som är länkad ovan till ditt valda leadmål för att se hur du ställer in leadmålet på att ta emot leads från marknadsplatserbjudandet. 
3. Anslut ditt erbjudande till leadmålet och publicera erbjudandet på marknadsplatsen i Partner Center. Se dokumentationen som länkas ovan för hur du gör detta.
4. Bekräfta att anslutningen till leadmålet har konfigurerats korrekt. När du har konfigurerat din leaddestination korrekt och har träffat Publicera på ditt erbjudande i Partner Center validerar vi anslutningen och skickar en testledare till dig. När du visar erbjudandet innan du sänder live kan du också testa din leadanslutning genom att försöka skaffa erbjudandet själv i förhandsversionen. 
5. Se till att anslutningen till leaddestinationen förblir uppdaterad så att du inte förlorar några leads, så se till att du uppdaterar dessa anslutningar när något har ändrats på din sida.
