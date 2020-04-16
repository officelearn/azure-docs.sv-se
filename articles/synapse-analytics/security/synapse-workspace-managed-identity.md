---
title: Hanterad identitet i Azure Synapse-arbetsytan
description: En artikel som förklarar hanterad identitet på Azure Synapse-arbetsytan
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ee0e6249acf3fbbab369d42ae691a5a826df1ee8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425121"
---
# <a name="azure-synapse-workspace-managed-identity-preview"></a>Azure Synapse arbetsyta hanterad identitet (förhandsversion)

I den här artikeln får du lära dig mer om hanterad identitet i Azure Synapse-arbetsytan.

## <a name="managed-identities"></a>Hanterade identiteter

Hanterad identitet för Azure-resurser är en funktion i Azure Active Directory. Funktionen förser Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Du kan använda funktionen Hanterad identitet för att autentisera till alla tjänster som stöder Azure AD-autentisering.

Hanterade identiteter för Azure-resurser är det nya namnet för tjänsten som tidigare kallades MSI (Managed Service Identity). Se [Hanterade identiteter om](../../active-directory/managed-identities-azure-resources/overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) du vill veta mer.

## <a name="azure-synapse-workspace-managed-identity"></a>Azure Synapse arbetsyta hanterad identitet

En systemtilldelad hanterad identitet skapas för din Azure Synapse-arbetsyta när du skapar arbetsytan.

>[!NOTE]
>Den här arbetsytehanterade identiteten kallas hanterad identitet via resten av det här dokumentet.

Azure Synapse använder den hanterade identiteten för att dirigera pipelines. Livscykeln för hanterad identitet är direkt knuten till Azure Synapse-arbetsytan. Om du tar bort Azure Synapse-arbetsytan rensas även den hanterade identiteten.

Arbetsytan hanterad identitet behöver behörigheter för att utföra åtgärder i pipelines. Du kan använda objekt-ID eller ditt Azure Synapse-arbetsytenamn för att hitta den hanterade identiteten när du beviljar behörigheter.

## <a name="retrieve-managed-identity-in-azure-portal"></a>Hämta hanterad identitet i Azure-portalen

Du kan hämta den hanterade identiteten i Azure-portalen. Öppna din Azure Synapse-arbetsyta i Azure-portalen och välj **Översikt** från vänster navigering. Den hanterade identitetens objekt-ID visas på huvudskärmen.

![Id för hanterade identitetsobjekt](./media/synapse-workspace-managed-identity/workspace-managed-identity-1.png)

Den hanterade identitetsinformationen visas också när du skapar en länkad tjänst som stöder autentisering av hanterad identitet från Azure Synapse Studio.

Starta **Azure Synapse Studio** och välj fliken **Hantera** från vänster navigering. Välj sedan **Länkade tjänster** och välj alternativet **+ Nytt** för att skapa en ny länkad tjänst.

![Skapande av länkade tjänster 1](./media/synapse-workspace-managed-identity/workspace-managed-identity-2.png)

Skriv Azure Data Lake *Storage Gen2*i fönstret **Nytt länkat tjänst** . Välj resurstypen **Azure Data Lake Storage Gen2** i listan nedan och välj **Fortsätt**.

![Skapande av länkade tjänster 2](./media/synapse-workspace-managed-identity/workspace-managed-identity-3.png)

I nästa fönster väljer du **Metoden Hanterad identitet** för **autentisering**. Du ser den hanterade identitetens **namn** och **objekt-ID**.

![Skapande av länkade tjänster 3](./media/synapse-workspace-managed-identity/workspace-managed-identity-4.png)

## <a name="next-steps"></a>Nästa steg

[Bevilja behörigheter till Azure Synapse-arbetsytans hanterade identitet](./how-to-grant-workspace-managed-identity-permissions.md)
