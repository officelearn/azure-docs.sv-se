---
title: "Skapa privat Docker-register – Azure Portal | Microsoft-dokument"
description: "Kom igång med att skapa och hantera privata Docker-behållarregister med Azure Portal"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 33944d34ce695e1729b761a7f762e24a6dce70a2
ms.lasthandoff: 03/06/2017

---

# <a name="create-a-private-docker-container-registry-using-the-azure-portal"></a>Skapa ett privat Docker-behållarregister med hjälp av Azure Portal
Använd Azure Portal för att skapa ett behållarregister och hantera dess inställningar. Du kan också skapa och hantera behållarregister med hjälp av [Azure CLI 2.0-kommandona](container-registry-get-started-azure-cli.md) eller via programmering med [REST-API:et](https://go.microsoft.com/fwlink/p/?linkid=834376) för Container Registry.

Bakgrund och koncept beskrivs i [översikten](container-registry-intro.md)


> [!NOTE]
> Container Registry finns för närvarande endast som förhandsversion.


## <a name="create-a-container-registry"></a>Skapa ett behållarregister
1. På [portalen](https://portal.azure.com) klickar du på **+ Nytt**.
2. Sök efter **container registry** på Marketplace.
3. Välj **Container Registry (preview)**, från utgivaren **Microsoft**. 
    ![Container Registry-tjänsten på Azure Marketplace](./media/container-registry-get-started-portal/container-registry-marketplace.png)
4. Klicka på **Skapa**. Bladet **Container Registry** visas.

    ![Inställningar för behållarregister](./media/container-registry-get-started-portal/container-registry-settings.png)
5. På bladet **Container Registry** anger du följande information. Klicka på **Skapa** när du är klar.
   
    a. **Registernamn**: Ett globalt unikt domännamn på den översta nivån för ditt specifika register. I det här exemplet är registernamnet *myRegistry01*, men du kan ersätta namnet med ett eget unikt namn. Namnet får bara innehålla bokstäver och siffror.
   
    b. **Resursgrupp**: Välj en befintlig [resursgrupp](../azure-resource-manager/resource-group-overview.md#resource-groups) eller skriv namnet på en ny resursgrupp. 
   
    c. **Plats**: Välj en plats för ett Azure-datacenter där tjänsten är [tillgänglig](https://azure.microsoft.com/regions/services/), t.ex. **USA, södra centrala**. 
   
    d. **Administratörsanvändarnamn**: Om du vill ger du en administratörsanvändare åtkomst till registret. Du kan ändra den här inställningen när du har skapat registret.
   
    > [!IMPORTANT]
    > Förutom att ge åtkomst genom ett administratörsanvändarkonto stöder behållarregister autentisering med Azure Active Directory-tjänstobjekt. Mer information och saker att tänka på finns i [Authenticate with a container registry](container-registry-authentication.md) (Autentisera med ett behållarregister).
    >
    
    e. **Lagringskonto**: Använd standardinställningen för att skapa ett [lagringskonto](../storage/storage-introduction.md), eller välj ett befintligt lagringskonto på samma plats. Premium-lagring stöds inte för närvarande.


## <a name="manage-registry-settings"></a>Hantera registerinställningar
När du har skapat registret kommer du åt registerinställningarna genom att först gå till bladet **Container Registries** (Behållarregister) på portalen. Du kan till exempel behöva inställningarna för att logga in i registret, eller så kanske du vill aktivera eller inaktivera administratörsanvändaren.

1. På bladet **Container Registries** (Behållarregister) klickar du på namnet på registret.
   
    ![Bladet för behållarregister](./media/container-registry-get-started-portal/container-registry-blade.png)
2. Om du vill hantera åtkomstinställningarna klickar du på **Åtkomstnyckel**.
   
    ![Åtkomst till behållarregister](./media/container-registry-get-started-portal/container-registry-access.png)
3. Notera följande inställningar:
   
   * **Inloggningsserver** –Det fullständigt kvalificerade namnet som du använder för att logga in i registret. I det här exemplet är det `myregistry01-contoso.azurecr.io`.
   * **Administratörsanvändare** – Växla om du vill aktivera eller inaktivera administratörsanvändarkontot för registret.
   * **Användarnamn** och **Lösenord** –Autentiseringsuppgifterna för administratörsanvändarkontot (om det är aktiverat) som du kan använda för att logga in i registret. Om du vill kan du återskapa lösenordet.

## <a name="next-steps"></a>Nästa steg
* [Skicka din första avbildning med hjälp av Docker CLI](container-registry-get-started-docker-cli.md)




