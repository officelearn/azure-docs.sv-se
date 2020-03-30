---
title: Skapa ett nytt åtkomstpaket i rättighetshantering - Azure AD
description: Lär dig hur du skapar ett nytt åtkomstpaket med resurser som du vill dela i Azure Active Directory-berättigandehantering.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c858a17d4574e6e45283df7c1276cd303f25297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262014"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Skapa ett nytt åtkomstpaket i Azure AD-berättigandehantering

Med ett åtkomstpaket kan du göra en engångsinställning av resurser och principer som automatiskt administrerar åtkomst under åtkomstpaketets livstid. I den här artikeln beskrivs hur du skapar ett nytt åtkomstpaket.

## <a name="overview"></a>Översikt

Alla åtkomstpaket måste placeras i en behållare som kallas katalog. En katalog definierar vilka resurser du kan lägga till i ditt åtkomstpaket. Om du inte anger en katalog placeras åtkomstpaketet i katalogen Allmänt. För närvarande kan du inte flytta ett befintligt åtkomstpaket till en annan katalog.

Om du är en åtkomstpakethanterare kan du inte lägga till resurser som du äger i en katalog. Du är begränsad till att använda de resurser som är tillgängliga i katalogen. Om du behöver lägga till resurser i en katalog kan du fråga katalogägaren.

Alla åtkomstpaket måste ha minst en princip. Principer anger vem som kan begära åtkomstpaketet och även godkännande- och livscykelinställningar. När du skapar ett nytt åtkomstpaket kan du skapa en första princip för användare i katalogen, för användare som inte finns i katalogen, endast för administratörsdirekta tilldelningar, eller så kan du välja att skapa principen senare.

![Skapa ett åtkomstpaket](./media/entitlement-management-access-package-create/access-package-create.png)

Här är stegen på hög nivå för att skapa ett nytt åtkomstpaket.

1. Starta processen för att skapa ett nytt åtkomstpaket i Identitetsstyrning.

1. Välj den katalog som du vill skapa åtkomstpaketet i.

1. Lägg till resurser från katalogen i ditt åtkomstpaket.

1. Tilldela resursroller för varje resurs.

1. Ange användare som kan begära åtkomst.

1. Ange eventuella godkännandeinställningar.

1. Ange livscykelinställningar.

## <a name="start-new-access-package"></a>Starta nytt åtkomstpaket

**Viktig roll:** Global administratör, användaradministratör, katalogägare eller åtkomstpakethanterare

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Klicka på **Azure Active Directory** och sedan på **Identitetsstyrning**.

1. Klicka på **Access-paket**på menyn till vänster.

1. Klicka på **Nytt åtkomstpaket**.
   
    ![Hantering av rättigheter i Azure-portalen](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>Grundläggande inställningar

På fliken **Grunderna** ger du åtkomstpaketet ett namn och anger vilken katalog som ska skapa åtkomstpaketet i.

1. Ange ett visningsnamn och en beskrivning för åtkomstpaketet. Användarna ser den här informationen när de skickar en begäran om åtkomstpaketet.

1. I listrutan **Katalog** väljer du den katalog som du vill skapa åtkomstpaketet i. Du kan till exempel ha en katalogägare som hanterar alla marknadsföringsresurser som kan begäras. I det här fallet kan du välja marknadsföringskatalogen.

    Du ser bara kataloger som du har behörighet att skapa åtkomstpaket i. Om du vill skapa ett åtkomstpaket i en befintlig katalog måste du vara global administratör eller användaradministratör, eller så måste du vara katalogägare eller åtkomstpakethanterare i den katalogen.

    ![Access-paket - Grunderna](./media/entitlement-management-access-package-create/basics.png)

    Om du är global administratör, användaradministratör eller katalogskapare och vill skapa ditt åtkomstpaket i en ny katalog som inte finns med i listan klickar du på **Skapa ny katalog**. Ange namn och beskrivning av katalogen och klicka sedan på **Skapa**.

    Åtkomstpaketet som du skapar och alla resurser som ingår i det läggs till i den nya katalogen. Du kan också lägga till ytterligare katalogägare senare.

1. Klicka på **Nästa**.

## <a name="resource-roles"></a>Resursroller

På fliken **Resursroller** väljer du de resurser som ska inkluderas i åtkomstpaketet. Användare som begär och tar emot åtkomstpaketet får alla resursroller i åtkomstpaketet.

1. Klicka på den resurstyp som du vill lägga till (**Grupper och grupper,** **program**eller **SharePoint-webbplatser**).

1. Markera en eller flera resurser i listan i fönstret Välj som visas.

    ![Åtkomstpaket - Resursroller](./media/entitlement-management-access-package-create/resource-roles.png)

    Om du skapar åtkomstpaketet i katalogen Allmänt eller en ny katalog kan du välja vilken resurs som helst från den katalog som du äger. Du måste vara minst global administratör, användaradministratör eller skapare av katalog.

    Om du skapar åtkomstpaketet i en befintlig katalog kan du välja en resurs som redan finns i katalogen utan att äga det.

    Om du är global administratör, användaradministratör eller katalogägare har du ytterligare möjlighet att välja resurser som du äger och som ännu inte finns i katalogen. Om du väljer resurser som inte finns i den valda katalogen läggs dessa resurser också till i katalogen för andra katalogadministratörer att skapa åtkomstpaket med. Om du bara vill markera resurser som för närvarande finns i den markerade katalogen markerar du kryssrutan **Endast se** högst upp i fönstret Markera.

1. När du har valt resurserna väljer du den roll som du vill att användarna ska tilldelas för resursen i **rolllistan.**

    ![Access-paket - Val av resursroll](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Klicka på **Nästa**.

## <a name="requests"></a>Begäranden

På fliken **Begärander** skapar du den första principen för att ange vem som kan begära åtkomstpaketet och även godkännandeinställningar. Senare kan du skapa fler begäransprinciper så att ytterligare grupper av användare kan begära åtkomstpaketet med sina egna godkännandeinställningar.

![Fliken Åtkomstpaket - fliken Begäranden](./media/entitlement-management-access-package-create/requests.png)

Beroende på vem du vill kunna begära det här åtkomstpaketet utför du stegen i något av följande avsnitt.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Granska + skapa

På fliken **Granska + skapa** kan du granska dina inställningar och söka efter valideringsfel.

1. Granska inställningarna för åtkomstpaketet

    ![Åtkomstpaket - Principinställning Aktivera principinställning](./media/entitlement-management-access-package-create/review-create.png)

1. Klicka på **Skapa** om du vill skapa åtkomstpaketet.

    Det nya åtkomstpaketet visas i listan över åtkomstpaket.

## <a name="creating-an-access-package-programmatically"></a>Skapa ett åtkomstpaket programmässigt

Du kan också skapa ett åtkomstpaket med Microsoft Graph.  En användare i en lämplig roll med `EntitlementManagement.ReadWrite.All` ett program som har delegerad behörighet kan anropa API:et till

1. [Lista accessPackageResources i katalogen](https://docs.microsoft.com/graph/api/accesspackagecatalog-list-accesspackageresources?view=graph-rest-beta) och [skapa en accessPackageResourceRequest](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta) för alla resurser som ännu inte finns i katalogen.
1. [Lista accessPackageResourceRoles](https://docs.microsoft.com/graph/api/accesspackagecatalog-list-accesspackageresourceroles?view=graph-rest-beta) för varje accessPackageResource i en accessPackageCatalog. Den här listan med roller används sedan för att välja en roll, när du sedan skapar en accessPackageResourceRoleScope.
1. [Skapa en accessPackage](https://docs.microsoft.com/graph/api/accesspackage-post?view=graph-rest-beta).
1. [Skapa en accessPackageAssignmentPolicy](https://docs.microsoft.com/graph/api/accesspackageassignmentpolicy-post?view=graph-rest-beta).
1. [Skapa en accessPackageResourceRoleScope](https://docs.microsoft.com/graph/api/accesspackage-post-accesspackageresourcerolescopes?view=graph-rest-beta) för varje resursroll som behövs i åtkomstpaketet.

## <a name="next-steps"></a>Nästa steg

- [Dela länk för att begära ett åtkomstpaket](entitlement-management-access-package-settings.md)
- [Ändra resursroller för ett åtkomstpaket](entitlement-management-access-package-resources.md)
- [Tilldela en användare direkt till åtkomstpaketet](entitlement-management-access-package-assignments.md)
