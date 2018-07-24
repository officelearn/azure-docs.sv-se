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
ms.openlocfilehash: b55bc7f6aab522a6313498b6fdccc88870796224
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213035"
---
# <a name="download-and-extract-the-azure-stack-development-kit-asdk"></a>Ladda ned och extrahera Azure Stack Development Kit (ASDK)
När du har säkerställt att värddatorn development kit uppfyller de grundläggande kraven för att installera ASDK, är nästa steg att ladda ned och extrahera ASDK distributionspaketet för att få Cloudbuilder.vhdx.

## <a name="download-the-asdk"></a>Ladda ned ASDK
1. Kontrollera att datorn uppfyller följande krav innan du startar hämtning:

  - Datorn måste ha minst 60 GB ledigt utrymme på fyra separata, identiska logiska hårddiskar dessutom att operativsystemdisken.
  - [.NET framework 4.6 (eller en senare version)](https://aka.ms/r6mkiy) måste vara installerad.

2. [Gå till sidan komma igång](https://azure.microsoft.com/overview/azure-stack/try/?v=try) där du kan hämta Azure Stack Development Kit, ange din information och klicka sedan på **skicka**.
3. Ladda ned och kör den [distribution av förutsättningar för Azure Stack Development Kit](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409) kravkontrollen skript. Det här skriptet för fristående går igenom vilka förutsättningar kontroller som utförs av installationsprogrammet för Azure Stack Development Kit. Den gör det möjligt att bekräfta att du uppfyller kraven på maskinvara och programvara innan du laddar ned större paketet för Azure Stack Development Kit.
4. Under **ladda ned programvaran**, klickar du på **Azure Stack Development Kit**.

  > [!NOTE]
  > ASDK download (AzureStackDevelopmentKit.exe) är cirka 10GB.

## <a name="extract-the-asdk"></a>Extrahera ASDK
1. När nedladdningen är klar klickar du på **kör** att starta ASDK Self-Extractor (AzureStackDevelopmentKit.exe).
2. Granska och acceptera licensavtalet visas från den **licensavtal** i Self-Extractor guiden och klicka sedan på **nästa**.
3. Granska sekretessinformationen, som visas på den **viktigt meddelande** i Self-Extractor guiden och klicka sedan på **nästa**.
4. Välj plats för Azure Stack-installationsfiler ska extraheras till på den **Välj målplats** i Self-Extractor guiden och klicka sedan på **nästa**. Standardplatsen är *aktuella mappen*\Azure Stack Development Kit. 
5. Granska sammanfattningen målplatsen den **redo att extrahera** i Self-Extractor guiden, och klicka sedan på **extrahera** att extrahera CloudBuilder.vhdx (cirka 25 GB) och ThirdPartyLicenses.rtf filer. Den här processen tar lite tid att slutföra.
6. Kopiera eller flytta filen CloudBuilder.vhdx till roten C:\ enheten (C:\CloudBuilder.vhdx) på värddatorn ASDK.

> [!NOTE]
> När du har extraherat filerna kan du ta bort den. EXE och. BIN-filer för återställning av utrymme på hårddisken. Eller du kan säkerhetskopiera dessa filer så att du inte behöver ladda ned filerna igen om du vill distribuera om ASDK.


## <a name="next-steps"></a>Nästa steg
[Förbereda ASDK värddatorn](asdk-prepare-host.md)