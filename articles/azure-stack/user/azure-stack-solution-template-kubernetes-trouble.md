---
title: Felsöka ditt Kubernetes-distribution till Azure Stack | Microsoft Docs
description: Lär dig hur du felsöker ditt Kubernetes-distribution till Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.author: mabvrigg
ms.date: 03/20/2019
ms.reviewer: waltero
ms.lastreviewed: 03/20/2019
ms.openlocfilehash: 01a9405c98160149782ab2cf248f64818d631dde
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58293795"
---
# <a name="troubleshoot-your-kubernetes-deployment-to-azure-stack"></a>Felsöka ditt Kubernetes-distribution till Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

> [!Note]  
> Kubernetes på Azure Stack är en förhandsversion. Azure Stack-frånkopplade scenariot stöds inte för närvarande av förhandsversionen.

I följande artikel tittar på felsökning av ditt Kubernetes-kluster. Du kan granska aviseringen för distribution och granska statusen för distributionen av element som krävs för distributionen. Du kan behöva samlar in distributionsloggarna från Azure Stack- eller Linux-datorer som är värdar för Kubernetes. Du kan också behöva arbeta med Azure Stack-administratören om du vill hämta loggar från en administrativ slutpunkt.

## <a name="overview-of-deployment"></a>Översikt över distribution

Innan du felsöker ditt kluster, kanske du vill granska distributionsprocessen för Azure Stack Kubernetes-kluster. Distributionen använder en lösningsmall för Azure Resource Manager-för att skapa de virtuella datorerna och installera ACS-motor för klustret.

### <a name="deployment-workflow"></a>Arbetsflöde för distribution

Följande diagram visar den allmänna processen för att distribuera klustret.

![Distribuera Kubernetes-processen](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>Distributionssteg

1. Samla in indataparametrar från marketplace-objekt.

    Ange de värden som du behöver ställa in Kubernetes-kluster, inklusive:
    -  **Användarnamnet**: Användarnamn för Linux-datorer som ingår i Kubernetes-kluster och DVM.
    -  **Offentlig SSH-nyckel**: Den nyckel som används för godkännande av alla Linux-datorer som har skapats som en del av Kubernetes-kluster och DVM.
    -  **Tjänstens huvudnamn**: Det ID som används av providern för Kubernetes Azure-molnet. Klient-ID som identifieras som program-ID när du skapade tjänstens huvudnamn. 
    -  **Klienthemlighet**: De nyckel du skapade när du skapade tjänstens huvudnamn.

2. Skapa VM-distribution och tillägget för anpassat skript.
    -  Skapa distribution Linux VM med hjälp av Linux-avbildning marketplace **Ubuntu Server 16.04-LTS**.
    -  Hämta och kör det anpassade skripttillägget från marketplace. Skriptet är **anpassat skript för Linux 2.0**.
    -  Kör DVM anpassade skript. Skriptet utför följande uppgifter:
        1. Hämtar slutpunkt för galleri från Azure Resource Manager metadataslutpunkt.
        2. Hämtar active directory-resurs-ID från Azure Resource Manager metadataslutpunkt.
        3. Läser in API-modellen för ACS-motor.
        4. Distribuerar ACS-Engine till Kubernetes-klustret och sparar profilen för Azure Stack-molnet till `/etc/kubernetes/azurestackcloud.json`.
3. Skapa överordnade virtuella datorer.

4. Hämta och kör anpassade skripttillägg.

5. Kör skriptet master.

    Skriptet utför följande uppgifter:
    - Installerar etcd, Docker och Kubernetes resurser, till exempel kubelet. etcd är en distribuerad nyckelvärde som gör det möjligt att lagra data i ett kluster med datorer. Docker har stöd för utan ben operativsystemet på servernivå virtualizations kallas behållare. Kubelet är noden agenten som körs på varje nod i Kubernetes.
    - Ställer in den **etcd** service.
    - Ställer in den **kubelet** service.
    - Startar kubelet. Detta omfattar följande steg:
        1. Startar tjänsten API.
        2. Startar tjänsten controller.
        3. Startar tjänsten Schemaläggaren.
6. Skapa virtuella datorer för agenten.

7. Hämta och kör det anpassade skripttillägget.

7. Kör skriptet agent. Det anpassade skriptet för agenten har följande uppgifter:
    - Installerar **etcd**.
    - Ställer in den **kubelet** service.
    - Ansluter till Kubernetes-klustret.

## <a name="steps-for-troubleshooting"></a>Anvisningar för att felsöka

Du kan samla in loggar på de virtuella datorerna som har stöd för Kubernetes-klustret. Du kan också granska loggen för distribution. Du kan behöva tala med din Azure Stack-administratör för att kontrollera vilken version av Azure Stack som du behöver för att använda och hämta loggar från Azure Stack som är relaterade till din distribution.

1. Granska den [Distributionsstatus](#review-deployment-status) och [hämta loggarna](#get-logs-from-a-vm) från huvudnoden i Kubernetes-klustret.
2. Var noga med att du använder den senaste versionen av Azure Stack. Om du är osäker på vilken version du använder administratören Azure Stack.
3.  Granska dina filer för virtuell dator skapas. Du kanske har haft följande problem:  
    - Den offentliga nyckeln kan vara ogiltig. Granska den nyckel som du skapade.  
    - Skapa en virtuell dator kan ha utlöses ett internt fel eller utlöses ett fel när skapades. Ett antal faktorer kan orsaka sådana fel, inklusive kapacitetsbegränsningar för Azure Stack-prenumerationen.
    - Se till att det fullständigt kvalificerade domännamnet (FQDN) för den virtuella datorn börjar med en duplicerad prefix.
4.  Om den virtuella datorn är **OK**, sedan utvärderar DVM. Om DVM har ett felmeddelande visas:

    - Den offentliga nyckeln kan vara ogiltig. Granska den nyckel som du skapade.  
    - Du måste kontakta Azure Stack-administratören om du vill hämta loggar för Azure Stack med hjälp av Privilegierade slutpunkter. Mer information finns i [Azure Stack diagnosverktyg](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics).
5. Om du har en fråga om distributionen kan du publicera den eller se om någon redan har besvarat frågan i den [Azure Stack-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="review-deployment-status"></a>Granska status för distribution

När du distribuerar ett Kubernetes-kluster kan granska du distributionsstatusen för om eventuella problem.

1. Öppna den [Azure Stack-portalen](https://portal.local.azurestack.external).
2. Välj **resursgrupper**, och välj sedan namnet på resursgruppen som du använde när du distribuerar Kubernetes-klustret.
3. Välj **distributioner**, och välj sedan den **distributionsnamn**.

    ![Felsökning](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  Läs fönstret felsökning. Alla distribuerade resurser innehåller följande information:
    
    | Egenskap  | Beskrivning |
    | ----     | ----        |
    | Resurs | Namnet på resursen. |
    | Type | Resursprovidern och typ av resurs. |
    | Status | Status för objektet. |
    | Tidsstämpel | UTC-tidsstämpel av tiden. |
    | Åtgärdsinformation | Åtgärdsinformationen som till exempel resursprovidern som ingick i åtgärden, resurs-slutpunkten och namnet på resursen. |

    Varje objekt har en statusikon av grönt eller rött.

## <a name="review-deployment-logs"></a>Granska distributionsloggar

Om Azure Stack-portalen inte innehåller tillräckligt med information för dig att felsöka eller lösa distributionsfel, är nästa steg att fördjupa dig i loggarna för klustret. Om du vill hämta distributionsloggarna manuellt, vanligtvis måste du ansluta till en av klustrets master virtuella datorerna. En enklare metod för alternativ är att ladda ned och kör följande [Bash-skript](https://aka.ms/AzsK8sLogCollectorScript) tillhandahålls av Azure Stack-teamet. Det här skriptet ansluts till DVM och klustrets virtuella datorer, samlar in information om vilka och klustret loggar och hämtas de tillbaka till din arbetsstation.

### <a name="prerequisites"></a>Förutsättningar

Du behöver ett Bash-Kommandotolken på den dator som du använder för att hantera Azure Stack. På en Windows-dator, du kan få en Bash fråga genom att installera [Git för Windows](https://git-scm.com/downloads). När du har installerat leta efter _Git Bash_ på start-menyn.

### <a name="retrieving-the-logs"></a>Hämtar loggarna

Följ stegen nedan för att samla in och hämta loggar för klustret:

1. Öppna en Bash-Kommandotolken. En Windows-dator, öppna _Git Bash_ eller köra: `C:\Program Files\Git\git-bash.exe`.

2. Ladda ned log insamlaren skriptet genom att köra följande kommandon i Bash-Kommandotolken:

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    chmod 744 getkuberneteslogs.sh
    ```

3. Leta efter informationen som krävs av skriptet och kör den:

    | Parameter           | Beskrivning                                                                                                      | Exempel                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -d, --vmd-host      | Den offentliga IP-Adressen eller det fullständigt kvalificerade domännamnet (FQDN) av DVM. Virtuella datornamn som börjar med `vmd-`. | IP: 192.168.102.38<br>DNS: vmd-myk8s.local.cloudapp.azurestack.external |
    | -h, --help  | Skriva ut kommandoanvändning. | |
    | -i, --identity-file | Den privata nyckelfilen RSA har överförts till marketplace-objekt när du skapar Kubernetes-klustret. Krävs för att fjärrsupport i Kubernetes-noderna. | C:\data\id_rsa.pem (Putty)<br>~/.ssh/id_rsa (SSH)
    | m-,--värd för överordnad Server   | Den offentliga IP-Adressen eller det fullständigt kvalificerade domännamnet (FQDN) för ett Kubernetes-huvudnod. Virtuella datornamn som börjar med `k8s-master-`. | IP: 192.168.102.37<br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u, --user          | Användarnamnet som skickades till marketplace-objekt när du skapar Kubernetes-klustret. Behövde fjärrsupport i Kubernetes-noderna | azureuser (standardvärde) |


   När du lägger till dina parametervärden kommandot kan se ut ungefär så här:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\id_rsa.pem" --user azureuser --vmd-host 192.168.102.37
     ```

4. Efter ett par minuter skriptets utdata insamlade loggar till en katalog med namnet `KubernetesLogs_{{time-stamp}}`. Där finns en katalog för varje virtuell dator som tillhör klustret.

    Log insamlaren skriptet också leta efter fel i loggfilerna och inkludera felsökning om det händer att hitta ett känt problem. Kontrollera att du kör den senaste versionen av skript för att öka risken för att söka efter kända problem.

> [!Note]  
> Kolla in den här GitHub [databasen](https://github.com/msazurestackworkloads/azurestack-gallery/tree/master/diagnosis) att lära dig mer om log insamlaren skriptet.

## <a name="next-steps"></a>Nästa steg

[Distribuera Kubernetes till Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)

[Lägga till ett Kubernetes-kluster i Marketplace (för Azure Stack-operatör)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes på Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
