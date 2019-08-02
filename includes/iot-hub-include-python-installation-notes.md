---
title: ta med fil
description: ta med fil
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 07/24/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 103ad020b8d9f50ffe690f502b7cac08dab9237a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640447"
---
* Ett aktivt Azure-konto. (Om du inte har något konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.)

* **Windows**

    * [Python 2. x eller 3. x](https://www.python.org/downloads/). Se till att använda en 32-bitars eller 64-bitars installation beroende på vad som krävs för din konfiguration. Se till att du lägger till Python i den plattformsspecifika miljövariabeln när du uppmanas att göra det under installationen. Om du använder Python 2.x kan du behöva [installera eller uppgradera *PIP* (pakethanteringssystemet för Python)](https://pip.pypa.io/en/stable/installing/).

    * Om du använder Windows OS måste du se till att du har rätt version av det [visuella C++ omdistribuerbara paketet](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads) för att kunna använda inbyggda dll: er från python. Vi rekommenderar att du använder den senaste versionen.

    * Om det behövs installerar du paketet [Azure-iothub-Device-client](https://pypi.org/project/azure-iothub-device-client/) med hjälp av kommandot`pip install azure-iothub-device-client`

    * Om det behövs installerar du paketet [Azure-iothub-service-client](https://pypi.org/project/azure-iothub-service-client/) med kommandot`pip install azure-iothub-service-client`

* **Mac OS**

    För Mac OS behöver du python 3.7.0 (eller 2,7) + libboost-1,67 + spiral 7.61.1 (alla installerade via homebrew). Alla andra distributioner och operativ system kommer förmodligen att bädda in olika versioner av Boost &-beroenden som inte fungerar och leder till en ImportError vid körning.

    *Pip*-paketet för `azure-iothub-service-client` och `azure-iothub-device-client` är endast tillgänglig för Windows-Operativsystemet. För Linux/Mac OS, se avsnitten Linux och Mac OS-vissa i avsnittet [förbereda din utvecklings miljö för python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) post.

> [!NOTE]
> Det finns flera fel rapporter när du importerar iothub_client i ett exempel. Mer information om hur du hanterar **ImportError** -problem finns i [hantera problem med ImportError](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues).
