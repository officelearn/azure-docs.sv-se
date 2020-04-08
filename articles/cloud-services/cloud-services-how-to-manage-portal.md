---
title: Vanliga molntjänsthanteringsuppgifter | Microsoft-dokument
description: Läs om hur du hanterar Molntjänster i Azure-portalen. Dessa exempel använder Azure-portalen.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: 80481bc11933b0404079221f23b5054024f00acb
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811365"
---
# <a name="manage-cloud-services-in-the-azure-portal"></a>Hantera molntjänster i Azure-portalen
I området **Molntjänster** på Azure-portalen kan du:

* Uppdatera en tjänstroll eller en distribution.
* Befordra en stegvis distribution till produktion.
* Länka resurser till molntjänsten så att du kan se resursberoenden och skala resurserna tillsammans.
* Ta bort en molntjänst eller en distribution.

Mer information om hur du skalar molntjänsten finns i [Konfigurera automatisk skalning för en molntjänst i portalen](cloud-services-how-to-scale-portal.md).

## <a name="update-a-cloud-service-role-or-deployment"></a>Uppdatera en molntjänstroll eller distribution
Om du behöver uppdatera programkoden för molntjänsten använder du **Uppdatera** på molntjänstbladet. Du kan uppdatera en enskild roll eller alla roller. Om du vill uppdatera kan du ladda upp ett nytt tjänstpaket eller tjänstkonfigurationsfil.

1. Välj den molntjänst som du vill uppdatera i [Azure-portalen.][Azure portal] Det här steget öppnar instansbladet för molntjänst.

2. Välj **Uppdatera**på bladet .

    ![Knappen Uppdatera](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Uppdatera distributionen med en ny tjänstpaketfil (.cspkg) och tjänstkonfigurationsfilen (.cscfg).

    ![Uppdateradeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Du kan också uppdatera lagringskontot och distributionsetiketten.

5. Om några roller bara har en rollinstans markerar du kryssrutan **Distribuera även om en eller flera roller innehåller en enda instans** för att uppgraderingen ska kunna fortsätta.

    Azure kan garantera endast 99,95 procent tjänsttillgänglighet under en uppdatering av molntjänsten om varje roll har minst två rollinstanser (virtuella datorer). Med två rollinstanser bearbetar en virtuell dator klientbegäranden medan den andra uppdateras.

6. Markera kryssrutan **Starta distribution om** du vill använda uppdateringen när överföringen av paketet har slutförts.

7. Välj **OK** för att börja uppdatera tjänsten.

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Byt distributioner för att främja en stegvis distribution till produktion
När du bestämmer dig för att distribuera en ny version av en molntjänst, fasa och testa din nya version i din molntjänst mellanlagringsmiljö. Använd **Swap** för att växla webbadresser som de två distributionerna adresseras efter och befordra en ny version till produktion.

Du kan byta distributioner från cloud **services-sidan** eller instrumentpanelen.

1. Välj den molntjänst som du vill uppdatera i [Azure-portalen.][Azure portal] Det här steget öppnar instansbladet för molntjänst.

2. Välj **Byt på**bladet .

    ![Knappen Byta molntjänster](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Följande bekräftelsemeddelande öppnas:

    ![Byte av molntjänster](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. När du har verifierat distributionsinformationen väljer du **OK** för att byta distributionerna.

    Distributionsswatningen sker snabbt eftersom det enda som ändras är de virtuella IP-adresserna (VIP) för distributionerna.

    Om du vill spara beräkningskostnader kan du ta bort mellanlagringsdistributionen när du har kontrollerat att produktionsdistributionen fungerar som förväntat.

### <a name="common-questions-about-swapping-deployments"></a>Vanliga frågor om att byta distributioner

**Vilka är förutsättningarna för att byta distributioner?**

Det finns två viktiga förutsättningar för en lyckad distributionsswap:

- Om du vill använda en statisk IP-adress för din produktionsplats måste du också reservera en för din mellanlagringsplats. Annars misslyckas bytet.

- Alla instanser av dina roller måste köras innan du kan utföra bytet. Du kan kontrollera status för dina instanser på **bladet Översikt** för Azure-portalen. Du kan också använda kommandot [Get-AzureRole](/powershell/module/servicemanagement/azure/get-azurerole?view=azuresmps-3.7.0) i Windows PowerShell.

Observera att gästoperativsystemsuppdateringar och tjänstläkningsåtgärder också kan orsaka att distributionsswappar misslyckas. Mer information finns i [Felsöka distributionsproblem för molntjänster](cloud-services-troubleshoot-deployment-problems.md).

**Har en växling medför driftstopp för min ansökan? Hur ska jag hantera det?**

Som beskrivs i föregående avsnitt är en distributionsswap vanligtvis snabb eftersom det bara är en konfigurationsändring i Azure-belastningsutjämnaren. I vissa fall kan det ta 10 sekunder eller fler sekunder och resultera i tillfälliga anslutningsfel. Om du vill begränsa påverkan för dina kunder bör du överväga att implementera [logik för återförsök för klienter](../best-practices-retry-general.md).

## <a name="delete-deployments-and-a-cloud-service"></a>Ta bort distributioner och en molntjänst
Innan du kan ta bort en molntjänst måste du ta bort varje befintlig distribution.

Om du vill spara beräkningskostnader kan du ta bort mellanlagringsdistributionen när du har kontrollerat att produktionsdistributionen fungerar som förväntat. Du debiteras för beräkningskostnader för distribuerade rollinstanser som stoppas.

Använd följande procedur för att ta bort en distribution eller din molntjänst.

1. Välj den molntjänst som du vill ta bort i [Azure-portalen.][Azure portal] Det här steget öppnar instansbladet för molntjänst.

2. Välj **Ta bort**på bladet .

    ![Knappen Ta bort molntjänster](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Om du vill ta bort hela molntjänsten markerar du kryssrutan **Molntjänsten och dess distributioner.** Du kan också välja antingen **distributionen Produktion** eller distributionen **för mellanlagring.**

    ![Ta bort molntjänster](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Välj **Ta bort** längst ned.

5. Om du vill ta bort molntjänsten väljer du **Ta bort molntjänsten**. Välj sedan **Ja**vid bekräftelseprompten .

> [!NOTE]
> När en molntjänst tas bort och detaljerad övervakning konfigureras måste du ta bort data manuellt från ditt lagringskonto. Information om var du hittar statistiktabellerna finns i [Introduktion till molntjänstövervakning](cloud-services-how-to-monitor.md).


## <a name="find-more-information-about-failed-deployments"></a>Hitta mer information om misslyckade distributioner
**Bladet Översikt** har ett statusfält högst upp. När du väljer stapeln öppnas ett nytt blad och visar eventuell felinformation. Om distributionen inte innehåller några fel är informationsbladet tomt.

![Översikt över molntjänster](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Nästa steg
* [Allmän konfiguration av molntjänsten](cloud-services-how-to-configure-portal.md).
* Lär dig hur du [distribuerar en molntjänst](cloud-services-how-to-create-deploy-portal.md).
* Konfigurera ett [eget domännamn](cloud-services-custom-domain-name-portal.md).
* Konfigurera [TLS/SSL-certifikat](cloud-services-configure-ssl-certificate-portal.md).



