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
ms.openlocfilehash: 2bfa10441cf5531c9383527a21b033da26322b34
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54073246"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>Uppdatera ett befintligt erbjudande för Azure Marketplace 
==============================================

Det finns olika typer av uppdateringar som du kan göra ditt erbjudande när det lanseras, till exempel:

1.  Lägger till nya \"paketet\" till en befintlig SKU
2.  Att lägga till nya SKU: er till ett befintligt erbjudande
3.  Uppdaterar erbjudande/SKU marketplace metadata

Du kommer använda Cloud Partner Portal för att implementera dessa ändringar och sedan publicera ditt erbjudande. Den här artikeln vägleder dig genom de olika delarna för att uppdatera ditt erbjudande i Azure-program.

<a name="unpermitted-changes-to-azure-application-offer"></a>Unpermitted ändringar i erbjudandet för Azure-program 
-----------------------------------------------

Det finns attribut för en Azure-program för erbjudande/SKU som inte kan ändras när erbjudandet går live i Azure Marketplace.

* Erbjudande-ID och Publicerings-ID för erbjudandet.
* SKU-ID för befintliga SKU: er.
* Uppdatera ett paket som har publicerats.

<a name="adding-a-new-package-to-an-existing-sku"></a>Att lägga till ett nytt paket till en befintlig SKU 
---------------------------------------

Utgivaren kanske vill lägga till en ny version av paketet för att uppdatera ett befintligt paket. Det här tillägget kan göras genom att ladda upp ett nytt paket med ett annat versionsnummer.

1.  Logga in på den [partnerportalen i molnet](http://cloudpartner.azure.com)
2.  I **alla erbjudanden**, erbjudandet som du vill uppdatera
3.  På den **SKU: er** klickar du på på SKU som\'s-paketet som du hade vilja uppdatera
4.  Klicka på \"nytt paket\" och ange en ny version
5.  Ladda upp en ny ZIP-fil som innehåller den uppdaterade mall-filen
6.  Klicka på Publicera inledningsanförande publicera arbetsflödet har ditt nya SKU: N som publiceras.

<a name="adding-a-new-sku-to-an-existing-offer"></a>Att lägga till en ny SKU i ett befintligt erbjudande
-------------------------------------

Du kan välja att göra en ny SKU som är tillgängliga för ditt befintliga erbjudande via följande steg:

1.  Logga in på den [partnerportalen i molnet](http://cloudpartner.azure.com)
2.  I **alla erbjudanden**, erbjudandet som du vill uppdatera
3.  På den **SKU: er** formuläret, klicka på Lägg till nya SKU: N och ange en SKU-ID i popup-fönstret.
4.  Följ resten av stegen som anges [här](./cloud-partner-portal-managed-app-publish.md).
5.  Klicka på **publicera** starta publicera arbetsflödet om du vill att din nya SKU: N som publiceras.

<a name="updating-offer-marketplace-metadata"></a>Uppdaterar erbjuder Marketplace metadata 
-----------------------------------

Du kan ha scenarier där du behöver uppdatera marketplace metadata som är associerade med ditt erbjudande som uppdatering av din logotyp, osv. Följ stegen nedan.

1.  Logga in på partnerportalen i molnet.
2.  I **alla erbjudanden**, erbjudandet som du vill uppdatera
3.  Gå till Marketplace formuläret och följ instruktionerna [här](../cloud-partner-portal/azure-applications/cpp-marketplace-tab.md) göra några ändringar.
4.  Klicka på Publicera inledningsanförande publicera arbetsflödet till att dina ändringar publiceras.
