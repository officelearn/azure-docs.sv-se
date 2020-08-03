---
title: Azure-prenumerationstillstånd
description: I den här artikeln beskrivs olika tillstånd och status för en Azure-prenumeration.
keywords: status tillstånd för azure-prenumeration
author: anuragdalmia
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 07/24/2020
ms.author: andalmia
ms.openlocfilehash: 0d301087578c8a944920bca932bcae3127ae50ef
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282644"
---
# <a name="azure-subscription-states"></a>Azure-prenumerationstillstånd

I den här artikeln beskrivs de olika tillstånd en Azure-prenumeration kan ha. Dessa tillstånd visas som **Status** i Azure-portalens prenumerationsområden.

| Prenumerationstillstånd | Beskrivning |
|-------------| ----------------|
| **Aktiv**/**aktiverad** | Din Azure-prenumeration är aktiv. Du kan använda den här prenumerationen för att distribuera nya resurser och hantera befintliga.<br><br>Alla åtgärder (PUT, PATCH, DELETE, POST, GET) är tillgängliga för de resursprovidrar som har [registrerats för din prenumeration](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). |
| **Borttaget** | Din Azure-prenumeration har tagits bort tillsammans med alla underliggande resurser/data.<br><br>Inga åtgärder är tillgängliga. |
| **Inaktiverad** | Din Azure-prenumeration är inaktiverad och kan inte längre användas för att skapa eller hantera Azure-resurser. I det här tillståndet frigörs dina virtuella datorer och temporära IP-adresser, lagringen skrivskyddas och andra tjänster inaktiveras. En prenumeration kan inaktiveras på grund av följande: Din kredit kan ha gått ut. Du kanske har nått utgiftsgränsen. Du har en förfallen faktura. Kreditkortsgränsen har överskridits. Eller så har den uttryckligen inaktiverats eller avbrutits. Beroende på prenumerationstypen kan en prenumeration vara inaktiverad mellan 1 och 90 dagar. Efter det raderas den permanent. Mer information finns i artikeln [Återaktivera en inaktiverad Azure-prenumeration](subscription-disabled.md).<br><br>Åtgärder för att skapa eller uppdatera resurser (PUT, PATCH) är inaktiverade. Åtgärder som vidtar en åtgärd (POST) är också inaktiverade. Du kan hämta eller ta bort resurser (GET, DELETE). Dina resurser är fortfarande tillgängliga. |
| **Upphört** | Din Azure-prenumeration har upphört eftersom den avbröts. Du kan återaktivera en förfallen prenumeration. Mer information finns i artikeln [Återaktivera en inaktiverad Azure-prenumeration](subscription-disabled.md).<br><br>Åtgärder för att skapa eller uppdatera resurser (PUT, PATCH) är inaktiverade. Åtgärder som vidtar en åtgärd (POST) är också inaktiverade. Du kan hämta eller ta bort resurser (GET, DELETE).|
| **Förfallodatum har passerat** | Din Azure-prenumeration har en väntande utestående betalning. Prenumerationen är fortfarande aktiv men prenumerationen kan komma att inaktiveras om de förfallna beloppen inte betalas. Mer information finns i [Så här löser du förfallet belopp för din Azure-prenumeration.](resolve-past-due-balance.md).<br><br>Alla åtgärder är tillgängliga. |
| **Varnad** | Din Azure-prenumeration är i ett varningstillstånd och kan användas normalt, men den inaktiveras inom kort om inte varningsorsaken åtgärdas. En prenumeration kan till exempel vara i ett varningstillstånd om betalningen har förfallit, avbrutits av användaren eller om prenumerationsperioden har gått ut.<br><br>Alla åtgärder är tillgängliga. |
