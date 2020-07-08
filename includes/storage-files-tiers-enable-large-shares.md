---
title: inkludera fil
description: inkludera fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cd7b889560acbe484581f065b641375c222f7ca8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "81536510"
---
Som standard kan standard fil resurser bara omfatta upp till 5 TiB, även om resurs gränsen kan ökas till 100 TiB. För att göra detta måste funktionen *stor fil resurs* vara aktive rad på lagrings konto nivå. Premium Storage-konton (*FileStorage* lagrings konton) har inte den stora fil resursens funktions flagga eftersom alla Premium-filresurser redan har Aktiver ATS för etablering upp till den fullständiga 100 TIB-kapaciteten.

Du kan bara aktivera stora fil resurser på lokalt redundanta eller redundanta standard lagrings konton. När du har aktiverat funktions flaggan stor fil resurs kan du inte ändra redundans nivån till Geo-redundant eller geo-Zone-redundant lagring.

Om du vill aktivera stora fil resurser på ett befintligt lagrings konto navigerar du till vyn **konfiguration** i lagrings kontots innehålls förteckning och byter ut den stora fil resursens rockare till aktive rad:

![En skärm bild av växeln aktivera stor fil resurs Rock i Azure Portal](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

Du kan också aktivera 100 TiB fil resurser via [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) PowerShell-cmdleten och [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) Azure CLI-kommandot. Detaljerade anvisningar om hur du aktiverar stora fil resurser finns i [Aktivera och skapa stora fil resurser](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Mer information om hur du skapar fil resurser på nya lagrings konton finns i [skapa en Azure-filresurs](../articles/storage/files/storage-how-to-create-file-share.md).
