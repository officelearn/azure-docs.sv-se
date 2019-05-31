---
title: Jämför baslinjer med Filintegritetsövervakning i Azure Security Center | Microsoft Docs
description: Lär dig hur du kan jämföra baslinjer med Filintegritetsövervakning i Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: monhaber
ms.openlocfilehash: e403a9bd4d3f8668544dab1d81e9052b37839bef
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358445"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Jämför baslinjer med hjälp av filen integritet övervakning (FIM)

Filen integritet övervakning (FIM) meddelar dig när ändringar sker till känsliga områden i dina resurser, så att du kan undersöka och åtgärda obehörig aktivitet. FIM övervakar Windows-filer, Windows-register och Linux-filer.

Det här avsnittet förklarar hur du aktiverar FIM för filer och register. Läs mer om FIM [Filintegritetsövervakning i Azure Security Center](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>Varför använda FIM?

Operativsystem, program och tillhörande konfigurationer styra beteende och säkerhetsfrågor tillståndet för dina resurser. Därför rikta angripare filerna som styr dina resurser, för att kunna kör operativsystemet för en resurs och/eller köra aktiviteter utan att identifieras.

I själva verket kräver många regelefterlevnad standarder som PCI-DSS och ISO 17799 implementera FIM-kontroller.  

## <a name="enable-built-in-recursive-registry-checks"></a>Aktivera inbyggda rekursiv registret kontroller

FIM registret hive standardinställningarna ger ett enkelt sätt att övervaka ändringar av rekursiva inom vanliga säkerhetsområden.  En angripare kan till exempel konfigurera ett skript för att köra i LOCAL_SYSTEM kontext genom att konfigurera en körning vid start eller avstängning.  Aktivera inbyggda kontrollen för att övervaka ändringar av den här typen.  

![Registret](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Rekursiva kontroller gäller endast för rekommenderade security registreringsdatafilerna och inte anpassade Registersökvägar.  

## <a name="adding-a-custom-registry-check"></a>Att lägga till en anpassad Registerkontroll

FIM-baslinjer starta genom att identifiera egenskaper för ett fungerande tillstånd för operativsystemet och stöd för program.  I det här exemplet ska vi fokusera på principkonfigurationer lösenord för Windows Server 2008 och senare.


|Principnamn                 | Registerinställning|
|---------------------------------------|-------------|
|Domänkontrollant: Tillåt inte ändringar av lösenord för datorkonton| MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RefusePasswordChange|
|Domänmedlem: Digitalt kryptera eller signera data för säker kanal (alltid)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireSignOrSeal|
|Domänmedlem: Digitalt kryptera data för säker kanal (om det är möjligt)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SealSecureChannel|
|Domänmedlem: Signera säkra kanaldata (om det är möjligt)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SignSecureChannel|
|Domänmedlem: Inaktivera lösenord för datorkonton|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DisablePasswordChange|
|Domänmedlem: Maximal ålder för lösenord för datorkonton|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\MaximumPasswordAge|
|Domänmedlem: Kräv stark sessionsnyckel för (Windows 2000 eller senare)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireStrongKey|
|Nätverkssäkerhet: Begränsa NTLM:  NTLM-autentisering i den här domänen|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RestrictNTLMInDomain|
|Nätverkssäkerhet: Begränsa NTLM: Lägg till serverundantag i den här domänen|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DCAllowedNTLMServers|
|Nätverkssäkerhet: Begränsa NTLM: Granska NTLM-autentisering i den här domänen|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Mer information om registerinställningar som stöds av olika versioner av operativsystemet, referera till den [grupprincipinställningar referera till kalkylbladet](https://www.microsoft.com/en-us/download/confirmation.aspx?id=25250).

*Konfigurera FIM för att övervaka registret baslinjer:*

1. I den **lägga till Windows-registret för ändringsspårning** fönstret i den **Windows-registernyckeln** text anger registernyckeln.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![Aktivera FIM på ett register](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Spåra ändringar på Windows-filer

1. I den **Lägg till Windows-fil för ändringsspårning** fönstret i den **RETUR sökväg** text, ange den mapp som innehåller de filer som du vill spåra. I exemplet i följande bild, **Contoso Web App** finns i D:\ enhet i den **ContosWebApp** mappstrukturen.  
1. Skapa en anpassad Windows-filpost genom att ange ett namn för inställningen-klassen, aktivera rekursion och ange den översta mappen med ett jokertecken (*)-suffix.

    ![Aktivera FIM för en fil](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>Hämtar sammanställning

Övervakning av filintegritet data finns i Azure Log Analytics / ange ConfigurationChange tabell.  

 1. Ange ett tidsintervall för att hämta en sammanfattning av ändringarna av resurs.
I följande exempel hämtar vi alla ändringar under de senaste 14 dagarna i kategorier av registret och filer:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Visa information om ändringar i registret:

    1. Ta bort **filer** från den **där** sats 
    1. Ta bort raden sammanfattning och Ersätt den med en skrivordning sats:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

Rapporter kan exporteras till CSV för arkivering och/eller channeled i en Power BI-rapport.  

![FIM-data](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)