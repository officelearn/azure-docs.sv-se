---
title: Microsoft Security Code Analysis-versioner
description: I den här artikeln beskrivs kommande versioner för tillägget Microsoft Security Code Analysis
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/14/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: a07ce7437d664baca0cfdc310dbc2631f41fdbcc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81462040"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Utgåvor och färdplan för analys av Microsofts säkerhetskod

Microsoft Security Code Analysis team i samarbete med Developer Support är stolta över att kunna meddela de senaste och kommande förbättringarna av vår MSCA-förlängning. Se färdplanen nedan.

![Versioner](./media/security-code-analysis-releases/releases.png)

## <a name="credential-scanner-v20-released-on-april-1-2020"></a>Autentiseringsskanner v2.0: Släpptes den 1 april 2020

### <a name="innovations--improvements"></a>Innovationer & förbättringar

- **Kärnmotor**

   - Genomsnittlig prestandauppgradering på 25 % med nära linjära körtider
   - Kontext/evidensbaserad sökning och rangordning för ökad noggrannhet
   - Förbättringar av allmänna lösenordsidentifieringar och matchande logik för uppenbara platshållare (till exempel fakePassword)

- **Täckning** - Stöd för 25+ hemliga typer inklusive följande topp begärda:

   - Lösenord för fabric-kontocertifikat
   - Klienthemlighet/API-nyckel
   - HTTP-auktoriseringshuvud
   - Amazon S3 Client Secret Access Key
   - Azure Active Directory-klientåtkomsttoken
   - Azure-funktionshanterare/API-nyckel
   - Power BI-åtkomstnyckel
   - Lösenordsmönster för Azure Resource Manager-mall

- **Utdata**

   - Stöd för filformat för SARIF 2.1 och CSV-filutdata

## <a name="binskim-v160-to-be-released-on-april-2020"></a>BinSkim v1.6.0: Släpps i april 2020

### <a name="improvements"></a>Förbättringar

- FEATURE: Uppdatera till slutlig SARIF v2 (version 2.1.16). Detta möjliggör resultatcacheing när du skickar --hashar på kommandoraden, en signifikant prestandaförbättring när rekursivt analysera kataloger med flera kopior av skanningsmål.
- BUGG FIX: Fixa stavfel i BA2021. DoNotMarkWritableSectionsAsExecutable utdata.
- PRESTANDA: Eliminera PDB-inläsning för alla icke-blandade lägen för hanterade sammansättningar, inklusive IL-bibliotek (i förväg kompilerade) binärfiler.
- FALSKT NEGATIVT KORRIGERING: Kontrollera att en pdb som placeras bredvid en binär faktiskt matchar den binära under analys
- FEATURE: Ge --lokal-symbol-kataloger argument för att ange ytterligare (lokal, icke-symbol-server) PDB-uppslagplatser
- FALSKT POSITIV FIX: Hoppa över PDB-driven analys för den genererade .NET-kärnans inbyggda bootstrap exe (som inte är användarkontrollbar kod).

## <a name="whats-next-in-fy20"></a>Framtida tid i FY20?

- Verktyget Java-säkerhetsanalys
- Verktyget Analys av Python-säkerhet
- ES Lint ersätter TS Lint för TypeScript och JavaScript

## <a name="next-steps"></a>Nästa steg

Instruktioner om hur du ar ombord och installerar Microsofts säkerhetskodsanalys finns i vår [introduktions- och installationsguide](security-code-analysis-onboard.md).

Om du har fler frågor om tillägget och de verktyg som erbjuds, kolla in vår [FAQ sida](security-code-analysis-faq.md).
