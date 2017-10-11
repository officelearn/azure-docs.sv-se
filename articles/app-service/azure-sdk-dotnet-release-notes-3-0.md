---
title: "Azure SDK för .NET 3.0 viktig information | Microsoft Docs"
description: "Azure SDK för .NET 3.0 viktig information"
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: 
ms.assetid: c83d815b-fc19-4260-821e-7d2a7206dffc
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 03/07/2017
ms.author: juliako
ms.openlocfilehash: eea4e569ac2d0192ed7872d2fcb9bed03614832b
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="azure-sdk-for-net-30-release-notes"></a>Azure SDK för .NET 3.0 viktig information

Det här avsnittet innehåller viktig information för version 3.0 av Azure SDK för .NET.

##<a name="azure-sdk-for-net-30-release-summary"></a>Azure SDK för .NET 3.0-utgåvan sammanfattning

Utgivningsdatum: 03/07/2017
 
Inga senaste Azure SDK 3.0 ändringar har införts i den här versionen. Det finns inga uppgraderingsprocessen som behövs för att utnyttja detta SDK med befintliga Cloud Service-projekt. Om du vill tillåta användning av Azure SDK 3.0 utan en uppgraderingsprocessen installerar Azure SDK 3.0 till samma kataloger som Azure SDK 2.9. De flesta komponenter att ändra inte den högre versionen från 2.9 men i stället uppdatera build-nummer.

## <a name="visual-studio-2017-rtw"></a>Visual Studio 2017 RTW

- I Visual Studio 2017 skapas den här versionen av Azure SDK för .NET Azure-arbetsbelastning. Alla verktyg som du behöver göra Azure-utveckling ska ingå i Visual Studio 2017 framöver. SDK: N blir fortfarande tillgängliga via WebPI för Visual Studio 2015. Vi har avbrutit Azure SDK för .NET-versioner för Visual Studio 2013 nu när Visual Studio 2017 har släppts.

### <a name="azure-diagnostics"></a>Azure Diagnostics

- Ändra beteende för att lagra endast en partiell anslutningssträng med nyckel ersättas med en token för anslutningssträngen för lagring av molntjänster diagnostik. Den faktiska lagringsnyckeln lagras nu på mappen så att dess åtkomst kan kontrolleras. Visual Studio läser lagringsnyckeln från mapp för lokala felsökning och publiceringsprocessen. 
- Som svar på ändringar som beskrivs ovan, förbättrad Visual Studio Online-teamet aktivitet för Azure Cloud Services Distributionsmall så att användarna kan ange lagringsnyckeln för att ställa in diagnostik tillägget vid publicering till Azure i kontinuerlig integrering och distribution.
- Vi har gjort det möjligt att lagra säker anslutningssträngen och tokenisering för Azure Diagnostics (BOMULLSTUSS), som hjälper dig att lösa problem med konfigurationen över environements.
 
### <a name="windows-server-2016-virtual-machines"></a>Windows Server 2016 virtuella datorer

- Visual Studio stöder nu distribuera Cloud Services till OS-familjen 5 (Windows Server 2016) virtuella datorer. Du kan ändra dina inställningar om du vill rikta nya OS-familjen för befintliga molntjänster. När du skapar nya molntjänster, om du väljer att skapa tjänsten med hjälp av .net 4.6 eller högre, kommer den som standard tjänsten om du vill använda OS-familjen 5.  Mer information kan du granska den [gäst-OS-familjen stöder tabellen](../cloud-services/cloud-services-guestos-update-matrix.md).

### <a name="known-issues"></a>Kända problem

- Azure .NET SDK 3.0 introducerade ett problem när du tar bort Visual Studio-2017 i en konfiguration för sida vid sida med Visual Studio 2015.  Om du har Azure SDK för Visual Studio 2015 tas Microsoft Azure Storage-emulatorn och Microsoft Azure Compute Emulator bort om du avinstallerar Visual Studio 2017.  Detta genererar ett fel när du skapar och felsökning nya Cloud services-projekt i Visual Studio 2015. För att åtgärda problemet genom att installera Azure SDK från installationsprogram för webbplattform.  Problemet löses i en framtida uppdatering för Visual Studio-2017.  .

 
### <a name="azure-in-role-cache"></a>Azure i Rollinstanser 

- Stöd för cachelagring i Rollinstanser för Azure upphörde 30 November 2016. Mer information klickar du på [här](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).




