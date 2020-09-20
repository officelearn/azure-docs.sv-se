---
title: Aktivera din Azure VMware-lösnings resurs
description: Lär dig hur du skickar en support förfrågan för att aktivera din Azure VMware-lösnings resurs. Du kan också begära fler noder i ditt befintliga privata moln i Azure VMware-lösningen.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 72a7aca97067ce4e9ed0e901e4016c82b3549eb1
ms.sourcegitcommit: 0fd1f3fe7817ad44d878d580ec167e1508051795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2020
ms.locfileid: "90817906"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Så här aktiverar du Azure VMware-lösnings resurser
Lär dig hur du skickar en support förfrågan för att aktivera din Azure VMware-lösnings resurs. Du kan också begära fler noder i ditt befintliga privata moln i Azure VMware-lösningen.

## <a name="eligibility-criteria"></a>Behörighetskrav

* Du behöver ett [Azure-Enterprise-avtal (EA)](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-agreements) med Microsoft.
* Du behöver ett Azure-konto i en Azure-prenumeration.


## <a name="enable-azure-vmware-solution-resource"></a>Aktivera Azure VMware-lösningen Resource
Innan du skapar din Azure VMware-lösnings resurs måste du skicka in ett support ärende om du vill att dina noder ska tilldelas. När support teamet har tagit emot din begäran tar det upp till fem arbets dagar för att bekräfta din begäran och allokera noderna. Om du har ett befintligt privat moln i Azure VMware-lösningen och vill att fler noder ska tilldelas, går du igenom samma process.


1. I Azure Portal, under **Hjälp + Support**, skapa en **[ny supportbegäran](https://rc.portal.azure.com/#create/Microsoft.Support)** och ange följande information för biljetten:
   - **Typ av problem:** Produkt
   - **Prenumeration:** Välj din prenumeration
   - **Tjänst:** Alla tjänster > Azure VMware-lösning
   - **Resurs:** Allmän fråga 
   - **Sammanfattning:** Behöver kapacitet
   - **Problem typ:** Problem med kapacitets hantering
   - **Problem under typ:** Kund förfrågan om ytterligare värd kvot/-kapacitet

1. I **beskrivningen** av support ärendet på fliken **information** anger du:

   - POC eller produktion 
   - Regionsnamn
   - Antal noder
   - Annan information

   >[!NOTE]
   >Azure VMware-lösningen rekommenderar minst tre noder för att skapa ett privat moln och för redundans av N + 1-noder. 

1. Välj **Granska + skapa** för att skicka begäran.

   Det tar upp till fem arbets dagar för en support representant att bekräfta din begäran.

   >[!IMPORTANT] 
   >Om du redan har en befintlig Azure VMware-lösning, och du begär ytterligare noder, bör vi tänka på att vi behöver fem arbets dagar för att allokera noderna. 

1. Innan du kan etablera dina noder bör du kontrol lera att du registrerar resurs leverantören för **Microsoft. AVS** i Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Ytterligare sätt att registrera resurs leverantören finns i [Azure Resource providers och-typer](../azure-resource-manager/management/resource-providers-and-types.md).