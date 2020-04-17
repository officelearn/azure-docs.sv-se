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
ms.openlocfilehash: cd7b889560acbe484581f065b641375c222f7ca8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536510"
---
Som standard filresurser kan endast sträcka sig över upp till 5 TiB, även om delningsgränsen kan höjas till 100 TiB. För att göra detta måste *funktionen för stor filresurs* vara aktiverad på lagringskontonivå. Premium lagringskonton *(FileStorage* lagringskonton) har inte den stora filresursfunktionen flaggan som alla premiumfilresurser redan är aktiverade för etablering upp till hela 100 TiB kapacitet.

Du kan bara aktivera stora filresurser på lokalt redundanta eller zon redundanta standardlagringskonton. När du har aktiverat den stora filresursfunktionsflaggan kan du inte ändra redundansnivån till geo-redundant eller geo-zon-redundant lagring.

Om du vill aktivera stora filresurser på ett befintligt lagringskonto navigerar du till **konfigurationsvyn** i lagringskontots innehållsförteckning och växlar den stora fildelningsvippern till aktiverad:

![En skärmbild av aktivera stor fildelningsvippel i Azure-portalen](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

Du kan också aktivera 100 TiB-filresurser via [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) PowerShell-cmdlet och Azure CLI-kommandot. Detaljerade instruktioner om hur du aktiverar stora filresurser finns i [aktivera och skapa stora filresurser](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Mer information om hur du skapar filresurser på nya lagringskonton finns i [skapa en Azure-filresurs](../articles/storage/files/storage-how-to-create-file-share.md).
