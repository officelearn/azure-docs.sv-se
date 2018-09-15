---
title: Lägg till ett Kubernetes-kluster i Azure Stack Marketplace | Microsoft Docs
description: Lär dig hur du lägger till ett Kubernetes-kluster i Azure Stack Marketplace.
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
ms.date: 09/12/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: ded2aa17fe9b8de2d8c8f662f5d99b1ce33a2b25
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45634217"
---
# <a name="add-a-kubernetes-cluster-to-the-azure-stack-marketplace"></a>Lägg till ett Kubernetes-kluster i Azure Stack Marketplace

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

> [!note]  
> Motorn för AKS (Azure Kubernetes Service) på Azure Stack är i privat förhandsversion. Att begära åtkomst till Kubernetes marknadsplats-objektet som behövs för att göra det som beskrivs i den här artikeln [ansöka om att få åtkomst](https://aka.ms/azsk8).

Du kan erbjuda ett Kubernetes-kluster som ett Marketplace-objekt till dina användare. Användarna kan distribuera Kubernetes i en enda, samordnad åtgärd.

I följande artikel titta på med en Azure Resource Manager-mall för att distribuera och etablera resurser för ett fristående Kubernetes-kluster. Innan du börjar, kontrollera Azure Stack och inställningar för globala Azure-klient. Samla in nödvändig information om Azure Stack. Lägga till nödvändiga resurser i din klient och Azure Stack Marketplace. Klustret är beroende av en Ubuntu-server, anpassade skript och Kubernetes-kluster-objekt i marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Skapa en plan, ett erbjudande och en prenumeration

Skapa en plan, ett erbjudande och en prenumeration för Kubernetes-kluster Marketplace-objekt. Du kan också använda en befintlig plan och erbjudande.

1. Logga in på den [administrationsportalen.](https://adminportal.local.azurestack.external)

1. Skapa en plan som den grundläggande planen. Anvisningar finns i [skapa en plan i Azure Stack](azure-stack-create-plan.md).

1. Skapa ett erbjudande. Anvisningar finns i [skapa ett erbjudande i Azure Stack](azure-stack-create-offer.md).

1. Välj **erbjuder**, och hitta erbjudandet som du skapade.

1. Välj **översikt** på bladet erbjudandet.

1. Välj **ändra tillståndet**. Välj **offentliga**.

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

1. Välj server med följande profil:
    - **Publisher**: Canonical
    - **Erbjuder**: UbuntuServer
    - **SKU**: 16.04 LTS
    - **Version**: 16.04.201802220

    > [!Note]  
    > Mer än en version av Ubuntu Server 16.04 LTS finnas. Du behöver att lägga till den version som matchar. Kubernetes-kluster kräver den exakta versionen av objektet.

1. Välj **ladda ned.**

## <a name="add-a-custom-script-for-linux"></a>Lägg till ett anpassat skript för Linux

Lägg till Kubernetes-klustret från Marketplace:

1. Öppna den [administrationsportalen](https://adminportal.local.azurestack.external).

1. Välj **alla tjänster** och sedan under den **ADMINISTRATION** kategori, väljer **Marketplace Management**.

1. Välj **+ Lägg till från Azure**.

1. Ange `Custom Script for Linux`.

1. Väljer du skriptet med följande profil:
    - **Erbjuder**: anpassat skript för Linux 2.0
    - **Version**: 2.0.3
    - **Publisher**: Microsoft Corp

    > [!Note]  
    > Mer än en version av anpassat skript för Linux kan anges. Du behöver att lägga till den version som matchar. Kubernetes-kluster kräver den exakta versionen av objektet.

1. Välj **ladda ned.**


## <a name="add-the-kubernetes-cluster-to-the-marketplace"></a>Lägg till Kubernetes-klustret på Marketplace

1. Öppna den [administrationsportalen](https://adminportal.local.azurestack.external).

1. Välj **lägga till tjänster** och sedan under den **ADMINISTRATION** kategori, väljer **Marketplace Management**.

1. Välj **+ Lägg till från Azure**.

1. Ange `Kubernetes Cluster`.

1. Välj `Kubernetes Cluster`.

1. Välj **ladda ned.**

    > [!note]  
    > Det kan ta fem minuter för marketplace-objekt ska visas i Marketplace.

    ![Kubernetes-kluster](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes-cluster"></a>Uppdatera eller ta bort Kubernetes-kluster 

När du uppdaterar Kubernetes-kluster-objektet, måste du ta bort objekt som finns i Marketplace. Sedan kan du följa anvisningarna i den här artikeln för att lägga till Kubernetes-klustret i marketplace.

Ta bort objektet Kubernetes-kluster:

1. Notera namnet på det aktuella objektet, till exempel `Microsoft.AzureStackKubernetesCluster.0.1.0`

1. Anslut till Azure Stack med PowerShell.

1. Använd följande PowerShell-cmdlet för att ta bort objekt:

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.1.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Nästa steg

[Distribuera ett Kubernetes-kluster till Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)



[Översikt över erbjudna tjänster i Azure Stack](azure-stack-offer-services-overview.md)