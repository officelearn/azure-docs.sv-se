---
title: Ladda ned och extrahera Azure Stack Development Kit (ASDK) | Microsoft Docs
description: Beskriver hur du ladda ned och extrahera Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 0cf389c9443a9cff477b884c277d72de27769afc
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
ms.locfileid: "29975883"
---
# <a name="download-and-extract-the-azure-stack-development-kit-asdk"></a>Ladda ned och extrahera Azure Stack Development Kit (ASDK)
När du har kontrollerat att värddatorn development kit uppfyller de grundläggande kraven för att installera ASDK, är nästa steg att ladda ned och extrahera ASDK distributionspaketet för att få Cloudbuilder.vhdx.

## <a name="download-the-asdk"></a>Hämta ASDK
1. Kontrollera att datorn uppfyller följande krav innan du startar hämtningen:

  - Datorn måste ha minst 60 GB ledigt utrymme på fyra separata, identiska logiska hårddiskar dessutom att operativsystemets disk.
  - [.NET framework 4.6 (eller en senare version)](https://aka.ms/r6mkiy) måste vara installerad.

2. [Gå till sidan komma igång](https://azure.microsoft.com/overview/azure-stack/try/?v=try) där du kan hämta Azure Stack Development Kit, ange din information och klickar sedan på **skicka**.
3. Hämta och kör den [distribution av förutsättningar för Azure-stacken Development Kit](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409) kravkontrollen skript. Fristående skriptet går igenom vilka förutsättningar kontroller som utförs av inställningarna för Azure-stacken Development Kit. Det ger ett sätt att bekräfta att du uppfyller kraven på maskinvara och programvara innan du hämtar större paketet för Azure-stacken Development Kit.
4. Under **ladda ned programvaran**, klickar du på **Azure Stack Development Kit**.

  > [!NOTE]
  > ASDK download (AzureStackDevelopmentKit.exe) är cirka 10GB.

## <a name="extract-the-asdk"></a>Extrahera ASDK
1. När nedladdningen är klar klickar du på **kör** att starta ASDK Self-Extractor (AzureStackDevelopmentKit.exe).
2. Granska och godkänn licensavtalet visas från den **licensavtalet** i Self-Extractor guiden och klicka sedan på **nästa**.
3. Granska sekretessinformationen, som visas på den **viktigt meddelande** i Self-Extractor guiden och klicka sedan på **nästa**.
4. Välj platsen för installationsfilerna för Azure-stacken extraheras till på den **Välj målplats** i Self-Extractor guiden och klicka sedan på **nästa**. Standardplatsen är *aktuella mappen*\Azure Stack Development Kit. 
5. Granska sammanfattningen målplatsen den **redo att extrahera** i Self-Extractor guiden, och klicka sedan på **extrahera** att extrahera CloudBuilder.vhdx (cirka 25 GB) och ThirdPartyLicenses.rtf filer. Den här processen tar en stund att slutföra.
6. Kopiera eller flytta filen CloudBuilder.vhdx till roten på enhet C:\ (C:\CloudBuilder.vhdx) på värddatorn ASDK.

> [!NOTE]
> När du har extraherat filerna kan du ta bort den. EXE och. BIN-filer för återställning av utrymme på hårddisken. Eller, du kan säkerhetskopiera dessa filer så att du inte behöver hämta filerna igen om du behöver distribuera om ASDK.


## <a name="next-steps"></a>Nästa steg
[Förbereda värddatorn ASDK](asdk-prepare-host.md)