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
ms.date: 05/08/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 71d920ea1c143ed65510b77e1804e9c5e38cc180
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="deploy-a-kubernetes-cluster-to-azure-stack"></a>Distribuera ett Kubernetes kluster till Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

> [!note]  
> Azure Container insamlingstjänsten (ACS) Kubernetes på Azure-stacken är i privat förhandsvisning. Azure Stack-operatorn måste begära åtkomst till Kubernetes Marketplace-objektet som behövs för att utföra instruktionerna i den här artikeln.

I följande artikel ser ut på att använda en lösningsmall för Azure Resource Manager-att distribuera och tillhandahålla resurser för Kubernetes i en enda, samordnad åtgärd. Du måste samla in nödvändig information om installationen av Azure-stacken, generera mallen, och sedan distribuerar till ditt moln.

## <a name="kubernetes-and-containers"></a>Kubernetes och behållare

Du kan installera tjänster för Azure-behållaren (ACS) Kubernetes på Azure-stacken. [Kubernetes](https://kubernetes.io) är ett system för öppen källkod för att automatisera distribution, skala och hantera program i behållare. En [behållare](https://www.docker.com/what-container) finns i en bild, som en virtuell dator. Till skillnad från en virtuell dator är det bild för behållaren innehåller resurser som behövs för att köra ett program, till exempel koden runtime att köra kod, vissa bibliotek och inställningar.

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

## <a name="create-a-service-principal-in-azure-ad"></a>Skapa ett huvudnamn för tjänsten i Azure AD

1. Logga in till den globala [Azure-portalen](http://www.poartal.azure.com).
2. Kontrollera att du har loggat in med Azure AD-klient som är associerade med Azure-stacken.
3. Skapa ett Azure AD-program.

    a. Välj **Azure Active Directory** > **+ App registreringar** > **nya Appregistrering**.

    b. Ange en **namn** av programmet.

    c. Välj **webbapp / API**

    d. Ange `http://localhost` för den **inloggnings-URL**.

    c. Klicka på **Skapa**

4. Anteckna den **program-ID**. Du behöver ID när klustret skapas. ID som refereras till som **klient-ID för tjänstens huvudnamn**.

5. Välj **inställningar** > **nycklar**.

    a. Ange den **beskrivning**.

    b. Välj **upphör aldrig att gälla** för **Expires**.

    c. Välj **Spara**. Se Observera strängen som nyckel. Du behöver strängen som nyckel när du skapar klustret. Nyckeln är referenser som den **tjänstens huvudnamn Klienthemlighet**.



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

2. Välj **+ ny** > **Compute** > **Kubernetes klustret**.

    ![Distribuera lösningsmall](../media/azure-stack-solution-template-kubernetes-cluster-add/azure-stack-kubernetes-cluster-solution-template.png)

3. Välj **parametrar** i den distribuera Lösningsmall.

    ![Distribuera lösningsmall](../media/azure-stack-solution-template-kubernetes-cluster-add/azure-stack-kubernetes-cluster-solution-template-parameters.png)

2. Ange den **Linux användarnamn**. Användarnamn för Linux virtuella datorer som ingår i klustret Kubernetes och DVM.

3. Ange den **offentliga SSH-nyckeln** används för alla Linux-datorer som skapats som en del av Kubernetes klustret och DVM godkännande.

4. Ange den **klient endpoint**. Är detta Azure Resource Manager-slutpunkten för att ansluta för att skapa resursgruppen för Kubernetes klustret. Du måste hämta slutpunkten för ett integrerat system från Azure Stack-operator. För den Azure Stack Development Kit (ASDK), som du kan använda `https://management.local.azurestack.external`.

5. Ange den **klient-ID** för klienten. Om du behöver hjälp med att hitta det här värdet finns [hämta klient-ID](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id). 

6. Ange den **master profil DNS-prefix** som är unikt för regionen. Detta måste vara en region-unikt namn som `k8s-12345`. Försök att välja det samma som resursgruppen namn som bästa praxis.

    > [!note]  
    > Använd ett DNS-prefix för nya och unika master profil för varje kluster.

7. Ange antalet agenter i klustret. Det här värdet kallas den **Agentantal Pool profil**. Det kan vara mellan 1 och 32

8. Ange den **service principal program-ID** används molnprovidern Kubernetes Azure.

9. Ange den **service principal klienthemlighet** som du skapade när du skapar huvudnamn tjänstprogram.

10. Ange den **Kubernetes Azure Cloud providerversion**. Det här är version för Kubernetes Azure-providern. Azure-stacken släpper en anpassad Kubernetes version för varje version av Azure-stacken.

12. Välj **OK**.

### <a name="specify-the-solution-values"></a>Ange värdena för lösning

1. Välj den **prenumeration**.

2. Ange namnet på en ny resursgrupp eller välj en befintlig resursgrupp. Resursnamnet måste vara alfanumeriska och gemener.

3. Ange platsen för en resursgrupp som **lokala**.

4. Välj **skapa.**

## <a name="connect-to-your-cluster"></a>Ansluta till ditt kluster

Du är nu redo att ansluta till klustret. Du behöver den **kubectl**, Kubernetes för klienten. Du hittar instruktioner för att ansluta till och hantera klustret i Azure Container Services-dokumentationen.   

Instruktioner finns i [Anslut till klustret](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough#connect-to-the-cluster).

## <a name="next-steps"></a>Nästa steg

[Lägga till en Kubernetes klustret Marketplace (för operatorn Azure Stack)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes på Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)