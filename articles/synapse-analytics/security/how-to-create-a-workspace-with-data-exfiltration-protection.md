---
title: Skapa en arbets yta med data exfiltrering Protection aktiverat
description: Den här artikeln beskriver hur du skapar en arbets yta med data exfiltrering Protection i Azure Synapse Analytics
author: NanditaV
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 8f11f104e8d3d0a4f3a004ebe9f149cb4132d910
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501493"
---
# <a name="create-a-workspace-with-data-exfiltration-protection-enabled"></a>Skapa en arbets yta med data exfiltrering Protection aktiverat
Den här artikeln beskriver hur du skapar en arbets yta med data exfiltrering Protection aktiverat och hur du hanterar godkända Azure AD-klienter för den här arbets ytan.

>[!Note]
>Du kan inte ändra konfigurationen för arbets ytan för hanterat virtuellt nätverk och data exfiltrering-skydd när arbets ytan har skapats.

## <a name="prerequisites"></a>Förutsättningar
- Behörigheter för att skapa en resurs för arbets ytor i Azure.
- Synapse-arbetsytans behörigheter för att skapa hanterade privata slut punkter.
- Prenumerationer registrerade för nätverks resurs leverantören. [Läs mer.](../../azure-resource-manager/management/resource-providers-and-types.md)

Följ anvisningarna i [snabb start: skapa en Synapse-arbetsyta](../quickstart-create-workspace.md) för att komma igång med att skapa din arbets yta. Innan du skapar din arbets yta använder du informationen nedan för att lägga till data exfiltrering skydd på din arbets yta.

## <a name="add-data-exfiltration-protection-when-creating-your-workspace"></a>Lägg till data exfiltrering-skydd när du skapar din arbets yta
1. På fliken nätverk markerar du kryss rutan "Aktivera hanterat virtuellt nätverk".
1. Välj "Ja" för alternativet Tillåt endast utgående data trafik till godkända mål.
1. Välj de godkända Azure AD-klienterna för den här arbets ytan.
1. Granska konfigurationen och skapa arbets ytan.
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-creation-data-exfiltration-protection.png" alt-text="Skapa en arbets yta med data exfiltrering Protection":::

## <a name="manage-approved-azure-active-directory-tenants-for-the-workspace"></a>Hantera godkända Azure Active Directory-klienter för arbets ytan
1. Från arbets ytans Azure Portal går du till "godkända Azure AD-klienter". Listan över godkända Azure AD-klienter för arbets ytan visas här. Arbets ytans klient ingår som standard och visas inte i listan.
1. Använd "+ Lägg till" för att inkludera nya klienter i listan över godkända.
1. Om du vill ta bort en Azure AD-klient från listan över godkända väljer du klienten och väljer sedan "ta bort" och sedan "Spara".
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-manage-aad-tenant-list.png" alt-text="Skapa en arbets yta med data exfiltrering Protection":::


## <a name="connecting-to-azure-resources-in-approved-azure-ad-tenants"></a>Ansluta till Azure-resurser i godkända Azure AD-klienter

Du kan skapa hanterade privata slut punkter för att ansluta till Azure-resurser som finns i Azure AD-klienter, som är godkända för en arbets yta. Följ stegen som visas i guiden för att [skapa hanterade privata slut punkter](./how-to-create-managed-private-endpoints.md).

>[!IMPORTANT]
>Resurser i andra klienter än arbets ytans klient organisation måste inte ha blockerade brand Väggs regler på plats för att SQL-poolerna ska kunna ansluta till dem. Resurser inom arbets ytans hanterade virtuella nätverk, till exempel Spark-kluster, kan ansluta via hanterade privata länkar till brand Väggs skyddade resurser.

## <a name="next-steps"></a>Nästa steg

Läs mer om [data exfiltrering Protection i Synapse-arbetsytor](./workspace-data-exfiltration-protection.md)

Läs mer om [hanterad arbets yta Virtual Network](./synapse-workspace-managed-vnet.md)

Läs mer om [hanterade privata slut punkter](./synapse-workspace-managed-private-endpoints.md)

[Skapa hanterade privata slut punkter till dina data källor](./how-to-create-managed-private-endpoints.md)
