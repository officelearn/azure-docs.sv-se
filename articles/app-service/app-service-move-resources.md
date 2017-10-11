---
title: Flytta Web App resurser till en annan resursgrupp
description: "Beskriver scenarier där du kan flytta webbprogram och Apptjänster från en resursgrupp till en annan."
services: app-service
documentationcenter: 
author: ZainRizvi
manager: erikre
editor: 
ms.assetid: 22f97607-072e-4d1f-a46f-8d500420c33c
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: zarizvi
ms.openlocfilehash: 1b5059dc052005b6079f70ecf6771a3771df8d87
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="supported-move-configurations"></a>Konfigurationer som stöds flytta
Du kan flytta Azure Web App resurser med hjälp av den [Resource Manager flytta resurser API](../azure-resource-manager/resource-group-move-resources.md).

Azure Web Apps stöder för närvarande följande move-scenarier:

* Flytta hela innehållet i en resursgrupp (webbappar, apptjänstplaner och certifikat) till en annan resursgrupp. 
   > [!Note]
   > Mål resursgruppens namn får inte innehålla några Microsoft.Web resurser i det här scenariot.

* Flytta enskilda webbprogram till en annan resursgrupp fortfarande värd dem i deras aktuella apptjänstplan (apptjänstplan förblir i den gamla resursgruppen).


