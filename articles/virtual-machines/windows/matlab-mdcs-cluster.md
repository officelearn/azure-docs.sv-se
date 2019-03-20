---
title: MATLAB-kluster på virtuella datorer | Microsoft Docs
description: Använd Microsoft Azure-datorer för att skapa MATLAB distribuerade Computing-serverkluster för att köra dina beräkningsintensiva parallella MATLAB arbetsbelastningar
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
ms.openlocfilehash: fd5ae375dff80c8b1179d2fd73566d07c5861e4a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58000338"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Skapa MATLAB distribuerad databehandling Server-kluster på Azure Virtual Machines
Använd Microsoft Azure-datorer för att skapa en eller flera MATLAB distribuerade Computing serverkluster för att köra dina beräkningsintensiva parallella MATLAB arbetsbelastningar. Installera din MATLAB databehandling Server för distribuerad programvara på en virtuell dator att använda som en grundläggande avbildning och använda en Azure-snabbstartsmall eller Azure PowerShell-skript (tillgängligt på [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) att distribuera och hantera klustret. Efter distributionen kan du ansluta till klustret för att köra arbetsbelastningar.

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Om MATLAB och MATLAB distribuerad databehandling Server
Den [MATLAB](https://www.mathworks.com/products/matlab/) plattform är optimerad för att lösa problem som tekniker och forskning. MATLAB-användare med storskaliga simuleringar och databearbetning kan använda MathWorks parallell databearbetning produkter påskynda sina beräkningsintensiva arbetsbelastningar genom att utnyttja beräkningskluster och rutnät tjänster. [Parallell databehandling verktygslådan](https://www.mathworks.com/products/parallel-computing/) användarna MATLAB parallellisera program och dra nytta av processorer med flera kärnor, GPU: er, och beräkningsklustren. [MATLAB databehandling Server för distribuerad](https://www.mathworks.com/products/distriben/) kan MATLAB-användare kan använda många datorer i ett beräkningskluster.

Du kan skapa MATLAB distribuerad databehandling Server-kluster som har samma metoder som är tillgängliga för att skicka parallellt arbete som lokala kluster, till exempel interaktiva jobb, batch-jobb, oberoende aktiviteter och kommunicerar med hjälp av Azure-datorer uppgifter. Använda Azure tillsammans med MATLAB-plattformen har många fördelar jämfört med etablering och med traditionell lokal maskinvara: ett intervall för virtuell dator storlekar: Skapa kluster på begäran så du betalar bara för de beräkningsresurser du använder, och möjlighet att testa modeller i hög skala.  

## <a name="prerequisites"></a>Förutsättningar
* **Klientdatorn** -du behöver en Windows-klientdator att kommunicera med Azure och MATLAB distribuerad databehandling serverklustret efter distributionen.
* **Azure PowerShell** – Se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) att installera den på klientdatorn.
* **Azure-prenumeration** -om du inte har en prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/) på bara några minuter. Överväg att en användningsbaserad prenumeration eller andra alternativ för större kluster.
* **virtuella processorer kvot** – du kan behöva öka vCPU-kvoten för att distribuera ett stort kluster eller flera MATLAB distribuerad databehandling Server-kluster. Att öka en kvot [öppna en supportbegäran online-kund](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) utan kostnad.
* **MATLAB, parallella databehandling verktygslådan och MATLAB distribuerad databehandling Server-licenser** -skripten förutsätts att den [MathWorks finns License Manager](https://www.mathworks.com/products/parallel-computing/mathworks-hosted-license-manager/) används för alla licenser.  
* **MATLAB distribuerad databehandling serverprogramvara** -kommer att installeras på en virtuell dator som ska användas som basavbildning för virtuell dator för virtuella datorer i klustret.

## <a name="high-level-steps"></a>Hög steg
Om du vill använda Azure-datorer för dina MATLAB distribuerad databehandling serverkluster, krävs följande anvisningar. Detaljerade anvisningar finns i dokumentationen som medföljer den quickstart-mallen och skripten på [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Skapa en grundläggande VM-avbildning**  

   * Hämta och installera MATLAB databehandling Server för distribuerad programvara på den här virtuella datorn.

     > [!NOTE]
     > Den här processen kan ta ett par timmar, men du behöver bara göra det när du använder för varje version av MATLAB.   
     >
     >
2. **Skapa en eller flera kluster**  

   * Använd det angivna PowerShell-skriptet eller Använd snabbstartsmallen för att skapa ett kluster från den grundläggande VM-avbildningen.   
   * Hantera kluster med hjälp av angivna PowerShell-skriptet som låter dig visa, pausa, återuppta och ta bort kluster.

## <a name="cluster-configurations"></a>Klusterkonfigurationer
För närvarande kan skriptet som skapar klustret och mall du skapa en enda MATLAB databehandling Server för distribuerad topologi. Om du vill skapa en eller flera ytterligare kluster med varje kluster med ett annat antal worker virtuella datorer med olika storlekar, och så vidare.

### <a name="matlab-client-and-cluster-in-azure"></a>MATLAB-klienten och -kluster i Azure
Den MATLAB klientnod och MATLAB jobbschemat noden MATLAB databehandling Server för distribuerad ”” arbetsnoder konfigureras alla som virtuella Azure-datorer i ett virtuellt nätverk enligt följande bild.


* Ansluta med Remote Desktop till klient-nod om du vill använda i klustret. MATLAB-klienten körs i klient-nod.
* Klient-nod har en filresurs som kan nås av alla arbeten.
* MathWorks finns License Manager används för licens-kontroller för alla MATLAB-programvara.
* Som standard en MATLAB databehandling Server för distribuerad arbetare per virtuell processor har skapats på worker virtuella datorer, men du kan ange ett tal.

## <a name="use-an-azure-based-cluster"></a>Använda en Azure-baserade kluster
Som med andra typer av MATLAB distribuerad databehandling Server-kluster kan behöva du använda Klusterhanteraren för profilen i MATLAB-klienten (på klienten VM) att skapa en profil för jobbschemat MATLAB-kluster.

![Profil för Klusterhanterare](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Nästa steg
* Detaljerade anvisningar om hur du distribuerar och hanterar MATLAB distribuerad databehandling Server-kluster i Azure finns i den [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) databasen som innehåller mallar och skript.
* Gå till den [MathWorks plats](https://www.mathworks.com/) detaljerad dokumentation för MATLAB och MATLAB distribuerad databehandling Server.
