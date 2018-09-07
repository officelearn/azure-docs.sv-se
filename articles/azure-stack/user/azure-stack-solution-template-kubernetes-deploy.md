---
title: Distribuera ett Kubernetes-kluster till Azure Stack | Microsoft Docs
description: Lär dig hur du distribuerar ett Kubernetes-kluster till Azure Stack.
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
ms.date: 09/06/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 5df8104907e30337cdf47e5a9489ffdb50451a94
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44055224"
---
# <a name="deploy-a-kubernetes-cluster-to-azure-stack"></a>Distribuera ett Kubernetes-kluster i Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

> [!Note]  
> Motorn för AKS (Azure Kubernetes Service) på Azure Stack är i privat förhandsversion. Azure Stack-operator måste begära åtkomst till Kubernetes marknadsplats-objektet som behövs för att göra det som beskrivs i den här artikeln.

I följande artikel tittar på med hjälp av en lösningsmall för Azure Resource Manager-för att distribuera och etablera resurserna för Kubernetes i en enda, samordnad åtgärd. Du behöver samla in nödvändig information om installationen av Azure Stack, generera mallen, och sedan distribuera till molnet.

## <a name="kubernetes-and-containers"></a>Kubernetes och behållare

Du kan installera Kubernetes med Azure Resource Manager-mallar som skapas av motorn för Azure Kubernetes Services (AKS) på Azure Stack. [Kubernetes](https://kubernetes.io) är ett system med öppen källkod för att automatisera distribution, skalning och hantering av program i behållare. En [behållare](https://www.docker.com/what-container) finns i en avbildning, som en virtuell dator. Till skillnad från en virtuell dator innehåller behållaravbildningen endast de resurser den behöver för att köra ett program, till exempel kod, runtime för att köra koden, specifika bibliotek och inställningar.

Du kan använda Kubernetes:

- Utveckla mycket skalbar och kan uppgraderas, program som kan distribueras på några sekunder. 
- Förenkla utformningen av programmet och förbättra tillförlitligheten genom olika Helm-program. [Helm](https://github.com/kubernetes/helm) är ett verktyg med öppen källkod paketering som hjälper dig att installera och hantera livscykeln för Kubernetes-program.
- Enkelt övervaka och diagnostisera hälsotillståndet för dina program med skala och uppgradera funktioner.

## <a name="prerequisites"></a>Förutsättningar 

Kom igång genom att kontrollera att du har rätt behörigheter och att Azure Stack är klar.

1. Kontrollera att du kan skapa program i Azure Active Directory (Azure AD)-klienten. Du behöver följande behörigheter för Kubernetes-distribution.

    Anvisningar om hur du felsöker dina behörigheter finns i [Kontrollera Azure Active Directory-behörigheter](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions).

1. Skapa en SSH offentlig och privat nyckel att logga in på Linux VM på Azure Stack. Du behöver den offentliga nyckeln när du skapar klustret.

    Anvisningar om att generera en nyckel finns i [SSH-nyckel Generation](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

1. Kontrollera att du har en giltig prenumeration i din klient Azure Stack-portalen och att du har tillräckligt med offentliga IP-adresser tillgängliga att lägga till nya program.

    Klustret kan inte distribueras till ett Azure Stack **administratör** prenumeration. Du måste använda en användare **-prenumeration. 

## <a name="create-a-service-principal-in-azure-ad"></a>Skapa ett huvudnamn för tjänsten i Azure AD

1. Logga in på den globala [Azure-portalen](http://portal.azure.com).
1. Kontrollera att du loggat in med Azure AD-klient som är associerade med Azure Stack-instans.
1. Skapa ett Azure AD-program.

    a. Välj **Azure Active Directory** > **+ Appregistreringar** > **ny programregistrering**.

    b. Ange en **namn** av programmet.

    c. Välj **webbapp / API**.

    d. Ange `http://localhost` för den **inloggnings-URL**.

    c. Klicka på **Skapa**.

1. Anteckna **Program-ID**. Du behöver det ID: T när du skapar klustret. ID: T är refereras till som **klient-ID för tjänstens huvudnamn**.

1. Välj **inställningar** > **nycklar**.

    a. Ange den **beskrivning**.

    b. Välj **upphör aldrig att gälla** för **förfaller**.

    c. Välj **Spara**. Kontrollera att Observera viktiga strängen. Du behöver strängen som nyckel när du skapar klustret. Nyckeln är refereras till som den **Klienthemlighet för tjänstens huvudnamn**.



## <a name="give-the-service-principal-access"></a>Ge tjänstens huvudnamn åtkomst

Ge tjänstens huvudnamn åtkomst till din prenumeration så att huvudkontot kan skapa resurser.

1.  Logga in på den [Azure Stack-portalen](https://portal.local.azurestack.external/).

1. Välj **fler tjänster** > **prenumerationer**.

1. Välj den prenumeration som du skapade.

1. Välj **åtkomstkontroll (IAM)** > Välj **+ Lägg till**.

1. Välj den **ägare** roll.

1. Välj namnet på programmet som skapats för tjänsten huvudnamn. Du kan behöva ange namnet i sökrutan.

1. Klicka på **Spara**.

## <a name="deploy-a-kubernetes-cluster"></a>Distribuera ett Kubernetes-kluster

1. Öppna den [Azure Stack-portalen](https://portal.local.azurestack.external).

1. Välj **+ ny** > **Compute** > **Kubernetes-kluster**. Klicka på **Skapa**.

    ![Distribuera lösningsmall](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

1. Välj **grunderna** i den skapa Kubernetes-kluster.

    ![Distribuera lösningsmall](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Ange den **Linux VM-administratörsanvändarnamn**. Användarnamn för Linux-datorer som ingår i Kubernetes-klustret och DVM.

1. Ange den **offentlig SSH-nyckel** används för alla Linux-datorer som skapats som en del av Kubernetes-kluster och DVM godkännande.

1. Ange den **Master profil DNS-Prefix** som är unikt för regionen. Detta måste vara en region – unikt namn, till exempel `k8s-12345`. Försök att välja det samma som resursgruppen namn som bästa praxis.

    > [!Note]  
    > Använda en DNS-prefix för nya och unika master profil för varje kluster.

1. Ange den **Agentantal Pool profil**. Antalet innehåller antalet agenter i klustret. Det kan vara från 1 till 4.

1. Ange den **tjänstens huvudnamn ClientId** används av providern för Kubernetes Azure-molnet.

1. Ange den **Klienthemlighet för tjänstens huvudnamn** som du skapade när du skapar service principal program.

1. Ange den **Kubernetes Azure Cloud-providerversion**. Det här är version för Kubernetes Azure-providern. Azure Stack släpper en anpassad Kubernetes-version för varje Azure Stack-version.

1. Välj din **prenumeration** -ID.

1. Ange namnet på en ny resursgrupp eller välj en befintlig resursgrupp. Resursnamnet måste vara alfanumeriskt och gemener.

1. Välj den **plats** för resursgruppen. Det här är den region som du väljer för din Azure Stack-installation.

### <a name="specify-the-azure-stack-settings"></a>Ange inställningar för Azure Stack

1. Välj den **inställningar för Azure Stack-stämpel**.

    ![Distribuera lösningsmall](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings.png)

1. Ange den **slutpunkt för Arm-klient**. Det här är Azure Resource Manager-slutpunkt att ansluta för att skapa resursgruppen för Kubernetes-klustret. Du behöver hämta slutpunkten från Azure Stack-operatör för ett integrerat system. För de Azure Stack Development Kit (ASDK), som du kan använda `https://management.local.azurestack.external`.

1. Ange den **klient-ID** för klienten. Om du behöver hjälp med att hitta det här värdet kan se [hämta klient-ID](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id). 

## <a name="connect-to-your-cluster"></a>Ansluta till ditt kluster

Du är nu redo att ansluta till klustret. Huvudservern finns i klusterresursgruppen och har namnet `k8s-master-<sequence-of-numbers>`. Använda en SSH-klient för att ansluta till huvuddatabasen. Du kan använda i bakgrunden **kubectl**, Kubernetes kommandoradsklient att hantera kluster. Anvisningar finns i [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Du kan också hitta den **Helm** pakethanterare som är användbara för att installera och distribuera appar till ditt kluster. Anvisningar för hur du installerar och använder Helm med ditt kluster finns i [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Nästa steg

[Lägga till ett Kubernetes-kluster i Marketplace (för Azure Stack-operatör)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes på Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
