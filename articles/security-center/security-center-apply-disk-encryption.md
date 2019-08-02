---
title: Använd disk kryptering i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskriver hur du implementerar Azure Security Center rekommendation **Använd disk kryptering**.
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
ms.openlocfilehash: 1abb0a5f5523032440086932eb9a1621d4cef455
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726240"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Använd disk kryptering i Azure Security Center
Azure Security Center rekommenderar att du använder disk kryptering om du har virtuella Windows-eller Linux-diskar som inte är krypterade med Azure Disk Encryption. Med disk kryptering kan du kryptera dina virtuella Windows-och Linux IaaS-diskar.  Kryptering rekommenderas både för OS- och datavolymer på den virtuella datorn.

Disk kryptering använder standarden för [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) i Windows och funktionen [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) i Linux. Dessa funktioner ger operativ system och data kryptering för att skydda och skydda dina data och uppfylla organisationens säkerhets-och efterlevnads åtaganden. Disk kryptering är integrerat med [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) för att hjälpa dig att styra och hantera disk krypterings nycklar och hemligheter i din Key Vault prenumeration, samtidigt som du ser till att alla data på de virtuella dator diskarna krypteras i vila i din [Azure Storage](https://azure.microsoft.com/documentation/services/storage/).

> [!NOTE]
> Azure Disk Encryption stöds i följande Windows Server-operativsystem – Windows Server 2008 R2, Windows Server 2012 och Windows Server 2012 R2. Disk kryptering stöds i följande Linux-serveroperativ system – Ubuntu, CentOS, SUSE och SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. På bladet **rekommendationer** väljer du **Använd disk kryptering**.
2. På bladet **Använd disk kryptering** visas en lista över virtuella datorer som disk kryptering rekommenderas för.
3. Följ instruktionerna för att tillämpa kryptering på de här virtuella datorerna.

![][1]

För att kryptera Azure-Virtual Machines som har identifierats av Security Center som behöver kryptering rekommenderar vi följande steg:

* Installera och konfigurera Azure PowerShell. På så sätt kan du köra de PowerShell-kommandon som krävs för att konfigurera de krav som krävs för att kryptera Azure-Virtual Machines.
* Hämta och kör skriptet Azure Disk Encryption förutsättningar Azure PowerShell.
* Kryptera dina virtuella datorer.

[Kryptera en virtuell Windows IaaS-dator med Azure PowerShell](../security/azure-disk-encryption-linux-powershell-quickstart.md) vägleder dig genom de här stegen. Det här avsnittet förutsätter att du använder en Windows-klientdator från vilken du konfigurerar disk kryptering.

Det finns många metoder som kan användas för Azure Virtual Machines. Om du redan känner till Azure PowerShell eller Azure CLI väl kanske du hellre vill använda alternativa metoder. Läs mer om de här andra metoderna i [Azure disk](../security/azure-security-disk-encryption.md)Encryption.

## <a name="see-also"></a>Se också
Det här dokumentet visar hur du implementerar Security Center rekommendation "Använd disk kryptering". Mer information om disk kryptering finns i följande avsnitt:

* [Kryptering och nyckel hantering med Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (video, 36 min 39 SEK)--Lär dig hur du använder disk kryptering för virtuella IaaS-datorer och Azure Key Vault för att skydda och skydda dina data.
* [Azure disk](../security/azure-security-disk-encryption-overview.md) Encryption (dokument)--Lär dig hur du aktiverar disk kryptering för virtuella Windows-och Linux-datorer.

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhets principer i Azure Security Center](tutorial-security-policy.md) – lär dig hur du konfigurerar säkerhets principer.
* [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md) – lär dig hur du övervakar Azure-resursernas hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Hantera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md) – lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhets blogg](https://blogs.msdn.com/b/azuresecurity/) – hitta blogg inlägg om säkerhet och efterlevnad i Azure.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
