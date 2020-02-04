---
title: Azure-prenumerationstillstånd
description: Beskriver olika tillstånd/status för en Azure-prenumeration
keywords: status tillstånd för azure-prenumeration
author: anuragdalmia
manager: andalmia
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: andalmia
ms.openlocfilehash: 741e7cf0869e36b5788d0a883a4e982caf041512
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "75995941"
---
# <a name="azure-subscription-states"></a>Azure-prenumerationstillstånd
I den här artikeln beskrivs de olika tillstånd en Azure-prenumeration kan ha. Dessa tillstånd visas som ”Status” under prenumerationsbladen.

| Prenumerationstillstånd | Beskrivning |
|-------------| ----------------|
| **Aktiv** | Din Azure-prenumeration är aktiv. Du kan använda den här prenumerationen för att distribuera nya resurser och hantera befintliga.|
| **Förfallodatum har passerat** | Din Azure-prenumeration har en väntande utestående betalning. Prenumerationen är fortfarande aktiv men prenumerationen kan komma att inaktiveras om de förfallna beloppen inte betalas. [Lösa förfallet belopp för din Azure-prenumeration.](https://docs.microsoft.com/azure/billing/billing-azure-subscription-past-due-balance) |
| **Inaktiverad** | Din Azure-prenumeration är inaktiverad och kan inte längre användas för att skapa eller hantera Azure-resurser. I det här tillståndet frigörs dina virtuella datorer och temporära IP-adresser, lagringen skrivskyddas och andra tjänster inaktiveras. Prenumerationen kan inaktiveras om din kredit har gått ut, om du har nått din utgiftsgräns, om du har en förfallen faktura, om din kreditkortsgräns har nåtts eller om du uttryckligen har inaktiverat eller avslutat den. Beroende på prenumerationstyp och inaktiveringsorsak kan en prenumeration inaktiveras mellan 1 och 90 dagar. Efter det tas den bort permanent. [Återaktivera en inaktiverad Azure-prenumeration.](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)|
| **Borttaget** | Din Azure-prenumeration har tagits bort tillsammans med alla underliggande resurser/data. |
