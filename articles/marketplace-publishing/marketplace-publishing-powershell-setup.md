---
title: Konfigurera PowerShell för att skapa en virtuell dator på Marketplace | Microsoft Docs
description: Instruktioner för att installera Azure PowerShell och använda det som en valfri process flow för att skapa VM-avbildningar för distribution till och sälja på Azure Marketplace
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: e19d6cda-76df-4e42-be84-c9fe47a636db
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2016
ms.author: mbaldwin
ms.openlocfilehash: 72ee1ac612fc4c7191718009a78f55272f0a44cf
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
ms.locfileid: "29937407"
---
# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Konfigurera Azure PowerShell för att skapa ett erbjudande för Azure Marketplace
Detaljerad information om hur du ställer in PowerShell i Azure finns [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview). En enkel metod är att använda metoden certifikat, som hämtar och importerar ett certifikat som krävs för autentisering. Hämta nödvändiga certifikat med den **Get-AzurePublishSettingsFile** cmdlet. När du uppmanas att spara filen. Om du vill importera certifikatet till en PowerShell-session, Använd den **importera AzurePublishSettingsFile** cmdlet.

Konfigurera och lagra vanliga inställningar för Microsoft Azure-prenumeration för PowerShell-session genom att använda den **Set AzureSubscription** och **Välj AzureSubscription** cmdlets:

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

Det första kommandot associerar standardkontot för lagring med prenumerationen (behövs för vissa etablering VM-åtgärder).  Andra blir prenumerationen det aktuella (som identifieras av andra cmdlet: ar).

## <a name="see-also"></a>Se också
* [Komma igång: hur du publicerar ett erbjudande på Azure Marketplace](marketplace-publishing-getting-started.md)
* [Skapa en avbildning av virtuell dator på Marketplace](marketplace-publishing-vm-image-creation.md)

