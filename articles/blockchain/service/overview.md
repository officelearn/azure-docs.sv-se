---
title: Översikt över Azure Blockchain-tjänsten
description: Översikt över Azure Blockchain-tjänsten
ms.date: 03/30/2020
ms.topic: overview
ms.reviewer: ravastra
ms.openlocfilehash: 821bac0da13209e5126f5bab109aa0895ade840a
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529146"
---
# <a name="what-is-azure-blockchain-service"></a>Vad är Azure Blockchain Service?

Azure Blockchain Service är en fullständigt hanterad redovisningstjänst som gör det möjligt för användare att växa och driva blockchain-nätverk i stor skala i Azure. Genom att tillhandahålla enhetlig kontroll för både infrastrukturhantering och styrning av blockchain-nätverk tillhandahåller Azure Blockchain Service:

* Enkel nätverksdistribution och drift
* Inbyggd konsortiumförvaltning
* Utveckla smarta kontrakt med välbekanta utvecklingsverktyg

Azure Blockchain Service har utformats för att stödja flera redovisningsprotokoll. För närvarande ger det stöd för Ethereum [Quorum](https://www.goquorum.com/) liggare med hjälp av [Istanbul bysantinska feltolerans (IBFT)](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus) konsensus mekanism.

Dessa funktioner kräver nästan ingen administration och de tillhandahålls utan extra kostnad. Du kan fokusera på apputveckling och affärslogik i stället för att tilldela tid och resurser för att hantera virtuella datorer och infrastruktur. Dessutom kan du fortsätta att utveckla ditt program med de verktyg och en plattform med öppen källkod som du väljer för att leverera dina lösningar utan att behöva lära dig nya färdigheter.

## <a name="network-deployment-and-operations"></a>Nätverksdistribution och åtgärder

Distribution av Azure Blockchain-tjänsten sker via Azure-portalen, Azure CLI eller genom Visual Studio-kod med Azure Blockchain-tillägget. Distributionen förenklas, inklusive etablering av både transaktions- och validatornoder, Azure Virtual Networks för säkerhetsisolering samt tjänsthanterad lagring.  När användarna distribuerar en ny blockchain-medlem skapar eller ansluter användarna dessutom till ett konsortium.  Konsortier gör det möjligt för flera parter i olika Azure-prenumerationer att på ett säkert sätt kommunicera med varandra på en delad blockchain.  Den här förenklade distributionen minskar distributionen av blockchain-nätverk från dagar till minuter.

### <a name="performance-and-service-tiers"></a>Prestanda- och tjänstnivåer

Azure Blockchain Service erbjuder två tjänstnivåer: *Basic* och *Standard*. Varje nivå erbjuder olika prestanda och funktioner för att stödja lättviktsutveckling och testarbetsbelastningar upp till kraftigt skalade produktionskedjedistributioner. Använd *basic-nivån* för utveckling, testning och bevis på begrepp. Använd *standardnivån* för distributioner av produktionsresultat. Båda nivåerna innehåller minst en transaktionsnod och en validerarnod (Basic) eller två validerare noder (Standard). 

![Prisnivåer](./media/overview/pricing-tiers.png)

Förutom att erbjuda två validerarnoder tillhandahåller *standardnivån* två *virtuella kärnor* för varje transaktions- och validatornod medan *basic-nivån* erbjuder en 1 vCore-konfiguration.  Genom att erbjuda 2 virtuella kärnor för transaktions- och validatornoder kan 1 vCore dedikeras till kvorumredovisningen medan de återstående 1 virtuella kärnorna kan användas för andra infrastrukturrelaterade tjänster, vilket säkerställer optimal prestanda för produktions blockchain-arbetsbelastningar. Mer information om prisinformation finns i [Azure Blockchain Service-priser](https://azure.microsoft.com/pricing/details/blockchain-service).

### <a name="security-and-maintenance"></a>Säkerhet och underhåll

När du har etablerat din första blockchain-medlem har du möjlighet att lägga till ytterligare transaktionsnoder till din medlem.  Som standard skyddas transaktionsnoder genom brandväggsregler och kräver konfiguration för åtkomst.  Dessutom krypterar alla transaktionsnoder data i rörelse via TLS.  Det finns flera alternativ för att skydda åtkomst till transaktionsnoder, inklusive brandväggsregler, grundläggande autentisering, åtkomstnycklar och Azure Active Directory-integrering. Mer information finns i [konfigurera transaktionsnoder](configure-transaction-nodes.md) och [konfigurera Azure Active Directory-åtkomst](configure-aad.md).

Som en hanterad tjänst säkerställer Azure Blockchain Service att din blockchain-medlems noder korrigeras med de senaste värdoperativsystemen och redovisningsprogramstackuppdateringarna, konfigurerade för hög tillgänglighet (endast standardnivå), vilket eliminerar mycket av DevOps som krävs för traditionella IaaS blockchain-noder.  Mer information om korrigering och uppdateringar finns i [Azure Blockchain Service-redovisningsversioner som stöds](ledger-versions.md).

### <a name="monitoring-and-logging"></a>Övervakning och loggning

Dessutom tillhandahåller Azure Blockchain Service omfattande mått via Azure Monitor Service som ger insikter om noders CPU, minne och lagringsanvändning.  Azure Monitor ger också användbara insikter om blockchain-nätverksaktivitet som transaktioner och blockeringar som bryts, transaktionsklindjup och aktiva anslutningar.  Mått kan anpassas för att ge vyer till de insikter som är viktiga för ditt blockchain-program.  Dessutom kan tröskelvärden definieras genom aviseringar som gör det möjligt för användare att utlösa åtgärder som att skicka ett e-postmeddelande eller sms, köra en Logikapp, Azure-funktion eller skicka till en anpassad definierad webhook.

![Mått](./media/overview/metrics.png)

Via Azure Log Analytics kan användare visa loggar som är relaterade till kvorumredovisningen eller annan viktig information, till exempel försök till anslutningar till transaktionsnoderna.

## <a name="built-in-consortium-management"></a>Inbyggd konsortiumförvaltning

När du distribuerar din första blockchain-medlem går du antingen med i eller skapar ett nytt konsortium.  Ett konsortium är en logisk grupp som används för att hantera styrning och konnektivitet mellan blockchain-medlemmar som handlar i en process med flera parter.  Azure Blockchain Service tillhandahåller inbyggda styrningskontroller genom fördefinierade smarta kontrakt, som avgör vilka åtgärder medlemmar i konsortiet kan vidta.  Dessa styrningskontroller kan anpassas efter behov av konsortieadministratören. När du skapar ett nytt konsortium är din blockchain-medlem standardadministratör för konsortiet, vilket gör det möjligt att bjuda in andra parter att gå med i ditt konsortium.  Du kan bara gå med i ett konsortium om du tidigare har bjudits in.  När du går med i ett konsortium omfattas din blockchain-medlem av de styrningskontroller som konsortiets administratör har infört.

![Konsortieledning](./media/overview/consortium.png)

Konsortiehanteringsåtgärder som att lägga till och ta bort medlemmar från ett konsortium kan nås via PowerShell och ett REST API. Du kan programmässigt hantera ett konsortium med hjälp av gemensamma gränssnitt i stället för att ändra och skicka soliditetsbaserade smarta kontrakt. Mer information finns i [konsortiehantering](consortium.md).

## <a name="develop-using-familiar-development-tools"></a>Utveckla med välbekanta utvecklingsverktyg

Baserat på den kvorumeteum-huvudreskontra med öppen källkod kan du utveckla program för Azure Blockchain Service på samma sätt som för befintliga Ethereum-program. Azure Blockchain Development Kit Visual Studio Code-tillägget samarbetar med ledande branschpartners och gör det möjligt för utvecklare att utnyttja välbekanta verktyg som Tryffel Suite för att skapa smarta kontrakt. Med hjälp av Tillägget Azure Blockchain Development Kit kan utvecklare skapa eller ansluta till och befintligt konsortium så att du kan skapa och distribuera dina smarta kontrakt från en IDE. Med azure blockchain Visual Studio Code-tillägget kan du skapa eller ansluta till ett befintligt konsortium så att du kan skapa och distribuera dina smarta kontrakt från ett IDE. Mer information finns [i Azure Blockchain Development Kit på VS-kodmarknadsplatsen](https://aka.ms/vscodebcextension) och [användarhandboken för Azure Blockchain Development Kit](https://aka.ms/vscodebcextensionwiki).

## <a name="publish-blockchain-data"></a>Publicera blockkedjedata

Blockchain Data Manager för Azure Blockchain Service samlar in, transformerar och levererar Azure Blockchain Service-transaktionsdata till Azure Event Grid-ämnen som ger tillförlitlig och skalbar blockchain-redovisningsintegrering med Azure-tjänster. Du kan använda Blockchain Data Manager för att integrera appar och datalager utanför kedjan. Mer information finns i [Blockchain Data Manager för Azure Blockchain Service](data-manager.md).

## <a name="support-and-feedback"></a>Support och feedback

För Azure Blockchain-nyheter kan du besöka [Azure Blockchain-bloggen](https://azure.microsoft.com/blog/topics/blockchain/) för att hålla dig uppdaterad om blockchain-tjänsterbjudanden och information från Azure Blockchain-teknikteamet.

Om du vill ge produktfeedback eller begära nya funktioner kan du rösta på en idé via [Azures feedbackforum för blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Community-support

Samarbeta med Microsoft-tekniker och Azure Blockchain-communityexperter.

* [Azure Blockchain MSDN-forum](https://social.msdn.microsoft.com/Forums/home?forum=azureblockchain)
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/AzureBlockchainService)

## <a name="next-steps"></a>Nästa steg

Prova en snabbstart eller mer information från dessa resurser för att komma igång.
* [Skapa en blockchain-medlem med Azure-portalen](create-member.md) eller [skapa en blockchain-medlem med Azure CLI](create-member-cli.md)
* Kostnadsjämställare och kalkylatorer finns på [prissidan](https://azure.microsoft.com/pricing/details/blockchain-service).
* Skapa din första app med Hjälp av [Azure Blockchain Development Kit](https://github.com/Azure-Samples/blockchain-devkit)
* [Användarhandbok](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki) för Azure Blockchain VSCode Extension
