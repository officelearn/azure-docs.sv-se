---
title: Konfigurera rollbaserad åtkomst kontroll i Azure (Azure RBAC) för Azure API för FHIR
description: Den här artikeln beskriver hur du konfigurerar Azure RBAC för Azure API för FHIR data plan
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: matjazl
ms.reviewer: dseven
ms.openlocfilehash: c4a78168f2759f9d7bb9e50165b8cf03c71ecab5
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87845945"
---
# <a name="configure-azure-rbac-for-fhir"></a>Konfigurera Azure RBAC för FHIR 

I den här artikeln får du lära dig hur du använder [Azures rollbaserad åtkomst kontroll (Azure RBAC)](https://docs.microsoft.com/azure/role-based-access-control/) för att tilldela åtkomst till Azure-API: et för FHIR data plan. Azure RBAC är de bästa metoderna för att tilldela data Plans åtkomst när data planet användare hanteras i den Azure Active Directory klient som är associerad med din Azure-prenumeration. Om du använder en extern Azure Active Directory klient organisation, se den [lokala referensen för RBAC-tilldelning](configure-local-rbac.md).

## <a name="confirm-azure-rbac-mode"></a>Bekräfta Azure RBAC-läge

För att kunna använda Azure RBAC måste ditt Azure API för FHIR konfigureras för att använda din Azure-prenumeration för data planet och det ska inte finnas några tilldelade ID: n för identitets objekt. Du kan verifiera dina inställningar genom att granska bladet **autentisering** i ditt Azure API för FHIR:

:::image type="content" source="media/rbac/confirm-azure-rbac-mode.png" alt-text="Bekräfta Azure RBAC-läge":::

**Utfärdaren** ska vara inställd på den Azure Active Directory-klient som är associerad med din prenumeration och det får inte finnas några GUID i rutan med etiketten **tillåtna objekt-ID: n**. Du ser också att rutan är inaktive rad och att en etikett visar att Azure RBAC ska användas för att tilldela data Plans roller.

## <a name="assign-roles"></a>Tilldela roller

Om du vill ge användare, tjänstens huvud namn eller grupper åtkomst till FHIR-dataplanen klickar du på **åtkomst kontroll (IAM)** och klickar sedan på **roll tilldelningar** och klickar på **+ Lägg till**:

:::image type="content" source="media/rbac/add-azure-rbac-role-assignment.png" alt-text="Lägg till roll tilldelning i Azure":::

I **roll** valet söker du efter någon av de inbyggda rollerna för FHIR-dataplanen:

:::image type="content" source="media/rbac/built-in-fhir-data-roles.png" alt-text="Inbyggda FHIR data roller":::

Du kan välja mellan:

* FHIR data läsare: kan läsa (och söka) FHIR-data.
* FHIR data skrivare: kan läsa, skriva och mjuka ta bort FHIR-data.
* FHIR data exportör: kan läsa och exportera `$export` data (operator).
* FHIR data deltagare: kan utföra alla data Plans åtgärder.

Om dessa roller inte räcker för ditt behov kan du också [skapa anpassade roller](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell).

I rutan **Välj** söker du efter en användare, tjänstens huvud namn eller grupp som du vill tilldela rollen till.

## <a name="caching-behavior"></a>Funktioner för cachelagring

Azure API för FHIR kommer att cachelagra beslut i upp till 5 minuter. Om du beviljar en användare åtkomst till FHIR-servern genom att lägga till dem i listan över tillåtna objekt-ID: n, eller om du tar bort dem från listan, bör du förvänta dig att det tar upp till fem minuter innan ändringar av behörigheter för att spridas.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du tilldelar Azure-roller för FHIR data planet. Härnäst lär du dig mer om ytterligare inställningar för Azure API för FHIR:
 
>[!div class="nextstepaction"]
>[Ytterligare inställningar Azure API för FHIR](azure-api-for-fhir-additional-settings.md)
