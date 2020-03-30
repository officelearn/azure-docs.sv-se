---
title: MATLAB-kluster på virtuella datorer
description: Använd virtuella Microsoft Azure-datorer för att skapa MATLAB Distributed Computing Server-kluster för att köra dina beräkningsintensiva parallella MATLAB-arbetsbelastningar
services: virtual-machines-windows
documentationcenter: ''
author: mscurrell
manager: gwallace
editor: ''
ms.assetid: e9980ce9-124a-41f1-b9ec-f444c8ea5c72
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: Windows
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: a2fb2479f5544b869b51e796085fcb4d0b76121a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74038147"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Skapa MATLAB Distributed Computing Server-kluster på virtuella Azure-datorer
Använd virtuella Microsoft Azure-datorer för att skapa ett eller flera MATLAB Distributed Computing Server-kluster för att köra dina beräkningsintensiva parallella MATLAB-arbetsbelastningar. Installera din MATLAB Distributed Computing Server programvara på en virtuell dator för att använda som en basavbildning och använda en Azure snabbstart mall eller Azure PowerShell-skript (tillgänglig på [GitHub)](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)för att distribuera och hantera klustret. Efter distributionen ansluter du till klustret för att köra dina arbetsbelastningar.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Om MATLAB och MATLAB Distributed Computing Server
[MATLAB-plattformen](https://www.mathworks.com/products/matlab/) är optimerad för att lösa tekniska och vetenskapliga problem. MATLAB-användare med storskaliga simuleringar och databehandlingsuppgifter kan använda MathWorks parallella datorsystem för att snabba upp sina beräkningsintensiva arbetsbelastningar genom att dra nytta av beräkningskluster och rutnätstjänster. [Med Toolbox](https://www.mathworks.com/products/parallel-computing/) för parallella datorer kan MATLAB-användare parallellisera program och dra nytta av processorer med flera kärnor, GPU:er och beräkningskluster. [MATLAB Distributed Computing Server](https://www.mathworks.com/products/distriben/) gör det möjligt för MATLAB-användare att använda många datorer i ett beräkningskluster.

Genom att använda virtuella Azure-datorer kan du skapa MATLAB Distributed Computing Server-kluster som har alla samma mekanismer tillgängliga för att skicka parallellt arbete som lokala kluster, till exempel interaktiva jobb, batchjobb, oberoende uppgifter och kommunikation Uppgifter. Att använda Azure tillsammans med MATLAB-plattformen har många fördelar jämfört med etablering och användning av traditionell lokal maskinvara: en rad storlekar på virtuella datorer, skapande av kluster på begäran så att du bara betalar för de beräkningsresurser du använder och förmåga att testa modeller i stor skala.  

## <a name="prerequisites"></a>Krav
* **Klientdator** – Du behöver en Windows-baserad klientdator för att kommunicera med Azure och MATLAB Distributed Computing Server-klustret efter distributionen.
* **Azure PowerShell** - Se [Hur du installerar och konfigurerar Azure PowerShell](/powershell/azure/overview) för att installera det på klientdatorn.
* **Azure-prenumeration** – Om du inte har en prenumeration kan du skapa ett [kostnadsfritt konto på](https://azure.microsoft.com/free/) bara några minuter. För större kluster bör du överväga en prenumeration per användning eller andra köpalternativ.
* **vCPUs-kvot** - Du kan behöva öka vCPU-kvoten för att distribuera ett stort kluster eller mer än ett MATLAB Distributed Computing Server-kluster. Om du vill öka en kvot [öppnar du en kundsupportbegäran online](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) utan kostnad.
* **MATLAB, Parallel Computing Toolbox och MATLAB Distributed Computing Server licenser** - Skripten förutsätter att [MathWorks Hosted License Manager](https://www.mathworks.com/help/install/license-management.html) används för alla licenser.  
* **MATLAB Distributed Computing Server programvara** - Kommer att installeras på en virtuell dator som kommer att användas som bas VM-avbildning för klustret virtuella datorer.

## <a name="high-level-steps"></a>Steg på hög nivå
Om du vill använda virtuella Azure-datorer för dina MATLAB Distributed Computing Server-kluster krävs följande steg på hög nivå. Detaljerade instruktioner finns i dokumentationen som medföljer snabbstartsmallen och skripten på [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Skapa en grundläggande VM-avbildning**  

   * Ladda ner och installera MATLAB Distributed Computing Server programvara på denna virtuella dator.

     > [!NOTE]
     > Denna process kan ta ett par timmar, men du behöver bara göra det en gång för varje version av MATLAB du använder.   
     >
     >
2. **Skapa ett eller flera kluster**  

   * Använd det medföljande PowerShell-skriptet eller använd snabbstartsmallen för att skapa ett kluster från grundavbildningen av virtuella datorer.   
   * Hantera kluster med det medföljande PowerShell-skriptet som gör att du kan lista, pausa, återuppta och ta bort kluster.

## <a name="cluster-configurations"></a>Klusterkonfigurationer
För närvarande kan skriptet och mallen för kluster skapa dig för att skapa en enda MATLAB Distributed Computing Server-topologi. Om du vill kan du skapa ett eller flera ytterligare kluster, där varje kluster har olika antal virtuella arbets- och arbetsdlar, med hjälp av olika vm-storlekar och så vidare.

### <a name="matlab-client-and-cluster-in-azure"></a>MATLAB-klient och kluster i Azure
MATLAB-klientnoden, MATLAB Job Scheduler-noden och MATLAB Distributed Computing Server -noderna för "arbetare" är alla konfigurerade som virtuella Azure-datorer i ett virtuellt nätverk, som visas i följande bild.


* Om du vill använda klustret ansluter du via Fjärrskrivbord till klientnoden. Klientnoden kör MATLAB-klienten.
* Klientnoden har en filresurs som kan nås av alla arbetare.
* MathWorks Hosted License Manager används för licenskontroller för alla MATLAB-program.
* Som standard skapas en MATLAB Distributed Computing Server-arbetare per vCPU på arbets-VMs, men du kan ange valfritt nummer.

## <a name="use-an-azure-based-cluster"></a>Använda ett Azure-baserat kluster
Precis som med andra typer av MATLAB Distributed Computing Server-kluster måste du använda klusterprofilhanteraren i MATLAB-klienten (på klientens virtuella dator) för att skapa en MATLAB Job Scheduler-klusterprofil.

![Hanteraren för klusterprofil](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Nästa steg
* Detaljerade instruktioner för att distribuera och hantera MATLAB Distributed Computing Server-kluster i Azure finns i [GitHub-databasen](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) som innehåller mallar och skript.
* Gå till [MathWorks webbplats](https://www.mathworks.com/) för detaljerad dokumentation för MATLAB och MATLAB Distributed Computing Server.
