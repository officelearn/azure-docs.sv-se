---
title: Vanliga cloud service hanteringsuppgifter (klassisk) | Microsoft Docs
description: "Lär dig mer om att hantera molntjänster i den klassiska Azure-portalen."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 41a30e50-067c-485b-96fd-434a6d057abc
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: adegeo
ms.openlocfilehash: 2ee76dfcb579e53975b1f61a6590f8d78dc0961b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-cloud-services"></a>Så här hanterar du molntjänster
> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-how-to-manage-portal.md)
> * [Klassisk Azure-portal](cloud-services-how-to-manage.md)
>
>

I den **molntjänster** område i Azure klassiska portal, du kan uppdatera en rolltjänst eller en distribution, befordra en stegvis distribution till produktion, länka resurser till din molntjänst så att du kan se resursberoenden och skala resurser tillsammans och ta bort en molnbaserad tjänst eller en distribution.

## <a name="how-to-update-a-cloud-service-role-or-deployment"></a>Så här: uppdatera en rolltjänst för molnet eller distribution
Om du behöver uppdatera programkoden för din molntjänst använder **uppdatera** på instrumentpanelen, **molntjänster** sidan eller **instanser** sidan. Du kan uppdatera en roll eller alla roller. Du måste överföra en ny servicepaket och konfigurationsfilen för tjänsten.

1. I den [klassiska Azure-portalen](https://manage.windowsazure.com/), på instrumentpanelen, **molntjänster** sidan eller **instanser** klickar du på **uppdatering**.

    ![UpdateDeployment](./media/cloud-services-how-to-manage/CloudServices_UpdateDeployment.png)

2. I **distributionsetiketten**, ange ett namn som identifierar distributionen (till exempel mycloudservice4). Du hittar distributionsetiketten under **Snabbstart** på instrumentpanelen.
3. I **paketet**, använda **Bläddra** att överföra servicepaketfil (.cspkg).
4. I **Configuration**, använda **Bläddra** att överföra tjänstekonfigurationsfilen (.cscfg).
5. I **rollen**väljer **alla** om du vill uppgradera alla roller i Molntjänsten. Markera den roll som du vill uppdatera om du vill utföra en enda roll-uppdatering. Även om du väljer en viss roll att uppdatera tillämpas uppdateringarna i tjänstkonfigurationsfilen på alla roller.
6. Om uppdateringen ändras antalet roller eller storleken på någon roll, väljer du den **Tillåt uppdateringen om rollstorlekar eller antalet roller ändras** kryssrutan om du vill aktivera uppdatering att gå vidare.

    Tänk på att om du ändrar storleken på en roll (det vill säga storleken för en virtuell dator som är värd för en rollinstans) eller antalet roller varje instans av serverroll (virtuell dator) måste vara återställts från en avbildning och lokala data går förlorade.

7. Om några service-roller har endast en rollinstans, Välj den **uppdatera även om en eller flera roller innehåller en enda instans kryssruta** att uppgraderingen kan fortsätta.

    Azure kan bara garantera tjänsttillgänglighet 99,95% under en molnet tjänstuppdatering om rollerna har minst två rollinstanser (virtuella datorer). Som gör det möjligt för en virtuell dator att bearbeta klientbegäranden medan den andra uppdateras.

8. Klicka på **OK** (markering) för att starta tjänsten.

## <a name="how-to-swap-deployments-to-promote-a-staged-deployment-to-production"></a>Så här: växla distributioner att befordra en stegvis distribution till produktion
Använd **växla** att befordra en fristående distribution av en tjänst i molnet till produktionen. När du vill distribuera en ny version av en molnbaserad tjänst kan du testa den nya versionen i din fristående molntjänstmiljö medan dina kunder använder den aktuella versionen i produktion mellanlagra. När du är redo att flytta upp den nya versionen för produktion, kan du använda **växla** växla URL: er som två distributioner behandlas.

Du kan växla distributioner från den **molntjänster** eller instrumentpanelen.

1. I den [klassiska Azure-portalen](https://manage.windowsazure.com/), klickar du på **molntjänster**.
2. Klicka på Molntjänsten för att markera den i listan över molntjänster.
3. Klicka på **växla**.

    Följande bekräftelse öppnas.

    ![Cloud Services växlingsutrymme](./media/cloud-services-how-to-manage/CloudServices_Swap.png)

4. När du har kontrollerat distributionsinformationen klickar du på **Ja** att växla distributioner.

    Distribution växlingen sker snabbt eftersom det enda som ändrar de virtuella IP-adresserna (VIP) för distributioner.

    Ta bort distributionen i mellanlagringsmiljön för att spara kostnader för beräkning, när du är säker på att nya Produktionsdistribution fungerar som förväntat.

### <a name="common-questions-about-swapping-deployments"></a>Vanliga frågor om växling distributioner

**Vilka är kraven för att byta distributioner?**

Det finns två viktiga krav för en lyckad distribution växling:

- Om du vill använda en statisk IP-adress för din produktionsplatsen måste du reservera en för din mellanlagringsplatsen. Annars misslyckas växlingen.

- Alla instanser av dina roller måste köras innan du kan utföra växlingen. Du kan kontrollera statusen för dina instanser i den klassiska Azure-portalen eller med hjälp av [kommandot Get-AzureRole i Windows PowerShell](/powershell/module/azure/get-azurerole?view=azuresmps-3.7.0).

Observera att gäst OS-uppdateringar och återställning operations kan även orsaka växlingar distributionen misslyckas. Se [distribution felsöka cloud service](cloud-services-troubleshoot-deployment-problems.md) för mer information.

**Medför en växling driftstopp för Mina program? Hur får jag hantera den?**

Enligt beskrivningen i det sista avsnittet är en distribution växling vanligtvis mycket snabbt eftersom det är bara en konfigurationsändring i Azure belastningsutjämnaren. I vissa fall kan det dock ta tio eller fler sekunder och resultera i tillfälliga anslutningsfel. Om du vill begränsa effekten för kunderna att överväga att implementera [klienten logik](../best-practices-retry-general.md).

## <a name="how-to-link-a-resource-to-a-cloud-service"></a>Så här: länka en resurs till en tjänst i molnet
Om du vill visa din molntjänsts beroenden av andra resurser kan du länka en Azure SQL-databasinstans eller ett lagringskonto till molntjänsten. Du kan länka och Avlänka resurser på den **länkade resurser** sidan och övervaka deras användning på cloud service-instrumentpanelen. Om ett länkade storage-konto har övervakning aktiverat kan övervaka du Totalt antal begäranden på cloud service-instrumentpanelen.

Använd **länk** att länka en ny eller befintlig SQL-instans eller lagring databaskonto till Molntjänsten. Du kan sedan skala databasen tillsammans med rolltjänsten moln som använder den på den **skala** sidan. (Ett lagringskonto skalas automatiskt som Minnesanvändningen ökar). Mer information finns i [hur du skalar en tjänst i molnet och länkade resurser](cloud-services-how-to-scale.md).

Du också kan övervaka, hantera och skala databasen i den **databaser** nod i den klassiska Azure-portalen.

”Länka” en resurs på detta sätt ansluta inte din app till resursen. Om du skapar en ny databas med hjälp av **länk**, måste du lägga till anslutningssträngarna i programkoden och sedan uppgradera Molntjänsten. Du måste också lägga till anslutningssträngar om din app använder resurser i ett länkat lagringskonto.

Följande procedur beskriver hur du länkar en ny SQL Database-instans som har distribuerats på en ny SQL Database-server till en molntjänst.

### <a name="to-link-a-sql-database-instance-to-a-cloud-service"></a>Länka en instans av SQL-databas till en tjänst i molnet
1. I den [klassiska Azure-portalen](http://manage.windowsazure.com/), klickar du på **molntjänster**. Klicka sedan på namnet på Molntjänsten att öppna instrumentpanelen.
2. Klicka på **länkade resurser**.

    Den **länkade resurser** öppnas.

    ![LinkedResourcesPage](./media/cloud-services-how-to-manage/CloudServices_LinkedResourcesPage.png)

3. Klicka på antingen **länka en resurs** eller **länk**.

    Den **länk resurs** startas guiden.

    ![Länken Sida1](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkPage1.png)

4. Klicka på **skapar en ny resurs** eller **länka en befintlig resurs**.
5. Välj typ av resurs för att länka. I den [klassiska Azure-portalen](http://manage.windowsazure.com/), klickar du på **SQL-databas**. (Endast den klassiska Azure-portalen stöder länka ett storage-konto till en tjänst i molnet.)
6. För att slutföra databaskonfigurationen följer du anvisningarna i hjälpen för de **SQL-databaser** område i den klassiska Azure-portalen.

    Du kan följa förloppet för åtgärden länkande i meddelandeområdet.

    När länkar är klar kan övervaka du status för länkade resurser på cloud service-instrumentpanelen. Information om att skala en länkad SQL-databas finns [hur du skalar en tjänst i molnet och länkade resurser](cloud-services-how-to-scale.md).

### <a name="to-unlink-a-linked-resource"></a>Ta bort kopplingen länkade resurser
1. I den [klassiska Azure-portalen](http://manage.windowsazure.com/), klickar du på **molntjänster**. Klicka sedan på namnet på Molntjänsten att öppna instrumentpanelen.
2. Klicka på **länkade resurser**, och välj sedan resursen.
3. Klicka på **Avlänka**. Klicka på **Ja** uppmanas att bekräfta.

    Bryta länken till en SQL-databas har ingen effekt på databasen eller programmets anslutningar till databasen. Du kan hantera databasen i den **SQL-databaser** område i den klassiska Azure-portalen.

## <a name="how-to-delete-deployments-and-a-cloud-service"></a>Så här: ta bort distributioner och en tjänst i molnet
Innan du kan ta bort en molnbaserad tjänst, måste du ta bort alla befintliga distributionen.

Ta bort distributionen fristående för att spara kostnader för beräkning, när du har kontrollerat att din Produktionsdistribution fungerar som förväntat. Du är fakturerade beräkning kostnader för rollinstanser även om en molnbaserad tjänst inte körs.

Använd följande procedur för att ta bort en distribution eller tjänst i molnet.

1. I den [klassiska Azure-portalen](http://manage.windowsazure.com/), klickar du på **molntjänster**.
2. Välj Molntjänsten och klicka sedan på **ta bort**. (Om du vill välja en tjänst i molnet utan att öppna instrumentpanelen, klicka var som helst utom på namnet i posten cloud service.)

    Om du har en distribution i Förproduktion eller produktion, visas en meny med alternativ som liknar den följande längst ned i fönstret. Innan du kan ta bort Molntjänsten, måste du ta bort alla befintliga distributioner.

    ![Ta bort meny](./media/cloud-services-how-to-manage/CloudServices_DeleteMenu.png)

3. Ta bort en distribution genom att klicka på **ta bort Produktionsdistribution** eller **ta bort distributionen av fristående**. I bekräftelsedialogrutan, klicka på **Ja**.
4. Om du planerar att ta bort Molntjänsten upprepar du steg 3, om du vill ta bort andra distributionen.
5. Ta bort Molntjänsten, klicka på **ta bort Molntjänsten**. I bekräftelsedialogrutan, klicka på **Ja**.

> [!NOTE]
> Om utförlig övervakning har konfigurerats för din molntjänst tar Azure inte bort övervakningsdata från storage-konto när du tar bort Molntjänsten. Du måste ta bort data manuellt. Information om var du hittar tabellerna mått finns i ”så här: åtkomst utförlig övervakningsdata utanför den klassiska Azure-portalen” i [så övervakaren molntjänster](cloud-services-how-to-monitor.md).
>
>

## <a name="next-steps"></a>Nästa steg
* [Allmän konfiguration av Molntjänsten](cloud-services-how-to-configure.md).
* Lär dig hur du [distribuera en tjänst i molnet](cloud-services-how-to-create-deploy.md).
* Konfigurera en [domännamn](cloud-services-custom-domain-name.md).
* Konfigurera [ssl-certifikat](cloud-services-configure-ssl-certificate.md).
