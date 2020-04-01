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
ms.openlocfilehash: e0677ecc63573d04e384a0104ee0e4e28b4d5e3b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473270"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Använda diskkryptering i Azure Security Center

Azure Security Center rekommenderar att du använder Azure Disk Encryption på okrypterade diskar på både Windows och Linux VM. Med Disk Encryption kan du kryptera diskar för dina virtuella Windows- och Linux-IaaS-datorer.  Kryptering rekommenderas både för OS- och datavolymer på den virtuella datorn.

Diskkryptering använder branschstandard [BitLocker-funktionen](https://technet.microsoft.com/library/cc732774.aspx) i Windows och [DM-Crypt-funktionen](https://en.wikipedia.org/wiki/Dm-crypt) i Linux. Dessa funktioner ger OS- och datakryptering för att skydda och skydda dina data och uppfylla dina organisatoriska säkerhets- och efterlevnadsåtaganden. Diskkryptering är integrerat med [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) för att hjälpa dig att styra och hantera diskkrypteringsnycklar och hemligheter i din Key Vault-prenumeration, samtidigt som du ser till att alla data på VM-diskarna krypteras i vila i din Azure [Storage](https://azure.microsoft.com/documentation/services/storage/).

Information om listan över versioner av Windows och Linux som stöds finns i [virtuella datorer och operativsystem som stöds](../virtual-machines/windows/disk-encryption-overview.md#supported-vms-and-operating-systems) i dokumentationen för Azure Disk Encryption.

## <a name="implement-the-recommendation"></a>Genomföra rekommendationen
1. På sidan **Rekommendationer** väljer du **Diskkryptering ska tillämpas på virtuella datorer**.
2. Välj en virtuell dator som diskkryptering rekommenderas för från **de felaktiga resurserna.**
3. Följ instruktionerna för att tillämpa kryptering på dessa virtuella datorer.

![Använda diskkryptering](./media/security-center-apply-disk-encryption/apply-disk-encryption.png)

Om du vill kryptera virtuella Azure-datorer som av Security Center har identifierats som behöver kryptering rekommenderar vi följande steg:

* Installera och konfigurera Azure PowerShell. På så sätt kan du köra de PowerShell-kommandon som krävs för att ställa in de förutsättningar som krävs för att kryptera Virtuella Azure-datorer.
* Hämta och kör Azure Disk Encryption Förutsättningar Azure PowerShell-skriptet.
* Kryptera dina virtuella datorer.

[Kryptera en Windows IaaS VM med Azure PowerShell](../virtual-machines/windows/disk-encryption-powershell-quickstart.md) - Guidar dig genom dessa steg och förutsätter att du använder en Windows-klientdator där du kan konfigurera diskkryptering.

Det finns många metoder som kan användas för virtuella Azure-datorer. Om du redan är väl insatt i Azure PowerShell eller Azure CLI kanske du föredrar att använda alternativa metoder. Mer information om dessa andra metoder finns i [Azure diskkryptering](../security/fundamentals/encryption-overview.md).

## <a name="see-also"></a>Se även
Det här dokumentet visade hur du implementerar rekommendationen "Tillämpa diskkryptering". Mer information om diskkryptering finns i:

* [Kryptering och nyckelhantering med Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (video, 36 min 39 sek) - Lär dig hur du använder diskkrypteringshantering för virtuella IaaS-datorer och Azure Key Vault för att skydda och skydda dina data.
* [Azure diskkryptering](../security/fundamentals/encryption-overview.md) (dokument)--Lär dig hur du aktiverar diskkryptering för Windows och Linux virtuella datorer.