---
title: Så här integrerar du Windows administrations Center med Azure Security Center | Microsoft Docs
description: Den här artikeln förklarar hur du integrerar Azure Security Center med Windows administrations Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: cbbdf724b9d7fe4948553e7526410b994f491b49
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80435266"
---
# <a name="integrate-azure-security-center-with-windows-admin-center"></a>Integrera Azure Security Center med administrations Center för Windows

Windows administrations Center är ett hanterings verktyg för dina Windows-servrar. Det är en enda plats för system administratörer som har till gång till de flesta av de vanligaste administratörs verktygen. Inifrån Windows administrations Center kan du direkt publicera dina lokal-servrar i Azure Security Center. Du kan sedan Visa en sammanfattning av dina säkerhets rekommendationer och aviseringar direkt i Windows administrations Center-upplevelsen.

> [!NOTE]
> Din Azure-prenumeration och den associerade Log Analytics arbets ytan måste båda ha Security Center standard nivå aktive rad för att kunna aktivera integrering med Windows administrations Center.
> Standard nivån är kostnads fri under de första 30 dagarna om du inte tidigare har använt den på prenumerationen och arbets ytan. Mer information finns [på sidan med pris information](security-center-pricing.md).
>

När du har registrerat en server från Windows administrations Center till Azure Security Center kan du:

* Visa säkerhets aviseringar och rekommendationer i Security Center tillägget i Windows administrations Center
* Visa säkerhets position och hämta ytterligare detaljerad information om dina hanterade Windows administrations Center-servrar i Security Center inom Azure Portal (eller via ett API)

Genom att kombinera dessa två verktyg blir Security Center det enda fönstret av glas för att visa all din säkerhets information, oavsett resurs: skydda dina Windows administrations Center-hanterade lokal-servrar, dina virtuella datorer och eventuella ytterligare PaaS-arbetsbelastningar.

## <a name="onboarding-windows-admin-center-managed-servers-into-security-center"></a>Registrera Windows administrations Center-hanterade servrar i Security Center

1. I Windows administrations Center väljer du en av dina servrar och i fönstret **verktyg** väljer du tillägget Azure Security Center:

    ![Azure Security Center tillägget i Windows administrations Center](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Om servern redan har registrerats på Security Center visas inte konfigurations fönstret.

1. Klicka på **Logga in på Azure och konfigurera**.
    ![Registrera Windows Admin Center-tillägg till Azure Security Center](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Följ anvisningarna för att ansluta servern till Security Center. När du har angett nödvändig information och bekräftat, gör Security Center nödvändiga konfigurations ändringar för att säkerställa att alla följande är uppfyllda:
    * En Azure-Gateway har registrerats.
    * Servern har en arbets yta att rapportera till och en associerad prenumeration.
    * Security Center standard nivån Log Analytics lösning är aktive rad på arbets ytan. Den här lösningen innehåller Security Center standard-nivå funktioner för *alla* servrar och virtuella datorer som rapporterar till den här arbets ytan.
    * Security Center standard pris nivån för den virtuella datorn är aktive rad för prenumerationen.
    * Log Analytics Agent installeras på servern och konfigureras att rapportera till den valda arbets ytan. Om servern redan rapporterar till en annan arbets yta har den kon figurer ATS för att rapportera till den nyligen valda arbets ytan.

    > [!NOTE]
    > Det kan ta en stund innan rekommendationerna visas. Beroende på din server aktivitet får du faktiskt inte ta emot *några* aviseringar. Om du vill generera test aviseringar för att testa att dina aviseringar fungerar korrekt, följer du anvisningarna i [proceduren för att verifiera aviseringar](security-center-alert-validation.md).


## <a name="viewing-security-recommendations-and-alerts-in-windows-admin-center"></a>Visa säkerhets rekommendationer och aviseringar i Windows administrations Center

När du har registrerat dig kan du Visa aviseringar och rekommendationer direkt i Azure Security Centers delen av Windows administrations Center. Klicka på en rekommendation eller en avisering om du vill visa dem i Azure Portal. Där får du ytterligare information och lär dig hur du kan åtgärda problem.

[![Security Center rekommendationer och aviseringar som visas i administrations Center för Windows](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="viewing-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Visa säkerhets rekommendationer och aviseringar för Windows administrations Center hanterade servrar i Security Center
Från Azure Security Center:

* Öppna **compute &-appar** och klicka på fliken **virtuella datorer och datorer** om du vill se säkerhets rekommendationer för alla dina Windows administrations Center-servrar. gör så här:

    [![Visa säkerhets rekommendationer för Windows administrations Center hanterade servrar](media/windows-admin-center-integration/viewing-recommendations-wac.png)](media/windows-admin-center-integration/viewing-recommendations-wac.png#lightbox)

* Öppna **säkerhets aviseringar**för att Visa säkerhets aviseringar för alla dina Windows administrations Center-servrar. Klicka på **filter** och se till att **endast** "icke-Azure" är valt:

    ![Filtrera säkerhets aviseringar för hanterade servrar i Windows administrations Center](./media/windows-admin-center-integration/filtering-alerts-to-non-azure.png)

    [![Visa säkerhets aviseringar för hanterade servrar i Windows administrations Center](media/windows-admin-center-integration/viewing-alerts-wac.png)](media/windows-admin-center-integration/viewing-alerts-wac.png#lightbox)