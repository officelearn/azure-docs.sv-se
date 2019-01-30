---
title: Distribuera Kubernetes i Azure Stack med Azure Active Directory (Azure AD) | Microsoft Docs
description: Lär dig hur du distribuerar Kubernetes i Azure Stack med Azure Active Directory (AD Azure).
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
ms.date: 01/16/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: c207fb866cdd17e4db1796e415c159eb887eef08
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243563"
---
# <a name="deploy-kubernetes-to-azure-stack-using-azure-active-directory"></a>Distribuera Kubernetes i Azure Stack med Azure Active Directory

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

> [!Note]  
> Kubernetes på Azure Stack är en förhandsversion.

Du kan följa stegen i den här artikeln för att distribuera och konfigurera resurser för Kubernetes, när du använder Azure Active Directory (Azure AD) som din identity management-tjänsten, i en enda, samordnad åtgärd.

## <a name="prerequisites"></a>Förutsättningar

Kom igång genom att kontrollera att du har rätt behörigheter och att Azure Stack är klar.

1. Kontrollera att du kan skapa program i Azure Active Directory (Azure AD)-klienten. Du behöver följande behörigheter för Kubernetes-distribution.

    Anvisningar om hur du felsöker dina behörigheter finns i [Kontrollera Azure Active Directory-behörigheter](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions).

1. Skapa en SSH offentlig och privat nyckel att logga in på Linux VM på Azure Stack. Du behöver den offentliga nyckeln när du skapar klustret.

    Anvisningar om att generera en nyckel finns i [SSH-nyckel Generation](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

1. Kontrollera att du har en giltig prenumeration i din klient Azure Stack-portalen och att du har tillräckligt med offentliga IP-adresser tillgängliga att lägga till nya program.

    Klustret kan inte distribueras till ett Azure Stack **administratör** prenumeration. Du måste använda en **användaren** prenumeration. 

1. Om du inte har Kubernetes-kluster i din marketplace, kontakta administratören Azure Stack.

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Konfigurera ett huvudnamn för tjänsten i Azure. Tjänstens huvudnamn ger din ansökan ger åtkomst till Azure Stack-resurser.

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

## <a name="deploy-kubernetes"></a>Distribuera Kubernetes

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

    ![Distribuera lösningsmall](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-aad.png)

1. Ange den **Linux VM-administratörsanvändarnamn**. Användarnamn för Linux-datorer som ingår i Kubernetes-klustret och DVM.

1. Ange den **offentlig SSH-nyckel** används för alla Linux-datorer som skapats som en del av Kubernetes-kluster och DVM godkännande.

1. Ange den **Master profil DNS-Prefix** som är unikt för regionen. Detta måste vara en region – unikt namn, till exempel `k8s-12345`. Försök att välja det samma som resursgruppen namn som bästa praxis.

    > [!Note]  
    > Använda en DNS-prefix för nya och unika master profil för varje kluster.

1. Välj den **Kubernetes Master Pool profil antal**. Antalet innehåller antalet noder i poolen master. Det kan vara mellan 1 och 7. Det här värdet ska vara ett udda tal.

1. Välj **The VMSize med Kubernetes överordnade virtuella datorer**.

1. Välj den **Kubernetes Nodantal Pool profil**. Antalet innehåller antalet agenter i klustret. 

1. Välj den **Lagringsprofilen**. Du kan välja **Blob Disk** eller **Managed Disk**. Detta anger att noden VM-storlek för Kubernetes virtuella datorer. 

1. Välj **Azure AD** för den **Azure Stack-identitetssystemet** för Azure Stack-installationen. 

1. Ange den **tjänstens huvudnamn ClientId** används av providern för Kubernetes Azure-molnet. Klient-ID som identifieras som program-ID när du skapade tjänstens huvudnamn.

1. Ange den **Klienthemlighet för tjänstens huvudnamn** som du skapade när du skapar tjänstens huvudnamn.

1. Ange den **Kubernetes Azure Cloud-providerversion**. Det här är version för Kubernetes Azure-providern. Azure Stack släpper en anpassad Kubernetes-version för varje Azure Stack-version.

### <a name="3-summary"></a>3. Sammanfattning

1. Välj Sammanfattning. Bladet visar ett verifieringsmeddelande om för dina inställningar för konfigurationer av Kubernetes-kluster.

    ![Distribuera lösningsmall](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Granska dina inställningar.

3. Välj **OK** att distribuera klustret.

> [!TIP]  
>  Om du har frågor om distributionen kan du publicera din fråga eller se om någon redan har besvarat frågan i den [Azure Stack-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).


## <a name="next-steps"></a>Nästa steg

[Ansluta till ditt kluster](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)