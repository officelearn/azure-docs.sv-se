---
title: Azure Mobile Engagement Android SDK-Integration
description: "Senaste uppdateringarna och procedurer för Android SDK för Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 585341f9-3f39-459a-af42-864e400a0128
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: c179c39a43da0aa35e945acceacbf27fe8e328f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="release-notes"></a>Viktig information

## <a name="431-07172017"></a>4.3.1 (07/17/2017)
* Åtgärda en krasch sällan kan inträffa när du anropar `EngagementAgentUtils.isInDedicatedEngagementProcess`, som också används av den `EngagementApplication` klass.

## <a name="430-06272017"></a>4.3.0 (06/27/2017)
* 8 stöd för Android (tidigare versioner av SDK inte fungerar på Android 8).
* Inget mer beroende stödbibliotek.
* Ta bort `EngagementFragmentActivity` klass.
* På grund av att [bakgrund körning gränser](https://developer.android.com/preview/features/background.html) på Android 8 loggar i bakgrunden kan fördröjas tills användaren interagerar med enheten, detta kan påverka Push-kampanj **levererade** och **Systemmeddelande visas** statistik att försenas om enheten är i viloläge (meddelandet fortfarande visas, kommer ring och vibrerar i realtid utan problem).
* På grund av att [bakgrund plats gränser](https://developer.android.com/preview/features/background-location-limits.html)verkliga tidpunkten platsen i bakgrunden inte kommer att uppdateras ofta på Android 8.

## <a name="424-03302017"></a>4.2.4 (03/30/2017)
* Åtgärda meddelande i appen färger på Android 7 ska vara identisk med äldre versioner av Android.

## <a name="423-08102016"></a>4.2.3 (08/10/2016)
* Ingen mer WIFI-Lås.
* Åtgärda ett dödläge vid anrop av getDeviceId innan init (bugg introducerades i 4.2.0).

## <a name="422-05172016"></a>4.2.2 (05/17/2016)
* Stabilitetsförbättringar.

## <a name="421-05102016"></a>4.2.1 (05/10/2016)
* Säkerhet: inaktivera webbåtkomst visa lokal fil.
* Säkerhet: ta bort `EngagementPreferenceActivity` klass som utökar föråldrat och osäkra `PreferenceActivity` klass.
* Säkerhet: reach aktiviteter dokumenteras nu om du vill använda `exported="false"`, den här flaggan kan också användas i tidigare versioner av SDK.

## <a name="420-03112016"></a>4.2.0 (03/11/2016)
* SDK är nu licensierad under MIT.
* Tillåt att ange en anpassad enhetsidentifierare till Initieringstid SDK.

## <a name="415-02012016"></a>4.1.5 (02/01/2016)
* Stabilitetsförbättringar.

## <a name="414-01262016"></a>4.1.4 (01/26/2016)
* Stabilitetsförbättringar.

## <a name="413-1292015"></a>4.1.3 (12/9/2015)
* Stabilitetsförbättringar.

## <a name="412-11252015"></a>4.1.2 (11/25/2015)
* Stabilitetsförbättringar.

## <a name="411-11042015"></a>4.1.1 (11/04/2015)
* Stabilitetsförbättringar.

## <a name="410-08252015"></a>4.1.0 (08/25/2015)
* Hantera nya behörighetsmodellen för Android M.
* Kan nu konfigurera funktioner för plats vid körning i stället för `AndroidManifest.xml`.
* Åtgärda ett programfel behörighet: Om du använder `ACCESS_FINE_LOCATION`, sedan `ACCESS_COARSE_LOCATION` behövs inte längre.
* Stabilitetsförbättringar.

## <a name="400-07062015"></a>4.0.0 (07/06/2015)
* Interna protokollet ändras och göra analyser och push mer tillförlitlig.
* Intern push (GCM/ADM) nu används också för i appen meddelanden så att du måste konfigurera native push-autentiseringsuppgifter för alla typer av push-kampanj.
* Åtgärda helheten meddelande: de var 10-endast tal visas efter att pushas.
* Åtgärda ett programfel i webbvyn: Klicka på en länk kördes också standard åtgärds-URL.
* Åtgärda en sällsynt krasch relaterat till hantering av lokal lagring.
* Åtgärda dynamisk sträng konfigurationshantering.
* Uppdatera LICENSAVTALET.

## <a name="300-02172015"></a>3.0.0 (02/17/2015)
* Första versionen av Azure Mobile Engagement
* appId configuration ersätts av en sträng anslutningskonfiguration.
* Ta bort API för att skicka och ta emot godtycklig XMPP meddelanden från valfri XMPP entiteter.
* Ta bort API för att skicka och ta emot meddelanden mellan enheter.
* Förbättringar av säkerhet.
* Spårning av Google Play och SmartAd tas bort.

