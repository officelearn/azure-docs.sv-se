---
title: Aktivera kryptering för storage-konto i Azure Security Center | Microsoft Docs
description: Det här dokumentet visar hur du implementerar Azure Security Center-rekommendationer **Aktivera kryptering för Azure Storage-konto**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 9f8679e988513eecd778970ac796264b274a8088
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56111670"
---
# <a name="enable-encryption-for-azure-storage-account-in-azure-security-center"></a>Aktivera kryptering för Azure storage-konto i Azure Security Center
Azure Security Center rekommendera att du aktiverar Azure Storage Service Encryption för vilande data.

Storage Service Encryption (SSE) fungerar genom att kryptera data när de skrivs till Azure storage och dekryptera data före hämtning.  SSE är för närvarande endast tillgänglig för Azure Blob service och kan användas för blockblobbar, sidblobbar och tilläggsblobbar.  Mer information finns i [Storage Service Encryption för vilande data](../storage/common/storage-service-encryption.md).


> [!Note]
> När du har aktiverat kryptering krypteras bara nya data. Alla befintliga blobar i lagringskontot förbli okrypterade. För att kryptera befintliga blobar kan se den [Storage Service Encryption vanliga frågor och svar](../storage/common/storage-service-encryption.md#faq-for-storage-service-encryption).
>
>

Kryptering av lagringstjänst stöds bara på Resource Manager-lagringskonton. Klassiska lagringskonton stöds inte för närvarande. Information om klassiska och Resource Manager distributionsmodellerna finns i [Azures distributionsmodeller](../azure-classic-rm.md).

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det här dokumentet är inte en stegvis guide.
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. I den **rekommendationer** bladet väljer **Aktivera kryptering för Azure Storage-konto**.
   ![Aktivera kryptering för lagringskonto][1]
2. Den **aktiverar kryptering av** blad öppnas. Det här bladet visar en lista över Azure storage-konton där kryptering är inaktiverat. I det här exemplet väljer vi **storageacct1**.
   ![Aktivera lagringskryptering][2]
3. Den **kryptering** bladet för **storageacct1** öppnas. Välj **aktiverat**.
   ![Kryptering bladet][3]
4. Välj **Spara**.

Du har nu aktiverats lagringskryptering för **storageacct1**.


## <a name="see-also"></a>Se också
Det här dokumentet visade hur du implementerar Security Center-rekommendationen ”Aktivera kryptering för Azure Storage-konto”. Läs mer om Azure Storage Service Encryption finns i:

* [Azure Storage Service Encryption för vilande Data](../storage/common/storage-service-encryption.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Lär dig hur du ställer in säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Vanliga frågor om Azure Security Center](security-center-faq.md) -finns vanliga frågor om tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/) -hittar du blogginlägg om Azure-säkerhet och regelefterlevnad.

<!--Image references-->
[1]: ./media/security-center-enable-encryption-for-storage-account/enable-encryption-for-storage-account.png
[2]: ./media/security-center-enable-encryption-for-storage-account/enable-storage-encryption.png
[3]: ./media/security-center-enable-encryption-for-storage-account/encryption-blade.png
