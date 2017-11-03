---
title: Azure Stack administration grunderna | Microsoft Docs
description: "Lär dig vad du behöver veta för att administrera Azure stacken."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 04dea8f055eb562455b568c43553a6fefe749467
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-administration-basics"></a>Grunderna i Azure Stack administration

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Det finns flera saker du behöver veta om du har använt Azure Stack-administration. Vägledningen innehåller en översikt över din roll som operatör Azure Stack och vad du behöver för att tala om för användarna för att snabbt blir mer produktiv.

## <a name="understand-the-builds"></a>Förstå versionerna

### <a name="integrated-systems"></a>Integrerade system

Om du använder ett Azure-stacken integrerat system distribueras uppdaterade versioner av Azure-stacken genom uppdateringspaket. Du kan importera dessa paket och använda dem med hjälp av panelen uppdateringar i administratörsportalen.
 
### <a name="development-kit"></a>Development kit

Om du använder Azure Stack Development Kit granska den [vad är Azure Stack?](azure-stack-poc.md) artikel för att kontrollera att du förstå syftet med development kit och vissa begränsningar. Du bör använda development kit som en ”sandbox”, där du kan utvärdera Azure Stack och utveckla och testa dina appar i en produktionsmiljö. (Information om distribution finns i [Azure Stack Development Kit distribution](azure-stack-deploy-overview.md) quickstart.)

Som Azure förnya det snabbt. Regelbundet släpper vi nya versioner. Om du kör i development kit och du vill flytta till den senaste versionen, måste du [omdistribuera Azure Stack](azure-stack-redeploy.md). Du kan inte använda uppdateringspaket. Den här processen tar tid, men fördelen är att du kan prova att använda de senaste funktionerna. Development kit-dokumentationen på vår webbplats visar den senaste version-versionen.

## <a name="learn-about-available-services"></a>Lär dig mer om tillgängliga tjänster

Du behöver en medvetenhet för tjänster som du kan göra tillgängliga för användarna. Azure-stacken stöder en delmängd av Azure-tjänster. Listan över stöds tjänster fortsätter att utvecklas.

**Grundläggande tjänster**

Azure-stacken innehåller som standard följande ”grundläggande tjänster” när du distribuerar Azure Stack:

- Compute
- Lagring
- Nätverk
- Key Vault

Dessa grundläggande tjänster kan du erbjuda Infrastructure-as-a-Service (IaaS) till användarna med minimal konfiguration.

**Ytterligare tjänster**

Vi stöder för närvarande följande ytterligare plattform som en-tjänst (PaaS)-tjänster:

- App Service
- Azure Functions
- SQL- och MySQL-databaser

Dessa tjänster kräver ytterligare konfiguration innan du kan göra dem tillgängliga för användarna. Mer information finns i ”självstudier” och ”anvisningar guides\Offer tjänster” avsnitt av vår dokumentation för Azure Stack-operator.

**Översikt över Service**

Azure-stacken fortsätter att lägga till stöd för Azure-tjänster. Planerade översikt finns i [Azure stacken: ett tillägg för Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409) vitbok. Du kan också övervaka den [Azure Stack blogginlägg](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) efter nya meddelanden.

## <a name="what-tools-do-i-use-to-manage"></a>Vilka verktyg använda för att hantera?
 
Du kan använda den [administratörsportal](azure-stack-manage-portals.md) eller PowerShell för att hantera Azure-stacken. Det enklaste sättet att lära dig grunderna är via portalen. Om du vill använda PowerShell finns förberedelser. Du måste [installera](azure-stack-powershell-install.md) PowerShell [hämta](azure-stack-powershell-download.md) ytterligare moduler och [konfigurera](azure-stack-powershell-configure-admin.md) PowerShell.

Azure-stacken använder Azure Resource Manager som dess underliggande mekanism för distribution, hantering och organisation. Om du ska hantera Azure-stacken och stöd för användare, bör du lära dig om Resource Manager. Finns det [komma igång med Azure Resource Manager](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) vitbok.

## <a name="your-typical-responsibilities"></a>Dina vanliga ansvarsområden

Dina användare vill använda tjänster. Din huvudsakliga roll är att tjänsterna är tillgängliga för dem från sina perspektiv. Du måste bestämma vilka tjänster att erbjuda och tillhandahålla de tjänsterna genom att skapa planer, erbjudanden och kvoter. Mer information finns i [översikt över erbjuda tjänster i Azure-stacken](azure-stack-offer-services-overview.md). 

Du måste också lägga till objekt i [marketplace](azure-stack-marketplace.md), till exempel avbildningar av virtuella datorer. Det enklaste sättet är att [hämta marketplace-objekt från Azure till Azure-stacken](azure-stack-download-azure-marketplace-item.md).

> [!NOTE]
> Om du vill testa dina planer, erbjudanden och tjänster, bör du använda den [användarportalen](azure-stack-manage-portals.md); inte administratörsportalen.

Förutom att tillhandahålla tjänster, måste du utföra alla vanliga uppgifter för en operator att Azure-stacken igång. Dessa uppgifter inkluderar följande:

- Lägga till användarkonton (för [Azure Active Directory](azure-stack-add-new-user-aad.md) distribution eller för [Active Directory Federation Services](azure-stack-add-users-adfs.md) distribution)
- [Tilldela roller för åtkomstkontroll (RBAC) för rollbaserad åtkomst](azure-stack-manage-permissions.md) (detta är inte begränsade till administratörer.)
- [Övervaka infrastrukturen hälsa](azure-stack-monitor-health.md)
- Hantera [nätverk](azure-stack-viewing-public-ip-address-consumption.md) och [lagring](azure-stack-manage-storage-accounts.md) resurser
- Ersätta felaktig maskinvara, till exempel [ersätta en disk som misslyckats](azure-stack-replace-disk.md).

## <a name="what-to-tell-your-users"></a>Vad du ska berätta för användarna

Du måste du låta dina användare veta hur du arbetar med tjänster i Azure-stacken, hur du ansluter till miljön och hur du prenumererar på erbjudanden. Förutom eventuella anpassade dokumentationen som du kanske vill ge dina användare kan du dirigera användare till användare dokumentationsplatsen för Azure-stacken.

**Förstå hur du arbetar med tjänster i Azure-stacken**

Det finns information som användarna måste förstå innan de använder tjänster och skapa appar i Azure-stacken. Det finns till exempel särskilda krav för API: er och PowerShell version. Det finns även vissa funktionen går mellan en tjänst i Azure och motsvarande tjänst i Azure-stacken. Se till att användarna granska följande artiklar:

- [Nyckeln överväganden: använda tjänster eller utveckla appar för Azure-Stack](user/azure-stack-considerations.md)
- [Överväganden för virtuella datorer i Azure-stacken](user/azure-stack-vm-considerations.md)
- [Lagring: skillnader och överväganden](user/azure-stack-acs-differences.md)

Informationen i de här artiklarna sammanfattas skillnaderna mellan en tjänst i Azure och Azure-stacken. Det förbättrar den information som är tillgänglig för en Azure-tjänst i globala Azure-dokumentationen.

**Ansluta till Azure-stacken som en användare**

I en utvecklingsmiljö kit, om en användare inte har åtkomst via fjärrskrivbord till development kit värden, måste de konfigurera en anslutning för virtuellt privat nätverk (VPN) innan de kan komma åt Azure-stacken. Se [ansluta till Azure-stacken](azure-stack-connect-azure-stack.md). 

Dina användare vill du veta hur du [åtkomst till användarportalen ](user/azure-stack-use-portal.md) eller hur du ansluter via PowerShell. I en miljö med integrerade system varierar användare Portaladress per distribution. Du behöver ge användarna med rätt URL.

Om du använder PowerShell kanske användare att registrera resursproviders innan de kan använda tjänsterna. (En resursleverantör hanterar en tjänst. Till exempel nätverk resursprovidern hanterar resurser, till exempel virtuella nätverk, nätverkskort och belastningsutjämnare.) De måste [installera](user/azure-stack-powershell-install.md) PowerShell [hämta](user/azure-stack-powershell-download.md) ytterligare moduler och [konfigurera](user/azure-stack-powershell-configure-user.md) PowerShell (som inkluderar registrering av resursprovider).

**Prenumerera på ett erbjudande**

Innan en användare har åtkomst till tjänster, måste de [prenumererar på ett erbjudande](azure-stack-subscribe-plan-provision-vm.md) som du har skapat som en operatör.

## <a name="where-to-get-support"></a>Var du kan få support

### <a name="integrated-systems"></a>Integrerade system

För ett integrerat system finns ett samordnat eskalering och lösningsprocessen mellan Microsoft och våra samarbetspartners för OEM-tillverkaren (OEM).

Om det finns ett problem med cloud services, erbjuds support via Microsoft kundservice (CSS). Om du klickar du på ikonen Hjälp och support (frågetecken) i övre högra hörnet i administratörsportalen och klicka sedan på **ny supportbegäran**, öppnas en plats där du kan öppna en supportbegäran direkt.

Om det finns ett problem med distribution, kontakta korrigering och uppdateringen, maskinvara (inklusive enheter som kan bytas fältet) och alla maskinvaru-märkta programvara, till exempel program som körs på värddatorn för maskinvara livscykel, maskinvaruleverantören OEM först.

Kontakta Microsoft-CSS för något annat.

### <a name="development-kit"></a>Development kit

För SDK-paket, kan du be support-relaterade frågor i den [Microsoft forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Om du klickar du på ikonen Hjälp och support (frågetecken) i övre högra hörnet i administratörsportalen och klicka sedan på **ny supportbegäran**, forum plats öppnas direkt. Dessa forum övervakas regelbundet. Eftersom development kit är en utvärderingsmiljö, stöds inte officiellt erbjuds via Microsoft CSS.

## <a name="next-steps"></a>Nästa steg

- [Regionhantering av i Azure-stacken](azure-stack-region-management.md)


