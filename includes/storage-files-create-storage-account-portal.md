---
title: storage-files-create-storage-account-portal
description: Skapa ett lagringskonto för Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 03/28/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 82e141eb4941dbd58be95e19dae186acd18caf94
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
Ett lagringskonto är en delad lagringspool i vilken du kan distribuera en Azure-filresurs eller andra lagringsresurser, t.ex. blobar eller köer. Ett lagringskonto kan innehålla ett obegränsat antal resurser och en resurs kan lagra ett obegränsat antal filer, upp till lagringskontots kapacitetsgräns.

Skapa ett lagringskonto:

1. Skapa en resurs genom att klicka på **+** på den vänstra menyn.
2. Skriv **lagringskonto** i sökrutan (1) och välj **Lagringskonto – blob, fil, tabell, kö** (2) och klicka sedan på **Skapa**.
    ![En skärmbild av hur lagringskontoposten bör se ut i dialogrutan för resurssökning](../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png)

3. Skriv *mystorageacct* i **Namn**, följt av några slumptal fram till det att en grön bockmarkering indikerar att det är ett unikt namn. Ett lagringskontonamn får bara bestå av gemener och måste vara globalt unikt. Skriv ned namnet på ditt lagringskonto eftersom du kommer att använda det senare. 
4. Låt standardvärdet **Resource manager** vara i **Distributionsmodell**. Mer information om skillnaderna mellan Azure Resource Manager och klassisk distribution finns i [Distributionsmodeller och dina resursers tillstånd](../articles/azure-resource-manager/resource-manager-deployment-model.md).
5. Välj **StorageV2** i **Kontotyp**. Mer information om de olika typerna av lagringskonton finns i [Azure-lagringskonton](../articles/storage/common/storage-account-options.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
6. Behåll standardvärdet för **Standardlagring** i **Prestanda**. Azure Files stöder för närvarande endast standardlagring. Även om du väljer premiumlagring så lagras din filresurs med standardlagring.
7. Välj *lokalt redundant lagring (LRS)* i **Replikering**. 
8. Vi rekommenderar att du alltid väljer **Aktiverad** i **Säker överföring krävs**. Mer information om det här alternativet finns i [Kryptering under överföring](../articles/storage/common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
9. Välj den prenumeration där du vill skapa nätverket i **Prenumeration**. Om du bara har en prenumeration bör den vara standard.
10. Välj **Skapa ny** i **Resursgrupp** och skriv *myResourceGroup* som namn.
11. Välj **USA, östra** i **Plats**.
12. Lämna standardalternativet som *Inaktiverat* i **Virtuella nätverk**. 
13. Välj **Fäst på instrumentpanelen** så att det blir lättare att hitta lagringskontot.
14. När du är klar startar du distributionen genom att klicka på **Skapa**.