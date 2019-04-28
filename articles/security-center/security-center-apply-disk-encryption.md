---
title: Tillämpa diskkryptering i Azure Security Center | Microsoft Docs
description: Det här dokumentet visar hur du implementerar Azure Security Center-rekommendationen **tillämpa diskkryptering**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: d285461aef1e56dfd08a162e51ada0340d6eeae4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095429"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Tillämpa diskkryptering i Azure Security Center
Azure Security Center rekommenderar att du installerar diskkryptering om du har Windows- eller Linux VM-diskar som inte är krypterade med Azure Disk Encryption. Diskkryptering kan du kryptera din Windows- och Linux IaaS VM-diskar.  Kryptering rekommenderas både för OS- och datavolymer på den virtuella datorn.

Disk Encryption används branschstandard [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funktion i Windows och [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funktion i Linux. De här funktionerna ger OS och datakryptering för att skydda och skydda dina data och uppfyller organisationens säkerhet och efterlevnad. Diskkryptering är integrerad med [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) som hjälper dig att hantera diskkrypteringsnycklarna och hemligheter i Key Vault-prenumerationen, medan du säkerställer att alla data på de Virtuella datordiskarna krypteras i vila i din [Azure Storage](https://azure.microsoft.com/documentation/services/storage/).

> [!NOTE]
> Azure Disk Encryption stöds på följande Windows serveroperativsystem – Windows Server 2008 R2, Windows Server 2012 och Windows Server 2012 R2. Diskkryptering stöds i de följande Linux serveroperativsystem - Ubuntu, CentOS, SUSE och SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. I den **rekommendationer** bladet väljer **tillämpa diskkryptering**.
2. I den **tillämpa diskkryptering** bladet visas en lista över virtuella datorer som diskkryptering rekommenderas.
3. Följ anvisningarna för att tillämpa kryptering för dessa virtuella datorer.

![][1]

Om du vill kryptera Azure-datorer som har identifierats av Security Center som behov av kryptering, rekommenderar vi följande steg:

* Installera och konfigurera Azure PowerShell. På så sätt kan du köra PowerShell-kommandon som krävs för att ställa in kraven och kryptera Azure-datorer.
* Hämta och kör Azure Disk Encryption krav för Azure PowerShell-skript.
* Kryptera dina virtuella datorer.

[Kryptera en virtuell dator IaaS Windows med Azure PowerShell](../security/quick-encrypt-vm-powershell.md) vägleder dig genom stegen. Det här avsnittet förutsätter att du använder en Windows-klientdator där du konfigurera diskkryptering.

Det finns flera metoder som kan användas för Azure Virtual Machines. Om du redan känner till Azure PowerShell eller Azure CLI väl kanske du hellre vill använda alternativa metoder. Mer information om dessa andra metoder, se [Azure-diskkryptering](../security/azure-security-disk-encryption.md).

## <a name="see-also"></a>Se också
Det här dokumentet visade hur du implementerar Security Center-rekommendationen ”tillämpa diskkryptering”. Mer information om diskkryptering finns i:

* [Kryptering och nyckelhantering hantering med Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (video och 36 min 39 sek) – Lär dig hur du använder kryptering Diskhantering för virtuella IaaS-datorer och Azure Key Vault för att skydda och skydda dina data.
* [Azure-diskkryptering](../security/azure-security-disk-encryption-overview.md) (dokument) – Lär dig hur du aktiverar diskkryptering för Windows och Linux-datorer.

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Lär dig hur du ställer in säkerhetsprinciper.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/) --hittar du blogginlägg om Azure-säkerhet och regelefterlevnad.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
