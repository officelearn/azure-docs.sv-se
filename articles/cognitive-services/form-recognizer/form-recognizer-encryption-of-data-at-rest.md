---
title: Formulär igenkännings tjänst kryptering av vilande data
titleSuffix: Azure Cognitive Services
description: Microsoft erbjuder Microsoft-hanterade krypterings nycklar och du kan också hantera dina Cognitive Services prenumerationer med dina egna nycklar, som kallas Kundhanterade nycklar (CMK). Den här artikeln beskriver data kryptering i vila för formulär igenkänning och hur du aktiverar och hanterar CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: c959231826125349ecd6a62afe529248f7ac2eec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326804"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Formulär tolkens kryptering av data i vila

Azure formulär tolken krypterar automatiskt dina data när de behålls i molnet. Formulär igenkännings kryptering skyddar dina data så att du kan uppfylla organisationens säkerhets-och efterlevnads åtaganden.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Kundhanterade nycklar är bara tillgängliga resurser som skapats efter 11 maj 2020. Om du vill använda CMK med formulär tolken måste du skapa en ny formulär igenkännings resurs. När resursen har skapats kan du använda Azure Key Vault för att konfigurera din hanterade identitet.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Nästa steg

* [Formulär för formulär igenkänning Customer-Managed nyckel](https://aka.ms/cogsvc-cmk)
* [Läs mer om Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)