---
title: Uppgradera insamlingsprogrammet i Azure Migrate | Microsoft Docs
description: Innehåller information om uppgraderingar för Azure Migrate Collector-installationen.
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: 7cd44318716200d665ece9ffecc45225bdfb85eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60685927"
---
# <a name="collector-appliance-updates"></a>Uppdateringar för installation av insamlaren

Den här artikeln sammanfattas uppgraderingsinformation för insamlingsprogrammet i [Azure Migrate](migrate-overview.md).

Azure Migrate Collector är en enkel installation som används för att identifiera en lokal vCenter-miljö för utvärdering innan du migrerar till Azure. [Läs mer](concepts-collector.md).

## <a name="how-to-upgrade-the-appliance"></a>Uppgradera installationen

Du kan uppgradera insamlaren till den senaste versionen utan att hämta ova-filen igen.

1. Stäng alla webbläsarfönster och eventuella öppna filer/mappar i installationen.
2. Ladda ned det senaste uppgraderingspaketet från listan över uppdateringar som nämns i den här artikeln.
3. För att säkerställa att det Hämta paketet är säker, Öppna kommandofönster för administratör och kör följande kommando för att generera en hash för ZIP-filen. Den genererade hashen måste matcha med hash nämns mot den specifika versionen:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    Exempel: **C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)**
4. Kopiera zip-filen till insamlingsprogrammet VM.
5. Högerklicka på zip-fil > **extrahera alla**.
6. Högerklicka på **Setup.ps1** > **kör med PowerShell**, och Följ installationsanvisningarna.

## <a name="collector-update-release-history"></a>Insamlaren uppdateringshistorik för versionen

### <a name="continuous-discovery-upgrade-versions"></a>Kontinuerlig identifiering: Uppgradera versioner

#### <a name="version-101014-released-on-03292019"></a>Version 1.0.10.14 (utgiven 03/29/2019)

Innehåller några förbättringar av Användargränssnittet.

Hash-värden för uppgradering [paketera 1.0.10.14](https://aka.ms/migrate/col/upgrade_10_14)

**Algoritm** | **Hash-värde**
--- | ---
MD5 | 846b1eb29ef2806bcf388d10519d78e6
SHA1 | 6243239fa49c6b3f5305f77e9fd4426a392d33a0
SHA256 | fb058205c945a83cc4a31842b9377428ff79b08247f3fb8bb4ff30c125aa47ad

#### <a name="version-101012-released-on-03132019"></a>Version 1.0.10.12 (utgiven 03/13/2019)

Innehåller korrigeringar för problem väljer Azure i molnet i installationen.

Hash-värden för uppgradering [paketera 1.0.10.12](https://aka.ms/migrate/col/upgrade_10_12)

**Algoritm** | **Hash-värde**
--- | ---
MD5 | 27704154082344c058238000dff9ae44
SHA1 | 41e9e2fb71a8dac14d64f91f0fd780e0d606785e
SHA256 | c6e7504fcda46908b636bfe25b8c73f067e3465b748f77e50027e66f2727c2a9

### <a name="one-time-discovery-deprecated-now-previous-upgrade-versions"></a>Enstaka identifiering (nu inaktuellt): Tidigare versioner av uppgradering

> [!NOTE]
> Installationen för engångsidentifiering är nu inaktuell eftersom den här metoden förlitade sig på vCenter Servers statistikinställningarna för tillgänglighet av prestandadatapunkt och samlade in räknare för genomsnittlig prestanda, vilket resulterade i för små VM-storlekar för migrering till Azure.

#### <a name="version-10916-released-on-10292018"></a>Version 1.0.9.16 (ut den 29/10/2018)

Innehåller korrigeringar av PowerCLI problem har när du konfigurerar installationen.

Hash-värden för uppgradering [paketera 1.0.9.16](https://aka.ms/migrate/col/upgrade_9_16)

**Algoritm** | **Hash-värde**
--- | ---
MD5 | d2c53f683b0ec7aaf5ba3d532a7382e1
SHA1 | e5f922a725d81026fa113b0c27da185911942a01
SHA256 | a159063ff508e86b4b3b7b9a42d724262ec0f2315bdba8418bce95d973f80cfc

#### <a name="version-10914"></a>Version 1.0.9.14

Hash-värden för uppgradering [paketera 1.0.9.14](https://aka.ms/migrate/col/upgrade_9_14)

**Algoritm** | **Hash-värde**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

#### <a name="version-10913"></a>Version 1.0.9.13

Hash-värden för uppgradering [paketera 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13)

**Algoritm** | **Hash-värde**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e


## <a name="next-steps"></a>Nästa steg

- [Läs mer](concepts-collector.md) om insamlingsprogrammet.
- [Kör en utvärdering](tutorial-assessment-vmware.md) för virtuella VMware-datorer.
