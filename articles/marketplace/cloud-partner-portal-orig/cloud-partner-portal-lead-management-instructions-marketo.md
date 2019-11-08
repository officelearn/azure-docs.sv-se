---
title: Konfigurera ledar hantering i Marketo | Azure Marketplace
description: Konfigurera ledar hantering för Marketo för Azure Marketplace-kunder.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 7949507c8c7ef57cded25cde8579c1945aa93a81
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825202"
---
# <a name="configure-lead-management-in-marketo"></a>Konfigurera ledar hantering i Marketo

Den här artikeln beskriver hur du konfigurerar Marketo för att hantera Microsoft Sales-leads.

1. Logga in på Marketo.
2. Välj **Design Studio**.
    ![Marketo Design Studio](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Välj **nytt formulär**.
    ![Marketo New form](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Fyll i de obligatoriska fälten i det nya formuläret och välj sedan **skapa**.
    ![Marketo skapa nytt formulär](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  På fält information väljer du **Slutför**.
    ![Marketo-slutförd form](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Godkänn och Stäng.

6.  På fliken MarketplaceLeadBacked väljer du **bädda in kod**.
    ![Marketo embed-kod alternativ](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  Marketo Inbäddnings kod visar kod som liknar följande exempel.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Kopiera värdena som visas i Inbäddnings kod så att du kan konfigurera **Server-ID**, **Munchkin-ID**och **formulär-ID** i Marketo-fälten på Cloud Partner Portal.

Använd nästa exempel som en guide för att hämta de ID: n du behöver från Marketo embed-kod exemplet.

- Server-ID = **ys12**
- Munchkin-ID = **123-PQR-789**
- Formulär-ID = **1179**\
