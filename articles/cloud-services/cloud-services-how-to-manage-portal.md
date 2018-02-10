---
title: "Vanliga hanteringsuppgifter för cloud service | Microsoft Docs"
description: "Lär dig mer om att hantera molntjänster i Azure-portalen. De här exemplen använder Azure-portalen."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: cb218ad9-77d4-4149-83db-71159c00767e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: e60bf5c82e68d49abaa44d80ac9fafba2d8265da
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="manage-cloud-services-in-the-azure-portal"></a>Hantera molntjänster i Azure-portalen
I den **molntjänster** område i Azure-portalen kan du:

* Uppdatera en rolltjänst eller en distribution.
* Flytta upp en stegvis distribution till produktionen.
* Länka resurser till Molntjänsten så att du kan se resursberoenden och skala resurser tillsammans.
* Ta bort en molnbaserad tjänst eller en distribution.

Läs mer om hur du skala Molntjänsten [konfigurera automatisk skalning för en tjänst i molnet i portalen](cloud-services-how-to-scale-portal.md).

## <a name="update-a-cloud-service-role-or-deployment"></a>Uppdatera en rolltjänst för molnet eller distribution
Om du behöver uppdatera programkoden för din molntjänst använder **uppdatera** på bladet cloud service. Du kan uppdatera en roll eller alla roller. Om du vill uppdatera måste överföra du ett nytt tjänstpaket eller tjänstkonfigurationsfilen.

1. I den [Azure-portalen][Azure portal], Välj den molntjänst som du vill uppdatera. Det här steget öppnas bladet cloud service-instans.

2. På bladet välj **uppdatering**.

    ![Uppdateringsknappen](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Uppdatera distributionen med en ny servicepaketfil (.cspkg) och tjänstekonfigurationsfilen (.cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Du kan också uppdatera lagringskontot och distributionsetiketten.

5. Om några roller har endast en rollinstans, väljer du den **distribuera även om en eller flera roller innehåller en enda instans** kryssrutan för att aktivera uppgraderingen kan fortsätta.

    Azure kan garantera endast tjänsttillgänglighet 99,95% under en molnet tjänstuppdatering om rollerna har minst två rollinstanser (virtuella datorer). Med två rollinstanser bearbetar klientbegäranden av en virtuell dator medan den andra uppdateras.

6. Välj den **starta distribution** kryssrutan för att installera uppdateringen när uppladdningen av paketet är klar.

7. Välj **OK** att starta tjänsten.

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Växla distributioner för att flytta upp en stegvis distribution till produktion
När du vill distribuera en ny version av en tjänst i molnet, steg och testa den nya versionen i din fristående molntjänstmiljö. Använd **växla** växla URL: er som två distributioner behandlas och befordra en ny version till produktionen.

Du kan växla distributioner från den **molntjänster** eller instrumentpanelen.

1. I den [Azure-portalen][Azure portal], Välj den molntjänst som du vill uppdatera. Det här steget öppnas bladet cloud service-instans.

2. På bladet välj **växla**.

    ![Cloud Services växla knappen](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Följande bekräftelse öppnas:

    ![Cloud Services Swap](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. När du har kontrollerat distributionsinformationen Välj **OK** att växla distributioner.

    Distribution växlingen sker snabbt eftersom det enda som ändrar de virtuella IP-adresserna (VIP) för distributioner.

    Ta bort tillfälliga distributionen för att spara kostnader för beräkning, när du har kontrollerat att din Produktionsdistribution fungerar som förväntat.

### <a name="common-questions-about-swapping-deployments"></a>Vanliga frågor om växling distributioner

**Vilka är kraven för att byta distributioner?**

Det finns två viktiga krav för en lyckad distribution växling:

- Om du vill använda en statisk IP-adress för din produktionsplatsen måste du reservera en för din mellanlagringsplatsen. Annars misslyckas växlingen.

- Alla instanser av dina roller måste köras innan du kan utföra växlingen. Du kan kontrollera statusen för dina instanser på den **översikt** bladet i Azure-portalen. Du kan också använda den [Get-AzureRole](/powershell/module/azure/get-azurerole?view=azuresmps-3.7.0) i Windows PowerShell.

Observera att gäst OS-uppdateringar och återställning operations också kan orsaka växlingar distributionen misslyckas. Mer information finns i [distribution felsöka cloud service](cloud-services-troubleshoot-deployment-problems.md).

**Medför en växling driftstopp för Mina program? Hur får jag hantera den?**

Enligt beskrivningen i föregående avsnitt, är en distribution växling vanligtvis snabb eftersom det är bara en konfigurationsändring i Azure belastningsutjämnaren. I vissa fall kan ta det 10 eller flera sekunder och resultatet i tillfälliga anslutningsfel. Om du vill begränsa effekten för kunderna att överväga att implementera [klienten logik](../best-practices-retry-general.md).

## <a name="delete-deployments-and-a-cloud-service"></a>Ta bort distributioner och en tjänst i molnet
Innan du kan ta bort en molnbaserad tjänst, måste du ta bort alla befintliga distributionen.

Ta bort tillfälliga distributionen för att spara kostnader för beräkning, när du har kontrollerat att din Produktionsdistribution fungerar som förväntat. Du debiteras för beräkning kostnader för distribuerade rollinstanser som har stoppats.

Använd följande procedur för att ta bort en distribution eller tjänst i molnet.

1. I den [Azure-portalen][Azure portal], Välj den molntjänst som du vill ta bort. Det här steget öppnas bladet cloud service-instans.

2. På bladet välj **ta bort**.

    ![Cloud Services ta bort knappen](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Om du vill ta bort hela Molntjänsten, Välj den **Cloud service och dess distributioner** kryssrutan. Du kan också välja antingen den **Produktionsdistribution** eller **mellanlagring av distribution** kryssrutan.

    ![Ta bort cloud-tjänster](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Välj **ta bort** längst ned.

5. Om du vill ta bort Molntjänsten, Välj **ta bort Molntjänsten**. I bekräftelsedialogrutan väljer **Ja**.

> [!NOTE]
> När en tjänst i molnet har tagits bort och utförlig övervakning är konfigurerad, måste du ta bort data manuellt från ditt lagringskonto. Information om var du hittar tabellerna mått finns [introduktion till molnet tjänstövervakning](cloud-services-how-to-monitor.md).


## <a name="find-more-information-about-failed-deployments"></a>Mer information om misslyckade installationer
Den **översikt** bladet har ett statusfält längst upp. När du markerar fältet, ett nytt blad öppnas och visar den felinformation. Om distributionen inte innehåller några fel, är bladet information tomt.

![Översikt över cloud Services](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Nästa steg
* [Allmän konfiguration av Molntjänsten](cloud-services-how-to-configure-portal.md).
* Lär dig hur du [distribuera en tjänst i molnet](cloud-services-how-to-create-deploy-portal.md).
* Konfigurera en [domännamn](cloud-services-custom-domain-name-portal.md).
* Konfigurera [SSL-certifikat](cloud-services-configure-ssl-certificate-portal.md).
