---
title: Uppgradera insamlingsprogrammet i Azure Migrate | Microsoft Docs
description: Innehåller information om uppgraderingar för Azure Migrate Collector-installationen.
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: cd48b824845a0195fc78814a88dd449507c99394
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50241471"
---
# <a name="collector-update-release-history"></a>Insamlaren uppdateringshistorik för versionen

Den här artikeln sammanfattas uppgraderingsinformation för insamlingsprogrammet i [Azure Migrate](migrate-overview.md).

Azure Migrate Collector är en enkel installation som används för att identifiera en lokal vCenter-miljö för utvärdering innan du migrerar till Azure. [Läs mer](concepts-collector.md).


## <a name="one-time-discovery-upgrade-versions"></a>Enstaka identifiering: uppgradera versioner

### <a name="version-10916-released-on-10292018"></a>Version 1.0.9.16 (ut den 29/10/2018)

Innehåller korrigeringar av PowerCLI problem har när du konfigurerar installationen. 

Hash-värden för uppgradering [paketera 1.0.9.16](https://aka.ms/migrate/col/upgrade_9_16)

**Algoritm** | **Hash-värde**
--- | ---
MD5 | d2c53f683b0ec7aaf5ba3d532a7382e1
SHA1 | e5f922a725d81026fa113b0c27da185911942a01
SHA256 | a159063ff508e86b4b3b7b9a42d724262ec0f2315bdba8418bce95d973f80cfc

### <a name="version-10914"></a>Version 1.0.9.14

Hash-värden för uppgradering [paketera 1.0.9.14](https://aka.ms/migrate/col/upgrade_9_14)

**Algoritm** | **Hash-värde**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

### <a name="version-10913"></a>Version 1.0.9.13

Hash-värden för uppgradering [paketera 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13)

**Algoritm** | **Hash-värde**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e

### <a name="version-10911"></a>Version 1.0.9.11

Hash-värden för uppgradering [paketera 1.0.9.11](https://aka.ms/migrate/col/upgrade_9_11)

**Algoritm** | **Hash-värde**
--- | ---
MD5 | 0e36129ac5383b204720df7a56b95a60
SHA1 | aa422ef6aa6b6f8bc88f27727e80272241de1bdf
SHA256 | 5f76dbbe40c5ccab3502cc1c5f074e4b4bcbf356d3721fd52fb7ff583ff2b68f

### <a name="version-1097"></a>Version 1.0.9.7

Hash-värden för uppgradering [paketera 1.0.9.7](https://aka.ms/migrate/col/upgrade_9_7)

**Algoritm** | **Hash-värde**
--- | ---
MD5 | 01ccd6bc0281f63f2a672952a2a25363
SHA1 | 3e6c57523a30d5610acdaa14b833c070bffddbff
SHA256 | e3ee031fb2d47b7881cc5b13750fc7df541028e0a1cc038c796789139aa8e1e6

## <a name="continuous-discovery-upgrade-versions"></a>Kontinuerlig identifiering: uppgradera versioner

Ingen uppgradering för identifiering av kontinuerlig produkten är tillgänglig ännu.

## <a name="run-an-upgrade"></a>Utför en uppgradering

Du kan uppgradera insamlaren till den senaste versionen utan att hämta ova-filen igen.

1. Du kan hämta det senaste uppgraderingspaketet i listan nedan.
2. För att säkerställa att den nedladdade snabbkorrigeringen är säker, Öppna kommandofönster för administratör och kör följande kommando för att generera en hash för ZIP-filen. Den genererade hashen måste matcha med hash nämns mot den specifika versionen:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    Exempel: **C:\>CertUtil - HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)**
3. Kopiera zip-filen till insamlingsprogrammet VM.
4. Högerklicka på zip-fil > **extrahera alla**.
5. Högerklicka på **Setup.ps1** > **kör med PowerShell**, och Följ installationsanvisningarna.


## <a name="next-steps"></a>Nästa steg

- [Läs mer](concepts-collector.md) om insamlingsprogrammet.
- [Kör en utvärdering](tutorial-assessment-vmware.md) för virtuella VMware-datorer.
