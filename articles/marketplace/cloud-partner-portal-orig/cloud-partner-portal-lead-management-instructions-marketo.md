---
title: Marketo | Microsoft Docs
description: Konfigurera lead-hantering för Marketo.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: abb0abb94d3b3e7abc4dce58cdb11fa0c2cedd34
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48811282"
---
# <a name="configure-lead-management-in-marketo"></a>Konfigurera lead-hantering i Marketo

Den här artikeln beskriver hur du ställer in Marketo för att hantera Microsoft säljleads.

1. Logga in till Marketo.
2. Välj **utforma Studio**.
    ![Marketo Design Studio](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Välj **nytt formulär**.
    ![Marketo nytt formulär](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Fyll i de obligatoriska fälten i formuläret för nytt och välj sedan **skapa**.
    ![Marketo Skapa nytt formulär](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  Mer information hur du fältet, Välj **Slutför**.
    ![Marketo Slutför formuläret](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Godkänna och Stäng.

6.  På fliken MarketplaceLeadBacked väljer **inbäddningskoder**.
    ![Marketo bädda in kod alternativet](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  Bädda in kod i Marketo visar koden som liknar följande exempel.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

8.  Kopiera värdena som visas i bädda in kod så att du kan konfigurera den **Server-Id**, **Munchkin Id**, och **formulär-Id** i Marketo-fälten på partnerportalen i molnet.

Använd nästa exempel som en vägledning för att hämta ID: N som du behöver från kodexemplet för att bädda in Marketo.

- Server-Id = **ys12**
- Munchkin Id = **123-PQR-789**
- Formuläret Id = **1179**\
