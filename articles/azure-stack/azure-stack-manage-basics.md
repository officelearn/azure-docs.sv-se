---
title: Azure Stack administration grunder | Microsoft Docs
description: Lär dig vad du behöver veta för att administrera Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.openlocfilehash: 5d3073c3ed499ecdb20243e6db2b217aec3e1448
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51254527"
---
# <a name="azure-stack-administration-basics"></a>Azure Stack administration grunder
Det finns flera saker du behöver veta om du inte har använt Azure Stack-administration. Den här vägledningen ger en översikt över din roll som Azure Stack-operatör och vad du behöver att be användarna för dem att snabbt blir mer produktiv.

## <a name="understand-the-builds"></a>Förstå versionerna

### <a name="integrated-systems"></a>Integrerade system

Om du använder ett integrerat Azure Stack-system distribueras uppdaterade versioner av Azure Stack via paket. Du kan importera dessa paket och använda dem med hjälp av panelen uppdateringar i administratörsportalen.
 
### <a name="development-kit"></a>Development kit

Om du använder Azure Stack Development Kit kan du läsa den [vad är Azure Stack?](.\asdk\asdk-what-is.md) artikeln om du vill kontrollera att du förstår syftet med development kit och dess begränsningar. Som en ”sandlåda”, där du kan utvärdera Azure Stack, och utveckla och testa dina appar i en produktionsmiljö bör du använda i development kit. (Mer information om distribution finns i den [distribution av Azure Stack Development Kit](.\asdk\asdk-install.md) artikel.)

Som Azure utveckla vi snabbt. Vi släpper regelbundet nya versioner. Om du kör i development kit och du vill flytta till den senaste versionen, måste du [distribuera om Azure Stack](.\asdk\asdk-redeploy.md). Du kan inte använda uppdateringspaket. Den här processen tar tid, men fördelen är att du kan prova de senaste funktionerna. Development kit-dokumentationen på vår webbplats återspeglar den senaste version-versionen.

## <a name="learn-about-available-services"></a>Lär dig mer om tillgängliga tjänster

Du behöver en medvetenhet för vilka tjänster du kan göra tillgängliga för användarna. Azure Stack har stöd för en delmängd av Azure-tjänster. Listan över tjänster som stöds kommer att fortsätta att utvecklas.

**Grundläggande tjänster**

Som standard Azure Stack innehåller följande ”grundläggande” när du distribuerar Azure Stack:

- Compute
- Storage
- Nätverk
- Key Vault

Med dessa grundläggande tjänster, kan du erbjuda användarna med minimal konfiguration Infrastructure-as-a-Service (IaaS).

**Ytterligare tjänster**

Vi stöder för närvarande följande ytterligare Platform-as-a-Service (PaaS)-tjänster:

- App Service
- Azure Functions
- SQL- och MySQL-databaser

De här tjänsterna kräver ytterligare konfiguration innan du kan göra dem tillgängliga för användarna. Mer information finns i ”Tutorials” och ”How-to guides\Offer tjänster”-avsnitt i vår dokumentation för Azure Stack-operator.

**Planerna för tjänsten**

Azure Stack kommer att fortsätta att lägga till stöd för Azure-tjänster. Planerade översikt över och se den [Azure Stack: en utökning av Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409) White Paper. Du kan också övervaka den [Azure Stack-blogginlägg](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) efter nya meddelanden.

## <a name="what-account-should-i-use"></a>Vilket konto ska jag använda?
Det finns några överväganden för användarkonton som du bör känna till när du hanterar Azure Stack. Särskilt i distributioner med Windows Server Active Directory Federation Services (AD FS) som identitetsleverantör i stället för Azure Active Directory (AD Azure). Följande gäller konto både integrerade Azure Stack-system och ASDK distributioner:


|Konto|Azure AD|AD FS|
|-----|-----|-----|
|Lokal administratör (. \administratör)|ASDK värd administratör|ASDK värd administratör|
|AzureStack\AzureStackAdmin|ASDK värd administratör<br><br>Kan användas för att logga in på administrationsportalen för Azure Stack<br><br>Åtkomst till se och administrera Service Fabric-ringar|ASDK värd administratör<br><br>Ingen åtkomst till Azure Stack-administrationsportalen<br><br>Åtkomst till se och administrera Service Fabric-ringar<br><br>Inte längre ägare av standard providern prenumeration (DPS)|
|AzureStack\CloudAdmin|Kan komma åt och kör tillåtna kommandon inom den privilegierad slutpunkt|Kan komma åt och kör tillåtna kommandon inom den privilegierad slutpunkt<br><br>Kan inte logga in på ASDK värden<br><br>Ägaren av providern Standardprenumeration (DPS)|
|Global Azure AD-administratör|Används under installationen<br><br>Ägaren av providern Standardprenumeration (DPS)|Inte tillämpligt|
|

## <a name="what-tools-do-i-use-to-manage"></a>Vilka verktyg kan jag använda för att hantera?
 
Du kan använda den [administratörsportalen](azure-stack-manage-portals.md) eller PowerShell för att hantera Azure Stack. Det enklaste sättet att lära dig de grundläggande principerna är via portalen. Om du vill använda PowerShell finns steg för förberedelse. Du måste [installera](azure-stack-powershell-install.md) PowerShell [hämta](azure-stack-powershell-download.md) ytterligare moduler och [konfigurera](azure-stack-powershell-configure-admin.md) PowerShell.

Azure Stack använder Azure Resource Manager som dess underliggande mekanism för distribution, hantering och organisation. Om du planerar att hantera Azure Stack och att ge stöd för användare, bör du lära dig om Resource Manager. Se den [komma igång med Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) White Paper.

## <a name="your-typical-responsibilities"></a>Dina vanliga ansvarsområden

Dina användare vill använda tjänster. Deras ur är dina viktigaste roll att gör de här tjänsterna tillgängliga för dem. Du måste avgöra vilka tjänster som erbjuder och tillgängliggöra dessa tjänster genom att skapa planer, erbjudanden och kvoter. Mer information finns i [översikt över erbjudna tjänster i Azure Stack](azure-stack-offer-services-overview.md). 

Du måste också lägga till objekt i [marketplace](azure-stack-marketplace.md), till exempel avbildningar av virtuella datorer. Det enklaste sättet är att [hämta marketplace-objekt från Azure till Azure Stack](azure-stack-download-azure-marketplace-item.md).

> [!NOTE]
> Om du vill testa dina planer, erbjudanden och tjänster, bör du använda den [användarportalen](azure-stack-manage-portals.md); inte administratörsportalen.

Förutom att tillhandahålla tjänster, måste du utföra alla vanliga uppgifter för en operatör att hålla Azure Stack igång. Dessa uppgifter är följande:

- Lägga till användarkonton (för [Azure Active Directory](azure-stack-add-new-user-aad.md) distribution eller för [Active Directory Federation Services](azure-stack-add-users-adfs.md) distribution)
- [Tilldela roller för åtkomstkontroll (RBAC) för rollbaserad åtkomst](azure-stack-manage-permissions.md) (detta är inte begränsad till administratörer.)
- [Övervaka tillståndet i infrastrukturen](azure-stack-monitor-health.md)
- Hantera [nätverk](azure-stack-viewing-public-ip-address-consumption.md) och [storage](azure-stack-manage-storage-accounts.md) resurser
- Ersätta felaktig maskinvara, till exempel [ersätta felaktig](azure-stack-replace-disk.md).

## <a name="what-to-tell-your-users"></a>Vad du ska berätta för dina användare

Du måste du låta dina användare veta hur du arbetar med tjänster i Azure Stack, hur du ansluter till miljön och hur du prenumererar på erbjudanden. Förutom eventuella anpassad dokumentation som du kanske vill ge dina användare, kan du dirigera användare till webbplatsen dokumentation om Azure Stack-användare.

**Förstå hur du arbetar med tjänster i Azure Stack**

Det finns information som användarna måste förstå innan de använder tjänster och skapa appar i Azure Stack. Det finns till exempel särskilda krav för PowerShell och API-versionen. Det finns även vissa funktionen deltan mellan en tjänst i Azure och motsvarande tjänsten i Azure Stack. Se till att dina användare läser följande artiklar:

- [Viktiga överväganden: med hjälp av tjänster eller att skapa appar för Azure Stack](user/azure-stack-considerations.md)
- [Överväganden för virtuella datorer i Azure Stack](user/azure-stack-vm-considerations.md)
- [Lagring: skillnader och överväganden](user/azure-stack-acs-differences.md)

Informationen i de här artiklarna sammanfattas skillnaderna mellan en tjänst i Azure och Azure Stack. Den kompletterar den information som är tillgänglig för en Azure-tjänst i globala Azure-dokumentationen.

**Ansluta till Azure Stack som en användare**

I en utvecklingsmiljö kit, om en användare inte har åtkomst för fjärrskrivbord till development kit värden, måste de konfigurera en anslutning för virtuellt privat nätverk (VPN) innan de kan komma åt Azure Stack. Se [ansluta till Azure Stack](azure-stack-connect-azure-stack.md). 

Dina användare vill veta hur du [åtkomst till användarportalen ](user/azure-stack-use-portal.md) eller hur du ansluter via PowerShell. I en miljö med integrerade system varierar användare Portaladress per distribution. Du måste förse användarna med rätt URL.

Om du använder PowerShell kanske användare registrerar resursprovidrar innan de kan använda tjänster. (En resursprovider hanterar en tjänst. Till exempel nätverk resursprovidern hanterar resurser, till exempel virtuella nätverk, nätverksgränssnitt och belastningsutjämnare.) De måste [installera](user/azure-stack-powershell-install.md) PowerShell [hämta](user/azure-stack-powershell-download.md) ytterligare moduler och [konfigurera](user/azure-stack-powershell-configure-user.md) PowerShell (som innehåller resursproviderregistrering).

**Prenumerera på ett erbjudande**

Innan en användare kan komma åt tjänster, måste de [prenumerera på ett erbjudande](azure-stack-subscribe-plan-provision-vm.md) som du har skapat som en operatör.

## <a name="where-to-get-support"></a>Var du kan få support

### <a name="integrated-systems"></a>Integrerade system

Ett integrerat system har en samordnad eskalering och matchningsprocessen mellan Microsoft och våra samarbetspartners för OEM-tillverkare (original equipment manufacturer).

Om det finns ett problem för cloud services, erbjuds support via Microsofts kundsupport (CSS). Om du klickar du på ikonen Hjälp och support (frågetecken) i det övre högra hörnet av administratörsportalen och klicka sedan på **ny supportbegäran**, öppnas en plats där du kan öppna en supportbegäran om direkt.

Om det finns ett problem med distribution, kontakta patch och uppdatering, maskinvara (inklusive fältet replaceable enheter) och eventuella maskinvara anpassad programvara, till exempel programvara som körs på värden maskinvara livscykel maskinvaruleverantören OEM först.

Kontakta Microsoft-CSS för något annat.

### <a name="development-kit"></a>Development kit

För SDK-paket, kan du be supporten frågor i den [Microsofts forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Om du klickar du på ikonen Hjälp och support (frågetecken) i det övre högra hörnet av administratörsportalen och klicka sedan på **ny supportbegäran**, öppnas på forumwebbplatsen direkt. Dessa forum övervakas regelbundet. Eftersom i development kit är en utvecklingsmiljö, det finns inget officiella stöd som erbjuds via Microsoft CSS.

## <a name="next-steps"></a>Nästa steg

[Regionshantering i Azure Stack](azure-stack-region-management.md)


