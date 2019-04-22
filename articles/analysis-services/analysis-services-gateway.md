---
title: Lokal datagateway
description: En lokal gateway krävs om Analysis Services-servern i Azure ansluter till lokala datakällor.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f13dd1282a6384a0acca4c6936fe7900a051795f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58896031"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Ansluta till lokala datakällor med lokala Data Gateway
Den lokala datagatewayen tillhandahåller säker dataöverföring mellan lokala datakällor och din Azure Analysis Services-servrar i molnet. Utöver att arbeta med flera Azure Analysis Services-servrar i samma region, fungerar den senaste versionen av gatewayen även med Azure Logic Apps, Power BI, Powerapps och Microsoft Flow. Du kan associera flera tjänster i samma prenumeration och samma region med en enda gateway. 

Hämta installationsprogrammet med gatewayen första gången är en process som genomförs i fyra delar:

- **Ladda ned och kör installationsprogrammet** – det här steget installerar en gateway-tjänst på en dator i din organisation. Du också logga in till Azure med ett konto i din [klientens](/previous-versions/azure/azure-services/jj573650(v=azure.100)#BKMK_WhatIsAnAzureADTenant) Azure AD. Azure B2B (Gäst)-konton stöds inte.

- **Registrera din gateway** – i det här steget ska du ange ett namn och återställning för din gateway och väljer en region, registrera din gateway med Gateway-Molntjänsten. Din gateway-resurs kan registreras i valfri region, men vi rekommenderar att den är i samma region som Analysis Services-servrar. 

- **Skapa en resurs för gatewayen i Azure** – i det här steget skapar du en resurs för gatewayen i Azure-prenumerationen.

- **Anslut dina servrar till gatewayresursen** -när du har en gateway-resurs i din prenumeration kan du börja ansluta dina servrar till den. Du kan ansluta flera servrar och andra resurser, förutsatt att de är i samma prenumeration och samma region.

Kom igång nu direkt, se [installera och konfigurera den lokala datagatewayen](analysis-services-gateway-install.md).

## <a name="how-it-works"> </a>Så här fungerar det
Du installerar på en dator i din organisation gatewayen körs som en Windows-tjänst, **lokal datagateway**. Den här lokal tjänst har registrerats med Gateway-Molntjänsten via Azure Service Bus. Du kan sedan skapa en resurs för gatewayen Gateway-Molntjänsten för din Azure-prenumeration. Azure Analysis Services-servrar är sedan anslutna till gatewayresursen. När modeller på servern behöver ansluta till dina lokala data källor för frågor eller bearbetning, inom en fråga och dataflödet gatewayresursen, Azure Service Bus, lokala data gateway-tjänsten och dina datakällor. 

![Hur det fungerar](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Frågor och dataflödet:

1. En fråga har skapats av Molntjänsten med de krypterade autentiseringsuppgifterna för den lokala datakällan. Sedan skickas till en kö för gatewayen ska bearbeta.
2. Gatewaymolntjänsten analyserar frågan och skickar en begäran till den [Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/).
3. Den lokala datagatewayen avsöker Azure Service Bus för väntande begäranden.
4. Gatewayen hämtar frågan, dekrypterar autentiseringsuppgifterna och ansluter till datakällor med dessa autentiseringsuppgifter.
5. Gatewayen skickar frågan till datakällan för körning.
6. Resultaten skickas från datakällan, tillbaka till gatewayen och sedan till Molntjänsten och servern.

## <a name="windows-service-account"> </a>Windows-tjänstkontot
Den lokala datagatewayen är konfigurerad för att använda *NT SERVICE\PBIEgwService* för inloggning med autentiseringsuppgifter Windows-tjänsten. Som standard har behörigheten för inloggning som en tjänst. i samband med den dator som du installerar gatewayen på. Den här autentiseringsuppgiften är inte samma konto som används för att ansluta till lokala datakällor eller ditt Azure-konto.  

Om du får problem med proxyservern på grund av autentiseringen, kan du ändra Windows-tjänstkontot till en domänanvändare eller Hanterat tjänstkonto.

## <a name="ports"> </a>Portar
Gatewayen skapar en utgående anslutning till Azure Service Bus. Den kommunicerar via utgående portar: TCP 443 (standard), 5671, 5672, 9350 till 9354.  Gatewayen kräver inte ingående portar.

Vi rekommenderar att du godkänner IP-adresserna för ditt dataområde i brandväggen. Du kan ladda ned den [Microsoft Azure Datacenter IP-listan](https://www.microsoft.com/download/details.aspx?id=41653). Den här listan uppdateras varje vecka.

> [!NOTE]
> IP-adresserna som anges i Azure-Datacenter IP-listan är i CIDR-notation. Mer information finns i [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).
>
>

Följande är de fullständigt kvalificerade domännamn som används av gatewayen.

| Domännamn | Utgående portar | Beskrivning |
| --- | --- | --- |
| *.powerbi.com |80 |HTTP används för att hämta installationsprogrammet. |
| *.powerbi.com |443 |HTTPS |
| *. analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Avancerade Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Lyssnare på Service Bus Relay via TCP (kräver 443 för Access Control tokenförvärv) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Används för att testa Internetanslutningen om gatewayen inte kan nås av Power BI-tjänsten. |
| *.microsoftonline-p.com |443 |Används för autentisering beroende på konfiguration. |

### <a name="force-https"></a>Tvinga HTTPS-kommunikation med Azure Service Bus
Du kan tvinga gatewayen att kommunicera med Azure Service Bus med HTTPS i stället för direkt TCP; men kan detta avsevärt minska prestanda. Du kan ändra den *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* genom att ändra värdet från `AutoDetect` till `Https`. Den här filen finns normalt *C:\Program c:\program\lokal datagateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="tenant-level-administration"></a>Administration på klientnivå 

Det finns för närvarande inte någon enskild plats där klientadministratörer kan hantera alla gatewayer som andra användare har installerat och konfigurerat.  Om du är Innehavaradministratör, rekommenderas du ber användarna i organisationen att lägga till dig som en administratör i varje gateway som de installerar. Detta gör att du kan hantera alla gatewayer i din organisation via sidan Gateway-inställningar eller via [PowerShell-kommandon](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters#powershell-support-for-gateway-clusters). 


## <a name="faq"></a>Vanliga frågor och svar

### <a name="general"></a>Allmänt

**FRÅGOR OCH**: Behöver jag en gateway för datakällor i molnet, till exempel Azure SQL Database? <br/>
**S**: Nej. En gateway krävs för att ansluta till lokala datakällor endast.

**FRÅGOR OCH**: Har en gateway att installeras på samma dator som datakällan? <br/>
**S**: Nej. Gatewayen måste bara möjligheten att ansluta till servern, vanligtvis i samma nätverk.

<a name="why-azure-work-school-account"></a>

**FRÅGOR OCH**: Varför behöver jag använda ett arbets- eller skolkonto konto för att logga in? <br/>
**S**: Du kan bara använda ett organisationens arbets- eller skolkonto när du installerar den lokala datagatewayen. Och att kontot måste finnas i samma klient som prenumerationen som du konfigurerar gateway-resursen. Ditt inloggningskonto lagras i en klient som hanteras av Azure Active Directory (AD Azure). Vanligtvis är matchar din Azure AD-konto användarens huvudnamn (UPN) den e-postadressen.

**FRÅGOR OCH**: Var lagras mina autentiseringsuppgifter? <br/>
**S**: De autentiseringsuppgifter som du anger för en datakälla krypteras och lagras i gatewayens molntjänst. Autentiseringsuppgifterna dekrypteras i den lokala datagatewayen.

**FRÅGOR OCH**: Finns det några krav på bandbredd i nätverket? <br/>
**S**: Vi rekommenderar nätverksanslutningen har bra dataflöde. Varje miljö är annorlunda och mängden data som skickas påverkar resultaten. Med ExpressRoute kan bidra till att garantera en dataflödesnivå mellan lokala och Azure-datacenter.
Du kan använda verktyg från tredje part Azure Speed Test-appen för att mäta ditt dataflöde.

**FRÅGOR OCH**: Vad är svarstiden för att köra frågor till en datakälla från gatewayen? Vad är den bästa arkitekturen? <br/>
**S**: För att förkorta Nätverksfördröjningen ska du installera gatewayen som nära datakällan som möjligt. Om du kan installera gatewayen på den faktiska datakällan, minimerar svarstiden i den här närhet. Överväg att i datacenter för. Till exempel om tjänsten använder datacenter för västra USA och du har SQL Server på en Azure virtuell dator, din Azure VM: en vara i västra USA för. Den här närhet minimerar svarstiden och undviker onödiga avgifter för Azure VM.

**FRÅGOR OCH**: Hur skickas resultaten tillbaka till molnet? <br/>
**S**: Resultaten skickas via Azure Service Bus.

**FRÅGOR OCH**: Finns det några inkommande anslutningar till gatewayen från molnet? <br/>
**S**: Nej. Gatewayen använder utgående anslutningar till Azure Service Bus.

**FRÅGOR OCH**: Vad händer om jag blockerar utgående anslutningar? Vad behöver jag att öppna? <br/>
**S**: Se vilka portar och värdar som används av gatewayen.

**FRÅGOR OCH**: Vad heter den faktiska Windows-tjänsten?<br/>
**S**: I Services kallas gatewayen för lokala data gateway-tjänsten.

**FRÅGOR OCH**: Gatewayens Windows-tjänst kan köra med ett Azure Active Directory-konto? <br/>
**S**: Nej. Windows-tjänsten måste ha ett giltigt Windows-konto. Tjänsten körs med tjänst-SID NT SERVICE\PBIEgwService som standard.

**FRÅGOR OCH**: Hur kan jag gäller en gateway? <br/>
**S**: Ta över en gateway (genom att köra installationsprogrammet/ändra i Kontrollpanelen > program), måste du vara ägare för gatewayresursen i Azure och har återställningsnyckeln. Gateway-resursägare kan konfigureras i Access Control.

### <a name="high-availability"></a>Hög tillgänglighet och disaster recovery

**FRÅGOR OCH**: Hur kan vi ha hög tillgänglighet?  
**S**: Du kan installera en gateway på en annan dator för att skapa ett kluster. Mer information finns i [kluster med hög tillgänglighet för den lokala datagatewayen](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters) i Power BI Gateway-dokumenten.

**FRÅGOR OCH**: Vilka alternativ är tillgängliga för haveriberedskap? <br/>
**S**: Du kan använda återställningsnyckeln för att återställa eller flytta en gateway. När du installerar gatewayen anger du återställningsnyckeln.

**FRÅGOR OCH**: Vad är fördelen med återställningsnyckeln? <br/>
**S**: Återställningsnyckeln är ett sätt att migrera eller återställa gatewayinställningarna efter en katastrof.

## <a name="troubleshooting"> </a>Felsökning

**FRÅGOR OCH**: Varför ser jag min gateway i listan med gatewayinstanser när du försöker skapa gatewayresursen i Azure? <br/>
**S**: Det finns två möjliga orsaker. Först är en resurs redan har skapats för gatewayen i aktuellt eller några andra prenumeration. Räkna upp resurser av typen för att eliminera eventuella **lokala Datagatewayer** från portalen. Se till att välja alla prenumerationer vid uppräkning av alla resurser. När resursen har skapats kan gatewayen inte visas i listan med gatewayinstanser i Skapa Gatewayresursen-portalen. Det andra alternativet är att Azure AD-identitet för den användare som installerade gatewayen skiljer sig från användaren loggat in på Azure-portalen. Lös genom att logga in på portalen med samma konto som användaren som installerade gatewayen.

**FRÅGOR OCH**: Hur kan jag se vilka frågor som skickats till den lokala datakällan? <br/>
**S**: Du kan aktivera frågespårning, som innehåller de förfrågningar som skickas. Kom ihåg att ändra frågan spåra tillbaka till det ursprungliga värdet efter felsökningen. Lämna frågespårning aktiverad skapar större loggar.

Du kan också leta i verktyg som datakällan har för att spåra frågor. Du kan till exempel använda Extended Events eller SQL Profiler för SQL Server och Analysis Services.

**FRÅGOR OCH**: Var finns gatewayloggarna? <br/>
**S**: Se loggar för senare i den här artikeln.

### <a name="update"></a>Uppdatera till den senaste versionen

Många problem kan uppstå om gatewayversionen gateway-versionen blir inaktuella. Allmän bra tips är att se till att du använder den senaste versionen. Om du inte har uppdaterat din gateway i en månad eller längre, kan du överväga att installera den senaste versionen av gatewayen och se om du kan återskapa problemet.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Fel: Det gick inte att lägga till användare i gruppen. (-2147463168 PBIEgwService Performance Log Users)

Du kan få det här felet om du försöker installera gatewayen på en domänkontrollant som inte stöds. Kontrollera att du har distribuerat gatewayen på en dator som inte är en domänkontrollant.

## <a name="logs"></a>Loggar

Loggfiler är en viktig resurs när du felsöker.

#### <a name="enterprise-gateway-service-logs"></a>Företagsgatewayens tjänstloggar

`C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\<yyyyymmdd>.<Number>.log`

#### <a name="configuration-logs"></a>Konfigurationsloggar

`C:\Users\<username>\AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator.log`


#### <a name="event-logs"></a>Händelseloggar

Du kan hitta Data Management Gateway och PowerBIGateway loggar under **program- och tjänstloggar**.


## <a name="telemetry"></a>Telemetri
Telemetri kan användas för övervakning och felsökning. Som standard

**Aktivera telemetri**

1.  Kontrollera katalogen lokal data gateway-klienten på datorn. Det är vanligtvis **%systemdrive%\Program Files\On-premises datagateway**. Eller så kan du öppna en tjänstekonsolen och kontrollera sökvägen till körbar fil: En egenskap för lokala data gateway-tjänsten.
2.  I filen Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config från klientkatalog. Ändra SendTelemetry-inställningen till true.
        
    ```
        <setting name="SendTelemetry" serializeAs="String">
                    <value>true</value>
        </setting>
    ```

3.  Spara dina ändringar och starta om Windows-tjänsten: Lokala data gateway-tjänsten.




## <a name="next-steps"></a>Nästa steg
* [Installera och konfigurera den lokala datagatewayen](analysis-services-gateway-install.md).   
* [Manage Analysis Services](analysis-services-manage.md)
* [Hämta data från Azure Analysis Services](analysis-services-connect.md)
