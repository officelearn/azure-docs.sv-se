---
title: Använda Azure-portalen för att tilldela en RBAC-roll för dataåtkomst
titleSuffix: Azure Storage
description: Lär dig hur du använder Azure-portalen för att tilldela behörigheter till ett Azure Active Directory-säkerhetsobjekt med rollbaserad åtkomstkontroll (RBAC). Azure Storage stöder inbyggda och anpassade RBAC-roller för autentisering via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: d224bd9e9e7b1f8fc9eb45d85e78811d8642fc78
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519568"
---
# <a name="use-the-azure-portal-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Använd Azure-portalen för att tilldela en RBAC-roll för åtkomst till blob- och ködata

Azure Active Directory (Azure AD) godkänner åtkomsträttigheter till skyddade resurser via [rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md). Azure Storage definierar en uppsättning inbyggda RBAC-roller som omfattar vanliga behörighetsuppsättningar som används för att komma åt blob- eller ködata.

När en RBAC-roll tilldelas ett Azure AD-säkerhetsobjekt beviljar Azure åtkomst till dessa resurser för det säkerhetsobjektet. Åtkomst kan begränsas till prenumerationsnivån, resursgruppen, lagringskontot eller en enskild behållare eller kö. Ett Azure AD-säkerhetsobjekt kan vara en användare, en grupp, ett programtjänsthuvudnamn eller en [hanterad identitet för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

I den här artikeln beskrivs hur du använder Azure-portalen för att tilldela RBAC-roller. Azure-portalen tillhandahåller ett enkelt gränssnitt för att tilldela RBAC-roller och hantera åtkomst till dina lagringsresurser. Du kan också tilldela RBAC-roller för blob- och köresurser med hjälp av Azure-kommandoradsverktyg eller Azure Storage Management API:er. Mer information om RBAC-roller för lagringsresurser finns [i Autentisera åtkomst till Azure-blobbar och köer med Azure Active Directory](storage-auth-aad.md).

## <a name="rbac-roles-for-blobs-and-queues"></a>RBAC-roller för blobbar och köer

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Fastställ resursomfattning

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Tilldela RBAC-roller med Azure-portalen

När du har fastställt lämpligt scope för en rolltilldelning navigerar du till den resursen i Azure-portalen. Visa **inställningarna för åtkomstkontroll (IAM)** för resursen och följ dessa instruktioner för att hantera rolltilldelningar:

1. Tilldela lämplig Azure Storage RBAC-roll för att bevilja åtkomst till ett Azure AD-säkerhetsobjekt.

1. Tilldela Azure Resource Manager [Reader-rollen](../../role-based-access-control/built-in-roles.md#reader) till användare som behöver komma åt behållare eller köer via Azure-portalen med sina Azure AD-autentiseringsuppgifter. 

I följande avsnitt beskrivs vart och ett av dessa steg mer i detalj.

> [!NOTE]
> Som ägare av ditt Azure Storage-konto tilldelas du inte automatiskt behörigheter för åtkomst till data. Du måste uttryckligen tilldela dig själv en RBAC-roll för Azure Storage. Du kan tilldela den på prenumerationsnivå, resursgrupp, lagringskonto eller en behållare eller kö.
>
> Du kan inte tilldela en roll som är begränsad till en behållare eller kö om ditt lagringskonto har aktiverat ett hierarkiskt namnområde.

### <a name="assign-a-built-in-rbac-role"></a>Tilldela en inbyggd RBAC-roll

Innan du tilldelar en roll till ett säkerhetsobjekt måste du ta hänsyn till omfattningen av de behörigheter som du beviljar. Granska avsnittet [Bestäm resursomfattning](#determine-resource-scope) för att bestämma lämpligt scope.

Proceduren som visas här tilldelar en roll som begränsas till en behållare, men du kan följa samma steg för att tilldela en roll som omfattas av en kö:

1. Gå till ditt lagringskonto i [Azure-portalen](https://portal.azure.com)och visa **översikten** för kontot.
1. Under Tjänster väljer du **Blobbar**.
1. Leta reda på den behållare som du vill tilldela en roll för och visa behållarens inställningar.
1. Välj **Åtkomstkontroll (IAM)** om du vill visa åtkomstkontrollinställningar för behållaren. Välj fliken **Rolltilldelningar** om du vill visa listan över rolltilldelningar.

    ![Skärmbild som visar inställningar för kontroll av behållare](media/storage-auth-aad-rbac-portal/portal-access-control-for-storage.png)

1. Klicka på knappen **Lägg till rolltilldelning** om du vill lägga till en ny roll.
1. I fönstret **Lägg till rolltilldelning** väljer du den Azure Storage-roll som du vill tilldela. Sök sedan efter det säkerhetsobjekt som du vill tilldela rollen till.

    ![Skärmbild som visar hur du tilldelar en RBAC-roll](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. Klicka på **Spara**. Identiteten som du har tilldelat rollen visas under den rollen. Följande bild visar till exempel att den användare som har lagts till nu har läsbehörighet i data i behållaren med namnet *sample-container*.

    ![Skärmbild som visar lista över användare som tilldelats en roll](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

Du kan följa liknande steg för att tilldela en roll som omfattas av lagringskontot, resursgruppen eller prenumerationen.

### <a name="assign-the-reader-role-for-portal-access"></a>Tilldela rollläsaren för portalåtkomst

När du tilldelar en inbyggd eller anpassad roll för Azure Storage till ett säkerhetsobjekt, ger du behörighet till det säkerhetsobjektet att utföra åtgärder på data i ditt lagringskonto. De inbyggda **dataläsarerollerna** ger läsbehörighet för data i en behållare eller kö, medan de inbyggda **datadeltagarerollerna** ger läs-, skriv- och borttagningsbehörigheter till en behållare eller kö. Behörigheter begränsas till den angivna resursen.  
Om du till exempel tilldelar rollen **Storage Blob Data Contributor** till användaren Maria på nivån för en behållare med namnet **exempelbehållare**beviljas Maria läs-, skriv- och ta bort åtkomst till alla blobbar i behållaren.

Men om Mary vill visa en blob i Azure-portalen, kommer rollen **Storage Blob Data Contributor** i sig inte att ge tillräcklig behörighet för att navigera genom portalen till blobben för att kunna visa den. Ytterligare Azure AD-behörigheter krävs för att navigera genom portalen och visa andra resurser som är synliga där.

Om användarna behöver komma åt blobbar i Azure-portalen, sedan tilldela dem en ytterligare RBAC-roll, [Reader roll,](../../role-based-access-control/built-in-roles.md#reader) till dessa användare, på nivån för lagringskontot eller högre. **Rollen Läsare** är en Azure Resource Manager-roll som tillåter användare att visa lagringskontoresurser, men inte ändra dem. Den ger inte läsbehörighet till data i Azure Storage, utan endast till kontohanteringsresurser.

Följ dessa steg för att tilldela **reader-rollen** så att en användare kan komma åt blobbar från Azure-portalen. I det här exemplet begränsas tilldelningen till lagringskontot:

1. Navigera till ditt lagringskonto i [Azure-portalen.](https://portal.azure.com)
1. Välj **Åtkomstkontroll (IAM)** om du vill visa åtkomstkontrollinställningarna för lagringskontot. Välj fliken **Rolltilldelningar** om du vill visa listan över rolltilldelningar.
1. Välj roll för **rollen Lägg** **till roll.** 
1. Välj **Azure AD-användare, grupp eller tjänsthuvudnamn**i fältet **Tilldela åtkomst till.**
1. Sök för att hitta det säkerhetsobjekt som du vill tilldela rollen till.
1. Spara rolltilldelningen.

Tilldela **reader-rollen** är endast nödvändigt för användare som behöver komma åt blobbar eller köer med Hjälp av Azure-portalen.

> [!IMPORTANT]
> Förhandsversionen av Storage Explorer i Azure-portalen stöder inte att använda Azure AD-autentiseringsuppgifter för att visa och ändra blob- eller ködata. Storage Explorer i Azure-portalen använder alltid kontonycklarna för att komma åt data. Om du vill använda Storage Explorer i Azure-portalen måste du tilldelas en roll som innehåller **Microsoft.Storage/storageAccounts/listkeys/action**.

## <a name="next-steps"></a>Nästa steg

- Mer information om RBAC-roller för lagringsresurser finns [i Autentisera åtkomst till Azure-blobbar och köer med Azure Active Directory](storage-auth-aad.md). 
- Mer information om RBAC finns i [Vad är rollbaserad åtkomstkontroll (RBAC)?](../../role-based-access-control/overview.md).
- Mer information om hur du tilldelar och hanterar RBAC-rolltilldelningar med Azure PowerShell, Azure CLI eller REST API finns i följande artiklar:
    - [Hantera rollbaserad åtkomstkontroll (RBAC) med Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Hantera rollbaserad åtkomstkontroll (RBAC) med Azure CLI](../../role-based-access-control/role-assignments-cli.md)
    - [Hantera rollbaserad åtkomstkontroll (RBAC) med REST API](../../role-based-access-control/role-assignments-rest.md)
- Mer information om hur du auktoriserar åtkomst till behållare och köer från dina lagringsprogram finns i [Använda Azure AD med Azure Storage-program](storage-auth-aad-app.md).
