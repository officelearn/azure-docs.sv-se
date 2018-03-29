---
title: Aktivera kryptering för storage-konto i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskrivs hur du implementerar Azure Security Center-rekommendationerna **Aktivera kryptering för Azure Storage-konto**.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2016
ms.author: terrylan
ms.openlocfilehash: 82bb201c0b518d0b45e06a1eb25d54f60cb3e028
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="enable-encryption-for-azure-storage-account-in-azure-security-center"></a>Aktivera kryptering för Azure storage-konto i Azure Security Center
Azure Security Center kan rekommenderar att du aktiverar Azure Storage Service-kryptering för data i vila.

Kryptering för lagring-tjänsten (SSE) fungerar genom att kryptera data när de skrivs till Azure-lagring och dekryptera data innan hämtning.  SSE är endast tillgänglig för Azure Blob-tjänsten och kan användas för blockblobbar, sidblobbar, och tilläggsblobar.  Läs mer i [Lagringstjänstens kryptering av vilande data](../storage/common/storage-service-encryption.md).


> [!Note]
> När du har aktiverat kryptering krypteras endast nya data. Alla befintliga blobbar i ditt lagringskonto vara okrypterad. Om du vill kryptera befintliga blobbar finns i [vanliga frågor och svar Storage Service-kryptering](../storage/common/storage-service-encryption.md#faq-for-storage-service-encryption).
>
>

Lagringstjänstens kryptering stöds endast i hanteraren för filserverresurser storage-konton. Klassiska lagringskonton stöds inte för närvarande. Klassiskt och Resource Manager distributionsmodellerna finns [Azure distributionsmodeller](../azure-classic-rm.md).

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det här dokumentet är inte en stegvis guide.
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. I den **rekommendationer** bladet väljer **Aktivera kryptering för Azure Storage-konto**.
   ![Aktivera kryptering för lagringskonto][1]
2. Den **aktivera lagringskryptering** blad öppnas. Det här bladet visar Azure storage-konton där storage kryptering har inaktiverats. I det här exemplet ska vi väljer **storageacct1**.
   ![Aktivera lagringskryptering][2]
3. Den **kryptering** bladet för **storageacct1** öppnas. Välj **aktiverat**.
   ![Kryptering bladet][3]
4. Välj **Spara**.

Du har nu aktiverats lagringskryptering för **storageacct1**.


## <a name="see-also"></a>Se också
Det här dokumentet visar dig hur du implementerar Security Center-rekommendationen ”Aktivera kryptering för Azure Storage-konto”. Om du vill veta mer om Azure Storage Service-kryptering, finns i följande avsnitt:

* [Azure Storage Service-kryptering av vilande Data](../storage/common/storage-service-encryption.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Lär dig hur du ställer in säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig rekommendationer för att skydda dina Azure-resurser.
* [Vanliga frågor om Azure Security Center](security-center-faq.md) -finns vanliga frågor om hur du använder tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) -hittar du blogginlägg om säkerhet och Azure kompatibilitet.

<!--Image references-->
[1]: ./media/security-center-enable-encryption-for-storage-account/enable-encryption-for-storage-account.png
[2]: ./media/security-center-enable-encryption-for-storage-account/enable-storage-encryption.png
[3]: ./media/security-center-enable-encryption-for-storage-account/encryption-blade.png
