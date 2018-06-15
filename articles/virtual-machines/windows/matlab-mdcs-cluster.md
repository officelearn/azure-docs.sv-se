---
title: MATLAB kluster på virtuella datorer | Microsoft Docs
description: Använda Microsoft Azure-datorer för att skapa MATLAB distribuerade Computing-serverkluster för att köra beräkningsintensiva parallella MATLAB arbetsbelastningar
services: virtual-machines-windows
documentationcenter: ''
author: mscurrell
manager: jeconnoc
editor: ''
ms.assetid: e9980ce9-124a-41f1-b9ec-f444c8ea5c72
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Windows
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: 695833fb12c0c7a130e98fe9b3bdfa502672ab29
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
ms.locfileid: "30914926"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Skapa MATLAB distribuerad datoranvändning serverkluster på Azure Virtual Machines
Använda Microsoft Azure-datorer för att skapa en eller flera MATLAB distribuerade Computing serverkluster för att köra beräkningsintensiva parallella MATLAB arbetsbelastningar. Installera programvaran MATLAB Computing Server för distribuerad på en virtuell dator att använda som en grundläggande bild och använda en mall för Azure quickstart eller Azure PowerShell-skript (tillgänglig på [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) att distribuera och hantera klustret. Ansluta till klustret för att köra din nätverksbelastning efter distributionen.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Om MATLAB och MATLAB distribuerad datoranvändning Server
Den [MATLAB](http://www.mathworks.com/products/matlab/) plattformen är optimerad för att lösa problem med teknisk och vetenskaplig. MATLAB användare med stora simulering och databearbetningsuppgifter kan använda MathWorks parallell databearbetning produkter påskynda sina beräkningsintensiva arbetsbelastningar genom att dra nytta av beräkningskluster och rutnätet tjänster. [Parallell Computing verktygslådan](http://www.mathworks.com/products/parallel-computing/) användarna MATLAB parallelize program och dra nytta av processorer med flera kärnor, GPU-kort, och beräkningsklustren. [MATLAB Computing Server för distribuerad](http://www.mathworks.com/products/distriben/) MATLAB användarna kan använda många datorer i ett beräkningskluster.

Du kan skapa MATLAB distribuerad datoranvändning serverkluster som har samma metoder som är tillgängliga att skicka parallella arbete som lokala kluster, till exempel interaktiva jobb, batchjobb, oberoende uppgifter och kommunicerar med hjälp av Azure virtuella datorer aktiviteter. Använda Azure tillsammans med plattformens MATLAB har många fördelar jämfört med att etablera och med hjälp av traditionella lokala maskinvara: en uppsättning virtuella storlekar, skapa kluster på begäran så att du betalar bara för de beräkningsresurser som du använder, och möjlighet att testa modeller i större skala.  

## <a name="prerequisites"></a>Förutsättningar
* **Klientdatorn** -behöver du en Windows-klientdator att kommunicera med Azure och MATLAB distribuerad datoranvändning serverklustret efter distributionen.
* **Azure PowerShell** -finns [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) att installera den på din klientdator.
* **Azure-prenumeration** -om du inte har en prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/) på bara några minuter. Överväg att en prenumeration med användningsbaserad betalning eller andra köpalternativ för större kluster.
* **vCPUs kvot** – du kan behöva öka kvoten vCPU för att distribuera ett kluster för stora eller mer än en MATLAB distribuerad datoranvändning serverkluster. Att öka kvoten [öppna en supportbegäran online customer](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) utan kostnad.
* **MATLAB, parallella Computing verktygslådan och MATLAB Computing Server för distribuerad licenser** -skripten anta att den [MathWorks finns Licenshanteraren](http://www.mathworks.com/products/parallel-computing/mathworks-hosted-license-manager/) används för alla licenser.  
* **MATLAB distribuerad datoranvändning serverprogramvara** -kommer att installeras på en virtuell dator som ska användas som bas VM-avbildning för virtuella datorer.

## <a name="high-level-steps"></a>Hög nivå steg
Om du vill använda Azure virtuella datorer för din MATLAB distribuerad datoranvändning serverkluster, krävs följande anvisningar. Detaljerade anvisningar finns i dokumentationen som medföljer den quickstart mallen och skripten på [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Skapa en grundläggande VM-avbildning**  

   * Hämta och installera MATLAB Computing Server för distribuerad programvara på den här virtuella datorn.

     > [!NOTE]
     > Den här processen kan ta några timmar, men du behöver bara göra det när du använder för varje version av MATLAB.   
     >
     >
2. **Skapa ett eller flera kluster**  

   * Använd det angivna PowerShell-skriptet eller mallen quickstart för att skapa ett kluster från basavbildningen VM.   
   * Hantera kluster med hjälp av angivna PowerShell-skript som gör att du kan visa, pausa och återuppta och ta bort kluster.

## <a name="cluster-configurations"></a>Klusterkonfigurationer
För närvarande kan kluster Skapandeskriptet och mallen du skapa en enda MATLAB Computing Server för distribuerad topologi. Om du vill skapa en eller flera ytterligare kluster med varje kluster med ett annat antal worker virtuella datorer, med olika storlekar på VM, och så vidare.

### <a name="matlab-client-and-cluster-in-azure"></a>MATLAB klient och kluster i Azure
MATLAB klientnod, MATLAB jobbschemat nod och MATLAB Computing Server för distribuerad ”” arbetarnoder är alla konfigurerade som virtuella Azure-datorer i ett virtuellt nätverk som visas i följande bild.


* Anslut med fjärrskrivbord till klientnoden om du vill använda i klustret. Klientnoden kör MATLAB-klienten.
* Klientnoden har en filresurs som kan nås av alla anställda.
* MathWorks finns License Manager används för licens-kontroller för alla MATLAB program.
* Som standard skapas en MATLAB Computing Server för distribuerad arbetare per vCPU på worker virtuella datorer, men du kan ange ett tal.

## <a name="use-an-azure-based-cluster"></a>Använda ett Azure-baserade kluster
Som med andra typer av MATLAB distribuerad datoranvändning serverkluster, måste du använda Klusterhanteraren för profilen i MATLAB klienten (om klienten VM) att skapa en profil för MATLAB jobbschemat klustret.

![Hanteraren för profil](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Nästa steg
* Detaljerade anvisningar om hur du distribuerar och hanterar MATLAB distribuerad datoranvändning Server-kluster i Azure finns i [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) databasen som innehåller mallar och skript.
* Gå till den [MathWorks plats](http://www.mathworks.com/) mer detaljerad dokumentation för MATLAB och MATLAB distribuerad datoranvändning Server.
