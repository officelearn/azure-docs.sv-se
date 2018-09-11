---
title: Key Vault-versioner
description: De olika versionerna av Azure Key Vault
services: key-vault
documentationcenter: ''
author: bryanla
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e8622dcc-59a3-4f4b-9f63-cd2232515a65
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: bryanla
ms.openlocfilehash: 0db090d33e165415a2e1c3a4f095a0d0a48138cb
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296661"
---
# <a name="key-vault-versions"></a>Key Vault-versioner

## <a name="2016-10-01---managed-storage-account-keys"></a>2016-10-01 - hanterade nycklar för Lagringskonton

Sommar 2017 – Lagringskontonycklar funktionen har lagts till enklare integrering med Azure Storage. Se översiktsavsnittet om du vill veta mer [översikt över hanterade Lagringskontonycklar](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).

## <a name="2016-10-01---soft-delete"></a>2016-10-01-mjuk borttagning

Sommar 2017 - funktionen för mjuk borttagning har lagts till för bättre dataskydd av ditt nyckelvalv och nyckelvalvet objekt. Se översiktsavsnittet om du vill veta mer [översikt över mjuk borttagning](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015-06-01---certificate-management"></a>2015-06-01 - certifikathantering

Certifikathantering läggs till som en funktion till GA-version 2015-06-01 26 September 2016.

## <a name="2015-06-01---general-availability"></a>2015-06-01 - Allmänt

Allmänt tillgänglig version 2015-06-01, visats på 24 juni 2015.

Följande ändringar har gjorts på den här versionen:

- Ta bort en nyckel – ”använda”-fältet tas bort.
- Få information om en nyckel - ”använda” fältet tas bort.
- Importera en nyckel till ett valv – ”använda”-fältet tas bort.
- Återställa en nyckel – ”använda”-fältet tas bort.
- Ändrade ”RSA_OAEP” till ”RSA-OAEP” för RSA-algoritmer. Se [om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md).

## <a name="2015-02-01-preview"></a>2015-02-01-preview 

Andra preview version 2015-02-01-preview, meddelade den 20 April 2015. Mer information finns i [REST API-uppdatering](http://blogs.technet.com/b/kv/archive/2015/04/20/empty-3.aspx) blogginlägg.

Följande uppgifter har uppdaterats:

- Lista över nycklar i ett valv - har lagts till sidbrytning stöd för åtgärden.
- Lista över versionerna av en nyckel - har lagts till igen om du vill visa versionerna av en nyckel.
- Lista över hemligheterna i ett valv - stöd har lagts till sidbrytning.
- Lista över versionerna av en hemlighet – lägga till åtgärden om du vill visa versionerna av en hemlighet.
- Alla åtgärder – lagt till tidsstämplar som är skapade/uppdarerade attribut.
- Skapa en hemlighet - läggs Content-Type till hemligheter.
- Skapa en nyckel - lagt till taggar som valfri information.
- Skapa en hemlighet - lagt till taggar som valfri information.
- Uppdatera en nyckel - lagt till taggar som valfri information.
- Uppdatera en hemlighet - lagt till taggar som valfri information.
- Ändra maxstorleken för hemligheter från 10 K till 25 kB. Se, [om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md).

## <a name="2014-12-08-preview"></a>2014-12-08-förhandsversion

Första preview version 2014-12-08-preview, meddelat den 8 januari 2015.

## <a name="see-also"></a>Se också
- [Om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
