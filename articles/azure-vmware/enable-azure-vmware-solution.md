---
title: Aktivera din Azure VMware-lösnings resurs
description: Lär dig hur du skickar en support förfrågan för att aktivera din Azure VMware-lösnings resurs. Du kan också begära fler noder i ditt befintliga privata moln i Azure VMware-lösningen.
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: 7c805e9e622f55593ff1fbb72a355d233b7e3618
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576406"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Så här aktiverar du Azure VMware-lösnings resurser
Lär dig hur du skickar en support förfrågan för att aktivera din [Azure VMware-lösnings](introduction.md) resurs. Du kan också begära fler noder i ditt befintliga privata moln i Azure VMware-lösningen.

## <a name="eligibility-criteria"></a>Behörighetskrav

Du behöver ett Azure-konto i en Azure-prenumeration. Azure-prenumerationen måste vara kompatibel med något av följande kriterier:

* En prenumeration under ett [Azure-Enterprise-avtal (EA)](../cost-management-billing/manage/ea-portal-agreements.md) med Microsoft.
* En prenumeration på en moln lösnings leverantör (CSP) som hanteras i en Azure-plan.


## <a name="enable-azure-vmware-solution-for-ea-customers"></a>Aktivera Azure VMware-lösningen för EA-kunder
Innan du skapar din Azure VMware-lösnings resurs måste du skicka in ett support ärende om du vill att dina noder ska tilldelas. När support teamet har tagit emot din begäran tar det upp till fem arbets dagar för att bekräfta din begäran och allokera noderna. Om du har ett befintligt privat moln i Azure VMware-lösningen och vill att fler noder ska tilldelas, går du igenom samma process.


1. I Azure Portal, under **Hjälp + Support** , skapa en **[ny supportbegäran](https://rc.portal.azure.com/#create/Microsoft.Support)** och ange följande information för biljetten:
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

## <a name="enable-azure-vmware-solution-for-csp-customers"></a>Aktivera Azure VMware-lösningen för CSP-kunder 

Kryptografiproviders måste använda [Microsoft Partner Center](https://partner.microsoft.com) för att aktivera Azure VMware-lösningen för sina kunder. 

1. I **partner Center** väljer du **CSP** för att få åtkomst till avsnittet **kunder** .

   :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Microsoft Partner Center-kunder, region" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::

1. Välj din kund och välj sedan **Lägg till produkter**.

   :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Microsoft Partner Center" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::

1. Välj **Azure-plan** och välj sedan **Lägg till i kundvagn**. 

1. Granska och slutför de allmänna inställningarna för Azure plan-prenumerationen för din kund. Mer information finns i [dokumentationen till Microsoft Partner Center](https://docs.microsoft.com/partner-center/azure-plan-manage).

När du har konfigurerat Azure-planen och de nödvändiga RBAC-behörigheterna är på plats som en CSP, kommer du att använda en liknande procedur för att aktivera kvoten för en Azure plan-prenumeration. När den har lagts till i Azure-planen kan kunden eller partner administratören distribuera ett privat moln i Azure VMware-lösningen via Azure Portal. 

## <a name="next-steps"></a>Nästa steg

När du har aktiverat resursen för Azure VMware-lösningen och du har rätt nätverk på plats kan du [skapa ett privat moln](tutorial-create-private-cloud.md).