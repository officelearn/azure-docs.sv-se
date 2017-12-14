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
ms.openlocfilehash: a06becda42541b22d3e090087dd96cd08c980f44
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="how-to-manage-cloud-services"></a>Så här hanterar du molntjänster
I den **molntjänster** område i Azure portal, du kan uppdatera en rolltjänst eller en distribution, befordra en stegvis distribution till produktion, länka resurser till din molntjänst så att du kan se resursberoenden och skala den resurser tillsammans, och ta bort ett moln tjänst eller en distribution.

Mer information om hur du skala Molntjänsten [här](cloud-services-how-to-scale-portal.md).

## <a name="how-to-update-a-cloud-service-role-or-deployment"></a>Så här: uppdatera en rolltjänst för molnet eller distribution
Om du behöver uppdatera programkoden för din molntjänst använder **uppdatera** på bladet cloud service. Du kan uppdatera en roll eller alla roller. Om du vill uppdatera måste överföra du ett nytt tjänstpaket eller tjänstkonfigurationsfilen.

1. I den [Azure-portalen][Azure portal], Välj den molntjänst som du vill uppdatera. Det här steget öppnas bladet cloud service-instans.
2. I bladet, klickar du på den **uppdatering** knappen.

    ![Uppdateringsknappen](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Uppdatera distributionen med en ny servicepaketfil (.cspkg) och tjänstekonfigurationsfilen (.cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. **Du kan också** uppdatera distributionsetiketten och storage-konto.
5. Om några roller har endast en rollinstans, väljer du den **distribuera även om en eller flera roller innehåller en enda instans** att uppgraderingen kan fortsätta.

    Azure kan bara garantera tjänsttillgänglighet 99,95% under en molnet tjänstuppdatering om rollerna har minst två rollinstanser (virtuella datorer). Med två rollinstanser bearbetar klientbegäranden av en virtuell dator medan den andra uppdateras.

6. Kontrollera **starta distribution** att uppdateringen när uppladdningen av paketet är klar.
7. Klicka på **OK** att starta tjänsten.

## <a name="how-to-swap-deployments-to-promote-a-staged-deployment-to-production"></a>Så här: växla distributioner att befordra en stegvis distribution till produktion
När du vill distribuera en ny version av en tjänst i molnet, steg och testa den nya versionen i din fristående molntjänstmiljö. Använd **växla** växla URL: er som två distributioner behandlas och befordra en ny version till produktionen.

Du kan växla distributioner från den **molntjänster** eller instrumentpanelen.

1. I den [Azure-portalen][Azure portal], Välj den molntjänst som du vill uppdatera. Det här steget öppnas bladet cloud service-instans.
2. I bladet, klickar du på den **växla** knappen.

    ![Cloud Services växlingsutrymme](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Följande bekräftelse öppnas.

    ![Cloud Services växlingsutrymme](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. När du har kontrollerat distributionsinformationen klickar du på **OK** att växla distributioner.

    Distribution växlingen sker snabbt eftersom det enda som ändrar de virtuella IP-adresserna (VIP) för distributioner.

    Ta bort tillfälliga distributionen för att spara kostnader för beräkning, när du har kontrollerat att din Produktionsdistribution fungerar som förväntat.

### <a name="common-questions-about-swapping-deployments"></a>Vanliga frågor om växling distributioner

**Vilka är kraven för att byta distributioner?**

Det finns två viktiga krav för en lyckad distribution växling:

- Om du vill använda en statisk IP-adress för din produktionsplatsen måste du reservera en för din mellanlagringsplatsen. Annars misslyckas växlingen.

- Alla instanser av dina roller måste köras innan du kan utföra växlingen. Du kan kontrollera statusen för dina instanser i bladet översikt av Azure-portalen. Du kan också använda den [Get-AzureRole](/powershell/module/azure/get-azurerole?view=azuresmps-3.7.0) i Windows PowerShell.

Observera att Gästoperativsystem uppdateringar och service återställning åtgärder kan också orsakas av distributionen växlingar misslyckas. Mer information finns i [distribution felsöka cloud service](cloud-services-troubleshoot-deployment-problems.md).

**Medför en växling driftstopp för Mina program? Hur får jag hantera den?**

Enligt beskrivningen i det sista avsnittet är en distribution växling vanligtvis snabbt eftersom det är bara en konfigurationsändring i Azure belastningsutjämnaren. I vissa fall kan det dock ta tio eller fler sekunder och resultera i tillfälliga anslutningsfel. Om du vill begränsa effekten för kunderna att överväga att implementera [klienten logik](../best-practices-retry-general.md).

## <a name="how-to-delete-deployments-and-a-cloud-service"></a>Så här: ta bort distributioner och en tjänst i molnet
Innan du kan ta bort en molnbaserad tjänst, måste du ta bort alla befintliga distributionen.

Ta bort tillfälliga distributionen för att spara kostnader för beräkning, när du har kontrollerat att din Produktionsdistribution fungerar som förväntat. Du debiteras för beräkning kostnader för distribuerade rollinstanser som har stoppats.

Använd följande procedur för att ta bort en distribution eller tjänst i molnet.

1. I den [Azure-portalen][Azure portal], Välj den molntjänst som du vill ta bort. Det här steget öppnas bladet cloud service-instans.
2. I bladet, klickar du på den **ta bort** knappen.

    ![Cloud Services växlingsutrymme](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Du kan ta bort hela Molntjänsten genom att kontrollera **Cloud service och dess distributioner** eller välja antingen den **Produktionsdistribution** eller **mellanlagring av distribution**.

    ![Cloud Services växlingsutrymme](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Klicka på den **ta bort** längst ned.
5. Ta bort Molntjänsten, klicka på **ta bort Molntjänsten**. I bekräftelsedialogrutan, klicka på **Ja**.

> [!NOTE]
> När en tjänst i molnet har tagits bort och utförlig övervakning är konfigurerad, måste du ta bort data manuellt från ditt lagringskonto. Information om var du hittar tabellerna mått finns [detta](cloud-services-how-to-monitor.md) artikel.


## <a name="how-to-find-more-information-about-failed-deployments"></a>Så här: Mer information om misslyckade installationer
Den **översikt** bladet har ett statusfält längst upp. När du klickar på fältet ett nytt blad öppnas och visar den felinformation. Om distributionen inte innehåller några fel är bladet information tomt.

![Cloud Services växlingsutrymme](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Nästa steg
* [Allmän konfiguration av Molntjänsten](cloud-services-how-to-configure-portal.md).
* Lär dig hur du [distribuera en tjänst i molnet](cloud-services-how-to-create-deploy-portal.md).
* Konfigurera en [domännamn](cloud-services-custom-domain-name-portal.md).
* Konfigurera [ssl-certifikat](cloud-services-configure-ssl-certificate-portal.md).
