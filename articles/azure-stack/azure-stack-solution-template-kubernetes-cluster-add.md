---
title: Lägga till en Kubernetes-kluster i Azure-stacken Marketplace | Microsoft Docs
description: Lär dig hur du lägger till ett kluster Kubernetes Stack Azure Marketplace.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: c66b0d7ea5ade90c6bb8f88006f2a09bd407deaa
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="add-a-kubernetes-cluster-to-the-azure-stack-marketplace"></a>Lägga till en Kubernetes-kluster i Azure-stacken Marketplace

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

> [!note]  
> Azure Container insamlingstjänsten (ACS) Kubernetes på Azure-stacken är i privat förhandsvisning. Att begära åtkomst till Kubernetes Marketplace-objektet som behövs för att utföra instruktionerna i den här artikeln [skicka en begäran om att få tillgång till](https://aka.ms/azsk8).

Du kan erbjuda ett Kubernetes kluster som ett Marketplace-objekt till användarna. Användarna kan distribuera Kubernetes i en enda, samordnad åtgärd.

I följande artikel titta på med en Azure Resource Manager-mall för att distribuera och tillhandahålla resurser för ett fristående Kubernetes kluster. Innan du börjar, kontrollera din Azure-stacken och Azure-klient som globala inställningar. Samla in nödvändig information om Azure-stacken. Lägga till nödvändiga resurser för din klient och Azure-stacken Marketplace. Klustret beror på en Ubuntu server, anpassat skript och Kubernetes kluster-objekt i marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Skapa en plan, ett erbjudande och en prenumeration

Skapa en plan, ett erbjudande och en prenumeration för Kubernetes klustret Marketplace-objektet. Du kan också använda en befintlig plan och erbjuder.

1. Logga in på den [-administrationsportalen.](https://adminportal.local.azurestack.external)

2. Skapa en plan som den grundläggande planen. Instruktioner finns i [skapar en plan i Azure-stacken](azure-stack-create-plan.md).

3. Skapa ett erbjudande. Instruktioner finns i [skapa ett erbjudande på Azure-stacken](azure-stack-create-offer.md).

4. Välj **erbjuder**, och hitta det erbjudande som du skapade.

5. Välj **översikt** i bladet erbjudandet.

6. Välj **ändra tillståndet**. Välj **offentliga**.

7. Välj **+ ny** > **erbjuder och planerar** > **prenumeration** att skapa en ny prenumeration.

    a. Ange en **visningsnamn**.

    b. Ange en **användaren**. Använda Azure AD-kontot som är associerade med din klient.

    c. **Beskrivning av lagringsprovider**

    d. Ange den **Directory-klient** till Azure AD-klient för din Azure-stacken. 

    e. Välj **erbjuder**. Välj namnet på det erbjudande som du skapade. Anteckna prenumerations-ID.

## <a name="add-an-ubuntu-server-image"></a>Lägga till en bild för Ubuntu server

Lägg till följande Ubuntu Server bild Marketplace:

1. Logga in på den [-administrationsportalen](https://adminportal.local.azurestack.external).

2. Välj **fler tjänster** > **Marketplace Management**.

3. Välj **+ Lägg till från Azure**.

4. Ange `UbuntuServer`.

5. Välj servern med följande länk:
    - **Publisher**: kanoniska
    - **Erbjuder**: UbuntuServer
    - **SKU**: 16.04 LTS
    - **Version**: 16.04.201802220

6. Välj **hämta.**

## <a name="add-a-custom-script-for-linux"></a>Lägga till ett anpassat skript för Linux

Lägg till klustret Kubernetes från Marketplace:

1. Öppna den [-administrationsportalen](https://adminportal.local.azurestack.external).

2. Välj **fler tjänster** > **Marketplace Management**.

3. Välj **+ Lägg till från Azure**.

4. Ange `Custom Script for Linux`.

5. Välj skriptet med följande länk:
    - **Erbjuder**: anpassat skript för Linux 2.0
    - **Version**: 2.0.3
    - **Publisher**: Microsoft Corp

6. Välj **hämta.**


## <a name="add-the-kubernetes-cluster-to-the-marketplace"></a>Lägga till Kubernetes klustret marketplace

1. Öppna den [-administrationsportalen](https://adminportal.local.azurestack.external).

2. Välj **fler tjänster** > **Marketplace Management**.

3. Välj **+ Lägg till från Azure**.

4. Ange `Kubernetes Cluster`.

5. Välj `Kubernetes Cluster`.

6. Välj **hämta.**

    > [!note]  
    > Det kan ta fem minuter för marketplace-objektet ska visas i Marketplace.

    ![Kubernetes kluster](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes-cluster"></a>Uppdatera eller ta bort klustret Kubernetes 

När du uppdaterar objektet Kubernetes klustret, behöver du ta bort objektet som är i Marketplace. Följ sedan anvisningarna i den här artikeln för att lägga till klustret Kubernetes på Marketplace.

Ta bort objektet Kubernetes klustret:

1. Notera namnet på det aktuella objektet som `Microsoft.AzureStackKubernetesCluster.0.1.0`

2. Ansluta till Azure-stacken med PowerShell.

3. Använd följande PowerShell-cmdlet för att ta bort objektet:

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.1.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Nästa steg

[Distribuera ett Kubernetes kluster till Azure-stacken](/user/azure-stack-solution-template-kubernetes-deploy.md)

[Översikt över erbjuda tjänster i Azure-stacken](azure-stack-offer-services-overview.md)