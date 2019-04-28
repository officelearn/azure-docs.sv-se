---
title: Vanliga molntjänsthantering | Microsoft Docs
description: Lär dig hantera molntjänster i Azure-portalen. De här exemplen använder Azure portal.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: cb218ad9-77d4-4149-83db-71159c00767e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: d3d1ae759f0f3fa5edd417da61f1fa50b5d9cde7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61433964"
---
# <a name="manage-cloud-services-in-the-azure-portal"></a>Hantera molntjänster i Azure portal
I den **molntjänster** område i Azure-portalen kan du:

* Uppdatera en tjänstroll eller en distribution.
* Flytta upp en stegvis distribution till produktion.
* Länka resurser i din molntjänst så att du kan se resursberoenden och skala resurser tillsammans.
* Ta bort en molntjänst eller en distribution.

Läs mer om hur du skala Molntjänsten [konfigurera automatisk skalning för en molntjänst i portalen](cloud-services-how-to-scale-portal.md).

## <a name="update-a-cloud-service-role-or-deployment"></a>Uppdatera en molntjänstroll eller distribution
Om du vill uppdatera programkod för din molntjänst kan du använda **uppdatera** på bladet cloud service. Du kan uppdatera en roll eller alla roller. Om du vill uppdatera, kan du ladda upp ett nytt tjänstpaket eller tjänstkonfigurationsfilen.

1. I den [Azure-portalen][Azure portal], den molntjänst som du vill uppdatera. Det här steget öppnas bladet cloud service-instans.

2. På bladet välj **uppdatering**.

    ![Knappen Uppdatera](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Uppdatera distributionen med en ny tjänsten paketfil (.cspkg) och tjänstkonfigurationsfilen (.cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Du kan också uppdatera lagringskontot och distributionsetiketten.

5. Om några roller har endast en rollinstans, väljer du den **distribuera även om en eller flera roller innehåller en enda instans** kryssrutan för att aktivera uppgraderingen kan fortsätta.

    Azure kan garantera endast serviceavtal på 99,95% tjänstens tillgänglighet under en cloud service-uppdatering om varje roll har minst två rollinstanser (virtuella datorer). Med två rollinstanser bearbetar klientbegäranden av en virtuell dator medan den andra har uppdaterats.

6. Välj den **starta distribution** kryssrutan för att installera uppdateringen efter överföringen av paketet har slutförts.

7. Välj **OK** att börja uppdatera tjänsten.

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Växla distributioner för att flytta upp en stegvis distribution till produktion
När du vill distribuera en ny version av en tjänst i molnet, steg och testa din nya version i din cloud service mellanlagrings environment. Använd **växla** växla URL: er som de båda distributionerna behandlas och flytta upp en ny version till produktion.

Du kan växla distributioner från den **molntjänster** sida eller instrumentpanelen.

1. I den [Azure-portalen][Azure portal], den molntjänst som du vill uppdatera. Det här steget öppnas bladet cloud service-instans.

2. På bladet välj **växla**.

    ![Cloud Services växla knappen](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Följande bekräftelsefråga öppnas:

    ![Cloud Services Swap](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. När du har kontrollerat distributionsinformationen Välj **OK** växlar distributioner.

    Distribution av växlingen sker snabbt eftersom det enda som ändras är de virtuella IP-adresserna (VIP) för distributioner.

    Du kan ta bort tillfälliga distributionen när du har kontrollerat att din Produktionsdistribution fungerar som förväntat för att spara beräkningskostnader.

### <a name="common-questions-about-swapping-deployments"></a>Vanliga frågor om växlar distributioner

**Vilka är kraven för växlar distributioner?**

Det finns två viktiga förutsättningar för en lyckad distribution växling:

- Om du vill använda en statisk IP-adress för din produktionsplatsen kan reservera du en för din mellanlagringsplatsen. Annars misslyckas växlingen.

- Alla instanser av dina roller måste köras innan du kan utföra växlingen. Du kan kontrollera status för dina instanser på den **översikt** -bladet i Azure-portalen. Du kan också använda den [Get-AzureRole](/powershell/module/servicemanagement/azure/get-azurerole?view=azuresmps-3.7.0) i Windows PowerShell.

Observera att uppdateringar av gäst-OS och tjänsten återställning operations också orsaka distribution växlingar misslyckas. Mer information finns i [Felsök distributionsproblem med cloud service](cloud-services-troubleshoot-deployment-problems.md).

**Medför en växling på grund av mitt program? Hur ska jag hantera den?**

Enligt beskrivningen i föregående avsnitt, är en distribution växling vanligtvis snabbt eftersom det är bara en konfigurationsändring i Azure-belastningsutjämnaren. I vissa fall kan ta det 10 eller fler sekunder och resultatet i tillfälliga anslutningsfel. Om du vill begränsa påverkan på dina kunder att överväga att implementera [logik för omprövning av klienten](../best-practices-retry-general.md).

## <a name="delete-deployments-and-a-cloud-service"></a>Ta bort distributioner och en tjänst i molnet
Innan du kan ta bort en tjänst i molnet, måste du ta bort varje befintlig distribution.

Du kan ta bort tillfälliga distributionen när du har kontrollerat att din Produktionsdistribution fungerar som förväntat för att spara beräkningskostnader. Du debiteras för beräkningskostnaderna för distribuerade rollinstanser som inte är igång.

Använd följande procedur för att ta bort en distribution eller Molntjänsten.

1. I den [Azure-portalen][Azure portal], den molntjänst som du vill ta bort. Det här steget öppnas bladet cloud service-instans.

2. På bladet välj **ta bort**.

    ![Cloud Services ta bort-knappen](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Om du vill ta bort hela Molntjänsten, Välj den **Cloud service och dess distributioner** markerar du kryssrutan. Eller så kan du välja antingen den **Produktionsdistribution** eller **mellanlagringsdistribution** markerar du kryssrutan.

    ![Cloud Services Delete](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Välj **ta bort** längst ned på sidan.

5. Om du vill ta bort Molntjänsten, Välj **ta bort molntjänst**. Vid bekräftelse, Välj **Ja**.

> [!NOTE]
> När en molntjänst tas bort och utförlig övervakning är konfigurerad, måste du ta bort data manuellt från ditt lagringskonto. Information om var du hittar mått-tabeller finns i [introduktion till molnbaserad tjänstövervakning](cloud-services-how-to-monitor.md).


## <a name="find-more-information-about-failed-deployments"></a>Mer information om misslyckade distributioner
Den **översikt** bladet har ett statusfält överst. När du väljer fältet, ett nytt blad öppnas och visar den felinformation. Om distributionen inte innehåller några fel, är bladet information tomt.

![Översikt över cloud Services](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Nästa steg
* [Allmän konfiguration för din molntjänst](cloud-services-how-to-configure-portal.md).
* Lär dig hur du [distribuera en molntjänst](cloud-services-how-to-create-deploy-portal.md).
* Konfigurera en [domännamn](cloud-services-custom-domain-name-portal.md).
* Konfigurera [SSL-certifikat](cloud-services-configure-ssl-certificate-portal.md).
