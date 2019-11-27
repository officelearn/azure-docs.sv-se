---
title: Använd en Azure-filresurs med Azure Storage | Microsoft Docs
description: Lär dig hur du använder en Azure-filresurs med Windows och Windows Server.
author: todmccoy
manager: dcscontentpm
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: rogarana
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: e1e581134b4e3821659c43bb7f55003239594bd9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233797"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>Återställa ett borttaget lagrings konto

Azure Storage ger data återhämtning via automatiserade repliker, men förhindrar inte användare eller program kod från att skada data, oavsett om de är oavsiktligt eller skadligt. Att bevara data åter givningen under instanser av program eller användar fel kräver mer avancerade tekniker, till exempel att kopiera data till en sekundär lagrings plats med en Gransknings logg.

Följande tabell innehåller en översikt över omfånget för lagrings konto återställning beroende på en replikerings strategi.

| |LRS|ZRS|GRS|RA-GRS|
|---|---|---|---|---|
|Lagrings konto Azure Resource Manager|Ja|Ja|Ja|Ja|
|Klassiskt lagrings konto|Ja|Ja|Ja|Ja|

Samla in följande information och skicka en supportbegäran till Microsoft Support:

* Lagringskontots namn
* Datum för borttagning
* Lagrings konto region
* Hur togs lagrings kontot bort?
* Vilken metod tog bort lagrings kontot? (Portal, PowerShell osv.)

Viktiga punkter

* Det kan vanligt vis ta upp till 15 dagar från borttagningen av lagrings tjänsten att utföra skräp insamling, så återställning av lagrings konton kan inte återställas med ett service avtal.
* Microsoft Support kommer att försöka återställa containern/kontot på bästa möjliga sätt och kan inte garantera återställningen.

> [!NOTE]
> Återställningen kanske inte lyckas om kontot har återskapats. Om du redan har skapat om kontot måste du ta bort det innan återställningen kan utföras.
