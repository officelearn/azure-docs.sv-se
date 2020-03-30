---
title: Konfigurera Synkronisering av Azure-filer
description: Konfigurera Synkronisering av Azure-filer. Ett vanligt textblock som delas mellan migreringsdokument.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 53d7af6b43ff24ab12501570385162759f4c7633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209589"
---
Det här steget binder samman alla resurser och mappar som du har konfigurerat på Windows Server under föregående steg.

* Logga in på [Azure-portalen](https://portal.azure.com).
* Leta reda på din Storage Sync Service-resurs.
* Skapa en ny *synkroniseringsgrupp* i Storage Sync Service-resursen för varje Azure-filresurs. I Azure File Sync-terminologin blir Azure-filresursen en *molnslutpunkt* i synkroniseringstopologin som du beskriver med skapandet av en synkroniseringsgrupp. När du skapar synkroniseringsgruppen ger du den ett välbekant namn, så att du känner igen vilken uppsättning filer som synkroniseras här. Se till att du refererar till Azure-filresursen med ett matchande namn.
* När synkroniseringsgruppen har skapats visas en rad för den i listan över synkroniseringsgrupper. Klicka på namnet (en länk) för att visa innehållet i synkroniseringsgruppen. Du kommer att se din Azure-filresurs under "Cloud slutpunkter".
* Leta reda på kommandoknappen till *+ Lägg till serverslutpunkt*. Mappen på den lokala servern som du har etablerat blir sökvägen till den här *serverslutpunkten*.
