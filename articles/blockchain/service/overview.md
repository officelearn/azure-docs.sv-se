---
title: Översikt över Azure Blockchain Service
description: Översikt över tjänsten Azure Blockchain
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 4416c30f57b469ee125400c696e8b34311a94926
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028192"
---
# <a name="what-is-azure-blockchain-service"></a>Vad är Azure Blockchain Service?

Azure Blockchain-Service är en fullständigt hanterad ledger-tjänst som ger användare möjlighet att bygga och driva blockchain nätverk i stor skala i Azure. Genom att tillhandahålla enhetlig kontroll för både infrastruktur samt blockchain nätverk styrning tillhandahåller Azure Blockchain-tjänsten:

* Enkelt nätverksdistribution och drift
* Inbyggda consortium management
* Utveckla smarta kontrakt med välbekanta utvecklingsverktyg

Azure Blockchain Service har utformats för att stödja flera transaktionsregister protokoll. För närvarande den har stöd för Ethereum [kvorum](https://www.jpmorgan.com/Quorum) transaktionsregister med hjälp av den [IBFT](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus) konsensus mekanism.

Dessa funktioner kräver nästan ingen administration och de tillhandahålls utan extra kostnad. Du kan fokusera på utveckling av appar och affärslogik istället för att tid och resurser åt att hantera virtuella datorer och infrastruktur. Du kan dessutom fortsätta att utveckla ditt program med öppen källkod och plattform att leverera dina lösningar utan att behöva lära dig nya färdigheter.

## <a name="network-deployment-and-operations"></a>Nätverksdistribution och åtgärder

Distribuera Azure Blockchain-tjänsten kan göras via Azure-portalen, Azure CLI eller via Visual Studio code med Azure Blockchain-tillägget.  Distribution förenklad, inklusive etablering både transaktioner och verifieraren noder, Azure-nätverk för säkerhetsisolering som tjänsthanterad lagring.  Dessutom när du distribuerar en ny blockchain-medlem kan användare också skapa eller ansluta till en consortium.  Consortiums aktivera flera parter i olika Azure-prenumerationer för att kunna kommunicera säkert med varandra på en delad blockchain.  Den här förenklad distribution minskar blockchain nätverksdistribution från dagar till minuter.

### <a name="performance-and-service-tiers"></a>Programprestanda och nivåer

Tjänsten Azure Blockchain tjänstnivåer två: *Grundläggande* och *Standard*. Varje nivå erbjuder olika prestanda och funktioner till stöd för enkel utveckling och testa arbetsbelastningar upp till massivt skalad blockchain Produktionsdistribution. Båda nivåerna innehåller minst en transaktion nod, och en verifieraren nod (grundläggande) eller två verifieraren noder (Standard).

![Prisnivåer](./media/overview/pricing-tiers.png)

Förutom att du får två verifieraren noder i *Standard* nivån ger 2 *vCores* för varje transaktion och verifieraren nod Basic-nivån erbjuder en 1 vCore-konfiguration.  Genom att erbjuda 2 virtuella kärnor för transaktionen och verifieraren noder, vara 1 virtuell kärna dedikerad till kvorum redovisningen återstående 1 virtuell kärna kan användas för andra infrastruktur-relaterade tjänster som säkerställer optimala prestanda för produktion blockchain-arbetsbelastningar. Mer information om information om priser finns i [priser för Azure Blockchain Service](https://azure.microsoft.com/pricing/details/blockchain-service).

### <a name="security-and-maintenance"></a>Säkerhet och underhåll

När du har etablerat din första blockchain-medlemmen har du möjlighet att lägga till ytterligare transaktionsloggar noder i dina medlem.  Som standard transaktion noder skyddas via brandväggsregler och måste konfigureras för åtkomst.  Dessutom kan kryptera alla noder i transaktionen data i rörelse via TLS.  Det finns flera alternativ för att skydda transaktion noden åtkomst till, inklusive brandväggsregler, grundläggande autentisering, åtkomstnycklar, samt Azure Active Directory-integrering. Mer information finns i [konfigurera transaktion noder](configure-transaction-nodes.md) och [Konfigurera åtkomst till Azure Active Directory](configure-aad.md).

Som en hanterad tjänst garanterar Azure Blockchain Service att dina blockkedjor medlemsnoder som var är uppdaterad med den senaste värden driva system- och stack programuppdateringar, konfigurerats för hög tillgänglighet (endast Standard-nivån), vilket eliminerar mycket av DevOps krävs för traditionella IaaS blockchain-noder.  Mer information om korrigeringar och uppdateringar finns i [Azure Blockchain Service transaktionsregister versioner som stöds](ledger-versions.md).

### <a name="monitoring-and-logging"></a>Övervakning och loggning

Azure Blockchain Service tillhandahåller även omfattande mått via Azure Monitor-tjänsten tillhandahåller insikter om nodernas processor, minne och lagringsutrymme, samt användbara insikter om blockchain nätverksaktivitet, till exempel transaktioner och block som används, ködjup för transaktion, samt aktiva anslutningar.  Mått kan anpassas för att tillhandahålla vyer till insikter som är viktiga för ditt blockchain-program.  Dessutom kan du definiera tröskelvärden via aviseringar som användare att utlösa åtgärder, till exempel skickar ett e-post eller SMS-meddelande, som kör en Logikapp, Azure-funktion eller skickas till ett egendefinierat webhook.

![Mått](./media/overview/metrics.png)

Användare kan visa loggar som rör kvorum redovisningen eller andra viktig information som försökt anslutningar till transaktion noder via Azure Log Analytics.

## <a name="built-in-consortium-management"></a>Inbyggda consortium management

När du distribuerar din första blockchain-medlemmen kan antingen ansluta till eller skapa en ny consortium.  Ett konsortium är en logisk grupp som används för att hantera styrning och anslutning mellan blockchain-medlemmar som transact i en flerparti process.  Azure Blockchain Service tillhandahåller inbyggd styrning kontroller genom fördefinierade smarta kontrakt, som avgör vilka åtgärder som medlemmar i consortium kan vidta.  Kontrollerna styrning kan anpassas efter behov av administratör för consortium. När du skapar en ny consortium är din blockchain-medlemmen administratör av consortium, aktivera möjligheten att bjuda in andra parter att ansluta till din consortium.  Du kan ansluta till ett konsortium endast om du har bjudits in tidigare.  När du ansluter ett konsortium lyder blockchain-medlemmen under styrning-kontroller som införts av den consortium administratör.

![Consortium management](./media/overview/consortium.png)

Consortium hanteringsåtgärder som att lägga till och ta bort medlemmar i en consortium kan nås via PowerShell och REST-API. Programmässigt kan du hantera ett konsortium med hjälp av vanliga gränssnitt i stället ändra och skicka solidity-baserade smarta kontrakt. Mer information finns i [consortium management](consortium.md).

## <a name="develop-using-familiar-development-tools"></a>Utveckla med välbekanta utvecklingsverktyg

Baserat på öppen källkod kvorum Ethereum redovisningen, kan du utveckla program för Azure Blockchain-tjänsten på samma sätt som du gör med befintliga Ethereum-program. Arbeta med ledande branschpartners, kan Azure Blockchain Development Kit Visual Studio Code-tillägg utvecklare kan utnyttja bekanta verktyg som Truffle Suite för att skapa smarta kontrakt. Använd tillägget Azure Blockchain Development Kit och utvecklare kan skapa eller ansluta till och befintliga consortium så att du kan skapa och distribuera dina smarta kontrakt alla från en IDE. Med Azure Blockchain Visual Studio Code-tillägg kan du skapa eller ansluta till en befintlig consortium så att du kan skapa och distribuera dina smarta kontrakt allt från en IDE. Mer information finns i [Azure Blockchain Development Kit i VS Code marketplace](http://aka.ms/vscodebcextension) och [användarhandboken för Azure Blockchain Development Kit](http://aka.ms/vscodebcextensionwiki ).

## <a name="support-and-feedback"></a>Support och feedback

Behöver hjälp eller har du feedback?

* Gå till den [Azure blockkedjeblogg](https://azure.microsoft.com/blog/topics/blockchain/), [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain), och [Azure Blockchain-forumet](https://social.msdn.microsoft.com/Forums/home?forum=azureblockchain).
* Om du vill ge feedback eller begära nya funktioner, skapar du ett inlägg via [UserVoice](https://feedback.azure.com/forums/921130-azure-blockchain-service).

## <a name="next-steps"></a>Nästa steg

Kom igång genom att testa en Snabbstart eller ta reda på mer information från dessa resurser.
* [Skapa en blockchain-medlem med Azure portal](create-member.md) eller [skapa medlem blockchain med Azure CLI]()
* Kostnadsjämförelse och Kostnadsberäknare, finns i den [prissättningssidan](https://azure.microsoft.com/pricing/details/blockchain-service).
* Skapa din första app med den [Azure Blockchain Development Kit](https://github.com/Azure-Samples/blockchain-devkit)
* Azure Blockchain VSCode-tillägg [Användarhandbok](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki)
