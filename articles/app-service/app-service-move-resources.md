---
title: Flytta en Azure-Webbapp till en annan resursgrupp | Microsoft Docs
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
ms.date: 10/19/2017
ms.author: zarizvi
ms.openlocfilehash: 4d5d6540dd0b51a4f172649a1fd9a2343efeceee
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2017
---
# <a name="move-an-azure-web-app-to-another-resource-group"></a>Flytta en Azure-Webbapp till en annan resursgrupp
Du kan flytta en Webbapp och/eller dess relaterade resurser till en annan resursgrupp i samma prenumeration eller till en resursgrupp i en annan prenumeration. Detta görs som en del av standard resurshantering i Azure. Mer information finns i [flytta Azure-resurser till nya prenumerationen eller resursen grupp](../azure-resource-manager/resource-group-move-resources.md).

## <a name="limitations-when-moving-within-the-same-subscription"></a>Begränsningar när du flyttar inom samma prenumeration

När du flyttar en Webbapp _inom samma prenumeration_, du kan inte flytta de överförda SSL-certifikat. Men du kan flytta en Webbapp till den nya resursgruppen utan att flytta det överförda SSL-certifikatet och din app SSL fortfarande fungerar. 

Följ dessa steg om du vill flytta SSL-certifikat med webbprogrammet:

1.  Ta bort det överförda certifikatet från Web App.
2.  Flytta webbprogrammet.
3.  Överför certifikatet till det webbprogram som har flyttats.

## <a name="limitations-when-moving-across-subscriptions"></a>Begränsningar vid flytt mellan prenumerationer

När du flyttar en Webbapp _över prenumerationer_, gäller följande begränsningar:

- Mål resursgruppens namn får inte ha några befintliga resurser i Apptjänst. Apptjänst resurser inkluderar:
    - Web Apps
    - App Service-planer
    - Överförda eller importerade SSL-certifikat
    - Apptjänstmiljöer
- Alla Apptjänst resurser i resursgruppen måste flyttas tillsammans.
- Apptjänst resurser kan bara flyttas från resursgruppen där de skapades. Om en App Service-resursen är inte längre i dess ursprungliga resursgrupp, det måste flyttas tillbaka till den ursprungliga resursgruppen först och sedan den flyttas över prenumerationer. 
