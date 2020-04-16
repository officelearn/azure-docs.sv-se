---
title: Så här beviljar du behörigheter till hanterad identitet på Azure Synapse-arbetsytan
description: En artikel som förklarar hur du konfigurerar behörigheter för hanterad identitet i Azure Synapse-arbetsytan.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9f519022fffe98c565c3b2d30f6578b9ebb70c57
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428022"
---
# <a name="grant-permissions-to-workspace-managed-identity-preview"></a>Bevilja behörigheter till arbetsytans hanterade identitet (förhandsgranskning)

I den här artikeln får du lära dig hur du beviljar behörigheter till den hanterade identiteten i Azure-arbetsytan. Behörigheter tillåter i sin tur åtkomst till SQL-pooler i arbetsytan och ADLS gen2-lagringskontot via Azure-portalen.

>[!NOTE]
>Den här arbetsytehanterade identiteten kallas hanterad identitet via resten av det här dokumentet.

## <a name="grant-the-managed-identity--permissions-to-the-sql-pool"></a>Bevilja hanterade identitetsbehörigheter till SQL-poolen

Den hanterade identiteten ger behörigheter till SQL-poolerna på arbetsytan. Med beviljade behörigheter kan du dirigera pipelines som utför SQL-poolrelaterade aktiviteter. När du skapar en Azure Synapse-arbetsyta med Azure-portalen kan du bevilja behörigheter för hanterad identitetskontroll för SQL-pooler.

Välj **Säkerhet + nätverk** när du skapar din Azure Synapse-arbetsyta. Välj sedan **Bevilja KONTROLL till arbetsytans hanterade identitet i SQL-pooler**.

![BEHÖRIGHET FÖR KONTROLL för SQL-pooler](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>Bevilja hanterade identitetsbehörigheter till ADLS gen2-lagringskonto

Ett ADLS gen2-lagringskonto krävs för att skapa en Azure Synapse-arbetsyta. För att kunna starta Spark-pooler i Azure Synapse-arbetsytan behöver azure Synapse-hanterade identitet rollen *Storage Blob Data Contributor* på det här lagringskontot . Pipeline-orkestrering i Azure Synapse drar också nytta av den här rollen.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>Bevilja behörigheter till hanterad identitet när arbetsytan skapas

Azure Synapse försöker bevilja rollen Storage Blob Data Contributor till den hanterade identiteten när du har skapat Azure Synapse-arbetsytan med Azure-portalen. Du anger adls gen2-lagringskontoinformation på fliken **Grunderna.**

![Fliken Grunderna i flödet för att skapa arbetsytor](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

Välj ADLS gen2-lagringskonto och filsystem i **Kontonamn** och **Filsystemnamn**.

![Tillhandahålla en ADLS gen2-lagringskontoinformation](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

Om arbetsytans skapare också är **ägare** till ADLS gen2-lagringskontot, kommer Azure Synapse att tilldela rollen *Storage Blob Data Contributor* till den hanterade identiteten. Följande meddelande visas under information om lagringskontot som du angav.

![Tilldelning av storage blob-datadeltagare](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

Om arbetsytans skapare inte är ägare till ADLS gen2-lagringskontot tilldelar Azure Synapse inte rollen *Storage Blob Data Contributor* till den hanterade identiteten. Meddelandet som visas under lagringskontoinformationen meddelar arbetsytans skapare att de inte har tillräcklig behörighet för att ge rollen *Storage Blob Data Contributor* till den hanterade identiteten.

![Tilldelning av misslyckade storage blob-databe deltagare](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

Som meddelandet anger kan du inte skapa Spark-pooler om inte *Storage Blob Data Contributor* har tilldelats den hanterade identiteten.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>Bevilja behörigheter till hanterad identitet när arbetsytan har skapats

Om du inte tilldelar *den storage blob-datadeltagaren* till den hanterade identiteten under arbetsytan tilldelar **adls** gen2-lagringskontot rollen manuellt den rollen till identiteten. Följande steg hjälper dig att utföra manuell tilldelning.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>Steg 1: Navigera till ADLS gen2-lagringskontot i Azure-portalen

Öppna ADLS gen2-lagringskontot i Azure-portalen och välj **Översikt** från den vänstra navigeringen. Du behöver bara tilldela rollen *Storage Blob Data Contributor* på behållar- eller filsystemnivå. Välj **Behållare**.  
![Översikt över ADLS gen2-lagringskonto](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>Steg 2: Välj behållare

Den hanterade identiteten bör ha dataåtkomst till behållaren (filsystemet) som angavs när arbetsytan skapades. Du hittar den här behållaren eller filsystemet i Azure-portalen. Öppna Azure Synapse-arbetsytan i Azure-portalen och välj fliken **Översikt** från den vänstra navigeringen.
![ADLS gen2-lagringskontobehållare](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


Markera samma behållare eller filsystem om du vill bevilja rollen *Storage Blob Data Contributor* till den hanterade identiteten.
![Adls gen2 lagringskonto behållare val](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>Steg 3: Navigera till åtkomstkontrollen

Välj **Åtkomstkontroll (IAM)**.

![Åtkomstkontroll(IAM)](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>Steg 4: Lägga till en ny rolltilldelning

Välj **+ Lägg till**.

![Lägg till ny rolltilldelning](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-rbac-role"></a>Steg 5: Välj RBAC-rollen

Välj rollen **Storage Blob Data Contributor.**

![Välj RBAC-rollen](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>Steg 6: Välj säkerhetsobjektet För Azure AD

Välj **Azure AD-användare, grupp eller tjänsthuvudnamn** i **listrutan Tilldela åtkomst till.**

![Välj AAD-säkerhetsobjekt](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>Steg 7: Sök efter den hanterade identiteten

Den hanterade identitetens namn är också arbetsytans namn. Sök efter din hanterade identitet genom att ange azure Synapse-arbetsytenamn i **Välj**. Du bör se den hanterade identiteten i listan.

![Hitta den hanterade identiteten](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>Steg 8: Välj den hanterade identiteten

Välj den hanterade identiteten till de **markerade medlemmarna**. Välj **Spara** om du vill lägga till rolltilldelningen.

![Välj den hanterade identiteten](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>Steg 9: Kontrollera att rollen Storage Blob Data Contributor har tilldelats den hanterade identiteten

Välj **Åtkomstkontroll(IAM)** och välj sedan **Rolltilldelningar**.

![Verifiera rolltilldelning](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

Du bör se din hanterade identitet som anges under avsnittet **Storage Blob Data Contributor** med rollen Storage *Blob Data Contributor* tilldelad den. 
![Adls gen2 lagringskonto behållare val](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om [arbetsytehanterad identitet](./synapse-workspace-managed-identity.md)
