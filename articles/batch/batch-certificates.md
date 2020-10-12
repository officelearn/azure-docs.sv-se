---
title: Använda certifikat – Azure Batch
description: Använda certifikat för att aktivera autentisering av program
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 5f7d547ad549b2747ae41182ee4058b001d9e78a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89146393"
---
# <a name="using-certificates-with-batch"></a>Använda certifikat med Batch

För närvarande är det främsta skälet att använda certifikat med batch är om du har program som körs i pooler som behöver autentiseras med en slut punkt. 

Om du inte redan har ett certifikat kan du skapa ett självsignerat certifikat med hjälp av `makecert` kommando rads verktyget.

## <a name="upload-certificates-manually-through-the-azure-portal"></a>Ladda upp certifikat manuellt via Azure Portal

1. Från det batch-konto som du vill överföra ett certifikat till väljer du **certifikat** och väljer sedan **Lägg till**. 

2. Ladda upp certifikatet med fil namns tillägget. pfx eller. cer. 

När certifikatet har laddats upp läggs det till i en lista över certifikat och du kan verifiera tumavtrycket.

Nu när du skapar en batch-pool kan du navigera till certifikat i poolen och tilldela det certifikat som du laddade upp till poolen.

## <a name="next-steps"></a>Nästa steg

Batch har ett certifikat-API, [AZ batch Certificate Create](/cli/azure/batch/certificate)

Information om hur du använder Key Vault finns i [säker åtkomst Key Vault med batch](credential-access-key-vault.md).
