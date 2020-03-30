---
title: Så här integrerar du Windows Admin Center med Azure Security Center | Microsoft-dokument
description: I den här artikeln beskrivs hur du integrerar Azure Security Center med Administrationscenter för Windows
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5467794bf246fab4ff7ded9c445dbeee0c4093b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139631"
---
# <a name="integrate-azure-security-center-with-windows-admin-center"></a>Integrera Azure Security Center med Administrationscenter för Windows

Windows Admin Center är ett hanteringsverktyg för dina Windows-servrar. Det är en enda plats för systemadministratörer att komma åt de flesta av de vanligaste administrationsverktygen. I Windows Admin Center kan du direkt gå in på dina on-prem-servrar till Azure Security Center. Du kan sedan visa en sammanfattning av dina säkerhetsrekommendationer och aviseringar direkt i windows admincenter-upplevelsen.

> [!NOTE]
> Din Azure-prenumeration och den associerade Log Analytics-arbetsytan måste båda ha Security Centers standardnivå aktiverad för att aktivera Integreringen av Windows Admin Center.
> Standardnivån är kostnadsfri under de första 30 dagarna om du inte tidigare har använt den på prenumerationen och arbetsytan. Mer information finns [på sidan för prisinformation](security-center-pricing.md).
>

När du har installerat en server från Windows Admin Center till Azure Security Center kan du:

* Visa säkerhetsaviseringar och rekommendationer i tillägget Security Center i Administrationscenter för Windows
* Visa säkerhetspositionen och hämta ytterligare detaljerad information om dina Hanterade Servrar i Windows Admin Center i Security Center i Azure-portalen (eller via ett API)

Genom att kombinera dessa två verktyg blir Security Center din enda glasruta för att visa all säkerhetsinformation, oavsett resurs: skydda dina Windows Admin Center-hanterade on-prem-servrar, dina virtuella datorer och eventuella ytterligare PaaS-arbetsbelastningar.

## <a name="onboarding-windows-admin-center-managed-servers-into-security-center"></a>Introduktion till hanterade servrar i Windows Admin Center i Security Center

1. Välj en av dina servrar i serverpanelen i Windows och välj tillägget Azure Security Center i fönstret **Verktyg:**

    ![Azure Security Center-tillägg i Windows Admin Center](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Om servern redan är inbyggd i Security Center visas inte inställningsfönstret.

1. Klicka **på Logga in på Azure och konfigurera**.
    ![Introduktion till Windows Admin Center-tillägg till Azure Security Center](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Följ instruktionerna för att ansluta servern till Security Center. När du har angett nödvändiga uppgifter och bekräftat, security center gör nödvändiga konfigurationsändringar för att säkerställa att alla följande är sanna:
    * En Azure Gateway har registrerats.
    * Servern har en arbetsyta att rapportera till och en associerad prenumeration.
    * Security Centers standardnivålogganalyslösning är aktiverad på arbetsytan. Den här lösningen tillhandahåller Security Centers standardnivåfunktioner för *alla* servrar och virtuella datorer som rapporterar till den här arbetsytan.
    * Security Centers standardnivåpriser för virtuell dator är aktiverat för prenumerationen.
    * Microsoft Monitoring Agent (MMA) installeras på servern och konfigureras för att rapportera till den valda arbetsytan. Om servern redan rapporterar till en annan arbetsyta är den konfigurerad att rapportera till den nyligen valda arbetsytan också.

    > [!NOTE]
    > Det kan ta lite tid efter introduktionen för rekommendationer att visas. Beroende på serveraktiviteten kanske du inte får *några* aviseringar. Om du vill generera testvarningar för att testa att aviseringarna fungerar korrekt följer du anvisningarna i [valideringsproceduren](security-center-alert-validation.md)för aviseringar .


## <a name="viewing-security-recommendations-and-alerts-in-windows-admin-center"></a>Visa säkerhetsrekommendationer och aviseringar i Administrationscenter för Windows

När du är ombord kan du visa dina aviseringar och rekommendationer direkt i Azure Security Center-området i Windows Admin Center. Klicka på en rekommendation eller en avisering om du vill visa dem i Azure-portalen. Där får du ytterligare information och lär dig hur du åtgärdar problem.

[![Rekommendationer och aviseringar i Security Center som visas i Administrationscenter för Windows](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="viewing-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Visa säkerhetsrekommendationer och aviseringar för hanterade servrar i Administrationscenter i Windows Admin Center i Security Center
Från Azure Security Center:

* Om du vill visa säkerhetsrekommendationer för alla Windows Admin Center-servrar öppnar du **& Appar** och klickar på fliken Virtuella datorer **och datorer.**

    [![Visa säkerhetsrekommendationer för hanterade servrar i Windows Admin Center](media/windows-admin-center-integration/viewing-recommendations-wac.png)](media/windows-admin-center-integration/viewing-recommendations-wac.png#lightbox)

* Om du vill visa säkerhetsaviseringar för alla Windows Admin Center-servrar öppnar du **Säkerhetsvarningar**. Klicka på **Filter** och se till att **endast** "Icke-Azure" är markerat:

    ![Filtrera säkerhetsvarningar för hanterade servrar i Windows Admin Center](./media/windows-admin-center-integration/filtering-alerts-to-non-azure.png)

    [![Visa säkerhetsvarningar för hanterade servrar i Windows Admin Center](media/windows-admin-center-integration/viewing-alerts-wac.png)](media/windows-admin-center-integration/viewing-alerts-wac.png#lightbox)