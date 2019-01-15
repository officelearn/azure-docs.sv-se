---
title: Lägg till Kubernetes i Azure Stack Marketplace | Microsoft Docs
description: Lär dig hur du lägger till Kubernetes i Azure Stack Marketplace.
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
ms.date: 01/11/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: e89575323b87ba28ef4f062da098fea4f0e27035
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264062"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Lägg till Kubernetes i Azure Stack Marketplace

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

> [!note]  
> Kubernetes på Azure Stack är en förhandsversion.

Du kan erbjuda Kubernetes som ett Marketplace-objekt till dina användare. Användarna kan distribuera Kubernetes i en enda, samordnad åtgärd.

I följande artikel titta på med en Azure Resource Manager-mall för att distribuera och etablera resurser för ett fristående Kubernetes-kluster. Kubernetes-kluster Marketplace-objekt 0.3.0-betaversionen kräver Azure Stack-version 1808. Innan du börjar, kontrollera Azure Stack och inställningar för globala Azure-klient. Samla in nödvändig information om Azure Stack. Lägga till nödvändiga resurser i din klient och Azure Stack Marketplace. Klustret är beroende av en Ubuntu-server, anpassade skript och Kubernetes-objekten ska vara i marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Skapa en plan, ett erbjudande och en prenumeration

Skapa en plan, ett erbjudande och en prenumeration för Kubernetes Marketplace-objekt. Du kan också använda en befintlig plan och erbjudande.

1. Logga in på den [administrationsportalen.](https://adminportal.local.azurestack.external)

1. Skapa en plan som den grundläggande planen. Anvisningar finns i [skapa en plan i Azure Stack](azure-stack-create-plan.md).

1. Skapa ett erbjudande. Anvisningar finns i [skapa ett erbjudande i Azure Stack](azure-stack-create-offer.md).

1. Välj **erbjuder**, och hitta erbjudandet som du skapade.

1. Välj **översikt** på bladet erbjudandet.

1. Välj **ändra tillståndet**. Välj **Offentligt**.

1. Välj **+ skapa en resurs** > **erbjudanden och planer** > **prenumeration** att skapa en ny prenumeration.

    a. Ange en **visningsnamn**.

    b. Ange en **användaren**. Använda Azure AD-konto som är associerade med din klient.

    c. **Beskrivning av lagringsprovider**

    d. Ange den **Directory-klient** till Azure AD-klient för Azure Stack. 

    e. Välj **erbjuder**. Välj namnet på erbjudandet som du skapade. Anteckna prenumerations-ID.

## <a name="add-an-ubuntu-server-image"></a>Lägg till en Ubuntu server-avbildning

Lägg till följande Ubuntu Server bild Marketplace:

1. Logga in på den [administrationsportalen](https://adminportal.local.azurestack.external).

1. Välj **alla tjänster**, och sedan under den **ADMINISTRATION** kategori, väljer **Marketplace management**.

1. Välj **+ Lägg till från Azure**.

1. Ange `UbuntuServer`.

1. Välj den senaste versionen av servern. Kontrollera den fullständiga versionen och se till att du har den senaste versionen:
    - **Publisher**: Canonical
    - **Erbjuder**: UbuntuServer
    - **Version**: 16.04.201806120 (eller senare)
    - **SKU**: 16.04-LTS

1. Välj **ladda ned.**

## <a name="add-a-custom-script-for-linux"></a>Lägg till ett anpassat skript för Linux

Lägg till Kubernetes från Marketplace:

1. Öppna den [administrationsportalen](https://adminportal.local.azurestack.external).

1. Välj **alla tjänster** och sedan under den **ADMINISTRATION** kategori, väljer **Marketplace Management**.

1. Välj **+ Lägg till från Azure**.

1. Ange `Custom Script for Linux`.

1. Väljer du skriptet med följande profil:
    - **Erbjuder**: Anpassat skript för Linux 2.0
    - **Version**: 2.0.6 (eller senare)
    - **Publisher**: Microsoft Corp

    > [!Note]  
    > Mer än en version av anpassat skript för Linux kan anges. Du behöver att lägga till den senaste versionen av objektet.

1. Välj **ladda ned.**


## <a name="add-kubernetes-to-the-marketplace"></a>Lägg till Kubernetes i marketplace

1. Öppna den [administrationsportalen](https://adminportal.local.azurestack.external).

1. Välj **alla tjänster** och sedan under den **ADMINISTRATION** kategori, väljer **Marketplace Management**.

1. Välj **+ Lägg till från Azure**.

1. Ange `Kubernetes`.

1. Välj `Kubernetes Cluster`.

1. Välj **ladda ned.**

    > [!note]  
    > Det kan ta fem minuter för marketplace-objekt ska visas i Marketplace.

    ![Kubernetes](user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Uppdatera eller ta bort Kubernetes 

När du uppdaterar Kubernetes-objektet, måste du ta bort objekt som finns i Marketplace. Sedan kan du följa anvisningarna i den här artikeln för att lägga till Kubernetes i marketplace.

Ta bort Kubernetes-objekt:

1. Ansluta till Azure Stack med PowerShell som en operatör. Anvisningar finns i [Anslut till Azure Stack med PowerShell som operatör](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-admin).

2. Hitta det aktuella objektet i Kubernetes-kluster i galleriet.

    ```PowerShell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Notera namnet på det aktuella objektet, till exempel `Microsoft.AzureStackKubernetesCluster.0.3.0`

4. Använd följande PowerShell-cmdlet för att ta bort objekt:

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Nästa steg

[Distribuera ett Kubernetes till Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

[Översikt över erbjudna tjänster i Azure Stack](azure-stack-offer-services-overview.md)
