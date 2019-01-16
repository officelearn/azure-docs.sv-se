---
title: Distribuera Kubernetes till Azure Stack | Microsoft Docs
description: Lär dig hur du distribuerar Kubernetes i Azure Stack.
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
ms.date: 10/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 064a9fc48ae60e530a11633b0823764a2f862811
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54320029"
---
# <a name="deploy-kubernetes-to-azure-stack"></a>Distribuera Kubernetes till Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

> [!Note]  
> Kubernetes på Azure Stack är en förhandsversion.

I följande artikel tittar på med hjälp av en lösningsmall för Azure Resource Manager-för att distribuera och etablera resurserna för Kubernetes i en enda, samordnad åtgärd. Du behöver samla in nödvändig information om installationen av Azure Stack, generera mallen, och sedan distribuera till molnet. Obs mallen är inte samma hanterade AKS-tjänsten erbjuds i globala Azure.

## <a name="kubernetes-and-containers"></a>Kubernetes och behållare

Du kan installera Kubernetes med Azure Resource Manager-mallar som genererats av ACS-Engine på Azure Stack. [Kubernetes](https://kubernetes.io) är ett system med öppen källkod för att automatisera distribution, skalning och hantering av program i behållare. En [behållare](https://www.docker.com/what-container) finns i en avbildning, som en virtuell dator. Till skillnad från en virtuell dator innehåller behållaravbildningen endast de resurser den behöver för att köra ett program, till exempel kod, runtime för att köra koden, specifika bibliotek och inställningar.

Du kan använda Kubernetes:

- Utveckla mycket skalbar och kan uppgraderas, program som kan distribueras på några sekunder. 
- Förenkla utformningen av programmet och förbättra tillförlitligheten genom olika Helm-program. [Helm](https://github.com/kubernetes/helm) är ett verktyg med öppen källkod paketering som hjälper dig att installera och hantera livscykeln för Kubernetes-program.
- Enkelt övervaka och diagnostisera hälsotillståndet för dina program med skala och uppgradera funktioner.

Du debiteras endast för beräkning användningen enligt de noder som har stöd för ditt kluster. Mer information finns i [användning och fakturering i Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-billing-and-chargeback).

## <a name="prerequisites"></a>Förutsättningar 

Kom igång genom att kontrollera att du har rätt behörigheter och att Azure Stack är klar.

1. Kontrollera att du kan skapa program i Azure Active Directory (Azure AD)-klienten. Du behöver följande behörigheter för Kubernetes-distribution.

    Anvisningar om hur du felsöker dina behörigheter finns i [Kontrollera Azure Active Directory-behörigheter](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions).

1. Skapa en SSH offentlig och privat nyckel att logga in på Linux VM på Azure Stack. Du behöver den offentliga nyckeln när du skapar klustret.

    Anvisningar om att generera en nyckel finns i [SSH-nyckel Generation](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

1. Kontrollera att du har en giltig prenumeration i din klient Azure Stack-portalen och att du har tillräckligt med offentliga IP-adresser tillgängliga att lägga till nya program.

    Klustret kan inte distribueras till ett Azure Stack **administratör** prenumeration. Du måste använda en **användaren** prenumeration. 

1. Om du inte har Kubernetes-kluster i din marketplace tala med administratören för Azure Stack.

## <a name="create-a-service-principal-in-azure-ad"></a>Skapa ett tjänstobjekt i Azure AD

1. Logga in på den globala [Azure-portalen](http://portal.azure.com).

1. Kontrollera att du loggat in med Azure AD-klient som är associerade med Azure Stack-instans. Du kan växla din inloggning genom att klicka på filter-ikonen i verktygsfältet i Azure.

    ![Välj du AD-klient](media/azure-stack-solution-template-kubernetes-deploy/tenantselector.png)

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

1. Välj **alla tjänster** > **prenumerationer**.

1. Välj den prenumeration som skapats av din operatör för att använda Kubernetes-klustret.

1. Välj **åtkomstkontroll (IAM)** > Välj **Lägg till rolltilldelning**.

1. Välj den **deltagare** roll.

1. Välj namnet på programmet som skapats för tjänsten huvudnamn. Du kan behöva ange namnet i sökrutan.

1. Klicka på **Spara**.

## <a name="deploy-a-kubernetes"></a>Distribuera ett Kubernetes

1. Öppna den [Azure Stack-portalen](https://portal.local.azurestack.external).

1. Välj **+ skapa en resurs** > **Compute** > **Kubernetes-kluster**. Klicka på **Skapa**.

    ![Distribuera lösningsmall](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Grundläggande inställningar

1. Välj **grunderna** i skapar Kubernetes-kluster.

    ![Distribuera lösningsmall](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Välj din **prenumeration** -ID.

1. Ange namnet på en ny resursgrupp eller välj en befintlig resursgrupp. Resursnamnet måste vara alfanumeriskt och gemener.

1. Välj den **plats** för resursgruppen. Det här är den region som du väljer för din Azure Stack-installation.

### <a name="2-kubernetes-cluster-settings"></a>2. Inställningar för Kubernetes-kluster

1. Välj **Kubernetes klusterinställningar** i skapar Kubernetes-kluster.

    ![Distribuera lösningsmall](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings.png)

1. Ange den **Linux VM-administratörsanvändarnamn**. Användarnamn för Linux-datorer som ingår i Kubernetes-klustret och DVM.

1. Ange den **offentlig SSH-nyckel** används för alla Linux-datorer som skapats som en del av Kubernetes-kluster och DVM godkännande.

1. Ange den **Master profil DNS-Prefix** som är unikt för regionen. Detta måste vara en region – unikt namn, till exempel `k8s-12345`. Försök att göra det samma som resursgruppens namn som bästa praxis.

    > [!Note]  
    > Använda en DNS-prefix för nya och unika master profil för varje kluster.

1. Välj den **Kubernetes Master Pool profil antal**. Antalet innehåller antalet noder i poolen master. Det kan vara mellan 1 och 7. Det här värdet ska vara ett udda tal.

1. Välj **The VMSize med Kubernetes överordnade virtuella datorer**.

1. Välj den **Kubernetes Nodantal Pool profil**. Antalet innehåller antalet agenter i klustret. 

1. Välj den **Lagringsprofilen**. Du kan välja **Blob Disk** eller **Managed Disk**. 

1. Ange den **tjänstens huvudnamn ClientId** används av providern för Kubernetes Azure-molnet. Klient-ID har identifierats som program-ID när den skapade tjänstens huvudnamn.

1. Ange den **Klienthemlighet för tjänstens huvudnamn** som du skapade när du skapar tjänstens huvudnamn.

1. Ange den **Kubernetes Azure Cloud-providerversion**. Det här är version för Kubernetes Azure-providern. Azure Stack släpper en anpassad Kubernetes-version för varje Azure Stack-version.

### <a name="3-summary"></a>3. Sammanfattning

1. Välj Sammanfattning. Bladet visar ett verifieringsmeddelande om för dina inställningar för Kubernetes-kluster.

    ![Distribuera lösningsmall](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Granska dina inställningar.

3. Välj **OK** att distribuera klustret.

> [!TIP]  
>  Om du har frågor om distributionen kan du publicera din fråga eller se om någon redan har besvarat frågan i den [Azure Stack-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="connect-to-your-cluster"></a>Ansluta till ditt kluster

Du är nu redo att ansluta till klustret. Huvudservern finns i klusterresursgruppen och har namnet `k8s-master-<sequence-of-numbers>`. Använda en SSH-klient för att ansluta till huvuddatabasen. Du kan använda i bakgrunden **kubectl**, Kubernetes kommandoradsklient att hantera kluster. Anvisningar finns i [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Du kan också hitta den **Helm** pakethanterare som är användbara för att installera och distribuera appar till ditt kluster. Anvisningar för hur du installerar och använder Helm med ditt kluster finns i [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Nästa steg

[Lägga till ett Kubernetes i Marketplace (för Azure Stack-operatör)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes på Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
