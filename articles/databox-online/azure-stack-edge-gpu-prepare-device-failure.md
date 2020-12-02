---
title: Förbered för Azure Stack Edge Pro-enhets problem
description: Beskriver hur du får en ersättning för en Azure Stack Edge Pro-enhet som misslyckades.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: 986a3c56a1e0dcc79ab472a7e18d7eeb7e2fddb5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448345"
---
# <a name="prepare-for-an-azure-stack-edge-pro-gpu-device-failure"></a>Förbered för ett Azure Stack Edge Pro GPU-enhets problem

Den här artikeln hjälper dig att förbereda ett enhets fel genom detaljerad information om hur du sparar och säkerhetskopierar enhets konfigurationen och data på din Azure Stack Edge Pro GPU-enhet. 

Artikeln innehåller inte steg för att säkerhetskopiera Kubernetes-och IoT-behållare som distribuerats på Azure Stack Edge Pro GPU-enhet. 

## <a name="understand-device-failures"></a>Förstå enhets problem

Din Azure Stack Edge Pro GPU-enhet kan uppleva två typer av maskin varu problem.

- Tillåtna problem som kräver att du ersätter en maskin varu komponent. Med dessa problem kan du använda enheten i ett degraderat tillstånd. Exempel på dessa fel är en enskild havererad enhet (PSU) eller en enskild havererad disk på enheten. I vart och ett av dessa fall kan enheten fortsätta att arbeta. Du rekommenderas att kontakta Microsoft Support den tidigaste för att ersätta de komponenter som misslyckades.

- Icke-tillåtna problem som kräver att du ersätter hela enheten. Ett exempel på det här felet är när två diskar har misslyckats på enheten. I de här instanserna kontaktar du Microsoft Support och när de har fastställt att en enhets ersättning behövs kan du under lätta bytet av Azure Stack Edge-enheten.

Om du vill förbereda för uteblivna haverier måste du säkerhetskopiera följande på din enhet:

- Information om enhets konfiguration.
- Data som finns i lokala resurser och gräns moln resurser.
- Filer och mappar som är kopplade till de virtuella datorer som körs på enheten.


## <a name="back-up-device-configuration"></a>Säkerhetskopiera enhets konfigurationen

Under den inledande konfigurationen av enheten är det viktigt att du behåller en kopia av enhetens konfigurations information som beskrivs i [Check listan för distribution](azure-stack-edge-gpu-deploy-checklist.md). Under återställningen används den här konfigurations informationen för den nya ersättnings enheten. 

## <a name="protect-device-data"></a>Skydda data på enheter

Enhets data kan vara av någon av följande typer:

- Data i gräns moln resurser
- Data i lokala resurser
- Filer och mappar på virtuella datorer

I följande avsnitt beskrivs stegen och rekommendationerna för att skydda var och en av dessa typer av data på enheten.

## <a name="protect-data-in-edge-cloud-shares"></a>Skydda data i gräns moln resurser

Du kan skapa gräns moln resurser som nivå data från din enhet till Azure. Beroende på vilken nätverks bandbredd som är tillgänglig konfigurerar du bandbredden på enheten för att minimera eventuell data förlust vid ett uteblivet haveri haveri.

> [!IMPORTANT] 
> Om det finns ett icke-anslaget haveri på enheten kan lokala data som inte är i nivå av-enhet till Azure gå förlorade. 

## <a name="protect-data-in-edge-local-shares"></a>Skydda data i lokala Edge-resurser

Om du distribuerar Kubernetes eller IoT Edge konfigurerar du för att spara program data på enheten lokalt och synkroniserar inte med Azure Storage. Data lagras på en resurs på enheten. Du kanske tycker det är viktigt att säkerhetskopiera data i dessa resurser.

Följande data skydds lösningar från tredje part kan tillhandahålla en säkerhets kopierings lösning för data i de lokala SMB-eller NFS-resurserna. 

| Tredjepartsprogram           | Referens till lösningen                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | https://www.cohesity.com/solution/cloud/azure/ <br> Kontakta Cohesity om du vill ha mer information.          |
| CommVault                      | https://www.commvault.com/azure <br> Kontakta CommVault om du vill ha mer information.          |
| Veritas                        | http://veritas.com/azure <br> Kontakta Veritas om du vill ha mer information.   |


## <a name="protect-files-and-folders-on-vms"></a>Skydda filer och mappar på virtuella datorer

Azure Stack Edge fungerar med Azure Backup och andra data skydds lösningar från tredje part för att tillhandahålla en lösning för säkerhets kopiering för att skydda data som finns i de virtuella datorer som distribueras på enheten. I följande tabell visas referenser till tillgängliga lösningar som du kan välja mellan.


| Säkerhets kopierings lösningar        | Operativ system som stöds   | Referens                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Microsoft Azure Recovery Services MARS-agenten för Azure Backup | Windows        | [Om MARS-agent](../backup/backup-azure-about-mars.md)    |
| Cohesity                | Windows, Linux | [Microsoft Azure integrering, lösning för säkerhets kopiering och återställning kort](https://www.cohesity.com/solution/cloud/azure) <br>Kontakta Cohesity om du vill ha mer information.                          |
| CommVault               | Windows, Linux | https://www.commvault.com/azure <br>Kontakta CommVault om du vill ha mer information.                          |
| Veritas                 | Windows, Linux | http://veritas.com/azure <br> Kontakta Veritas om du vill ha mer information.                    |



## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [återställer från en misslyckad Azure Stack Edge Pro GPU-enhet](azure-stack-edge-gpu-recover-device-failure.md).