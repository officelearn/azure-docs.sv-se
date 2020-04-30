---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 39a013f5f4b587137366147ade77f0be1b353c4c
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204562"
---
- Det går för närvarande inte att flytta stegvisa ögonblicks bilder mellan prenumerationer.
- För närvarande kan du bara skapa SAS-URI: er på upp till fem ögonblicks bilder av en viss ögonblicks bild serie vid en given tidpunkt.
- Du kan inte skapa en stegvis ögonblicks bild för en viss disk utanför diskens prenumeration.
- Upp till sju stegvisa ögonblicks bilder per disk kan skapas var femte minut.
- Totalt 200 stegvisa ögonblicks bilder kan skapas för en enskild disk.
- Du kan inte hämta ändringarna mellan ögonblicks bilder som tagits innan och efter ändringen av storleken på den överordnade disken över 4 TB-gränser. Du måste återigen Ladda ned den fullständiga kopian av ögonblicks bilden som skapats efter storleken. Därefter kan du hämta ändringarna mellan ögonblicks bilderna som skapats efter storleks ändringen på 4 TB. 
