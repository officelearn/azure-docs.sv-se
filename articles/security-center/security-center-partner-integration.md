---
title: Partnerintegrering i Azure Security Center | Microsoft Docs
description: "Läs hur Azure Security Center integrerar med partners för att förbättra den övergripande säkerheten för dina Azure-resurser."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 8cc44da0f61362018d2757da58ca4fb3a9a43764
ms.contentlocale: sv-se
ms.lasthandoff: 09/14/2017

---
# <a name="partner-and-solutions-integration-in-azure-security-center"></a>Partnerintegrering i Azure Security Center

I den här artikeln beskriver vi hur Azure Security Center kan integreras med partners för att hjälpa dig att förbättra den övergripande säkerheten. Security Center ger dig en integrerad upplevelse i Azure och drar nytta av Azure Marketplace för partnercertifiering och fakturering.

## <a name="why-deploy-partner-solutions-from-security-center"></a>Varför ska du distribuera partnerlösningar från Security Center

De fyra viktigaste skälen att dra nytta av partnerintegrering i Security Center är:

- **Enkel distribution**. Det är mycket enklare att distribuera en partnerlösning genom att följa Security Center-rekommendationen. Distributionsprocessen kan automatiseras helt med hjälp av en standardiserad installation och nätverkstopologi. Kunder kan också välja ett halvautomatiserat alternativ för mer flexibilitet och anpassning.
- **Integrerade identifieringar**. Säkerhetshändelser från partnerlösningar samlas in, aggregeras och visas automatiskt som en del av aviseringarna och incidenterna i Security Center. Dessa händelser kombineras också med identifieringar från andra källor för att tillhandahålla funktioner för avancerad hotidentifiering.
- **Enhetlig hälsoövervakning och hantering**. Kunder kan använda integrerade hälsohändelser för att få en snabbövervakning av alla partnerlösningar. Grundläggande hantering finns tillgängligt med enkel åtkomst till avancerad konfiguration med hjälp av partnerlösningen.
- **Exportera till SIEM**. Kunder kan exportera alla Security Center- och partneraviseringar i Common Event Format (CEF) till lokala säkerhetsinformations och händelsehanterings-system (SIEM) med hjälp av Azure loggintegrering (förhandsversion).


## <a name="partners-that-integrate-with-security-center"></a>Partners som integreras med Security Center

För närvarande finns följande inbyggda funktioner för integrering av partnerlösningar i Microsoft Azure Marketplace med Security Center:

- Slutpunktsskydd ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec och [Microsoft Antimalware för Azure Cloud Services och Virtual Machines](https://docs.microsoft.com/azure/security/azure-security-antimalware)) 
- Brandvägg för webbaserade program ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets) och [Azure Application Gateway](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)) 
- Nästa generations brandvägg ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) och [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)) 
- Sårbarhetsbedömning ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

Framöver kommer Security Center att utöka antalet partners inom de här kategorierna samt lägga till fler kategorier. 

## <a name="deploy-a-partner-solution"></a>Distribuera en partnerlösning

Beroende på konfigurationen av din Azure-miljö och den säkerhetsprincip som du har definierat, kan Security Center rekommendera du distribuerar en partnerlösning. Rekommendationen vägleder dig genom processen att välja och installera en partnerlösning. Den övergripande distributionsupplevelsen kan variera beroende på typen av lösning och partner. Mer information finns i följande artiklar:

- [Installera slutpunktsskydd](security-center-install-endpoint-protection.md)
- [Lägga till en brandvägg för webbappar](security-center-add-web-application-firewall.md)
- [Lägg till en nästa generations brandvägg](security-center-add-next-generation-firewall.md)
- [Sårbarhetsbedömning inte installerad](security-center-vulnerability-assessment-recommendations.md)

## <a name="manage-partner-solutions"></a>Hantera partnerlösningar

Efter distributionen kan du visa information om hälsotillståndet för lösningen och utföra grundläggande hanteringsåtgärder genom att gå in i instrumentpanelen **Security Center** och välja alternativet **Partnerlösningar**.

![Integrerings av partnerlösningar](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

Det innehåll som du ser när du öppnar Security Solutions kan variera beroende på din infrastruktur. Med föregående bild som exempel har den här sidan tre avsnitt:

- **Anslutna lösningar**: visar lösningar som är anslutna till Security Center.
- **Identifierade lösningar**: visar lösningar som inte är anslutna till Security Center. Du kan ansluta dessa lösningar som då visas under Anslutna lösningar.  Det här avsnittet är dolt om Security Center inte kan identifiera några lösningar som inte är anslutna.
- **Lägg till datakällor**: visar Azure-datakällor och andra datakällor som du kan lägga till i Security Center.

### <a name="connected-solutions"></a>Anslutna lösningar

Avsnittet **Anslutna lösningar** visar alla säkerhetslösningar som är anslutna till Security Center för närvarande. 

![Anslutna lösningar](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Informationen som visas i varje avsnitt kan variera beroende på lösningen som används. En del information tillgänglig i varje panel kan innehålla:

- Partnerns företagsikon.  Om Security Center inte har företagsikonen, visas det eller de första tecknen i partnerns namn.
- Typ av lösning.
- Det kan hända att datornamnet visas.
- Hälsostatus.  Om en indikator för hälsotillstånd inte skickas visar Security Center datumet och tiden för senast mottagna händelse för att indikera om installationen rapporterar eller inte. Om Security Center inte får indikatorn för hälsotillstånd från en viss lösning, visas inte lösningens panel i det här avsnittet.

> [!NOTE]
> Security Center visar datum och tid för senast mottagna händelse för att indikera om installationen rapporterar eller inte. Lösningar som inte skickar en indikator för hälsotillstånd visas som anslutna om en avisering eller händelse skickades under de 14 senaste dagarna.
>  

Vissa av dessa lösningar kan integreras fullständigt i Azure, medan andra kan vara lokala. Eftersom Security Center stöder [CEF-formatet (Common Event Format)](https://docs.microsoft.com/azure/operations-management-suite/oms-security-connect-products#what-is-cef) kan den ansluta till lösningar som också använder CEF, till exempel en brandvägg som stöder CEF. När lösningen har lagts till i Security Center skickar brandväggen loggar i CEF-format till Security Center, som då hanterar loggarna i [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview). Brandväggen är inte en Azure-resurs och skickar därför händelser men ingen indikator för hälsotillstånd.  Den enda information Security Center har om hälsotillståndet är senaste gången den här installationen skickade en händelse.  För alla resurser som inte är Azure-resurser visar Security Center datumet och tiden då den senaste händelsen togs emot i hälsotillståndsområdet i panelen. Det indikerar att Azure-resursen fortfarande rapporterar.

### <a name="discovered-solutions"></a>Identifierade lösningar

Avsnittet **Identifierade lösningar** visar alla lösningar som har lagts till via Azure och som Security Center föreslår en anslutning till.

![Identifierade lösningar](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Security Center kan integreras med inbyggda Azure-lösningar som [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection). Om du har en licens för Azure AD Identity Protection, men inte har anslutit lösningen till Security Center, visas Azure AD Identity Protection under **Identifierade lösningar**. Om du vill integrera den här lösningen med Security Center, klickar du på **Anslut** på panelen **Azure AD Identity Protection**. Följande sida visas:

![Azure AD Identity Protection](./media/security-center-partner-integration/security-center-partner-integration-fig6.png)

Du måste välja den arbetsyta som data sparas i för att slutföra anslutningen av Azure AD Identity Protection. Alla data från Azure AD Identity Protection flödar från den arbetsyteregion som valdes i det här steget.  När du har gått igenom väljaren för arbetsytor och valt arbetsyta börjar data att flöda där.

Om du ska kunna ansluta till Security Center måste du vara global administratör eller en säkerhetsadministratör.  Knappen **Anslut** inaktiveras om du inte har behörighet. Om du inte har behörighet visas dessutom ett meddelande som förklarar varför knappen är inaktiverad.

Azure AD Identity Protection-aviseringar går igenom Security Centers identifieringsledning. På så sätt kan du hämta aviseringar från både Security Center och Azure Active Directory Identity Protection. Security Center sammanfogar alla aviseringar som verkar relevanta för att skapa en [säkerhetsincident](https://docs.microsoft.com/azure/security-center/security-center-incident). Beskrivningen av säkerhetsincidenten ger mer information om misstänkt aktivitet.

### <a name="add-data-sources"></a>Lägg till datakällor

Du kan lägga till Azure-datorer och andra datorer för integrering med Security Center.  Lägga till andra datorer än Azure-datorer innebär att du kan lägga till lokala datorer eller en installation som stöder CEF. 

![Datakällor](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="see-also"></a>Se även

Den här artikeln berättade om hur man integrerar partnerlösningar i Azure Security Center. I följande artiklar kan du lära dig mer om Security Center:

* [Planerings- och bruksanvisningsguide för Security Center](security-center-planning-and-operations-guide.md)
* [Hantera och besvara säkerhetsaviseringar i Security Center](security-center-managing-and-responding-alerts.md)
* [Säkerhetsaviseringar efter typ i Security Center](security-center-alerts-type.md)
* [Övervakning av säkerhetshälsa i Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Övervaka partnerlösningar med Security Center](security-center-partner-solutions.md). Lär dig att övervaka hälsotillståndet för dina partnerlösningar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Få svar på vanliga frågor om att använda tjänsten.
* [Azure säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

