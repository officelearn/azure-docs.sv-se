---
title: Övervakning av fil i Azure Security Center
description: Lär dig hur du jämför baslinjer med filintegritetsövervakning i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: memildin
ms.openlocfilehash: bb45e1d1ee17a6daf16bd688982f79fda986bde5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73664416"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Jämföra baslinjer med hjälp av övervakning av filintegritet (FIM)

Fim (File Integrity Monitoring) informerar dig när ändringar sker i känsliga områden i dina resurser, så att du kan undersöka och åtgärda obehörig aktivitet. FIM övervakar Windows-filer, Windows-register och Linux-filer.

I det här avsnittet beskrivs hur du aktiverar FIM i filerna och registren. Mer information om FIM finns [i Filintegritetsövervakning i Azure Security Center](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>Varför använda FIM?

Operativsystem, program och associerade konfigurationer styr resursernas beteende och säkerhetstillstånd. Därför riktar angripare in sig på de filer som styr dina resurser för att köra om en resurs operativsystem och/eller utföra aktiviteter utan att identifieras.

Faktum är att många standarder för regelefterlevnad, såsom PCI-DSS & ISO 17799, kräver att FIM-kontroller implementeras.  

## <a name="enable-built-in-recursive-registry-checks"></a>Aktivera inbyggda rekursiva registerkontroller

Fim-registrets hive-standardvärden är ett bekvämt sätt att övervaka rekursiva ändringar inom gemensamma säkerhetsområden.  En motståndare kan till exempel konfigurera ett skript så att det körs i LOCAL_SYSTEM kontext genom att konfigurera en körning vid start eller avstängning.  Aktivera den inbyggda kontrollen om du vill övervaka ändringar av den här typen.  

![Register](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Rekursiva kontroller gäller endast för rekommenderade säkerhetsfiler och inte anpassade registersökvägar.  

## <a name="adding-a-custom-registry-check"></a>Lägga till en anpassad registerkontroll

FIM-baslinjer börjar med att identifiera egenskaper hos ett känt tillstånd för operativsystemet och stödprogrammet.  I det här exemplet fokuserar vi på lösenordsprincipkonfigurationerna för Windows Server 2008 och senare.


|Principnamn                 | Registerinställning|
|---------------------------------------|-------------|
|Domänkontrollant: Tillåt inte ändringar av lösenord för datorkonton| MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RefusePasswordChange|
|Domänmedlem: Kryptera eller signera data för säker kanal digitalt (alltid)|MACHINE\System\CurrentControlSet\Services \netlogon\parameters\RequireSignOrSeal|
|Domänmedlem: Kryptera data för säker kanal digitalt (om det är möjligt)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SealSecureChannel|
|Domänmedlem: Signera säkra kanaldata digitalt (om det är möjligt)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SignSecureChannel|
|Domänmedlem: Inaktivera ändringar av lösenord för datorkonton|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DisablePasswordChange|
|Domänmedlem: Högsta ålder för lösenord för datorkontot|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\MaximumPasswordAge|
|Domänmedlem: Kräv stark sessionsnyckel (Windows 2000 eller senare)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireStrongKey|
|Nätverkssäkerhet: Begränsa NTLM: NTLM-autentisering i den här domänen|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RestrictNTLMInDomain|
|Nätverkssäkerhet: Begränsa NTLM: Lägg till serverundantag i den här domänen|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DCAllowedNTLMServers|
|Nätverkssäkerhet: Begränsa NTLM: Granska NTLM-autentisering i den här domänen|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Mer information om registerinställningar som stöds av olika operativsystemversioner finns i [referenskalkyl kalkylbladet Grupprincipinställningar](https://www.microsoft.com/download/confirmation.aspx?id=25250).

*Så här konfigurerar du FIM för att övervaka registerbaslinjer:*

1. Ange registernyckeln i textrutan **Windows-registernyckel** i fönstret **Lägg till Windows-register för ändringsspårning.**

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![Aktivera FIM i ett register](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Spåra ändringar i Windows-filer

1. I textrutan **Lägg till Windows-fil för ändringsspårning** anger du mappen som innehåller de filer som du vill spåra i textrutan **Ange sökväg.** I exemplet i följande bild finns **Contoso Web App** i D:\ inom **contosWebApp-mappstrukturen.**  
1. Skapa en anpassad Windows-filpost genom att ange ett namn på inställningsklassen, aktivera rekursion och ange den översta mappen med ett suffix för jokertecken (*).

    ![Aktivera FIM för en fil](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>Hämta ändringsdata

Data för filintegritetsövervakning finns i tabelluppsättningen Azure Log Analytics/ConfigurationChange.  

 1. Ange ett tidsintervall för att hämta en sammanfattning av ändringar per resurs.
I följande exempel hämtar vi alla ändringar under de senaste fjorton dagarna i kategorierna register och filer:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Så här visar du information om registerändringarna:

    1. Ta bort **filer** från **den plats där** satsen, 
    1. Ta bort summariseringsraden och ersätt den med en ordersats:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

Rapporter kan exporteras till CSV för arkivering och/eller kanaliseras till en Power BI-rapport.  

![FIM-data](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)