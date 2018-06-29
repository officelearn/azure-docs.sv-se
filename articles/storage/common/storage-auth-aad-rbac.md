---
title: Använda RBAC kan hantera åtkomsträttigheter till Azure Storage-behållare och köer (förhandsversion) | Microsoft Docs
description: Använda rollbaserad åtkomstkontroll (RBA) för att tilldela roller för åtkomst till Azure Storage-data till användare, grupper, tjänstens huvudnamn för programmet eller hanterad tjänstidentiteter. Azure Storage har stöd för inbyggda och anpassade roller för åtkomsträttigheter till behållare och köer.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/29/2018
ms.author: tamram
ms.openlocfilehash: 241808e0a7bde1d2c53cd0af1de677275c169214
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082238"
---
# <a name="manage-access-rights-to-azure-storage-data-with-rbac-preview"></a>Hantera behörigheter till Azure Storage-data med RBAC (förhandsgranskning)

Azure Active Directory (AD Azure) auktoriserar åtkomsträttigheter till skyddade resurser via [rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). Azure Storage definierar en uppsättning inbyggda RBAC-roller som omfattar vanliga uppsättningar av behörigheter som används för åtkomst till behållare eller köer. När en RBAC rollen tilldelas en Azure AD-identitet, identiteten beviljas åtkomst till dessa resurser, enligt det specificerade omfånget. Vara kan begränsad åtkomst till nivån för prenumerationen, resursgruppen, storage-konto eller en enskild behållare eller kön. Du kan tilldela behörigheter för Azure Storage-resurser med hjälp av Azure-portalen, Azure kommandoradsverktyg och Azure Management-API: er. 

En Azure AD-identitet kan vara en användare, grupp eller tjänstens huvudnamn för programmet eller det kan vara en *hanterade tjänstidentiteten*. Ett säkerhetsobjekt kan vara en användare, grupp eller tjänstens huvudnamn för programmet. En [hanterade tjänstidentiteten](../../active-directory/managed-service-identity/overview.md) är ett automatiskt hanterade identitet som används för att autentisera från program som körs i virtuella Azure-datorer, funktionen appar, skalningsuppsättningar i virtuella datorer och andra. En översikt över identitet i Azure AD finns [identitetslösningar förstå Azure](https://docs.microsoft.com/en-us/azure/active-directory/understand-azure-identity-solutions).

## <a name="rbac-roles-for-azure-storage"></a>RBAC-roller för Azure Storage

Azure Storage har stöd för både inbyggda och anpassade RBAC-roller. Azure Storage erbjuder de här inbyggda RBAC-roller för användning med Azure AD:

- [Storage-Blob deltagare (förhandsgranskning)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor-preview)
- [Blob för lagring-dataläsare (förhandsgranskning)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-blob-data-reader-preview)
- [Lagring kön deltagare (förhandsgranskning)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor-preview)
- [Kön för lagring-dataläsare (förhandsgranskning)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-queue-data-reader-preview)

Mer information om hur inbyggda roller har definierats för Azure Storage, se [förstå rolldefinitioner](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#management-and-data-operations-preview).

Du kan också definiera anpassade roller för användning med behållare och köer. Mer information finns i [skapa anpassade roller för rollbaserad åtkomstkontroll i](https://docs.microsoft.com/azure/role-based-access-control/custom-roles.md). 

> [!IMPORTANT]
> Den här förhandsgranskningen är endast avsedd för icke-produktion användning. Produktion servicenivåavtal (SLA) är inte tillgängliga förrän Azure AD-integrering för Azure Storage har deklarerats allmänt tillgänglig. Om Azure AD-integrering inte stöds ännu för ditt scenario, fortsätta att använda delad nyckel auktorisering eller SAS-token i dina program. Mer information om förhandsversionen finns [autentisera åtkomst till Azure Storage med hjälp av Azure Active Directory (förhandsgranskning)](storage-auth-aad.md).
>
> Under förhandsgranskningen inkluderas kan RBAC rolltilldelningar ta upp till fem minuter att sprida.

## <a name="assign-a-role-to-a-security-principal"></a>Tilldela en roll till ett säkerhetsobjekt

Tilldela en RBAC roll till en Azure identitet att tilldela behörigheter till behållare eller köer i ditt lagringskonto. Du kan definiera rolltilldelning till lagringskontot eller till en specifik behållare eller en kö. I följande tabell sammanfattas de åtkomstbehörigheter som beviljats av de inbyggda rollerna, beroende på omfattningen: 

|                                 |     Deltagare för BLOB-Data                                                 |     BLOB-dataläsare                                                |     Kön deltagare                                  |     Kön dataläsare                                 |
|---------------------------------|------------------------------------------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------|----------------------------------------------------------|
|    Begränsad till prenumeration       |    Läs-/ skrivåtkomst till alla behållare och blobbar i prenumerationen       |    Läsbehörighet till alla behållare och blobbar i prenumerationen       |    Läs-/ skrivåtkomst till alla köer i prenumerationen       |    Läsbehörighet till alla köer i prenumerationen         |
|    Begränsad till resursgruppen.     |    Läs-/ skrivåtkomst till alla behållare och blobbar i resursgruppen     |    Läsbehörighet till alla behållare och blobbar i resursgruppen     |    Läs-/ skrivåtkomst till alla köer i resursgruppen     |    Läsbehörighet till alla köer i resursgruppen     |
|    Begränsad till storage-konto    |    Full åtkomst till alla behållare och blobbar i storage-konto    |    Läsbehörighet till alla behållare och blobbar i storage-konto    |    Full åtkomst till alla köer i storage-konto    |    Läsbehörighet till alla köer i storage-konto    |
|    Begränsad till behållaren-kö    |    Åtkomst till den angivna behållaren och dess blobbar              |    Läsbehörighet till den angivna behållaren och dess blobbar              |    Läs-/ skrivåtkomst till kö som anges                  |    Läsbehörighet till den angivna kön                    |

> [!NOTE]
> Som en ägare av Azure Storage-konto tilldelas du inte automatiskt behörigheter för åtkomst till data. Du måste uttryckligen tilldela dig en RBAC roll för Azure Storage. Du kan tilldela den för din prenumeration, resursgrupp, storage-konto, eller en behållare eller en kö.

Mer information om de behörigheter som krävs för att anropa åtgärder i Azure Storage finns [behörigheter för att anropa REST-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

I följande avsnitt visas hur du tilldelar en roll som är begränsade till lagringskontot eller begränsade till en enskild behållare.

### <a name="assign-a-role-scoped-to-the-storage-account-in-the-azure-portal"></a>Tilldela en roll som är begränsade till storage-konto i Azure-portalen

Tilldela en inbyggd roll som beviljar åtkomst till alla behållare eller köer i storage-konto i Azure-portalen:

1. I den [Azure-portalen](https://portal.azure.com), navigera till ditt lagringskonto.
2. Välj ditt lagringskonto och sedan **Access Control (IAM)** att visa inställningar för åtkomstkontroll för kontot. Klicka på den **Lägg till** för att lägga till en ny roll.

    ![Skärmbild som visar behörighetsinställningar för lagring](media/storage-auth-aad-rbac/portal-access-control.png)

3. I den **lägga till behörigheter** -fönstret väljer du rollen som ska tilldelas en Azure AD-identitet. Sök sedan för att hitta den identitet som du vill tilldela rollen. Till exempel följande bild visar den **Blob för lagring-dataläsare (förhandsgranskning)** roll som tilldelas en användare.

    ![Skärmbild som visar hur du tilldelar en RBAC-roll](media/storage-auth-aad-rbac/add-rbac-role.png)

4. Klicka på **Spara**. Som du tilldelats rollen och visas listan under rollen. Följande bild visar exempelvis att användare som lagts till nu har läsbehörighet till alla blobbdata i lagringskontot.

    ![Skärmbild som visar listan över användare som har tilldelats en roll](media/storage-auth-aad-rbac/account-scoped-role.png)

### <a name="assign-a-role-scoped-to-a-container-or-queue-in-the-azure-portal"></a>Tilldela en roll som är begränsade till en behållare eller kön i Azure-portalen

Stegen för att tilldela en inbyggd roll som har begränsats till en behållare eller till en kö är liknande. Det förfarande som visas här tilldelar en roll som är begränsade till en behållare, men du kan följa samma steg för att tilldela en roll som är begränsade till en kö: 

1. I den [Azure-portalen](https://portal.azure.com), navigera till ditt lagringskonto och visa den **översikt** för kontot.
2. Välj under Blob-tjänsten **Bläddra Blobbar**. 
3. Leta upp den behållare som du vill tilldela en roll och visa behållarens inställningar. 
4. Välj **Access Control (IAM)** att visa inställningar för åtkomstkontroll för behållaren.
5. I den **lägga till behörigheter** fönster, Välj den roll som du vill tilldela till en Azure AD-identitet. Sedan söka efter den identitet som du vill tilldela rollen.
6. Klicka på **Spara**. Som du tilldelats rollen och visas listan under rollen. Följande bild visar exempelvis att användare som läggs till nu har läsbehörighet till data i behållare med namnet *exempel behållaren*.

    ![Skärmbild som visar listan över användare som har tilldelats en roll](media/storage-auth-aad-rbac/container-scoped-role.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om RBAC i [Kom igång med rollbaserad åtkomstkontroll](../../role-based-access-control/overview.md).
- Information om hur du tilldela och hantera RBAC rolltilldelningar med Azure PowerShell, finns Azure CLI eller REST-API dessa artiklar:
    - [Hantera rollbaserad åtkomstkontroll (RBAC) med Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Hantera rollbaserad åtkomstkontroll (RBAC) med Azure CLI](../../role-based-access-control/role-assignments-cli.md)
    - [Hantera rollbaserad åtkomstkontroll (RBAC) med REST API](../../role-based-access-control/role-assignments-rest.md)
- Om du vill lära dig mer om att bevilja åtkomst till behållare och köer från i storage-program, se [använda Azure AD med Azure Storage-program](storage-auth-aad-app.md).
- Mer information om Azure AD-integrering för Azure-behållare och köer finns i Azure Storage-teamets blogg inlägg, [om förhandsversionen av Azure AD-autentisering för Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
- 