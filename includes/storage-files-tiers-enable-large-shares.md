---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8a2e5defd0672516d52d4f3477641f39eca63368
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597848"
---
Som standard filresurser kan endast sträcka sig över upp till 5 TiB, även om delningsgränsen kan höjas till 100 TiB. För att göra detta måste *funktionen för stor filresurs* vara aktiverad på lagringskontonivå. Premium lagringskonton *(FileStorage* lagringskonton) har inte den stora filresursfunktionen flaggan som alla premiumfilresurser redan är aktiverade för etablering upp till hela 100 TiB kapacitet.

Du kan bara aktivera stora filresurser på lokalt redundanta eller zon redundanta standardlagringskonton. När du har aktiverat den stora filresursfunktionsflaggan kan du inte ändra redundansnivån till geo-redundant eller geo-zon-redundant lagring.

Om du vill aktivera stora filresurser på ett befintligt lagringskonto navigerar du till **konfigurationsvyn** i lagringskontots innehållsförteckning och växlar den stora fildelningsvippern till aktiverad:

![En skärmbild av aktivera stor fildelningsvippel i Azure-portalen](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

Du kan också aktivera 100 TiB-filresurser via [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) PowerShell-cmdlet och Azure CLI-kommandot.

Mer information om hur du aktiverar stora filresurser på nya lagringskonton finns i [skapa en Azure-filresurs](../articles/storage/files/storage-how-to-create-file-share.md).