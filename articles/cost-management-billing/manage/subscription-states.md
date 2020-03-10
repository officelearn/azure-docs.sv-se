---
title: Azure-prenumerationstillstånd
description: I den här artikeln beskrivs olika tillstånd och status för en Azure-prenumeration.
keywords: status tillstånd för azure-prenumeration
author: anuragdalmia
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: andalmia
ms.openlocfilehash: 29af3d064b481548cdb9b9518e9735eb34aaf034
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270225"
---
# <a name="azure-subscription-states"></a>Azure-prenumerationstillstånd

I den här artikeln beskrivs de olika tillstånd en Azure-prenumeration kan ha. Dessa tillstånd visas som **Status** i Azure-portalens prenumerationsområden.

| Prenumerationstillstånd | Beskrivning |
|-------------| ----------------|
| **Aktiv** | Din Azure-prenumeration är aktiv. Du kan använda den här prenumerationen för att distribuera nya resurser och hantera befintliga.|
| **Borttaget** | Din Azure-prenumeration har tagits bort tillsammans med alla underliggande resurser/data. |
| **Inaktiverad** | Din Azure-prenumeration är inaktiverad och kan inte längre användas för att skapa eller hantera Azure-resurser. I det här tillståndet frigörs dina virtuella datorer och temporära IP-adresser, lagringen skrivskyddas och andra tjänster inaktiveras. En prenumeration kan inaktiveras på grund av följande: Din kredit kan ha gått ut. Du kanske har nått utgiftsgränsen. Du har en förfallen faktura. Kreditkortsgränsen har överskridits. Eller så har den uttryckligen inaktiverats eller avbrutits. Beroende på prenumerationstypen kan en prenumeration vara inaktiverad mellan 1 och 90 dagar. Efter det raderas den permanent. Mer information finns i artikeln [Återaktivera en inaktiverad Azure-prenumeration](subscription-disabled.md). |
| **Upphört** | Din Azure-prenumeration har upphört eftersom den avbröts. Du kan återaktivera en förfallen prenumeration. Mer information finns i [Återaktivera en inaktiverad Azure-prenumeration](subscription-disabled.md).|
| **Förfallodatum har passerat** | Din Azure-prenumeration har en väntande utestående betalning. Prenumerationen är fortfarande aktiv men prenumerationen kan komma att inaktiveras om de förfallna beloppen inte betalas. Mer information finns i [Så här löser du förfallet belopp för din Azure-prenumeration.](resolve-past-due-balance.md). |
