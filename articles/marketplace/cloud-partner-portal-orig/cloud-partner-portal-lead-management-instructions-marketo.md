---
title: Konfigurera leadhantering i Marketo | Azure Marketplace
description: Konfigurera leadhantering för Marketo för Azure marketplace-kunder.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 9fa05eae2d297cbd6ae7243d191cae5a7a3f990e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288537"
---
# <a name="configure-lead-management-in-marketo"></a>Konfigurera leadhantering i Marketo

I den här artikeln beskrivs hur du konfigurerar Marketo för att hantera Microsofts försäljningsleads.

1. Logga in på Marketo.
2. Välj **Design Studio**.
    ![Marketo Design Studio](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Välj **Nytt formulär**.
    ![Marketo ny form](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Fyll i de obligatoriska fälten i det nya formuläret och välj sedan **Skapa**.
    ![Marketo skapa ny form](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  Välj **Slutför**på Fältinformation .
    ![Marketo-slutformulär](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Godkänn och stäng.

6.  Välj **Bädda in kod**på fliken MarketplaceLeadBacked .
    ![Marketo bädda in kod alternativ](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  Marketo Embed Code visar kod som liknar följande exempel.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Kopiera värdena som visas i Inbäddningskoden så att du kan konfigurera **server-ID,** **Munchkin-ID**och **formulär-ID** i fälten Marketo på Cloud Partner Portal.

Använd nästa exempel som en guide för att få de ID:er du behöver från exemplet Marketo Embed Code.

- Server-ID = **ys12**
- Munchkin Id = **123-PQR-789**
- Formulär-ID = **1179**
