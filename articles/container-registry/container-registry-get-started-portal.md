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
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: e74c5428f0e31d9d3cf06b85aa8cefde868e9d67
ms.lasthandoff: 03/27/2017

---

# <a name="create-a-private-docker-container-registry-using-the-azure-portal"></a>Skapa ett privat Docker-behållarregister med hjälp av Azure Portal
Använd Azure Portal för att skapa ett behållarregister och hantera dess inställningar. Du kan också skapa och hantera behållarregister med hjälp av [Azure CLI 2.0-kommandona](container-registry-get-started-azure-cli.md) eller via programmering med [REST-API:et](https://go.microsoft.com/fwlink/p/?linkid=834376) för Container Registry.

Bakgrund och koncept beskrivs i [översikten](container-registry-intro.md).



## <a name="create-a-container-registry"></a>Skapa ett behållarregister
1. Klicka på **+ Nytt** på [Azure Portal](https://portal.azure.com).
2. Sök efter **Azure Container Registry** på Marketplace.
3. Välj **Azure Container Registry**, med **Microsoft** som utgivare.
    ![Container Registry-tjänsten på Azure Marketplace](./media/container-registry-get-started-portal/container-registry-marketplace.png)
4. Klicka på **Skapa**. Bladet **Azure Container Registry** öppnas.

    ![Inställningar för behållarregister](./media/container-registry-get-started-portal/container-registry-settings.png)
5. Ange informationen nedan på bladet **Azure Container Registry**. Klicka på **Skapa** när du är klar.

    a. **Registernamn**: Ett globalt unikt domännamn på den översta nivån för ditt specifika register. I det här exemplet är registernamnet *myRegistry01*, men du kan ersätta namnet med ett eget unikt namn. Namnet får bara innehålla bokstäver och siffror.

    b. **Resursgrupp**: Välj en befintlig [resursgrupp](../azure-resource-manager/resource-group-overview.md#resource-groups) eller skriv namnet på en ny resursgrupp.

    c. **Plats**: Välj en plats för ett Azure-datacenter där tjänsten är [tillgänglig](https://azure.microsoft.com/regions/services/), t.ex. **USA, södra centrala**.

    d. **Administratörsanvändarnamn**: Om du vill ger du en administratörsanvändare åtkomst till registret. Du kan ändra den här inställningen när du har skapat registret.

    > [!IMPORTANT]
    > Förutom att ge åtkomst genom ett administratörsanvändarkonto stöder behållarregister autentisering med Azure Active Directory-tjänstobjekt. Mer information och saker att tänka på finns i [Authenticate with a container registry](container-registry-authentication.md) (Autentisera med ett behållarregister).


    e. **Lagringskonto**: Använd standardinställningen för att skapa ett [lagringskonto](../storage/storage-introduction.md), eller välj ett befintligt lagringskonto på samma plats. Premium-lagring stöds inte för närvarande.


## <a name="manage-registry-settings"></a>Hantera registerinställningar
När du har skapat registret kommer du åt registerinställningarna genom att först gå till bladet **Container Registries** (Behållarregister) på portalen. Du kan till exempel behöva inställningarna för att logga in i registret, eller så kanske du vill aktivera eller inaktivera administratörsanvändaren.

1. På bladet **Container Registries** (Behållarregister) klickar du på namnet på registret.

    ![Bladet för behållarregister](./media/container-registry-get-started-portal/container-registry-blade.png)
2. Om du vill hantera åtkomstinställningarna klickar du på **Åtkomstnyckel**.

    ![Åtkomst till behållarregister](./media/container-registry-get-started-portal/container-registry-access.png)
3. Notera följande inställningar:

   * **Inloggningsserver** –Det fullständigt kvalificerade namnet som du använder för att logga in i registret. I det här exemplet är det `myregistry01.azurecr.io`.
   * **Administratörsanvändare** – Växla om du vill aktivera eller inaktivera administratörsanvändarkontot för registret.
   * **Användarnamn** och **Lösenord** –Autentiseringsuppgifterna för administratörsanvändarkontot (om det är aktiverat) som du kan använda för att logga in i registret. Om du vill kan du återskapa lösenorden. Två lösenord skapas så att du kan upprätthålla anslutningar till registret genom att använda ett lösenord medan du återskapar det andra lösenordet. Om du vill autentisera med ett tjänstobjekt i stället läser du [Authenticate with a private Docker container registry](container-registry-authentication.md) (Autentisera med ett privat Docker-behållarregister).

## <a name="next-steps"></a>Nästa steg
* [Skicka din första avbildning med hjälp av Docker CLI](container-registry-get-started-docker-cli.md)

