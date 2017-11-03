---
title: "Tillämpa diskkryptering i Azure Security Center | Microsoft Docs"
description: "Det här dokumentet beskrivs hur du implementerar rekommenderar Azure Security Center ** gäller disk encryption **."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 67cff664f3723b2194ecd1519729cca17069d07f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Tillämpa diskkryptering i Azure Security Center
Azure Security Center rekommenderar att du tillämpar kryptering om du har Windows eller Linux VM diskar som inte är krypterade med hjälp av Azure Disk Encryption. Diskkryptering kan du kryptera din Windows- och Linux IaaS-VM-diskar.  Kryptering rekommenderas både för OS- och datavolymer på den virtuella datorn.

Diskkryptering använder branschstandarden [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funktion i Windows och [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funktion i Linux. De här funktionerna ger OS och datakryptering för att skydda och skydda dina data och uppfylla din organisations säkerhet och efterlevnad åtaganden. Kryptering är integrerad med [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) som hjälper dig att styra och hantera disk krypteringsnycklar och hemligheter i Nyckelvalvet-prenumeration, medan du säkerställer att krypteras alla data i de Virtuella diskarna i vila i din [Azure Storage](https://azure.microsoft.com/documentation/services/storage/).

> [!NOTE]
> Azure Disk Encryption stöds på följande Windows serveroperativsystem - Windows Server 2008 R2, Windows Server 2012 och Windows Server 2012 R2. Diskkryptering stöds i de följande Linux serveroperativsystem - Ubuntu och CentOS, SUSE SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. I den **rekommendationer** bladet väljer **gäller kryptering av**.
2. I den **gäller diskkryptering** bladet visas en lista över virtuella datorer som diskkryptering rekommenderas.
3. Följ instruktionerna för att tillämpa kryptering på dessa virtuella datorer.

![][1]

Om du vill kryptera virtuella Azure-datorer som har identifierats av Security Center som behov av kryptering, rekommenderar vi följande:

* Installera och konfigurera Azure PowerShell. På så sätt kan du köra PowerShell-kommandon som krävs för att ställa in kraven och kryptera Azure virtuella datorer.
* Hämta och köra Azure Disk Encryption krav Azure PowerShell-skript.
* Kryptera dina virtuella datorer.

[Kryptera en virtuell dator i Azure](security-center-disk-encryption.md) vägleder dig genom stegen.  Det här avsnittet förutsätter att du använder Windows 10 som den klientdator där du konfigurera diskkryptering.

Det finns flera metoder som kan användas för virtuella datorer i Azure. Om du redan känner till Azure PowerShell eller Azure CLI väl kanske du hellre vill använda alternativa metoder. Mer information om dessa andra metoder, se [Azure disk encryption](../security/azure-security-disk-encryption.md).

## <a name="see-also"></a>Se även
Det här dokumentet visar dig hur du implementerar Security Center-rekommendationen ”Använd disk encryption”. Mer information om kryptering finns i följande avsnitt:

* [Hantering av kryptering och nyckeln med Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (video-36 min 39 sekund) – Lär dig hur du använder kryptering Diskhantering för IaaS-VM och Azure Key Vault för att skydda och skydda dina data.
* [Kryptera en virtuell dator i Azure](security-center-disk-encryption.md) (dokument) – Lär dig hur du krypterar Azure virtuella datorer.
* [Azure disk encryption](../security/azure-security-disk-encryption.md) (dokument) – Lär dig hur du aktiverar kryptering för Windows och Linux virtuella datorer.

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Lär dig hur du ställer in säkerhetsprinciper.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig rekommendationer för att skydda dina Azure-resurser.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) --hittar du blogginlägg om säkerhet och Azure kompatibilitet.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
