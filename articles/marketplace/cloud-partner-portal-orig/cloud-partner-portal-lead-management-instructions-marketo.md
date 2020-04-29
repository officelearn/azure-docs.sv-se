---
title: Konfigurera ledar hantering i Marketo | Azure Marketplace
description: Konfigurera ledar hantering för Marketo för Azure Marketplace-kunder.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 9fa05eae2d297cbd6ae7243d191cae5a7a3f990e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80288537"
---
# <a name="configure-lead-management-in-marketo"></a>Konfigurera ledar hantering i Marketo

Den här artikeln beskriver hur du konfigurerar Marketo för att hantera Microsoft Sales-leads.

1. Logga in på Marketo.
2. Välj **Design Studio**.
    ![Marketo Design Studio](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Välj **nytt formulär**.
    ![Marketo-nytt formulär](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Fyll i de obligatoriska fälten i det nya formuläret och välj sedan **skapa**.
    ![Skapa nytt formulär på marknaden](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  På fält information väljer du **Slutför**.
    ![Slutför formulär för Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Godkänn och Stäng.

6.  På fliken MarketplaceLeadBacked väljer du **bädda in kod**.
    ![Marketo inbäddnings kod alternativ](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  Marketo Inbäddnings kod visar kod som liknar följande exempel.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Kopiera värdena som visas i Inbäddnings kod så att du kan konfigurera **Server-ID**, **Munchkin-ID**och **formulär-ID** i Marketo-fälten på Cloud Partner Portal.

Använd nästa exempel som en guide för att hämta de ID: n du behöver från Marketo embed-kod exemplet.

- Server-ID = **ys12**
- Munchkin-ID = **123-PQR-789**
- Formulär-ID = **1179**
