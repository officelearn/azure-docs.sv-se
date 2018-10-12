---
title: Felsöka din distribution till Kubernetes (K8) till Azure Stack | Microsoft Docs
description: Lär dig hur du felsöker din distribution till Kubernetes (K8) till Azure Stack.
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
ms.date: 10/11/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: fbb51d8dc3b1ea4c6b34120e8fe35474ae949cf2
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116920"
---
# <a name="troubleshoot-your-deployment-to-kubernetes-to-azure-stack"></a>Felsöka din distribution till Kubernetes i Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

> [!Note]  
> Kubernetes på Azure Stack är en förhandsversion.

I följande artikel tittar på felsökning av ditt Kubernetes-kluster. Du kan granska aviseringen för distribution och granska statusen för distributionen av element som krävs för distributionen. Du kan behöva samlar in distributionsloggarna från Azure Stack- eller Linux-datorer som är värdar för Kubernetes. Du kan dessutom behöva arbeta med Azure Stack-administratören om du vill hämta loggar från en administrativ slutpunkt.

## <a name="overview-of-deployment"></a>Översikt över distribution

Innan du får i steg du felsöker ditt kluster kan du granska distributionsprocessen för Azure Stack Kubernetes-kluster. Distributionen använder en lösningsmall för Azure Resource Manager-för att skapa de virtuella datorerna och installerar ACS-motor för klustret.

### <a name="deployment-workflow"></a>Arbetsflöde för distribution

Följande diagram visar den allmänna processen för att distribuera klustret.

![Distribuera Kubernetes-processen](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>Distributionssteg

1. Samlar in indataparametrar från marketplace-objekt.

    Ange de värden som du behöver du ställer in Kubernetes-kluster för inklusive:
    -  **Användarnamn** användarnamn för Linux-datorer som ingår i Kubernetes-klustret och DVM.
    -  **Offentlig SSH-nyckel** den nyckel som används för alla Linux-datorer som skapats som en del av Kubernetes-kluster och DVM godkännande
    -  **Tjänsten princip** ID. används av providern för Kubernetes Azure-molnet. Klient-ID som identifieras som program-ID när du skapade tjänstens huvudnamn. 
    -  **Klienthemlighet** de nycklar som du skapade när du skapar tjänstens huvudnamn.

2. Skapar distribution VM och tillägget för anpassat skript.
    -  Skapar distribution Linux VM med hjälp av marketplace-avbildning för Linux **Ubuntu Server 16.04-LTS**.
    -  Hämta och kör kundskriptstillägget från marketplace. Skriptet är den **anpassat skript för Linux 2.0**.
    -  Kör det anpassade skriptet DVM. Skriptet:
        1. Hämtar slutpunkt för galleri från Azure Resource Manager metadataslutpunkt.
        2. Hämtar active directory-resurs-ID från Azure Resource Manager metadataslutpunkt.
        3. Läser in API-modellen för ACS-motor.
        4. Distribuerar ACS-Engine till Kubernetes-klustret och sparar profilen för Azure Stack-molnet till `/etc/kubernetes/azurestackcloud.json`.
3. Skapar överordnade virtuella datorer.

    Hämtar och kör kundskriptstillägget.

4. Kör skriptet master.

    Skriptet:
    - Installerar etcd, Docker och Kubernetes resurser, till exempel kubelet. etcd är en distribuerad nyckelvärde som gör det möjligt att lagra data i ett kluster med datorer. Docker har stöd för utan ben operativsystemet på virtualizations kallas behållare. Kubelet är noden agenten som körs på varje nod i Kubernetes.
    - Ställer in etcd-tjänsten.
    - Ställer in Kubelet-tjänsten.
    - Startar kubelet. Detta omfattar följande:
        1. Startar API-tjänsten.
        2. Startar tjänsten Controller.
        3. Startar tjänsten Schemaläggaren.
5. Skapar virtuella datorer för agenten.

    Hämtar och kör kundskriptstillägget.

6. Kör skript för agenten. Det anpassade skriptet för agenten:
    - Installera etcd.
    - Konfigurera Kubelet-tjänst.
    - Ansluter till Kubernetes-klustret.

## <a name="steps-for-troubleshooting"></a>Anvisningar för att felsöka

Du kan samla in loggar på de virtuella datorerna som stöd för Kubernetes-klustret. Du kan också granska loggen för distribution. Du kan också behöva kontakta Azure Stack-administratören att kontrollera vilken version av Azure Stack som du använder och för att hämta loggar från Azure Stack som är relevanta för din distribution.

1. Granska den [Distributionsstatus](#review-deployment-status) och [hämta loggarna](#get-logs-from-a-vm) från huvudnoden i Kubernetes-klustret.
2. Du måste använda den senaste versionen av Azure Stack. Om du är osäker på din version av Azure Stack kan du kontakta administratören för Azure Stack. Kubernetes-kluster marketplace tiden 0.3.0-betaversionen kräver Azure Stack-version 1808 eller senare.
3.  Granska dina filer för virtuell dator skapas. Du kan ha uppstått följande problem:  
    - Den offentliga nyckeln kan vara ogiltig. Granska den nyckel som du har skapat.  
    - Skapa en virtuell dator kan ha utlöses ett internt fel eller utlöses ett fel när skapades. Fel kan ha orsakats av ett antal faktorer, bland annat kapacitetsbegränsningar för Azure Stack-prenumerationen.
    - Börjar det fullständigt kvalificerade domännamnet (fullständigt domännamn) för den virtuella datorn med dubbla prefixet?
4.  Om den virtuella datorn är **OK**, sedan utvärderar DVM. Om DVM har ett felmeddelande visas:

    - Den offentliga nyckeln kan vara ogiltig. Granska den nyckel som du har skapat.  
     - Du behöver kontakta Azure Stack-administratören om du vill hämta loggar för Azure Stack med hjälp av de Privilegierade slutpunkterna. Mer information finns i [Azure Stack diagnosverktyg](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics).
5. Om du har frågor om distributionen kan du publicera din fråga eller se om någon redan har besvarat frågan i den [Azure Stack-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="review-deployment-status"></a>Granska status för distribution

Du kan granska status för distribution när du distribuerar Kubernetes-klustret att granska eventuella problem.

1. Öppna den [Azure Stack-portalen](https://portal.local.azurestack.external).
2. Välj **resursgrupper**, och sedan väljer du namnet på resursgruppen användas när du distribuerar Kubernetes-klustret.
3. Välj **distributioner** och sedan den **distributionsnamn**.

    ![Felsökning](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  Läs fönstret felsökning. Alla distribuerade resurser innehåller följande information.
    
    | Egenskap  | Beskrivning |
    | ----     | ----        |
    | Resurs | Namnet på resursen. |
    | Typ | Resursprovidern och typ av resurs. |
    | Status | Status för objektet. |
    | Tidsstämpel | UTC-tidsstämpel av tiden. |
    | Åtgärdsinformation | Åtgärdsinformationen som till exempel resursprovidern som ingår i åtgärden, resurs-slutpunkten och namnet på resursen. |

    Varje objekt har en statusikon grön eller röd.

## <a name="get-logs-from-a-vm"></a>Hämta loggar från en virtuell dator

Du behöver ansluta till Virtuella huvuddatorn för klustret, öppna en bash-kommandotolk, och köra ett skript för att generera loggarna. Huvudservern finns i klusterresursgruppen och har namnet `k8s-master-<sequence-of-numbers>`. 

### <a name="prerequisites"></a>Förutsättningar

Du behöver en bash fråga din användning för att hantera Azure Stack på datorn. Använd bash för att köra skript som har åtkomst till loggarna. Du kan använda installeras med Git bash-Kommandotolken på en Windows-dator. Om du vill hämta den senaste versionen av git, se [git hämtar](https://git-scm.com/downloads).

### <a name="get-logs"></a>Hämta loggar

1. Öppna en bash-Kommandotolken. Om du använder git på en Windows-dator kan du öppna en bash-Kommandotolken från följande sökväg: `c:\programfiles\git\bin\bash.exe`.
2. Kör följande bash-kommandon:

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    sudo chmod 744 getkuberneteslogs.sh
    ```

    > [!Note]  
    > På Windows, behöver du inte köra `sudo` och kan bara använda `chmod 744 getkuberneteslogs.sh`.

3. I samma session, kör du följande kommando med parametrar uppdateras så att den matchar din miljö.

    ```Bash  
    ./getkuberneteslogs.sh --identity-file id_rsa --user azureuser --vmdhost 192.168.102.37
    ```

    Granska parametrarna och ange värden baserat på din miljö.
    | Parameter           | Beskrivning                                                                                                      | Exempel                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -i,--identity-fil | Filen RSA privat nyckel för att ansluta kubernetes master VM. Nyckeln måste börja med `-----BEGIN RSA PRIVATE KEY-----` | C:\data\privatekey.PEM                                                        |
    | h-,--värd          | Den offentliga IP-adressen eller det fullständigt kvalificerade domännamnet (FQDN) för Kubernetes-kluster huvudservern VM. VM-namnet börjar med `k8s-master-`.                       | IP: 192.168.102.37<br><br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u,--användare          | Användarnamn för Kubernetes-kluster huvudservern VM. Du kan ställa in det här namnet när du konfigurerar marketplace-objekt.                                                                    | azureuser                                                                     |
    | -d,--vmdhost       | Den offentliga IP-adressen eller det fullständiga Domännamnet för DVM. VM-namnet börjar med `vmd-`.                                                       | IP: 192.168.102.38<br><br>DNS: vmd-dnsk8-frog.local.cloudapp.azurestack.external |

   När du lägger till din parametervärdena kan det se ut ungefär som:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\secretsecret.pem" --user azureuser --vmdhost 192.168.102.37
     ```

    En lyckad körning skapar loggarna.

    ![Genererade loggar](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-generated-logs.png)


4. Hämta loggarna i mappar som skapas av kommandot. Kommandot skapar en ny mapp och tidsstämpeln den.
    - KubernetesLogs*YYYY-MM-DD-XX-XX-XX-XXX*
        - Dvmlogs
        - Acsengine-kubernetes-dvm.log

## <a name="next-steps"></a>Nästa steg

[Distribuera Kubernetes till Azure Stack](azure-stack-solution-template-kubernetes-deploy.md).

[Lägga till ett Kubernetes i Marketplace (för Azure Stack-operatör)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes på Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
