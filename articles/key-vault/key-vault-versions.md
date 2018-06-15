---
title: Key Vault-versioner
description: Olika versioner av Azure Key Vault
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e8622dcc-59a3-4f4b-9f63-cd2232515a65
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: beb73be66f36ccf95fe27d4d8128106cd12722a8
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012202"
---
# <a name="key-vault-versions"></a>Key Vault-versioner

## <a name="2016-10-01---managed-storage-account-keys"></a>2016-10-01 - hanterade Lagringskontonycklar

Sommar 2017 - Lagringskontonycklar funktionen lägga till enkel integrering med Azure Storage. Översiktsavsnittet för mer information finns i avsnittet [hanteras Lagringskontonycklar översikt](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).

## <a name="2016-10-01---soft-delete"></a>2016-10-01-mjuk borttagning

Sommar 2017 - funktionen för mjuk borttagning lagts till för förbättrad dataskydd nyckelvalv och nyckelvalv objekt. Översiktsavsnittet för mer information finns i avsnittet [mjuk borttagning översikt](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015-06-01---certificate-management"></a>2015-06-01 - certifikathantering

Certifikathantering läggs till som en funktion till GA-version 2015-06-01 26 September 2016.

## <a name="2015-06-01---general-availability"></a>2015-06-01 - allmän tillgänglighet

Allmän tillgänglighet version 2015-06-01 tillkännages i 24 juni 2015.

Följande ändringar har gjorts på den här versionen:

- Ta bort en nyckel, ”Använd” fältet tas bort.
- Hämta information om en nyckel - ”Använd” fältet tas bort.
- Importera en nyckel till ett valv, ”Använd” fältet tas bort.
- Återställa en nyckel, ”Använd” fältet tas bort.
- Ändrade ”RSA_OAEP” till ”RSA-OAEP” för RSA-algoritmer. Se [om nycklar och hemligheter certifikat](about-keys-secrets-and-certificates.md).

## <a name="2015-02-01-preview"></a>2015-02-01-preview 

Andra preview version 2015-02-01-preview, tillkännages i 20 April 2015. Mer information finns i [REST API-uppdatering](http://blogs.technet.com/b/kv/archive/2015/04/20/empty-3.aspx) blogginlägg.

Följande uppgifter har uppdaterats:

- Lista över nycklar i ett valv - tillagda sidbrytning stöd för åtgärden.
- Visa en lista med versionerna av en nyckel - lägga till åtgärden för att visa en lista med versionerna av en nyckel.
- Lista över hemligheter i ett valv - tillagda sidbrytning support.
- Visa en lista med versioner av en hemlighet – Lägg till åtgärden för att visa en lista med versionerna av en hemlighet.
- Alla åtgärder - lägga skapa/uppdatera tidsstämplar till attribut.
- Skapa en hemlighet - lägga till Content-Type hemligheter.
- Skapa en nyckel - läggs till taggar som valfri information.
- Skapa en hemlighet - läggs till taggar som valfri information.
- Uppdatera en nyckel - läggs till taggar som valfri information.
- Uppdatera en hemlighet - läggs till taggar som valfri information.
- Ändra maxstorlek för hemligheter från 10 K till 25 kB. Se, [om nycklar och hemligheter certifikat](about-keys-secrets-and-certificates.md).

## <a name="2014-12-08-preview"></a>2014-12-08-preview

Första version 2014-12-08-förhandsgranskning, tillkännages i 8 januari 2015.

## <a name="see-also"></a>Se också
- [Om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
