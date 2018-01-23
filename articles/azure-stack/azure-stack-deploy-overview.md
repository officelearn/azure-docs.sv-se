---
title: "Utvärdera Azure Stack Development Kit | Microsoft Docs"
description: "Lär dig hur du distribuerar Azure Stack Development Kit i utvärderingssyfte."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/22/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: 4ad2e0a91e2fd5023417722fc0a1a6fae93960d0
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Snabbstart: Utvärdera Azure Stack Development Kit

*Gäller för: Azure stacken Development Kit*

Den [Azure Stack Development Kit](azure-stack-poc.md) är en miljö för testning och utveckling som du kan distribuera om du vill utvärdera och visa Azure Stack-funktioner och tjänster. För att få behöver igång, du förbereda miljön maskinvaran och köra några skript (det kan ta flera timmar). Sedan kan logga du in på portalerna administratörs- och hantera Azure-stacken och testa erbjudanden. 

1. [**Planera din maskinvara, programvara och nätverk**](azure-stack-deploy.md). Den dator som är värd för development kit (development kit host) måste uppfylla nätverkskrav för maskinvara, programvara och. Du måste också välja mellan att använda Azure Active Directory eller Active Directory Federation Services. Se till att uppfylla dessa krav innan du påbörjar distributionen så att installationen körs utan problem. 

2. [**Ladda ned och extrahera distributionspaketet**](azure-stack-run-powershell-script.md#download-and-extract-the-development-kit). Du kan hämta distributionspaketet till development kit värden eller till en annan dator. De extraherade filerna tar upp 60 GB ledigt diskutrymme, så använder en annan dator kan minska maskinvarukraven för development kit värden.

3. [**Förbereda development kit värden** ](azure-stack-run-powershell-script.md) med hjälp av installationsprogrammet. Efter det här steget startar development kit värden till Cloudbuilder.vhdx (installera filer i en virtuell hårddisk som innehåller ett startbart operativsystem och Azure-stacken).

4. [**Distribuera i development kit** ](azure-stack-run-powershell-script.md) på development kit värden.

5. Om distributionen av Azure-stacken använder Azure Active Directory, måste du [registrera Azure stacken med Azure](azure-stack-register.md) så att du kan [hämta Azure marketplace-objekt](azure-stack-download-azure-marketplace-item.md) till Azure-stacken.

När du har slutfört de här stegen har du en development kit miljö med både administratörs- och portaler. Därefter kan du [ansluta och logga in](azure-stack-connect-azure-stack.md) till portalen. Du kan sedan starta distribuera resursproviders, skapa [erbjuder](azure-stack-key-features.md#regions-services-plans-offers-and-subscriptions), och fylla i Azure-stacken [marketplace](azure-stack-marketplace.md).
