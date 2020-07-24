---
title: MATLAB-kluster på virtuella datorer
description: Använd Microsoft Azure virtuella datorer för att skapa MATLAB-kluster för distribuerade dator servrar för att köra beräknings intensiva parallella MATLAB-arbetsbelastningar
author: mscurrell
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: a3f3dbd74ef74f091ca923f8c09680c6913ac300
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87074231"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Skapa kluster för MATLAB-distribuerade dator servrar på virtuella Azure-datorer
Använd Microsoft Azure virtuella datorer för att skapa ett eller flera MATLAB-kluster för distribuerade dator servrar för att köra beräknings intensiva parallella MATLAB-arbetsbelastningar. Installera ditt MATLAB-distribuerade program serverprogram på en virtuell dator som ska användas som en bas avbildning och Använd en Azure snabb starts mall eller ett Azure PowerShell skript (tillgängligt på [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) för att distribuera och hantera klustret. Efter distributionen ansluter du till klustret för att köra arbets belastningarna.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Om MATLAB och MATLAB-distribuerad dator server
[MATLAB](https://www.mathworks.com/products/matlab/) -plattformen är optimerad för att lösa tekniska och vetenskapliga problem. MATLAB-användare med storskaliga simuleringar och data bearbetnings uppgifter kan använda MathWorks Parallel Computing-produkter för att påskynda beräknings intensiva arbets belastningar genom att dra nytta av beräknings kluster och rutnäts tjänster. Med [verktyg för parallell dator användning](https://www.mathworks.com/products/parallel-computing/) kan MATLAB-användare parallellisera program och dra nytta av processorer med flera kärnor, GPU: er och beräknings kluster. [MATLAB-distribuerad dator server](https://www.mathworks.com/products/distriben/) gör det möjligt för MATLAB-användare att använda många datorer i ett beräknings kluster.

Genom att använda Azure Virtual Machines kan du skapa MATLAB-kluster för distribuerade dator servrar som har alla de metoder som är tillgängliga för att skicka parallella arbeten som lokala kluster, till exempel interaktiva jobb, batch-jobb, oberoende uppgifter och kommunikation av uppgifter. Att använda Azure tillsammans med MATLAB-plattformen har många fördelar jämfört med etablering och användning av traditionell lokal maskin vara: en uppsättning virtuella dator storlekar, skapandet av kluster på begäran, så att du bara betalar för de beräknings resurser du använder och möjligheten att testa modeller i stor skala.  

## <a name="prerequisites"></a>Förutsättningar
* **Klient dator** – du behöver en Windows-baserad klient dator för att kommunicera med Azure och det MATLAB-distribuerade dator Server klustret efter distributionen.
* **Azure PowerShell** – se [installera och konfigurera Azure PowerShell](/powershell/azure/) för att installera det på klient datorn.
* **Azure-prenumeration** – om du inte har en prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) på bara några minuter. För större kluster bör du överväga att betala per användning-prenumeration eller andra köp alternativ.
* **virtuella processorer-kvot** – du kan behöva öka vCPU-kvoten för att distribuera ett stort kluster eller mer än ett MATLAB-distribuerat dator Server kluster. Om du vill öka en kvot [öppnar du en kund support förfrågan online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) utan kostnad.
* **MATLAB, verktyg för parallell data behandling och MATLAB-distribuerade data behandlings servrar** – skripten förutsätter att den [MathWorks värdbaserade licens hanteraren](https://www.mathworks.com/help/install/license-management.html) används för alla licenser.  
* **MATLAB-distribuerad dator server-program** vara installeras på en virtuell dator som ska användas som bas avbildning för virtuella datorer för de virtuella datorerna i klustret.

## <a name="high-level-steps"></a>Steg på hög nivå
För att kunna använda Azure Virtual Machines för dina MATLAB-kluster med distribuerade dator servrar krävs följande steg på hög nivå. Detaljerade instruktioner finns i dokumentationen som följer snabb starts mal len och skripten på [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Skapa en bas avbildning av virtuell dator**  

   * Ladda ned och installera program vara från MATLAB-distribuerad dator på den här virtuella datorn.

     > [!NOTE]
     > Den här processen kan ta några timmar, men du behöver bara göra det en gång för varje version av MATLAB som du använder.   
     >
     >
2. **Skapa ett eller flera kluster**  

   * Använd det angivna PowerShell-skriptet eller Använd snabb starts mal len för att skapa ett kluster från den virtuella bas avbildningen.   
   * Hantera klustren med hjälp av det angivna PowerShell-skriptet så att du kan visa, pausa, återuppta och ta bort kluster.

## <a name="cluster-configurations"></a>Klusterkonfigurationer
För närvarande kan du skapa ett enda MATLAB-topologi för distribuerad dator server med skriptet och mallen för kluster skapande. Om du vill kan du skapa ett eller flera ytterligare kluster, där varje kluster har olika antal virtuella arbets datorer, med olika VM-storlekar och så vidare.

### <a name="matlab-client-and-cluster-in-azure"></a>MATLAB-klient och kluster i Azure
MATLAB-klienttjänsten, MATLAB-jobbschemaläggaren och MATLAB-distribuerade data behandlings serverns noder är konfigurerade som virtuella Azure-datorer i ett virtuellt nätverk, som du ser i följande bild.


* Om du vill använda klustret ansluter du via fjärr skrivbord till klient-noden. Klient-noden kör MATLAB-klienten.
* Noden klient har en fil resurs som kan användas av alla anställda.
* MathWorks-värdbaserad licens hanterare används för licens kontroller för alla MATLAB-program.
* Som standard skapas en virtuell dator med MATLAB-distribuerad data behandling per vCPU på arbets-VM: ar, men du kan ange vilken siffra som helst.

## <a name="use-an-azure-based-cluster"></a>Använd ett Azure-baserat kluster
Precis som med andra typer av MATLAB-kluster för distribuerade dator servrar måste du använda kluster profil hanteraren i MATLAB-klienten (på den virtuella klient datorn) för att skapa en kluster profil för MATLAB-jobb.

![Kluster profil hanterare](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Nästa steg
* Detaljerade anvisningar om hur du distribuerar och hanterar MATLAB-distribuerade dator kluster i Azure finns i [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) -lagringsplatsen som innehåller mallarna och skripten.
* Gå till [MathWorks-webbplatsen](https://www.mathworks.com/) om du vill ha detaljerad dokumentation för MATLAB-och MATLAB-distribuerad dator server.
