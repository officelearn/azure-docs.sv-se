---
title: Konfigurera kundhanterade nycklar i Azure Sentinel| Microsoft-dokument
description: Lär dig hur du konfigurerar kundhanterade nycklar (CMK) i Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: yelevin
ms.openlocfilehash: 5eed208ed79aeab4e46ed90dd4d340a8b445be96
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461641"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Konfigurera Azure Sentinel kundhanterad nyckel


Den här artikeln innehåller bakgrundsinformation och steg för att konfigurera en kundhanterad nyckel (CMK) för Azure Sentinel. CMK gör det möjligt för alla data som sparats eller skickas till Azure Sentinel att krypteras i alla relevanta lagringsresurser med en Azure Key Vault-nyckel som skapats eller ägs av dig.

> [!NOTE]
> -   Azure Sentinel CMK-funktionen tillhandahålls endast till kunder som är **nya** och åtkomst till den här funktionen styrs av Azure-funktionsregistrering.Du kan begära åtkomst azuresentinelCMK@microsoft.comgenom att kontakta , och som kapacitet är tillgänglig godkänns väntande begäranden.
> -   Azure Sentinel CMK-funktionen är endast tillgänglig i östra USA, västra USA 2 och södra centrala USA.
> -   CMK-funktionen är endast tillgänglig för kunder som skickar 1 TB per dag eller mer. Du får information om ytterligare priser när du ansöker till Microsoft om att etablera CMK på din Azure-prenumeration. Läs mer om [Logganalysladdning.](../azure-monitor/platform/customer-managed-keys.md#disclaimers)

## <a name="how-cmk-works"></a>Så här fungerar CMK 

Azure Sentinel-lösningen använder flera lagringsresurser för logginsamling och funktioner, dessa inkluderar Logganalys och andra lagringsresurser. Som en del av Azure Sentinel CMK-konfigurationen måste du också konfigurera CMK-inställningarna för relaterade lagringsresurser. Data som sparats i andra lagringsresurser än Log Analytics krypteras också.

> [!NOTE]
> Om du aktiverar CMK på Azure Sentinel aktiveras inte alla offentliga förhandsversionsfunktioner som inte stöder CMK.

## <a name="enable-cmk"></a>Aktivera CMK 

Så här etablerar du CMK: 

1.  Skapa ett Azure Key Vault och lagra nyckel.

2.  Aktivera CMK på arbetsytan Log Analytics.

3.  Registrera dig för Cosmos DB.

4.  Lägg till en åtkomstprincip i din Azure Key Vault-instans.

5.  Aktivera CMK i Azure Sentinel.

6.  Aktivera Azure Sentinel.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>STEG 1: Skapa ett Azure Key Vault och lagra nyckel

1.  [Skapa Azure Key Vault-resurs](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal?view=azs-1910)och generera eller importera sedan en nyckel som ska användas för datakryptering.
    > [!NOTE]
    >  Azure Key Vault måste konfigureras som återställningsbara för att skydda din nyckel och åtkomsten.

1.  [Aktivera återställningsalternativ:](../key-vault/general/best-practices.md#turn-on-recovery-options)

    -   Kontrollera att [Mjuk borttagning](../key-vault/general/overview-soft-delete.md) är aktiverat.

    -   Aktivera [Rensa skydd](../key-vault/general/overview-soft-delete.md#purge-protection) för att skydda mot påtvingad borttagning av hemligheten/valvet även efter mjuk borttagning.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>STEG 2: Aktivera CMK på din Log Analytics-arbetsyta

Följ instruktionerna i [Azure Monitor kundhanterad nyckelkonfiguration](../azure-monitor/platform/customer-managed-keys.md) för att skapa en CMK-arbetsyta som ska användas som Azure Sentinel-arbetsyta i följande steg.

### <a name="step-3-register-for-cosmos-db"></a>STEG 3: Registrera dig för Cosmos DB

Azure Sentinel fungerar med Cosmos DB som ytterligare en lagringsresurs. Se till att registrera dig till Cosmos DB.

Följ Cosmos DB-instruktionen för att [registrera Azure Cosmos DB-resursprovidern](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) för din Azure-prenumeration.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>STEG 4: Lägga till en åtkomstprincip i azure key vault-instansen

Se till att lägga till åtkomst från Cosmos DB till din Azure Key Vault-instans. Följ Cosmos DB-instruktionen för att lägga till [en åtkomstprincip i din Azure Key Vault-instans](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance) med Azure Cosmos DB-huvudnamn.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>STEG 5: Aktivera CMK i Azure Sentinel

Azure Sentinel CMK-funktionen tillhandahålls till nya kunder först efter att ha fått åtkomst direkt från Azure-produktgruppen. Använd dina kontakter på Microsoft för att få godkännande från Azure Sentinel-teamet för att aktivera CMK i din lösning.

När du har fått godkännande blir du ombedd att ange följande information för att aktivera CMK-funktionen.

-  Arbetsområdes-ID där du vill aktivera CMK

-  Url till nyckelvalv: Kopiera nyckelns "nyckelidentifierare" fram till det sista snedstrecket:  
    

    ![nyckelidentifierare](./media/customer-managed-keys/key-identifier.png)

    Azure Sentinel-teamet aktiverar Azure Sentinel CMK-funktionen för din medföljande arbetsyta.

-  Verifiering från Azure Sentinel-produktteamet att du har godkänts för att använda den här funktionen. Du måste ha den här innan du fortsätter.

### <a name="step-6-enable-azure-sentinel"></a>STEG 6: Aktivera Azure Sentinel


Gå till Azure-portalen och aktivera Azure Sentinel på arbetsytan där du konfigurerar CMK. Mer information finns i [Azure Sentinel Onboarding](quickstart-onboard.md).

## <a name="key-encryption-key-revocation-or-deletion"></a>Återkallning eller borttagning av nyckelkrypteringsnyckel


I händelse av att en användare återkallar nyckelkrypteringsnyckeln, antingen genom att ta bort den eller ta bort åtkomst för Azure Sentinel, inom en timme, kommer Azure Sentinel att respektera ändringen och bete sig som om data inte längre är tillgängliga. Nu förhindras alla åtgärder som utförs som använder beständiga lagringsresurser som datainmatning, beständiga konfigurationsändringar och skapande av incidenter. Tidigare lagrade data kommer inte att tas bort men förblir otillgängliga. Otillgängliga data styrs av datalagringsprincipen och rensas i enlighet med den principen.

Den enda möjliga åtgärden efter att krypteringsnyckeln har återkallats eller tagits bort är borttagning av konton.

Om åtkomsten återställs efter återkallande återställs åtkomsten till data inom en timme.

Mer information om hur det fungerar i Azure Monitor finns i [Azure Monitor CMK-återkallning](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-revocation).

## <a name="key-encryption-key-rotation"></a>Rotation av nyckelkrypteringsnyckel


Azure Sentinel och Log Analytics stöder nyckelrotation. När en användare utför nyckelrotation i Key Vault stöder Azure Sentinel den nya nyckeln inom en timme.

I Key Vault kan du utföra tangentrotation genom att skapa en ny version av nyckeln:

![tangentrotation](./media/customer-managed-keys/key-rotation.png)

Du kan inaktivera den tidigare versionen av nyckeln efter 24 timmar, eller efter att granskningsloggarna för Azure Key Vault inte längre visar någon aktivitet som använder den tidigare versionen.

Om du använder samma nyckel i Azure Sentinel och i Logganalys är det nödvändigt att utföra nyckelrotation som du uttryckligen måste uppdatera klusterresursen i Logganalys med den nya Azure Key Vault-nyckelversionen. Mer information finns i [Azure Monitor CMK-rotation](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-rotation).

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du konfigurerar en kundhanterad nyckel i Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).
- [Använd arbetsböcker](tutorial-monitor-your-data.md) för att övervaka dina data.

