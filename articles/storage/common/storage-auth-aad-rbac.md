---
title: Använda Azure-portalen för att hantera Azure AD-åtkomstbehörigheter till behållare och köer med RBAC - Azure Storage | Microsoft Docs
description: Använda rollbaserad åtkomstkontroll (RBAC från Azure-portalen) för att tilldela åtkomst till behållare och köer till säkerhetsobjekt. Azure Storage stöder inbyggda och anpassade RBAC-roller för autentisering via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 66e6cff40d71842ef19f99d7c96219af83fc9b4e
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368246"
---
# <a name="grant-access-to-azure-containers-and-queues-with-rbac-in-the-azure-portal"></a>Bevilja åtkomst till Azure-behållare och köer med RBAC i Azure portal

Azure Active Directory (Azure AD) auktoriserar åtkomsträttigheter till skyddade resurser via [rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md). Azure Storage definierar en uppsättning inbyggda RBAC-roller som omfattar vanliga uppsättningar av behörigheter som används för åtkomst till behållare eller köer. 

När en RBAC-roll tilldelas till en Azure AD-säkerhetsobjekt, Azure beviljar åtkomst till att dessa resurser för det säkerhetsobjektet. Åtkomst kan begränsas till nivån för prenumerationen, resursgruppen, storage-konto eller en enskild behållare eller en kö. En Azure AD-säkerhetsobjekt kan vara en användare, en grupp, tjänstens huvudnamn för ett program eller en [hanterad identitet för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

Den här artikeln beskriver hur du använder Azure-portalen för att tilldela RBAC-roller. Azure portal innehåller ett enkelt gränssnitt för att tilldela RBAC-roller och hantera åtkomst till dina lagringsresurser. Du kan också tilldela RBAC-roller för blob och kö resurser med hjälp av Azure-kommandoradsverktygen eller Azure Storage management API: er. Mer information om RBAC-roller för storage-resurser finns i [autentisera åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](storage-auth-aad.md). 

## <a name="rbac-roles-for-blobs-and-queues"></a>RBAC-roller för blobbar och köer

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Bestäm resource omfattning 

Fastställa omfattningen av åtkomst som säkerhetsobjektet bör ha innan du tilldelar en RBAC-roll till ett säkerhetsobjekt. Bästa metoder kräver att det alltid är bäst att bevilja endast den minsta möjliga definitionsområdet.

I följande lista beskrivs de nivåer som du kan begränsa åtkomst till Azure blob och kö-resurser, från och med det minsta definitionsområdet:

- **En enskild behållare.** I den här omfattningen har åtkomst till alla blobar i behållaren, samt egenskaper för behållare och metadata i ett säkerhetsobjekt.
- **En enskild kö.** I den här omfattningen har åtkomst till meddelanden i kön, samt köegenskaper och metadata i ett säkerhetsobjekt.
- **Storage-konto.** I den här omfattningen har åtkomst till alla behållare och deras blobbar eller till alla köer och deras meddelanden i ett säkerhetsobjekt.
- **Resursgruppen.** I den här omfattningen har säkerhetsobjekt åtkomst till alla behållare eller köer i alla lagringskonton i resursgruppen.
- **Prenumerationen.** I den här omfattningen har säkerhetsobjekt åtkomst till alla behållare eller köer i alla lagringskonton i alla resursgrupper i prenumerationen.

När du har fastställt det önskade omfånget för en rolltilldelning, navigera till rätt resurs i Azure-portalen. Visa den **åtkomstkontroll (IAM)** inställningar för resursen, och följ instruktionerna i följande avsnitt för att hantera rolltilldelningar.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Tilldela RBAC-roller med hjälp av Azure portal

Bevilja åtkomst till blob och kö resurser med Azure AD-autentiseringsuppgifter i Azure portal omfattar följande steg: 

1. Tilldela rollen RBAC i Azure Storage att bevilja åtkomst till behållare eller köer. Läsbehörighet, tilldela den **Blob Data-läsare** eller **Lagringsködata-läsare** roll. För att läsa, skriva och ta bort, tilldela den **Blob Data-deltagare** eller **Lagringsködata-deltagare** roll. Du kan också tilldela en anpassad roll.

1. Tilldela Azure Resource Manager [läsare](../../role-based-access-control/built-in-roles.md#reader) roll till användare som behöver åtkomst till behållare eller köer via Azure-portalen med sina autentiseringsuppgifter för Azure AD. 

I följande avsnitt beskrivs var och en av de här stegen i detalj.

### <a name="assign-a-built-in-rbac-role"></a>Tilldela en inbyggd RBAC-roll

Innan du tilldelar en roll till en säkerhetsprincip måste du Överväg omfattningen för de behörigheter som du beviljar. Granska den [Bestäm resource omfattning](#determine-resource-scope) avsnitt för att bestämma lämpliga omfattningar.

Proceduren som visas här tilldelas en roll som är begränsade till en behållare, men du kan följa samma steg för att tilldela en roll som är begränsade till en kö: 

1. I den [Azure-portalen](https://portal.azure.com)går du till ditt lagringskonto och visa den **översikt** för kontot.
1. Under Services väljer **Blobar**. 
1. Leta rätt på behållaren som du vill tilldela en roll och visa behållarens inställningar. 
1. Välj **åtkomstkontroll (IAM)** att visa inställningar för åtkomstkontroll för behållaren. Välj den **rolltilldelningar** flik för att se en lista över rolltilldelningar.

    ![Skärmbild som visar behörighetsinställningar för behållare](media/storage-auth-aad-rbac/portal-access-control-container.png)

1. Klicka på den **Lägg till rolltilldelning** för att lägga till en ny roll.
1. I den **Lägg till rolltilldelning** fönstret, Välj den Azure Storage-roll som du vill tilldela. Och sedan söka efter säkerhetsobjektet som du vill tilldela rollen.

    ![Skärmbild som visar hur du tilldelar en RBAC-roll](media/storage-auth-aad-rbac/add-rbac-role.png)

1. Klicka på **Spara**. Identiteten som du tilldelats rollen visas listan under rollen. Följande bild visar till exempel att användaren nu har läsbehörighet till data i behållare med namnet *exempelbehållaren*.

    ![Skärmbild som visar listan över användare som har tilldelats en roll](media/storage-auth-aad-rbac/container-scoped-role.png)

Du kan följa liknande steg om du vill tilldela en roll som är begränsade till storage-konto, resursgrupp eller prenumeration.

> [!NOTE]
> Ägare för Azure Storage-kontot kan tilldelas du automatiskt inte behörighet att komma åt data. Du måste uttryckligen tilldela dig själv en RBAC-roll för Azure Storage. Du kan tilldela den på nivån för din prenumeration, resursgrupp, storage-konto, eller en behållare eller en kö.
> 
> Du kan inte tilldela en roll som är begränsade till en behållare eller en kö om ditt storage-konto har ett hierarkiskt namnområde aktiverat.

### <a name="assign-the-reader-role-for-portal-access"></a>Tilldela rollen Läsare för portalåtkomst

När du tilldelar en inbyggda eller anpassade roll för Azure Storage till säkerhetsobjekt, beviljar du behörigheter till det säkerhetsobjektet att utföra åtgärder på data i ditt storage-konto. Inbyggt **Dataläsaren** roller ger läsbehörighet för data i en behållare eller en kö, samtidigt som inbyggt **Data-deltagare** roller tillhandahåller läsa, skriva och ta bort behörigheter till en behållare eller kön. Behörigheter är begränsade till den angivna resursen.  

Exempel: Om du tilldelar den **Storage Blob Data-deltagare** roll till användare Mary på nivån för en behållare med namnet **exempelbehållaren**, sedan Mary beviljas läsa, skriva och ta bort åtkomst till alla blobar i den behållaren.

Men om Mary vill visa en blob i Azure-portalen kommer **Storage Blob Data-deltagare** rollen påverkar i sig inte ger tillräcklig behörighet för att gå via portalen till bloben för att kunna visa den. Ytterligare Azure AD-behörigheter krävs för att gå via portalen och visa de resurser som är synliga det.

Om användarna behöver för att kunna få åtkomst till blobar i Azure portal och sedan tilldela dem en ytterligare RBAC-roll i [läsare](../../role-based-access-control/built-in-roles.md#reader) roll, som dessa användare på nivån för storage-konto eller högre. Den **läsare** är en Azure Resource Manager-roll som tillåter användare att visa lagringskontoresurserna, men inte ändra dem. Det ger inte läsbehörighet till data i Azure Storage, men endast konto management-resurser.

Följ dessa steg för att tilldela den **läsare** rollen så att en användare kan komma åt blobar från Azure-portalen. Tilldelningen är begränsad till storage-kontot i det här exemplet:

1. I den [Azure-portalen](https://portal.azure.com), navigera till ditt lagringskonto.
1. Välj **åtkomstkontroll (IAM)** att visa inställningarna av åtkomstkontrollen för lagringskontot. Välj den **rolltilldelningar** flik för att se en lista över rolltilldelningar.
1. I den **Lägg till rolltilldelning** väljer den **läsare** roll. 
1. Från den **tilldela åtkomst till** väljer **Azure AD-användare, grupp eller tjänstens huvudnamn**.
1. Söka efter säkerhetsobjektet som du vill tilldela rollen.
1. Spara rolltilldelningen.

> [!NOTE]
> Tilldela rollen Läsare krävs endast för användare som behöver åtkomst till blobar eller köer med hjälp av Azure-portalen. 

## <a name="next-steps"></a>Nästa steg

- Läs mer om RBAC i [vad är rollbaserad åtkomstkontroll (RBAC)?](../../role-based-access-control/overview.md).
- Om du vill lära dig mer om att tilldela och hantera RBAC-rolltilldelningar med Azure PowerShell, Azure CLI eller REST-API kan du läsa följande artiklar:
    - [Hantera rollbaserad åtkomstkontroll (RBAC) med Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Hantera rollbaserad åtkomstkontroll (RBAC) med Azure CLI](../../role-based-access-control/role-assignments-cli.md)
    - [Hantera rollbaserad åtkomstkontroll (RBAC med REST API)](../../role-based-access-control/role-assignments-rest.md)
- Läs hur du tillåter åtkomst till behållare och köer från i ditt storage-program i [använda Azure AD med Azure Storage-program](storage-auth-aad-app.md).
