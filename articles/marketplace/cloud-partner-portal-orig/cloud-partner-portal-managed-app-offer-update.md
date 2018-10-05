---
title: Uppdatera ett befintligt erbjudande för Azure Marketplace
description: Uppdatera ett befintligt erbjudande för Azure Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5633392bdf1293ee9196fafe67cf901e0d8c8014
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48811433"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>Uppdatera ett befintligt erbjudande för Azure Marketplace 
==============================================

Det finns olika typer av uppdateringar som du kan göra ditt erbjudande när den lanseras.

1.  Lägger till nya \"paketet\" till en befintlig SKU
2.  Att lägga till nya SKU: er till ett befintligt erbjudande
3.  Uppdaterar erbjudande/SKU marketplace metadata

Du måste uppdatera ditt erbjudande i partnerportalen i molnet och publicera. Den här artikeln beskriver olika aspekter för att uppdatera ditt erbjudande i Azure-program.

<a name="unpermitted-changes-to-azure-application-offersku"></a>Unpermitted ändringar i Azure-program erbjudande/SKU 
--------------------------------------------------

Det finns attribut för en Azure-program för erbjudande/SKU som inte kan ändras när erbjudandet går live i Azure Marketplace.

1.  Erbjudande-ID och Publicerings-ID för erbjudandet.
2.  SKU-ID för befintliga SKU: er.
3.  Uppdatera ett paket som har publicerats.

<a name="adding-a-new-package-to-an-existing-sku"></a>Att lägga till ett nytt paket till en befintlig SKU 
---------------------------------------

Utgivaren kanske vill lägga till en ny version av paketet för att uppdatera ett befintligt paket. Detta kan göras genom att ladda upp ett nytt paket med ett annat versionsnummer.

1.  Logga in på den [partnerportalen i molnet](http://cloudpartner.azure.com)
2.  Hitta erbjudandet som du skulle vilja uppdatera i alla erbjudanden
3.  Klicka på SKU: N på formuläret SKU: er som\'s-paketet som du hade vilja uppdatera
4.  Klicka på \"nytt paket\" och ange en ny version
5.  Ladda upp en ny ZIP-fil som innehåller den uppdaterade mall-filen
6.  Klicka på Publicera inledningsanförande publicera arbetsflödet har ditt nya SKU: N som publiceras.

<a name="adding-a-new-sku-to-an-existing-offer"></a>Att lägga till en ny SKU i ett befintligt erbjudande
-------------------------------------

Du kan välja att göra en ny SKU som är tillgängliga för ditt befintliga erbjudande. Så här aktiverar du det i stegen nedan.

1.  Logga in på den [partnerportalen i molnet](http://cloudpartner.azure.com)
2.  Hitta erbjudandet som du skulle vilja uppdatera i alla erbjudanden
3.  På de SKU: er formuläret clik på Lägg till nya SKU: N och ange en SKU-ID i popup-fönstret.
4.  Följ resten av stegen som anges [här](./cloud-partner-portal-managed-app-publish.md).
5.  Klicka på Publicera inledningsanförande publicera arbetsflödet har ditt nya SKU: N som publiceras.

<a name="updating-offer-marketplace-metadata"></a>Uppdaterar erbjuder Marketplace metadata 
-----------------------------------

Du kan ha scenarier där du behöver uppdatera marketplace metadata som är associerade med ditt erbjudande som uppdatering av din logotyp, osv. Följ stegen nedan.

1.  Logga in på partnerportalen i molnet
2.  Hitta erbjudandet som du skulle vilja uppdatera i alla erbjudanden
3.  Gå till Marketplace formuläret och följ instruktionerna [här](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-push-to-staging) göra några ändringar.
4.  Klicka på Publicera inledningsanförande publicera arbetsflödet till att dina ändringar publiceras.
