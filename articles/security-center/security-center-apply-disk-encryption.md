---
title: Använda diskkryptering i Azure Security Center | Microsoft-dokument
description: Det här dokumentet visar hur du implementerar rekommendationen **Ansök diskkryptering i**Azure Security Center .
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: d0f96fe758966a435f8fb8e448e75cbb18b85122
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604520"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Använda diskkryptering i Azure Security Center
Azure Security Center rekommenderar att du använder diskkryptering om du har virtuella Windows- eller Linux-diskar som inte är krypterade med Azure Disk Encryption. Med Disk Encryption kan du kryptera diskar för dina virtuella Windows- och Linux-IaaS-datorer.  Kryptering rekommenderas både för OS- och datavolymer på den virtuella datorn.

Diskkryptering använder branschstandard [BitLocker-funktionen](https://technet.microsoft.com/library/cc732774.aspx) i Windows och [DM-Crypt-funktionen](https://en.wikipedia.org/wiki/Dm-crypt) i Linux. Dessa funktioner ger OS- och datakryptering för att skydda och skydda dina data och uppfylla dina organisatoriska säkerhets- och efterlevnadsåtaganden. Diskkryptering är integrerat med [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) för att hjälpa dig att styra och hantera diskkrypteringsnycklar och hemligheter i din Key Vault-prenumeration, samtidigt som du ser till att alla data på VM-diskarna krypteras i vila i din Azure [Storage](https://azure.microsoft.com/documentation/services/storage/).

> [!NOTE]
> Azure Disk Encryption stöds på följande Windows-serveroperativsystem – Windows Server 2008 R2, Windows Server 2012 och Windows Server 2012 R2. Diskkryptering stöds på följande Linux-serveroperativsystem - Ubuntu, CentOS, SUSE och SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>Genomföra rekommendationen
1. I bladet **Rekommendationer** väljer du **Använd diskkryptering**.
2. I bladet **Tillämpa diskkryptering** visas en lista över virtuella datorer för vilka diskkryptering rekommenderas.
3. Följ instruktionerna för att tillämpa kryptering på dessa virtuella datorer.

![][1]

Om du vill kryptera virtuella Azure-datorer som av Security Center har identifierats som behöver kryptering rekommenderar vi följande steg:

* Installera och konfigurera Azure PowerShell. På så sätt kan du köra de PowerShell-kommandon som krävs för att ställa in de förutsättningar som krävs för att kryptera Virtuella Azure-datorer.
* Hämta och kör Azure Disk Encryption Förutsättningar Azure PowerShell-skriptet.
* Kryptera dina virtuella datorer.

[Kryptera en Virtuell Windows IaAS-dator med Azure PowerShell](../virtual-machines/windows/disk-encryption-powershell-quickstart.md) som går igenom de här stegen. Det här avsnittet förutsätter att du använder en Windows-klientdator som du konfigurerar diskkryptering från.

Det finns många metoder som kan användas för virtuella Azure-datorer. Om du redan känner till Azure PowerShell eller Azure CLI väl kanske du hellre vill använda alternativa metoder. Mer information om dessa andra metoder finns i [Azure diskkryptering](../security/fundamentals/encryption-overview.md).

## <a name="see-also"></a>Se även
Det här dokumentet visade hur du implementerar rekommendationen "Tillämpa diskkryptering". Mer information om diskkryptering finns i följande:

* [Kryptering och nyckelhantering med Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (video, 36 min 39 sek) – Lär dig hur du använder diskkrypteringshantering för virtuella IaaS-datorer och Azure Key Vault för att skydda och skydda dina data.
* [Azure diskkryptering](../security/fundamentals/encryption-overview.md) (dokument) – Lär dig hur du aktiverar diskkryptering för Virtuella Datorer med Windows och Linux.

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Lär dig hur du konfigurerar säkerhetsprinciper.
* Övervakning av [säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar hälsotillståndet för dina Azure-resurser.
* [Hantera och svara på säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Läs om hur du hanterar och svarar på säkerhetsaviseringar.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Azure Security-blogg](https://blogs.msdn.com/b/azuresecurity/) – Hitta blogginlägg om Azure-säkerhet och efterlevnad.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
