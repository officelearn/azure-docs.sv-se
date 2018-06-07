---
title: Distribuera ett Kubernetes kluster till Azure-stacken | Microsoft Docs
description: Lär dig hur du distribuerar ett Kubernetes kluster till Azure-stacken.
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
ms.date: 05/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 43c0b7c87f9ee1cd33da3d617747c11dc120e51a
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823630"
---
# <a name="deploy-a-kubernetes-cluster-to-azure-stack"></a>Distribuera ett Kubernetes kluster till Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

> [!Note]  
> Azure Container insamlingstjänsten (ACS) Kubernetes på Azure-stacken är i privat förhandsvisning. Azure Stack-operatorn måste begära åtkomst till Kubernetes Marketplace-objektet som behövs för att utföra instruktionerna i den här artikeln.

I följande artikel ser ut på att använda en lösningsmall för Azure Resource Manager-att distribuera och tillhandahålla resurser för Kubernetes i en enda, samordnad åtgärd. Du måste samla in nödvändig information om installationen av Azure-stacken, generera mallen, och sedan distribuerar till ditt moln.

## <a name="kubernetes-and-containers"></a>Kubernetes och behållare

Du kan installera Kubernetes med hjälp av Azure Resource Manager-mallar som genereras av Azure Container insamlingstjänsten (ACS)-motorn på Azure-stacken. [Kubernetes](https://kubernetes.io) är ett system för öppen källkod för att automatisera distribution, skala och hantera program i behållare. En [behållare](https://www.docker.com/what-container) finns i en bild, som en virtuell dator. Till skillnad från en virtuell dator innehåller behållaren avbildningen bara de resurser som behövs för att köra ett program, till exempel koden runtime att köra kod, vissa bibliotek och inställningar.

Du kan använda Kubernetes till:

- Utveckla mycket skalbar, uppgraderas, program som kan distribueras i sekunder. 
- Förenkla designen av ditt program och förbättra tillförlitligheten genom olika Helm program. [Helm](https://github.com/kubernetes/helm) är en öppen källkod paketering verktyg som hjälper dig att installera och hantera livscykeln för Kubernetes program.
- Enkelt kan övervaka och diagnostisera hälsotillståndet för dina program med en skala och uppgradera funktioner.

## <a name="prerequisites"></a>Förutsättningar 

Kom igång genom att kontrollera att du har rätt behörigheter och att Azure-stacken är klar.

1. Kontrollera att du kan skapa program i Azure Active Directory (Azure AD)-klienten. Du behöver följande behörigheter för Kubernetes distributionen.

    Anvisningar för att kontrollera dina behörigheter finns [Kontrollera Azure Active Directory-behörigheter](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions).

2. Skapa en SSH offentliga och privata nyckel att logga in på Linux VM på Azure-stacken. Du behöver den offentliga nyckeln när klustret skapas.

    Anvisningar för att skapa en nyckel finns [SSH-nyckel Generation](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

3. Kontrollera att du har en giltig prenumeration i Azure-stacken klient-portalen och att du har tillräckligt med offentliga IP-adresser tillgängliga att lägga till nya program.

    Klustret kan inte distribueras till en Azure-stacken **administratör** prenumeration. Du måste använda en användare **-prenumeration. 

## <a name="create-a-service-principal-in-azure-ad"></a>Skapa ett huvudnamn för tjänsten i Azure AD

1. Logga in till den globala [Azure-portalen](http://portal.azure.com).
2. Kontrollera att du har loggat in med Azure AD-klient som är associerade med Azure Stack-instans.
3. Skapa ett Azure AD-program.

    a. Välj **Azure Active Directory** > **+ App registreringar** > **nya Appregistrering**.

    b. Ange en **namn** av programmet.

    c. Välj **webbapp / API**.

    d. Ange `http://localhost` för den **inloggnings-URL**.

    c. Klicka på **Skapa**.

4. Anteckna den **program-ID**. Du behöver ID när klustret skapas. ID som refereras till som **klient-ID för tjänstens huvudnamn**.

5. Välj **inställningar** > **nycklar**.

    a. Ange den **beskrivning**.

    b. Välj **upphör aldrig att gälla** för **Expires**.

    c. Välj **Spara**. Se Observera strängen som nyckel. Du behöver strängen som nyckel när du skapar klustret. Nyckeln hänvisas till som den **tjänstens huvudnamn Klienthemlighet**.



## <a name="give-the-service-principal-access"></a>Ge service principal åtkomst

Ge service principal åtkomst till din prenumeration så att säkerhetsobjekt kan skapa resurser.

1.  Logga in på den [-administrationsportalen](https://adminportal.local.azurestack.external).

2. Välj **fler tjänster** > **användaren prenumerationer** > **+ Lägg till**.

3. Välj den prenumeration som du skapade.

4. Välj **åtkomstkontroll (IAM)** > Välj **+ Lägg till**.

5. Välj den **ägare** roll.

6. Välj namnet på programmet som skapats för din tjänst huvudnamn. Du kan behöva ange namnet i sökrutan.

7. Klicka på **Spara**.

## <a name="deploy-a-kubernetes-cluster"></a>Distribuera ett Kubernetes kluster

1. Öppna den [Stack Azure portal](https://portal.local.azurestack.external).

2. Välj **+ ny** > **Compute** > **Kubernetes klustret**. Klicka på **Skapa**.

    ![Distribuera lösningsmall](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

3. Välj **grunderna** i den skapar Kubernetes kluster.

    ![Distribuera lösningsmall](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

2. Ange den **Linux VM administratörsanvändarnamnet**. Användarnamn för Linux virtuella datorer som ingår i klustret Kubernetes och DVM.

3. Ange den **offentlig SSH-nyckel** används för alla Linux-datorer som skapats som en del av Kubernetes klustret och DVM godkännande.

4. Ange den **Master profil DNS-Prefix för** som är unikt för regionen. Detta måste vara en region-unikt namn som `k8s-12345`. Försök att välja det samma som resursgruppen namn som bästa praxis.

    > [!Note]  
    > Använd ett DNS-prefix för nya och unika master profil för varje kluster.

5. Ange den **Agentantal Pool profil**. Antalet innehåller antalet agenter i klustret. Det kan vara mellan 1 och 4.

6. Ange den **tjänstens huvudnamn ClientId** används molnprovidern Kubernetes Azure.

7. Ange den **tjänstens huvudnamn Klienthemlighet** som du skapade när du skapar huvudnamn tjänstprogram.

8. Ange den **Kubernetes Azure Cloud providerversion**. Det här är version för Kubernetes Azure-providern. Azure-stacken släpper en anpassad Kubernetes version för varje version av Azure-stacken.

9. Välj din **prenumeration** -ID.

10. Ange namnet på en ny resursgrupp eller välj en befintlig resursgrupp. Resursnamnet måste vara alfanumeriska och gemener.

11. Välj den **plats** av resursgruppen. Det här är den region som du väljer för din Azure Stack-installation.

### <a name="specify-the-azure-stack-settings"></a>Ange inställningar för Azure-stacken

1. Välj den **Azure Stack stämpel inställningar**.

    ![Distribuera lösningsmall](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings.png)

2. Ange den **klient Arm Endpoint**. Är detta Azure Resource Manager-slutpunkten för att ansluta för att skapa resursgruppen för Kubernetes klustret. Du måste hämta slutpunkten för ett integrerat system från Azure Stack-operator. För den Azure Stack Development Kit (ASDK), som du kan använda `https://management.local.azurestack.external`.

3. Ange den **klient-ID** för klienten. Om du behöver hjälp med att hitta det här värdet finns [hämta klient-ID](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id). 

## <a name="connect-to-your-cluster"></a>Ansluta till ditt kluster

Du är nu redo att ansluta till klustret. Huvudmålservern kan hittas i din klusterresursgrupp och heter `k8s-master-<sequence-of-numbers>`. Använda en SSH-klient för att ansluta till huvudservern. Du kan använda i bakgrunden **kubectl**, Kubernetes kommandoradsverktyget klienten att hantera ditt kluster. Instruktioner finns i [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Du kan också hitta den **Helm** Pakethanteraren användbart för att installera och distribuera appar i klustret. Anvisningar för hur du installerar och använder Helm med klustret finns [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Nästa steg

[Lägga till en Kubernetes klustret Marketplace (för operatorn Azure Stack)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes på Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
