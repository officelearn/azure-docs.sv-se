---
title: 'Azure AD Connect Cloud Provisioning agent: automatisk uppgradering | Microsoft Docs'
description: Den här artikeln beskriver den inbyggda funktionen för automatisk uppgradering i Azure AD Connect Cloud Provisioning-agenten.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d0f7093f44a284ec26907d7c4bcfb2bdfd04763
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85360919"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect Cloud Provisioning agent: automatisk uppgradering

Att se till att din Azure Active Directory (Azure AD) Connect-agenten för att ansluta till molnet är alltid uppdaterad är enkelt med funktionen för automatisk uppgradering.

Agenten installeras här: "program files\Azure AD Connect etablering Agent\AADConnectProvisioningAgent.exe"

Kontrol lera din version genom att högerklicka på den körbara filen och välja egenskaper och sedan information.

![Agent fil version](media/how-to-automatic-upgrade/agent1.png)

Agent uppdatering installeras här: "program files\Azure AD Connect Provisioning agent Updater\AzureADConnectAgentUpdater.exe"

Kontrol lera din version genom att högerklicka på den körbara filen och välja egenskaper och sedan information.

![Agent uppdaterings version](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>Avinstallera agenten
Om du vill ta bort agenten går du till **Avinstallera eller ändra ett program** och avinstallerar följande:

- **Microsoft Azure AD ansluta Agent uppdatering**
- **Microsoft Azure AD ansluta etablerings agent**
- **Microsoft Azure AD ansluta Agent paket för etablering**

![Borttagning av agent](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-provisioning.md)

