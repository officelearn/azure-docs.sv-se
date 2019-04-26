---
title: Dynamics 365 för Customer Engagement teknisk information fliken – Azure Marketplace | Microsoft Docs
description: Så här anger du den tekniska informationen för en Dynamics 365 för Customer Engagement-program på AppSource-marknadsplatsen.
services: Dynamics 365 for Customer Engagement Offer, Azure, Marketplace, Cloud Partner Portal, AppSource
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: pbutlerm
ms.openlocfilehash: 1dd488c2eb419b5e210a48d7a94f7d0bb423a2b1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60332896"
---
# <a name="dynamics-365-for-customer-engagement-technical-info-tab"></a>Dynamics 365 för Customer Engagement teknisk informationsfliken

Den **teknisk information** fliken den **nytt erbjudande** sidan kan du ange detaljerad information om din Dynamics 365 för Customer Engagement-appen, inklusive CRM-paketet och marknadsföring logotyp tillgångar.  Den här fliken är uppdelad i fyra delar: **Programinformation**, **CRM paketet**, **CRM paketet tillgänglighet**, och **marknadsföring artefakter**. En tillagda asterisk (*) på ett fältnamn visar att det krävs. 


## <a name="application-info-section"></a>Program som påträffats

Du ger information om ditt Dynamics 365-program i det här avsnittet.

![Program information i fliken teknisk information](./media/dynce-technical-info-tab1.png)

I följande tabell beskrivs de här fälten.

|      Fält                    |    Beskrivning                  |
|    ---------                  |  ---------------                |
|   Grundläggande license-modell          |  Licens modellen avgör hur kunder tilldelas ditt program i Dynamics 365 Admin Center. **Resursen** licensiering är instansbaserade, medan **användaren** licenser har tilldelats en per klient.  |
|  Utgående S2S och CRM Secure Store-åtkomst |  Gör det möjligt för konfiguration av CRM Secure Store eller Server-till-Server (S2S) utgående åtkomst. *Den här funktionen kräver särskilda hänsyn från Dynamics 365-teamet under fasen för certifiering.* Microsoft kontaktar dig för att slutföra ytterligare steg för att stödja den här funktionen.  |
| Prenumerera på händelser för CRM-livscykel | Integrering med livscykel för Dynamics 365-händelser måste du ange en dedikerad tjänst som har registrerats via ett särskilt avtal med Microsoft. *Den här funktionen kräver särskilda hänsyn från Dynamics 365-teamet under fasen för certifiering.* Du kommer att kontaktas Slutför ytterligare steg som stöder denna funktion.  |
| Programmets konfiguration Url | URL: en för den webbsida som gör det möjligt att konfigurera programmet |
| Tillämpliga Dynamics 365-produkter  | Välj Dynamics 365-produkter som det här erbjudandet gäller för. Det här erbjudandet visas under valda produkter i AppSource.  |
| Marknadsföring bara ändra         | Ange det här alternativet om Ja anger att endast marknadsföring/beskrivande ändringar har gjorts till det befintliga erbjudandet.  Sådana ändringar kan erbjudandet att kringgå certifiering och etablering faser.  |
|  |  |


## <a name="crm-package-section"></a>CRM-paketet avsnittet

Du får information om din AppSource paketfil i det här avsnittet.  Den här informationen används av Dynamics 365-team för validering och godkännande.

![CRM-paketet avsnittet Teknisk informationsfliken](./media/dynce-technical-info-tab2.png)

I följande tabell beskrivs de här fälten.

|      Fält                    |    Beskrivning                  |
|    ---------                  |  ---------------                |
|  Filnamn för ditt paket     |  Filnamn för ditt paket (.zip).  Det här namnet är *inte* offentliga och kommer att användas internt av certifieringsteam för Dynamics 365.  |
|  URL                          |  URL för ett Azure Storage-konto som innehåller filen uppladdade paketet. Den här URL: en ska inkludera en skrivskyddad SAS-nyckel så att vårt team att den använder ditt paket för verifiering.  |
| Mer än ett crm-paket     | Välj Ja endast om du stöder flera versioner av crm med olika paket.  Varje version har en motsvarande paketet fil här måste du skapa individuellt.  |
| Scenariot och Använd fallet tillgång   | Gör det möjligt för överföring av en funktionell specifikationsdokument för ditt program för användning av Dynamics 365-verifieringsteamet.  Det önskade formatet för den här specifikationen är den [E2E scenariot användarmallen](https://isvdocumentation.blob.core.windows.net/d365documentation/Power%20Platform%20E2E%20document.docx).  |
|  |  |


## <a name="crm-package-availability-section"></a>CRM-paketet datauppsättningars

Välj vilka geografiska områden programmet blir tillgängligt för kunder i det här avsnittet.  Distribuera till följande landsbaserade regioner *kräver särskild behörighet och verifiering* under certifieringsprocessen: [Tyskland](https://docs.microsoft.com/azure/germany/), [molnet för amerikanska myndigheter](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome), och tips.


## <a name="marketing-artifacts-section"></a>Marknadsföring artefakter avsnittet

Det här avsnittet måste du ladda upp en programlogotyp som ska användas för att representera ditt paket i AppSource-marknadsplatsen.  Fyrkantig logotyp måste vara i PNG-format och vara storleken 255 x 115 pixlar.


## <a name="next-steps"></a>Nästa steg

Vi rekommenderar att du tillhandahåller en demonstration av ditt program genom att slutföra den [Test Drive-fliken](./cpp-testdrive-tab.md)
