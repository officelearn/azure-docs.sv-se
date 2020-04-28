---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 694501fdaaaa92e898f4973838d86343e29144e3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "74895264"
---
Azure Storage krypterar alla data i ett lagrings konto i vila. Som standard krypteras data med Microsoft-hanterade nycklar. Om du vill ha mer kontroll över krypterings nycklar kan du ange Kundhanterade nycklar som ska användas för kryptering av BLOB-och fildata.

Kundhanterade nycklar måste lagras i en Azure Key Vault. Du kan antingen skapa egna nycklar och lagra dem i ett nyckel valv, eller så kan du använda Azure Key Vault API: er för att generera nycklar. Lagrings kontot och nyckel valvet måste finnas i samma region, men de kan finnas i olika prenumerationer. Mer information om Azure Storage kryptering och nyckel hantering finns i [Azure Storage kryptering för data i vila](../articles/storage/common/storage-service-encryption.md). Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../articles/key-vault/key-vault-overview.md)
