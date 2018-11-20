---
title: Skriptexempel för Azure CLI – Skapa och skicka ett jobb | Microsoft Docs
description: Azure CLI-skriptet i det här avsnittet visar hur du skickar ett jobb till en enkel kodningstransformering med hjälp av HTTPs-URL.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: 11e148181568f0c550b5eb094055c21c47c00cfc
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615831"
---
# <a name="cli-example-create-and-submit-a-job"></a>CLI-exempel: Skapa och skicka ett jobb

Azure CLI-skriptet i det här avsnittet visar hur du skapar och skickar ett jobb till en enkel kodningstransformering med hjälp av HTTPs-URL.

## <a name="prerequisites"></a>Nödvändiga komponenter 

- Installera och använd CLI lokalt – du måste ha Azure CLI version 2.0 eller senare. Kör `az --version` för att se vilken version du har. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

    För närvarande fungerar inte alla [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref)-kommandon i Azure Cloud Shell. Vi rekommenderar att du använder CLI lokalt.

- [Skapa ett Media Services-konto](../create-account-cli-how-to.md).

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/create-jobs/Create-Jobs.sh "Create and submit jobs")]

## <a name="next-steps"></a>Nästa steg

Fler exempel finns i [Azure CLI-exempel](../cli-samples.md).
